# Architecture — Lantern

> **Purpose of this file:** Explains *how* the app is built. Read `prd.md` first for *what* and *why*. No visual styling decisions here — those go in `design-system.md` later.

---

## 1. Big picture (plain language)

The app is **a web page wearing a desktop-app costume**, with **real PowerShell processes running behind it**.

- **Frontend (the face):** HTML/CSS/TypeScript inside a Tauri window. Draws the title bar, tabs, sidebar, and the terminal screen.
- **Backend (the muscle):** Rust. Starts PowerShell, pipes text in/out, reads files, measures CPU/RAM.
- **PowerShell (the brain):** Actually runs commands. We don't modify it.

```
┌──────────────────────── Tauri window (frameless) ────────────────────────┐
│ Title bar (custom HTML)                                [–] [□] [×]       │
│ Tabs: [ pwsh 1 ] [ pwsh 2 ] [+]                                          │
│ ┌──────────────┬──────────────────────────────────────────────────────┐  │
│ │ Sidebar      │  Terminal view (xterm.js)                            │  │
│ │ • Files      │                                                      │  │
│ │ • CPU / RAM  │                                                      │  │
│ └──────────────┴──────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────────┘
          ▲  Tauri commands (frontend → backend)
          ▼  Tauri events  (backend → frontend)
┌──────────────────────────── Rust backend ────────────────────────────────┐
│ PTY manager │ File service │ Stats service │ Settings service            │
└──────┬───────────────────────────────────────────────────────────────────┘
       ▼ ConPTY (Windows pseudo-console)
   pwsh.exe / powershell.exe   (one process per tab)
```

## 2. Tech stack

| Layer | Choice | Why |
|---|---|---|
| App shell | **Tauri v2** | Small, fast, low RAM; Rust backend |
| Frontend | **TypeScript + Vite** | Typed, fast dev server |
| UI framework | **Decide in M0** (lean: Svelte or React) | Keep it simple; record decision here |
| Terminal renderer | **xterm.js** + addons `fit`, `web-links`, `webgl` | Industry standard (VS Code uses it) |
| PTY | Rust crate **`portable-pty`** | Uses Windows **ConPTY** under the hood |
| System stats | Rust crate **`sysinfo`** | CPU + RAM |
| File watching | Rust crate **`notify`** | Live file explorer updates |
| Open files | **`tauri-plugin-opener`** | Opens files in default Windows app |
| Settings | **`serde` + `serde_json`** | Parse/validate JSON settings |

## 3. Key concept: the PTY (pseudo-terminal)

**Plain version:** PowerShell expects to talk to a real keyboard and screen. A PTY is a **fake keyboard + fake screen** we hand it. Whatever it "prints" we capture; whatever we "type" we feed in.

**On Windows**, this is called **ConPTY**. `portable-pty` wraps it.

### Data flow — typing a command
1. User presses a key → **xterm.js** fires `onData`.
2. Frontend calls Tauri command `pty_write(session_id, data)`.
3. Rust writes bytes into that session's PTY.
4. **PowerShell** runs the command and writes output.
5. A Rust reader thread reads the output and emits event `pty-output` `{ session_id, data }`.
6. Frontend calls `terminal.write(data)` on the matching xterm.js instance.

### Resize flow
Window/sidebar resizes → xterm.js `fit` addon computes new cols/rows → `pty_resize(session_id, cols, rows)` → Rust resizes the PTY so PowerShell redraws correctly.

## 4. Backend modules (Rust)

```
src-tauri/src/
├── main.rs            # Tauri setup, registers commands & plugins
├── pty/
│   ├── manager.rs     # HashMap<SessionId, PtySession>; spawn/write/resize/kill
│   └── shell.rs       # Find pwsh.exe → fallback powershell.exe; shell-integration script
├── fs/
│   ├── list.rs        # List directory contents
│   └── watch.rs       # Watch active directory with `notify`
├── stats.rs           # CPU + RAM sampling loop (sysinfo)
├── settings.rs        # Load, validate, default, save settings.json
└── error.rs           # Shared error type → readable messages for frontend
```

### Tauri commands (frontend → backend)
| Command | Purpose |
|---|---|
| `pty_spawn() -> session_id` | Start a new PowerShell session (new tab) |
| `pty_write(id, data)` | Send keystrokes |
| `pty_resize(id, cols, rows)` | Resize terminal |
| `pty_kill(id)` | Close tab's session |
| `fs_list(path)` | Get folder contents |
| `fs_watch(path)` | Watch active tab's folder |
| `open_path(path)` | Open file in default app |
| `settings_get()` / `settings_set(...)` | Read/write settings |

