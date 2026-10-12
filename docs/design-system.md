# Design System — Lantern

> **Purpose of this file:** The single source of truth for how Lantern *looks*. Read `prd.md` for *what* we build and `architecture.md` for *how* it is built. When you write UI code, take every colour, size and font from here. Do not invent values.

**Direction:** warm light in the dark. Dark warm-charcoal surfaces, one amber accent, softly rounded corners. Dark theme only in v1.

---

## 1. Principles

1. **One light source.** Amber (`accent`) is the only bright colour. Use it for one or two things per screen: the active tab, the cursor, the meter fill, the logo. If everything glows, nothing does.
2. **Surfaces step up in lightness.** Back to front: `bg-sunken` → `bg-base` → `bg-raised` → `bg-overlay` → `bg-active`. Never put a lighter surface behind a darker one.
3. **Borders over shadows.** Regions are separated by 1px `border-subtle` lines. Shadows are only for things that float (menus, banners).
4. **Colour is never the only signal.** Status colours always come with an icon and a word.
5. **Quiet and fast.** Hover and focus changes take 120ms ease-out, panels 160ms. Nothing animates inside the terminal. With `prefers-reduced-motion`, use 0ms.

## 2. Colour

Names below become CSS custom properties with the same name, for example `--bg-base`. Contrast was worked out for the main text and border pairs (4.5:1 for text, 3:1 for meaningful borders). The 16 ANSI colours were chosen for readability on `bg-base` but not each measured individually; recheck if one looks dim.

### Surfaces and borders

| Token | Hex | Use |
|---|---|---|
| `bg-sunken` | `#14110F` | Title bar and tab strip |
| `bg-base` | `#1A1714` | Window background and terminal screen |
| `bg-raised` | `#211D19` | Sidebar and panels |
| `bg-overlay` | `#2B261F` | Hovered rows and tabs, menus, meter tracks |
| `bg-active` | `#352E25` | Pressed or selected items |
| `border-subtle` | `#2E2822` | Decorative dividers between regions |
| `border-strong` | `#756A5B` | Borders that mark a control or panel edge (3:1 or better) |

### Text

| Token | Hex | Use |
|---|---|---|
| `text-primary` | `#EFE7DC` | Main text, terminal foreground |
| `text-secondary` | `#B8AD9D` | Inactive tab labels, file names |
| `text-muted` | `#9C9181` | Captions, section labels, hints. Not on `bg-active` |
| `text-disabled` | `#6E655A` | Disabled controls only (exempt from 4.5:1) |
| `text-link` | `#FFC866` | Clickable URLs and links. Always underlined |

### Accent

| Token | Value | Use |
|---|---|---|
| `accent` | `#F5A524` | The Lantern amber |
| `accent-hover` | `#FFB83D` | Hover of anything filled with `accent` |
| `accent-pressed` | `#D98E12` | Pressed state |
| `accent-subtle` | `#F5A52424` | Faint wash behind the active or current item |
| `on-accent` | `#1A1714` | Text or icons on a solid amber fill |
| `focus-ring` | alias of `accent-hover` | Keyboard focus: 2px solid, 2px offset |

### Status

| Token | Hex | Use |
|---|---|---|
| `success` | `#8CC084` | With a check icon and the word |
| `danger` | `#F2766B` | With an alert icon and the word |
| `info` | `#7FB2E5` | With an info icon and the word |
| `danger-solid` | `#C42B1C` | Hovered close button fill (same red as Windows) |
| `on-danger-solid` | `#FFFFFF` | Icon on `danger-solid` |

### Terminal

These map straight onto the xterm.js `theme` option.

| Token | Value | xterm.js option |
|---|---|---|
| `terminal-bg` | alias of `bg-base` | `background` |
| `terminal-fg` | alias of `text-primary` | `foreground` |
| `terminal-cursor` | alias of `accent` | `cursor` |
| `terminal-cursor-text` | alias of `on-accent` | `cursorAccent` |
| `terminal-selection` | `#F5A52440` | `selectionBackground` |

