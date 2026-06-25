# Changelog

All notable changes to SymbiontAI are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [3.1.0] — 2026-06-25

Hardening release that validates and rounds out the v3.0 multi-provider work. The
headline addition is an in-app provider selector — no more hand-editing config.

### Added
- **In-app provider selector (Settings → Providers).** Enable or disable any of
  the five providers (Claude, Codex, Gemini, Copilot, Antigravity) with a
  checkbox; **Apply & reload** rewrites the config and reloads the project — no
  hand-editing. A **max-simultaneous cap** (default 2) keeps teams small with a
  complexity warning when you go higher; disabling a routed provider **remaps
  routing automatically**.
- **Provider error guide.** Failures now show a clear fix (Gemini → set
  `GEMINI_API_KEY`; Copilot → `/login` or "session expired, start a new chat";
  missing CLI → "not on PATH") instead of a bare exit code. Copilot auto-retries
  once when a saved session has expired.
- **Animated dark/light theme toggle** (moon ⇄ sun), a **resizable message box**
  (drag to grow, Shift+Enter for a newline), and a **Setup & welcome** shortcut.
- **Intel-Mac build** (`darwin-x64`) alongside Apple Silicon, with a CI guard that
  fails the build if a runner's architecture drifts from its label.

### Changed
- **Settings drawer redesigned** into collapsible groups (Providers · Appearance ·
  Layout · Advanced); the **hamburger** now toggles it and stays visible.
- **Onboarding** reframed to list the AI providers it **detected** (all five, ✓/✗,
  enabled vs. available) rather than "required prerequisites" — you only need one.
- The **How to use** guide rewritten for five providers and Windows/macOS/Linux,
  with a Troubleshooting section.

### Fixed
- `@all` no longer breaks with a dispatch-only provider enabled; `/converse` warns
  instead of doing nothing; `/deliver` no longer misroutes to an unconfigured
  Claude; Copilot/Gemini success is no longer misreported as failure; the daemon
  fails fast with a clear message under Node < 22.

## [3.0.0] — 2026-06-24

The cross-platform + multi-provider release. SymbiontAI now runs on **Windows,
macOS, and Linux**, and can drive **up to five AI CLIs** — Claude Code, Codex,
Gemini, GitHub Copilot, and Antigravity — chosen per project. Claude + Codex
behaviour is unchanged; the new providers are purely additive and off by default.

### Added
- **Cross-platform packaging (macOS / Linux / Windows).** The bundled-Node
  fetcher resolves the right Node build for `darwin-arm64`, `darwin-x64`,
  `linux-x64`, `linux-arm64`, and `win32-x64/arm64`, lays the binary out where
  each platform's launcher expects it, and marks it executable on Unix. The
  packager stores symlinks (`zip -y`) so a macOS `.app` bundle stays valid.
- **Three new AI providers**, selectable alongside Claude Code and Codex:
  - **Gemini CLI** — full chat + coding support via `gemini -o stream-json`,
    with session continuity. Plan/yolo approval modes map to read-only vs. write.
  - **GitHub Copilot CLI** — full chat + coding support via JSON output, with
    session continuity. Spend is reported as premium-request count, not tokens.
  - **Antigravity CLI** (`agy`) — **dispatch-only** (one-shot coding tasks; the
    worktree diff is the deliverable). Its print mode does not stream text back,
    so it is not offered for chat or as a constrained reviewer.
- **Provider registry** — a single source of truth (`PROVIDER_CATALOG` +
  `ProviderRegistry`) that every surface (orchestrator, daemon, TUI, desktop
  panes) derives from. Enabling a provider is one config block; the panes,
  routing targets, `@mentions`, status lines, and settings rows all follow.
- **Per-project provider selection.** `sym.config.yaml` lists which providers are
  active; only configured providers that ship an adapter appear. Routing rules
  (`rules.*.provider`, `second_opinion.provider`) may now name any active provider.

### Changed
- The desktop and TUI agent panes are now generated dynamically from the active
  provider set instead of two hardcoded Claude/Codex columns, with a per-provider
  accent color. Model/effort overrides are keyed by provider.
- Runtime model/effort overrides generalized from Claude/Codex-specific fields to
  per-provider maps.

