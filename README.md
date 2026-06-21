<p align="center">
  <img src="assets/logo.svg" width="104" alt="SymbiontAI logo" />
</p>
<h1 align="center">SymbiontAI</h1>
<p align="center"><em>Two AI coding agents, in symbiosis.</em></p>

<p align="center">
  <a href="https://github.com/ESCID94/SymbiontAI-app/releases/latest"><img src="https://img.shields.io/github/v/release/ESCID94/SymbiontAI-app?label=download&style=for-the-badge" alt="Latest release" /></a>
</p>

> **This is the official public download & documentation page for SymbiontAI.**
> The application's source code is private; this repository hosts the releases,
> documentation, and release notes only.

<p align="center">
  <img src="assets/screenshots/app.png" width="900" alt="SymbiontAI desktop IDE — Claude and Codex agents side by side, task board, real usage statuslines" />
</p>

SymbiontAI is a **local desktop IDE** that runs **Claude Code** and **Codex**
side by side on the same codebase and makes them work *together*: a shared task
board, git-worktree isolation per task, turn-based messaging between the agents,
and a **symbiosis loop** where one agent implements while the other independently
reviews.

It is **fully local and offline by design** — no telemetry, no data collection,
no internet exposure. The only network traffic is whatever your own AI CLIs make.

---

## Download

➡️ **[Get the latest release](https://github.com/ESCID94/SymbiontAI-app/releases/latest)**

| Platform | File |
|---|---|
| Windows (x64) | `SymbiontAI-<version>-win32-x64.zip` |

Direct link to the latest Windows build:

```
https://github.com/ESCID94/SymbiontAI-app/releases/latest/download/SymbiontAI-win32-x64.zip
```

Unzip anywhere and run `SymbiontAI.exe`. No installation required. The first
launch opens an onboarding wizard.

> macOS and Linux builds are planned. The app is built on Electron and the build
> pipeline already targets all three platforms.

### Verify your download

Every release ships a `checksums.txt` with SHA-256 hashes.

```powershell
# Windows (PowerShell)
Get-FileHash .\SymbiontAI-1.1.1-win32-x64.zip -Algorithm SHA256
```

Compare the result with the value in `checksums.txt`.

## Requirements

On your machine you need:

- **Your own `claude` and `codex` CLIs**, installed and authenticated — the app
  drives them and stores no credentials.
- **Git.**
- **Node.js 22+** — used for the embedded database. (A build that bundles its own
  Node runtime is available on request, removing this requirement.)

The onboarding wizard checks all of this and shows the exact install/login
commands.

## What it does

- **Live agent sessions** — chatting with an agent is a long-lived, stateful
  session (like an open terminal), not a cold command per message. Sessions
  **survive app restarts**.
- **True parallel work** — dispatch to both agents at once, each in its own git
  worktree, with no collisions.
- **Cross-agent communication** — a shared relay lets each agent see what you and
  the other agent have said.
- **Coordinator-driven delivery** — describe a goal and a coordinator decomposes
  it into a reviewable plan and runs a team of specialist agents to ship it.
- **Worktree-isolated coding** — open a writable session in a fresh worktree;
  nothing lands without your review and an explicit merge.
- **Honest statusline** — shows each provider's **real** subscription usage from
  official sources (Claude `/usage`, Codex `/status`) — never an estimate.
- **Full IDE** — file tree, syntax-highlighted viewer/editor that docks beside or
  below the agent panes, drag-and-drop tabs, chat attachments, themes, and GitHub
  & git panels.
- **Built-in SDLC library** — curated, customizable skills and agent personas for
  the software lifecycle.

## Privacy & security

- **No telemetry, analytics, or data collection.**
- **No internet exposure** — the internal daemon binds to `127.0.0.1` only; the
  UI is sandboxed under a strict Content-Security-Policy.
- **No credentials stored** — authentication is delegated entirely to your
  `claude` and `codex` CLIs.
- **Nothing merges automatically** — you review every change and approve merges.

See [`SECURITY.md`](SECURITY.md) to report a vulnerability.

## License

- **Application:** proprietary **freeware** — free for personal and commercial
  use; the source is not distributed; resale, reverse engineering, and
  modification require written permission. See [`LICENSE`](LICENSE).
- **Documentation, screenshots, and mockups:** Creative Commons Attribution 4.0
  (CC BY 4.0).
- **"SymbiontAI" name and logo:** all rights reserved.

## Changelog

See [`CHANGELOG.md`](CHANGELOG.md).