| ANSI | Normal | Bright |
|---|---|---|
| Black | `ansi-black` `#3A332B` | `ansi-bright-black` `#8A8070` |
| Red | `ansi-red` `#E5786D` | `ansi-bright-red` `#FF8F82` |
| Green | `ansi-green` `#9CC27A` | `ansi-bright-green` `#B3DB8F` |
| Yellow | `ansi-yellow` `#E5B94E` | `ansi-bright-yellow` `#FFD36B` |
| Blue | `ansi-blue` `#6FA3DE` | `ansi-bright-blue` `#8FBBF0` |
| Magenta | `ansi-magenta` `#C792D6` | `ansi-bright-magenta` `#DDA8EC` |
| Cyan | `ansi-cyan` `#6CC0BE` | `ansi-bright-cyan` `#8ADAD7` |
| White | `ansi-white` `#D8CEBF` | `ansi-bright-white` `#F5EEE3` |

`ansi-black` is a background colour; do not set text in it. `ansi-bright-black` is deliberately readable (4.6:1) because PSReadLine suggestions and comments use it.

## 3. Typography

| Role | Family | Stack |
|---|---|---|
| Terminal and numbers | `mono` | `"Cascadia Mono", "Cascadia Code", Consolas, "Courier New", monospace` |
| Interface | `ui` | `"Segoe UI Variable Text", "Segoe UI", system-ui, sans-serif` |

The terminal font size and family are user settings; the values below are the defaults.

| Style | Family | Size / line | Weight | Use |
|---|---|---|---|---|
| `terminal-text` | mono | 14px / 1.2 | 400 | Everything in the xterm.js screen |
| `stat-value` | mono | 13px / 20px | 400 | CPU and RAM numbers (tabular digits) |
| `wordmark` | ui | 15px / 20px | 600 | "Lantern" beside the logo |
| `heading` | ui | 16px / 24px | 600 | Banner titles, empty states |
| `tab-label` | ui | 13px / 16px | 500 | Tab names |
| `body` | ui | 13px / 20px | 400 | File names, messages |
| `caption` | ui | 12px / 16px | 400 | Hints, paths, details |
| `section-label` | ui | 11px / 16px, letter-spacing 0.08em | 600 | FILES, SYSTEM. Uppercase |

## 4. Spacing, shape, depth, size

| Group | Token | Value | Use |
|---|---|---|---|
| Spacing | `space-1` | 4px | Icon-to-label gap |
| | `space-2` | 8px | Standard gap and padding |
| | `space-3` | 12px | Row and banner side padding |
| | `space-4` | 16px | Panel padding |
| | `space-5` | 24px | Gap between sidebar sections |
| | `space-6` | 32px | Large gaps, dialogs |
| Radius | `radius-sm` | 4px | Scrollbar thumbs, small chips, cursor |
| | `radius-md` | 8px | Buttons, tabs, file rows |
| | `radius-lg` | 12px | Menus, banners, dialogs, logo tile |
| | `radius-full` | 999px | Meter bars, status dots |
| Shadow | `shadow-menu` | `0 8px 24px 0 #00000066` | Floating menus, banners |
| | `shadow-glow` | `0 0 16px 0 #F5A52438` | Active tab, logo (sparingly) |
| | `shadow-glow-cursor` | `0 0 10px 0 #F5A52480` | Block cursor |
| Size | `titlebar-height` | 36px | Custom title bar |
| | `window-button-width` | 46px | Minimise, maximise, close |
| | `tab-height` | 32px | Tab and tab strip |
| | `row-height` | 28px | Sidebar file row |
| | `sidebar-width-default` | 260px | First-run sidebar width |
| | `sidebar-width-min` / `-max` | 180px / 480px | Drag limits |
| | `resize-handle-hit` | 6px | Grab area on the sidebar edge |
| | `icon-size` | 16px | All icons |

## 5. States

- **Hover:** one surface step lighter (`bg-overlay`). **Pressed:** two steps (`bg-active`). **Selected or current:** `accent-subtle` behind the row, `text-primary` text.
- **Filled amber controls:** `accent` → `accent-hover` → `accent-pressed`, text in `on-accent`.
- **Close button hover:** `danger-solid` with `on-danger-solid`.
- **Focus:** 2px solid `focus-ring`, 2px offset, on every focusable control. Never remove it. In the terminal the block cursor is the focus indicator.
- **Disabled:** `text-disabled`, no hover.

