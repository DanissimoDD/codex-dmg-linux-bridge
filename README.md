# codex-dmg-linux-bridge

Run a Codex macOS DMG payload on Ubuntu/Linux by wiring a Linux launcher around an extracted app payload.

This project ships scripts and docs only. It does **not** redistribute proprietary app binaries.

## Current status

Tested on Ubuntu/Linux with:

- Electron payload from extracted `Codex.dmg`
- launcher: `scripts/codex-dmg-linux-launcher.sh`
- Codex CLI: `/home/linuxbrew/.linuxbrew/bin/codex`

Working:

- app launches
- local app-server starts
- sending messages works

Known non-blocking issues:

- some `git` warnings if current directory is not a Git repo
- MCP providers may fail if auth/token is missing

## Why this exists

Some users have `Codex.dmg` and want to run it on Linux. The DMG itself is macOS-focused, so this guide provides a practical Linux bridge:

- launch extracted Electron payload on Linux
- point the app to the correct Codex CLI
- fix common runtime/model issues

## What is included

- `scripts/codex-dmg-linux-launcher.sh`: Linux launcher for an extracted payload
- `docs/SETUP.md`: end-to-end setup notes
- `docs/TROUBLESHOOTING.md`: common errors and fixes
- `posts/twitter-posts.md`: ready-to-post Twitter copy

## Prerequisites

- Ubuntu/Linux x64
- `bash`
- `npm` (or `npx`) available
- Codex CLI installed (`codex --version`)
- extracted payload folder containing:
  - `asar-unpacked/`
  - `tools/node/runtime/bin/npx`

## Quick start

1. Make launcher executable:

```bash
chmod +x scripts/codex-dmg-linux-launcher.sh
```

2. Run launcher (replace the workdir path):

```bash
CODEX_DMG_WORKDIR="$HOME/codex-dmg-attempt-latest" \
CODEX_CLI_PATH="/home/linuxbrew/.linuxbrew/bin/codex" \
./scripts/codex-dmg-linux-launcher.sh
```

3. Optional: install command to `~/.local/bin`:

```bash
mkdir -p ~/.local/bin
cp scripts/codex-dmg-linux-launcher.sh ~/.local/bin/codex-dmg-linux
chmod +x ~/.local/bin/codex-dmg-linux
```

Then run:

```bash
CODEX_DMG_WORKDIR="$HOME/codex-dmg-attempt-latest" ~/.local/bin/codex-dmg-linux
```

## Verify

After launch, validate backend quickly:

```bash
codex debug app-server send-message-v2 "reply with one word: ok"
```

Expected final response contains: `ok`.

## Docs

- setup flow: `docs/SETUP.md`
- common errors/fixes: `docs/TROUBLESHOOTING.md`

## License

MIT (see `LICENSE`).