### Notes
- **No behavioural change for Claude or Codex** — the existing test suite passes
  unedited; the registry refactor is byte-for-byte compatible for the original two.
- New providers require their own CLI installed and authenticated; each
  `healthcheck` is a cheap `--version` probe. No test spends money.

## [2.0.0] — 2026-06-21

### Added
- **Live conversation control (Stop / Queue / Steer).** Each agent pane now has a
  **⏹ Stop** button that interrupts the agent's current turn *and* clears its
  queue. Sending a message to an agent that is mid-turn opens an inline
  **Queue · Steer · Cancel** chooser (`Q` / `S` / `Esc`): **Queue** runs your
  message after the current turn; **Steer** interrupts the current turn and runs
  yours immediately. Interrupting recreates the live session from its stored
  resume id, so conversation history is preserved.
- **Editable message queue per agent.** Messages waiting behind a running turn are
  shown in a per-pane strip; each can be edited in place (Enter to save) or
  removed while it is still queued. The strip updates live as the queue drains.
- **Terminal-style command history.** ↑/↓ in the command bar walk previously sent
  lines (the in-progress draft is preserved as the newest entry); the autocomplete
  popup still owns ↑/↓ while it is open.
- **`/goal` command.** Set a standing team goal that is announced once to both
  agents and re-injected as a context line on every subsequent turn, so it stays
  in front of the team. `/goal` shows the current goal; `/goal clear` unsets it.
- **Modern file tree.** The explorer is now a proper collapsible IDE tree:
  folders-first ordering, persistent expand/collapse across refreshes, open/closed
  folder glyphs, and colored file-type monograms (TS, JS, JSON, MD, CSS, PY, …).
- **Tool-call detail in agent panes.** Tool/command events now show their input
  (the file path, command, or pattern) next to the tool name instead of a bare
  `· Read` / `· Grep` — so you can see *what* an agent is doing as it works.

### Changed
- The per-provider chat path moved from implicit promise-chaining to an explicit,
  inspectable queue in the orchestrator (`stopChat` / `getChatQueue` /
  `editChatQueued` / `removeChatQueued`, plus a `steer` send mode). `/converse`
  and existing call sites are unchanged (they default to `queue`).

### Notes
- This is the first **major** bump: it adds a new conversation-control surface and
  reworks the file explorer. No architecture invariants changed; the core is still
  the single writer and agents still run only inside their worktrees/sessions.

## [1.4.0] — 2026-06-21

### Added
- **Handoffs (terminal CLI ⇄ IDE).** A new **handoffs** sidebar tab implements a
  standardized handoff in both directions:
  - **Receive** (outside → IDE): load a `HANDOFF.md`, view the contract, kick it
    off with Claude/Codex (the agent confirms and waits for your go), and
    **announce / claim / release** on the package relay.
  - **Produce** (IDE → outside): scaffold a package (`HANDOFF.md` + `handoff.json`
    + `state.json` + `relay.jsonl`) natively — no Python — and copy the kickoff
    prompt for an external session.
  - The standard, templates, and Python kit are documented in
    `Symbiont Docs/handoffs/` (design credited to a separate session).
- **Per-agent session id + name in the title**, with a rename (✎) button; a
  reusable in-app input dialog (Electron disables `window.prompt`).
- **Bypass-mode warning** badge in the top bar with a hover tooltip explaining
  the autonomous-in-isolated-worktree model.
- **Clear button** (with a Yes/No confirmation modal) for the events / logs /
  conversation panels.

### Changed
- Removed the estimated **context %** from the statusline (it wasn't the real
  provider value); the statusline now shows model · effort and real usage only.
- Worktree statusline labels clarified ("project root (chat)", "…(review,
  read-only)").
- `@skill:`/`@agent:` now inject the **file path** (the agent reads it) instead
  of pasting the whole file every turn — cheaper and works for both providers.
- "New chat" clears the session name/id (not just the content).
- `@all /converse …` (and similar) now runs the command instead of sending the
  literal text to the agents.

## [1.3.0] — 2026-06-21

### Added
- **Conversation history & resume.** Every chat conversation is saved locally
  (under `.sym/sessions/`) with an auto title, timestamps, and a transcript. Each
  agent header has a **🕘 chats** dropdown of recent conversations — open one to
  replay it and continue that thread — and a **✚ new chat** button.
