# Security Policy

## Reporting a vulnerability

If you discover a security issue in SymbiontAI, please report it privately.

- **Preferred:** open a private vulnerability report via GitHub Security
  Advisories on the distribution repository
  (**Security → Report a vulnerability**).
- **Email:** as an alternative, contact the maintainer through the email on the
  GitHub profile [@ESCID94](https://github.com/ESCID94).

Please include: affected version, your OS, reproduction steps, and the impact
you observed. Do **not** open a public issue for security reports.

We aim to acknowledge reports within a few days and to address confirmed issues
as quickly as is practical.

## Scope and design

SymbiontAI is a **local, offline desktop application**. Its security posture:

- **No telemetry, analytics, or data collection.**
- **No network exposure** — the internal daemon binds to `127.0.0.1` only; the
  renderer runs sandboxed (`contextIsolation` on, `nodeIntegration` off) under a
  strict Content-Security-Policy limited to the local WebSocket.
- **No embedded secrets** — the app stores no API keys or tokens. It drives your
  own `claude` and `codex` CLIs, which you install and authenticate; their usage
  is governed by their own terms.
- The only outbound network traffic is whatever those third-party CLIs make on
  your behalf.

## Verifying a download

Each release includes a `checksums.txt` with SHA-256 hashes. Verify your
download before running it:

```powershell
# Windows (PowerShell)
Get-FileHash .\SymbiontAI-2.0.0-win32-x64.zip -Algorithm SHA256
```

```bash
# macOS / Linux
shasum -a 256 SymbiontAI-2.0.0-win32-x64.zip
```

Compare the output against the value in `checksums.txt`.

## Supported versions

The latest released version receives security fixes. Older versions are not
maintained.
