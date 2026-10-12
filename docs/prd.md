# PRD — Lantern (custom Windows terminal)

> **Purpose of this file:** Tells every Claude Code session *what* we are building and *why*. For *how* it's built, read `architecture.md`. Visual design lives in a future `design-system.md` (not written yet — do **not** invent a theme).

---

## 1. Problem

Existing Windows terminals (Windows Terminal, WezTerm, Tabby) are powerful but feel generic. I want a terminal that is **mine**: a fully custom window, plus a sidebar that shows useful context (files, system stats) right next to the shell.

## 2. Goal

Build a **highly customisable desktop terminal app for Windows** that runs **PowerShell** inside a fully custom window, with a toggleable sidebar for a file explorer and system stats.

This is **not** a new shell. PowerShell does all command execution. We build the *window around it*.

## 3. Audience & status

- **Primary user:** me (Shafin), daily driver on my Windows PC.
- **Development:** built in a **public GitHub repo** from day one.
- **Future:** may become a product if it goes well → keep code clean, modular, and documented.
- **Secondary goal:** learning. Code should be readable and explained, not clever.

## 4. Scope — v1 features

### 4.1 Core terminal
- Runs **PowerShell 7 (`pwsh.exe`)** by default.
- **Falls back** to built-in Windows PowerShell 5.1 (`powershell.exe`) if pwsh 7 isn't installed.
- Full interactive support: colours, cursor movement, progress bars, Ctrl+C, programs like `vim`/`less`.
- Copy/paste, scrollback, clickable URLs.
- Terminal resizes correctly when the window or sidebar resizes.

### 4.2 Custom window
- **Frameless window** (no default Windows title bar).
- **Custom title bar** with: drag-to-move area, minimise, maximise/restore, close.
- Double-click title bar → maximise/restore.
- Native-feeling window behaviour (snap, resize from edges).

### 4.3 Tabs
- Multiple tabs, each running its own independent PowerShell session.
- New tab, close tab, switch tab (mouse + keyboard shortcuts).
- Closing the last tab closes the app.

### 4.4 Sidebar
- **Toggle** show/hide with a keyboard shortcut.
- **Resizable** by dragging its edge; width is remembered.
- Contains two sections:

**a) File explorer**
- Shows contents of the **active tab's current directory** and updates when you `cd`.
- **Click a folder → `cd` into it** in the active terminal.
- **Click a file → open it** in its default Windows app.
- Updates when files are added/removed/renamed.

**b) System stats**
- **CPU usage** (%) and **RAM usage** (used / total), live-updating (~1–2s).

### 4.5 Settings
- A **JSON settings file** (like VS Code's `settings.json`) for: default shell path, sidebar width & visibility, keyboard shortcuts, font family/size, scrollback length.
- Invalid settings → fall back to defaults + show a clear error, never crash.

## 5. Non-goals (v1)

- ❌ Linux-to-Windows command translation (dropped)
- ❌ Writing our own shell
- ❌ Visual theme / design system (comes later in `design-system.md`)
- ❌ Split panes (maybe v2)
- ❌ Settings UI panel (JSON only for v1)
- ❌ CMD / Git Bash / WSL profiles (maybe later)
- ❌ macOS / Linux support
- ❌ Plugins, AI features, cloud sync

## 6. Success criteria

- I use it as my **daily terminal for 1 week** without going back to Windows Terminal.
- Opens in **< 1 second**, idle RAM reasonably low (Tauri advantage).
- Never loses a session due to UI bugs; one tab crashing doesn't kill others.
- A new contributor can clone the repo and run it using only the README.

## 7. Milestones

| # | Milestone | Done when… |
|---|---|---|
| M0 | Skeleton | Tauri app opens a window with one working PowerShell session |
| M1 | Custom window | Frameless window + custom title bar works like a native one |
| M2 | Tabs | Multiple independent sessions, keyboard shortcuts |
| M3 | Sidebar shell | Toggle + resize + remembered width |
| M4 | File explorer | Tracks cwd, folder → cd, file → open, live updates |
| M5 | System stats | Live CPU + RAM |
| M6 | Settings | JSON settings loaded, validated, applied |
| M7 | Polish | README, error handling, packaging (installer) |

## 8. Open questions

- Default keyboard shortcuts (propose in M2, confirm with me).
- Visual design → `design-system.md`.

## 9. Rules for Claude Code sessions

- Read `prd.md` + `architecture.md` before writing code.
- Work **one milestone at a time**; don't build ahead.
- **Explain each file you create** in plain language — this is a learning project.
- Don't add features outside v1 scope without asking.
- Update these docs when a decision changes.