### Tauri events (backend → frontend)
| Event | Payload |
|---|---|
| `pty-output` | `{ session_id, data }` |
| `pty-exit` | `{ session_id, code }` |
| `cwd-changed` | `{ session_id, path }` |
| `fs-changed` | `{ path }` |
| `stats-update` | `{ cpu_percent, ram_used, ram_total }` |

## 5. Key concept: knowing the current folder (shell integration)

**Problem:** The file explorer must follow `cd`, but the terminal only sees text. How does it know the folder changed?

**Solution:** When spawning PowerShell, inject a small script that wraps the `prompt` function. Every time the prompt is drawn, it also emits an invisible escape sequence containing the current path (OSC 7 or `OSC 9;9`, the same trick Windows Terminal uses).

- Frontend registers an xterm.js OSC handler → parses the path → updates that tab's `cwd` → sidebar refreshes.
- Must **wrap**, not replace, the user's existing prompt (so Oh My Posh etc. still work).

**Folder click → cd:** frontend sends `Set-Location -LiteralPath '<path>'` + Enter to the active PTY (escape single quotes in path).

**File click → open:** `open_path(path)` via `tauri-plugin-opener`.

## 6. Frontend structure

```
src/
├── main.ts
├── app/              # App root, layout (title bar / tabs / sidebar / terminal area)
├── titlebar/         # Drag region (data-tauri-drag-region), window buttons
├── tabs/             # Tab state: { id, sessionId, title, cwd }
├── terminal/         # xterm.js wrapper, one instance per tab, OSC cwd handler
├── sidebar/
│   ├── Sidebar       # Toggle + resize handle, width persisted to settings
│   ├── FileExplorer  # Lists active tab's cwd
│   └── SystemStats   # Subscribes to stats-update
├── settings/         # Typed settings + defaults
└── lib/ipc.ts        # Typed wrappers for all Tauri commands & events
```

**Rule:** components never call Tauri directly — always go through `lib/ipc.ts`.

## 7. Window setup

- `tauri.conf.json`: `decorations: false`, `transparent` decided later (design phase).
- Title bar drag via `data-tauri-drag-region`.
- Buttons call Tauri window API: `minimize()`, `toggleMaximize()`, `close()`.
- Edge resizing must still work with decorations off (verify in M1).

## 8. Settings

- Location: `%APPDATA%\Lantern\settings.json`
- Created with defaults on first run.
- Example shape (keys may grow):

```json
{
  "shell": { "path": "auto" },
  "sidebar": { "visible": true, "width": 260 },
  "terminal": { "fontFamily": "Cascadia Mono", "fontSize": 14, "scrollback": 5000 },
  "keybindings": {
    "newTab": "Ctrl+Shift+T",
    "closeTab": "Ctrl+Shift+W",
    "toggleSidebar": "Ctrl+B"
  }
}
```

- `"auto"` = pwsh 7 → fallback 5.1.
- Unknown/invalid values → use default + log warning. Never crash.

## 9. Error handling & robustness

- Each PTY session is isolated: one crashing session emits `pty-exit`, tab shows "Process exited", other tabs unaffected.
- No `unwrap()` on user-influenced data in Rust; return typed errors.
- File explorer handles: access denied, deleted folder, huge folders (cap + "show more").
- Stats loop runs on its own thread at a fixed interval; stops when app closes.

## 10. Performance notes

- Batch PTY output before emitting events (avoid one event per byte).
- Use xterm.js **WebGL** renderer, fall back to canvas if unavailable.
- Only watch the **active tab's** folder.

## 11. Repo & conventions

- Public GitHub repo. MIT license (confirm before first release).
- `README.md`: what it is, how to run (`npm install`, `npm run tauri dev`), prerequisites (Rust, Node, WebView2).
- Small commits per milestone; conventional commit messages (`feat:`, `fix:`, `docs:`).
- Docs (`prd.md`, `architecture.md`) updated whenever a decision changes.

## 12. Decisions log

| Date | Decision |
|---|---|
| 2026-10-12 | Tauri v2 chosen over Electron |
| 2026-10-12 | Linux command translation dropped |
| 2026-10-12 | PowerShell 7 default, 5.1 fallback |
| 2026-10-12 | v1 sidebar = file explorer + CPU/RAM stats |
| 2026-10-12 | Settings via JSON file only (no UI in v1) |
| 2026-10-12 | Visual design deferred to `design-system.md` |
| 2026-10-12 | App named **Lantern** |
