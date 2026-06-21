# Changelog

All notable changes to SymbiontAI are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.1] — 2026-06-21

### Fixed
- **Distributable started with the developer's machine state.** The packaged app
  keyed its per-user data (recent projects, settings, onboarding flag) by the dev
  package name, so it shared state with `npm run desktop` and previous builds —
  an unzipped copy reopened a prior project and skipped onboarding. The packaged
  app now uses its own isolated `SymbiontAI` user-data directory.
- **First run no longer guesses a project.** With no history, the app opens with
  no project and shows the onboarding wizard instead of falling back to a dev
  `sym-sandbox` path or the working directory; the daemon starts only once a
  project is chosen. (The release zip never contained user data — the leak was
  this shared-state behavior on the local machine.)

## [1.1.0] — 2026-06-21

### Added
- **Real Codex usage in the statusline.** Codex exposes its rate limits only in
  the TUI (`/status`), but it persists them to its session rollout files; the
  daemon now reads the freshest `rate_limits` (5h + weekly %, reset times, plan)
  and shows them — the same numbers as `codex /status`.
- **`@skill:` / `@agent:` autocomplete suggestions.** Typing `@` now lists the
  installed skills and agents (plus files and agent targets) to pick from,
  instead of only resolving an exact reference.
- **Docking viewer/editor.** The file viewer and editor now dock **to the right**
  of the agent panes (a third column) or **below** them — resizable, closeable,
  and position-remembered — so the agents stay visible while you read or edit.

### Changed
- **Honest usage only.** Removed every Symbiont-estimated token count and dollar
  cost from the UI. The statusline now shows exclusively **real** subscription
  usage from each provider's official status (Claude `/usage`, Codex `/status`),
  plus real context %. Removed the topbar budget readout, the tracked-spend
  windows, and the spend line from the terminal UI.
- Official, rewritten **README**; added this **CHANGELOG**.

### Removed
- The `/budget` command and the budget-cap display (the dispatch-halt cap was
  already non-enforcing; the cost figures behind it were estimates).

### Security
- Audited the repository and the distributable: no personal data, home/build
  paths, emails, machine names, secrets, or tokens leak into tracked files, the
  daemon bundle, or the packaged app. Runtime/build directories remain
  git-ignored.

### Fixed
- The viewer and editor refuse binary and compressed files (archives,
  executables, images, media, fonts, databases) instead of rendering garbage,
  detecting them by extension and by NUL-byte sniffing.

## [1.0.0] — 2026-06-20

First packaged release of the SymbiontAI desktop app.

### Added
- **Local distributable** — portable `.exe` and `.zip`, fully offline with no
  telemetry or data collection; daemon bundled to a single JS file. Optional
  bundled Node runtime (`dist:bundled`).
- **First-run onboarding wizard** that checks prerequisites and helps pick a
  project.
- **Real logo** for the app, plus the **window/taskbar icon** (SVG rasterized to
  PNG/ICO).
- Real **Claude** subscription usage in the statusline via `claude -p /usage`.

### Changed
- Layout fixes (global hidden rule; Codex pane minimum width).

## [0.4.0] — Desktop IDE features

### Added
- **Drag-and-drop tabs** between the left and right sidebars.
- **Editor tab** with syntax highlighting (edit/save files; colorized viewer).
- **Right sidebar** with selectable tab placement and an Environment (`.env`) tab.
- **Native top menu bar** (File / Edit / View / Help) and a **"How to use"**
  guide overlay.
- **Menu / settings drawer** — themes (dark/light), accessibility (font size,
  high contrast, reduce motion).
- **Interactive sidebar tabs** — directory tree, tasks, agents, skills, hooks,
  **GitHub** (issues/PRs via `gh`), and **git** (commits, branches, PRs).
- **Chat attachments** — paste screenshots and add files/folders to the prompt.
- **Per-provider statusline** — model · effort · context %, plus dir / worktree /
  branch.

### Removed
- The dispatch-halt token budget cap (spend was tracked but never enforced).

## [0.3.0] — Sessions, coordination & SDLC library

### Added
- **Persistent agent sessions** — live, stateful chat instead of one-shot
  commands, with **session resume across restarts** (chat and `/work`).
- **Persistent worktree coding sessions** — `/work`, `/w`, `/endwork` with real
  file writes and commits.
- **Coordinator-driven delivery** — `/deliver <goal>` decomposes a goal into a
  reviewable plan (approve before dispatch) and runs a team of specialist agents.
- **`/converse`** turn-taking so agents alternate and respond to each other.
- **Preloaded SDLC library** of customizable skills and agent personas, with
  AI-generated "New…" buttons; later rewritten to be more powerful.
- **Fast-Codex toggle** — skip MCP + plugins per project (~2× faster turns).
- Honest statusline (real model / effort / context %).

### Fixed
- Codex passivity via **task-first prompts**; chat view rendered inside the agent
  panes.

## [0.2.0] — IDE foundation & communication

### Added
- **SymbiontAI desktop app** (Electron) sharing one `Controller` with the TUI.
- **Project selector**, file explorer/viewer, task board, conversation view, and
  command autocomplete.
- **Shared conversation relay** so agents can see each other's messages.
- **Logging layer** (debug/info/warn/error) with live viewing.
- Resizable/collapsible panes and frontend design polish.

### Fixed
- Codex tasks that hung forever waiting on stdin.

## [0.1.0] — Core orchestrator

### Added
- Multi-agent CLI orchestrator: provider adapters, SQLite task board,
  worktree-per-task isolation, rule-based router + DAG scheduler, turn-based
  mailbox, the symbiosis review loop, and an Ink TUI.

[1.1.1]: https://github.com/ESCID94/SymbiontAI/releases/tag/v1.1.1
[1.1.0]: https://github.com/ESCID94/SymbiontAI/releases/tag/v1.1.0
[1.0.0]: https://github.com/ESCID94/SymbiontAI/releases/tag/v1.0.0