- **`/resume`** lists saved conversations (id · time · title) and restores the
  latest; **`/resume <id>`** reopens a specific one. **`/rename <id> <title>`**
  (and a ✎ button) renames a conversation.
- **New chat archives the current thread** into history before clearing, so a
  conversation is never lost.
- **One-time backfill** imports prior relay conversation into the history as
  read-only entries, so chats that predate this feature are still browsable.
- **Both agent panes restore on reopen** — reconnecting replays the prior
  conversation into the panes (not just the conversation tab).
- **Model & reasoning-effort controls** (Settings → Providers): Claude model,
  Codex model, and Codex effort (low/medium/high). Codex applies on the next
  turn; Claude restarts its session. Lower Codex effort for faster replies.
- **Per-agent "new chat"** and external links (feedback form) open in the browser.

### Changed
- Suggestion lists (`@agent`/`@skill`/`/command`) keep the highlighted item in
  view when navigating with the arrow keys.

### Fixed
- Clicking a tree file, or view/edit on an agent/skill, now reliably brings the
  viewer/editor to the front even when it lives in the right sidebar.

## [1.2.0] — 2026-06-21

### Added
- **Fully customizable panel layout.** claude, codex, and a tabbed viewer/editor
  ("docs") panel are now movable panels. Each can sit in the **center** (as a
  resizable column), move to the **right sidebar**, **hide**, or **maximize**
  (fill the center). Use the per-panel dropdown or the **⊞ panels** menu. The
  center shows 1–3 columns side by side. By default the viewer/editor panel is
  visible in the center as tabs (no longer hidden).
- **Double-click to open.** Double-clicking a file in the tree, or an agent or
  skill in the sidebar, opens it directly in the viewer.
- **Persona control.** "Use as persona" now shows an active-persona banner and a
  one-click **remove**, with a tooltip explaining it applies the agent's persona
  as system context for dispatched coding tasks (/work, /symbiose, /deliver).
- **Unified right-sidebar tabs.** Panels moved to the right sidebar now share one
  tab bar with the sidebar tabs (tree/tasks/env/…), so e.g. the viewer/editor and
  env appear as tabs together instead of separate stacked regions.
- **Pasted screenshots are now readable by the agents.** A pasted image is saved
  and referenced by its absolute path with an instruction to read it, so Claude
  and Codex can actually view it (previously only a filename was passed).
- **Feedback link** in the in-app guide, the public site, and the README.

### Fixed
- **Left sidebar is now resizable** — its drag handle referenced a wrong element
  id and silently failed.
- **Precise column resizing.** Dragging dividers between center columns is now
  pixel-accurate via explicit flex-basis (the previous approach scaled by panel
  count and jumped).
- A newly shown center panel no longer renders at ~0px width next to columns with
  stale widths; widths reset fairly when the set of center columns changes.
- External links (e.g. the feedback form) open in the system browser, not inside
  the app window.

## [1.1.2] — 2026-06-21

### Fixed
- **Removed the last estimated-cost display.** The task board panel still showed a
  per-task `$` estimate; it now shows only the task status. No estimated token or
  dollar figures appear anywhere in the UI — only real usage from each provider's
  official status remains.

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

[3.1.0]: https://github.com/ESCID94/SymbiontAI-app/releases/tag/v3.1.0
[3.0.0]: https://github.com/ESCID94/SymbiontAI-app/releases/tag/v3.0.0
[2.0.0]: https://github.com/ESCID94/SymbiontAI-app/releases/tag/v2.0.0
[1.4.0]: https://github.com/ESCID94/SymbiontAI-app/releases/tag/v1.4.0
[1.3.0]: https://github.com/ESCID94/SymbiontAI-app/releases/tag/v1.3.0
[1.2.0]: https://github.com/ESCID94/SymbiontAI-app/releases/tag/v1.2.0
[1.1.2]: https://github.com/ESCID94/SymbiontAI-app/releases/tag/v1.1.2
[1.1.1]: https://github.com/ESCID94/SymbiontAI-app/releases/tag/v1.1.1
[1.1.0]: https://github.com/ESCID94/SymbiontAI-app/releases/tag/v1.1.0
[1.0.0]: https://github.com/ESCID94/SymbiontAI-app/releases/tag/v1.0.0
