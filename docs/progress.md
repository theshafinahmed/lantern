# Progress — Lantern

> **Purpose of this file:** The "where are we right now" page. Any new Claude Code session reads this **after** `CLAUDE.md`, `prd.md`, `architecture.md` and `design-system.md`, and knows exactly what is done, what is next, and what is blocked. It records *state*, not design. Decisions go in the log at the bottom of `architecture.md`.

**Last updated:** 2026-10-12

---

## 1. Start here (next action)

**Current milestone:** none started. The project is in the **pre-M0** stage.

**Next action:** work through the pre-M0 checklist below, then show Shafin a short M0 plan and wait for his go-ahead before writing code.

### Pre-M0 checklist

- [ ] Check the machine has the tools: Rust (`rustc --version`), Node (`node --version`), and WebView2. Nothing has been checked yet.
- [ ] Decide the UI framework (Svelte, React, or plain TypeScript). Ask Shafin with a multiple-choice question, then log the decision in `architecture.md`.
- [ ] Pick a commit style. `architecture.md` says `feat:` / `fix:` prefixes, `CLAUDE.md` says plain imperative with no prefix. Until Shafin picks, follow `CLAUDE.md`.
- [ ] Show the M0 plan and get the go-ahead.

## 2. Milestones

Status values: `not started`, `in progress`, `done`, `blocked`. Update the row **and** the date when a status changes. Work one milestone at a time; do not build ahead.

| # | Milestone | Status | Notes |
|---|---|---|---|
| M0 | Skeleton: Tauri app opens a window with one working PowerShell session | not started | Needs the pre-M0 checklist first |
| M1 | Custom window: frameless window and custom title bar | not started | |
| M2 | Tabs: multiple independent sessions, keyboard shortcuts | not started | Default shortcuts must be proposed to Shafin and confirmed |
| M3 | Sidebar shell: toggle, resize, remembered width | not started | |
| M4 | File explorer: tracks cwd, folder → cd, file → open, live updates | not started | |
| M5 | System stats: live CPU and RAM | not started | |
| M6 | Settings: JSON loaded, validated, applied | not started | |
| M7 | Polish: README, error handling, installer | not started | App icon (`.ico`) is made from `docs/assets/logo.svg` here |

## 3. Done so far

| Date | What |
|---|---|
| 2026-10-12 | Project instructions (`CLAUDE.md`) added |
| 2026-10-12 | PRD and architecture docs written |
| 2026-10-12 | Design system written: warm amber on charcoal, dark only, softly rounded; logo B chosen (`design-system.md`, `assets/logo.svg`) |
| 2026-10-12 | Progress tracker added (this file) |

## 4. Repo state

- **Code:** none yet. The repo holds docs only (`CLAUDE.md`, `docs/`). There is no `package.json`, no `src/`, no `src-tauri/`.
- **Remote:** `https://github.com/theshafinahmed/lantern`. Default branch `main`. Never push to `main`; use a prefixed branch and a PR (squash merge).
- **Local leftovers:** old merged branches may still exist locally. Ask Shafin before deleting any. Never delete remote branches on your own.

## 5. Open items and blockers

| Item | Needed from | Where it matters |
|---|---|---|
| UI framework (Svelte / React / plain TS) | Shafin | M0 |
| Commit style: prefixes or plain imperative | Shafin | First code commit |
| Default keyboard shortcuts | Shafin | M2 |
| Icon source: Lucide is proposed | Shafin | First UI work (M1) |
| Tagline: "Warm light for the command line." is a placeholder | Shafin | M7 or the README |
| MIT licence for the public repo | Shafin | First release |
| ANSI colour contrast was not measured one by one | Claude | When the terminal first renders (M0 or M1) |

## 6. How to keep this file up to date

Every session, before finishing:

1. Update **Last updated** and section 1 so the next session knows the exact next action.
2. Update the milestone row if a status changed. Add a row to **Done so far** for anything finished.
3. Add or remove rows in **Open items** as they appear or get resolved.
4. If a decision changed, also add a row to the decisions log in `architecture.md` (and update `prd.md` if the scope changed).
5. Commit this file together with the work it describes, on a prefixed branch.

Keep it short. If something needs a long explanation, put it in the right doc and link to it from here.