## 6. Layout

```
┌ title bar  36px  bg-sunken ─────────────────────────────── [–][□][×] ┐
├ tab strip  32px  bg-sunken ───────────────────────────────────────────┤
│ sidebar 260px  │ terminal fills the rest                              │
│ bg-raised      │ bg-base                                              │
└────────────────┴──────────────────────────────────────────────────────┘
```

## 7. Components

Each component's rules are listed here; the visual previews live in the Lantern design system artifact (owned by Shafin).

| Component | Rule summary |
|---|---|
| **TitleBar** | 36px, `bg-sunken`, 1px `border-subtle` below. Logo mark (20px) and "Lantern" on the left, drag area, then 46px window buttons with thin 10px glyphs. Hover `bg-overlay`; close hover `danger-solid`. Dim name, logo and glyphs to `text-muted` when the window is inactive. |
| **Tabs** | 32px strip on `bg-sunken`. Tabs have `radius-md` top corners. Active tab: `bg-base`, `text-primary`, `shadow-glow`, 2px `accent` underline. Inactive: `text-secondary`; hover `bg-overlay`. Close button shows on hover and on the active tab. A finished process shows a `danger` dot and the word "exited". |
| **Sidebar** | `bg-raised`, 1px `border-subtle` on the right. Sections separated by `space-5`, each with a `section-label`. Resize handle: 6px grab area, 2px `accent` line on hover or drag. Clamp 180 to 480px. |
| **FileRow** | 28px, `space-3` side padding, `radius-md`. Folder icon `accent`, file icon `text-secondary`. ".." first, folders before files. Unreadable folder: lock icon, `text-muted`, "No access". Long lists end with a "Show more (N more)" row in `text-link`. |
| **StatMeter** | Label (`caption`), value (`stat-value`), 6px `radius-full` bar on `bg-overlay` with an `accent` fill. At 85% and above the fill turns `danger` and the word "High" appears. Always write the unit. |
| **Terminal** | xterm.js theme built from the terminal tokens only. Block cursor with `shadow-glow-cursor`, blink off by default. Links in `text-link`, underlined. "Process exited with code N" in `text-muted`. Never recolour what PowerShell prints. |
| **Banner** | `bg-raised`, 1px `border-strong`, `radius-lg`, `shadow-menu`. Icon plus a label word ("Error", "Info"), `heading` title, `body` message. One `accent` primary button at most. Settings problems show a banner and carry on with defaults. Never auto-dismiss errors. |

## 8. Voice

- Short, plain sentences in sentence case. Speak to the person as "you".
- Say what happened, what Lantern did instead, and what they can do. Example: "Couldn't read settings.json. Lantern is using the default settings. Fix the file and it reloads on its own."
- No emoji in the interface, no exclamation marks, no jokes in errors.
- Use Windows and PowerShell words: "folder", "tab", "Process exited".
- Buttons are verbs: "Open file", "Dismiss", "Show more".
- Always write units: "23%", "8.2 of 15.9 GB".

## 9. Icons

16px grid, 1.5px stroke, round caps and joins, no fill, drawn in `currentColor`. Window buttons use the thin Windows glyphs (line, square, two squares, cross). One icon family only; **Lucide (ISC licence) is proposed** and needs your confirmation before we add it as a dependency. No emoji.

## 10. Logo

The mark is an amber prompt chevron and a glowing cursor bar on a rounded `bg-raised` tile. File: [`assets/logo.svg`](assets/logo.svg).

- Use on `bg-sunken`, `bg-base` or `bg-raised`. Never on a light surface, never recoloured.
- Wordmark: "Lantern" in `wordmark` style, `text-primary`, `space-2` to the right of the mark.
- Clear space: a quarter of the mark's width on every side.
- At 16px and below, use the mark alone.
- The only glow is `shadow-glow`.
- The same mark becomes the Windows app icon (`.ico`: 16, 32, 48, 256) at the packaging step in M7.

## 11. Open items

- Confirm Lucide as the icon source.
- Confirm the tagline "Warm light for the command line." (placeholder).
- Light theme is out of scope for v1. The colour tokens are named by role, so a second theme can be added later.
