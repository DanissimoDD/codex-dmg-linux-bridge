# codex-dmg-linux-bridge

A practical guide to run the Codex desktop payload (from `Codex.dmg`) via a Linux bridge workflow, with an additional macOS Intel (`x86_64`) path.

This repository ships scripts and documentation only. It does **not** redistribute proprietary Codex binaries.

## 1) What this project does

`Codex.dmg` is built for macOS. This project provides a bridge workflow that helps you:

- extract the required content from `Codex.dmg`
- run the Electron payload (Linux-first flow)
- wire it to the correct `codex` CLI binary

Additional notes for macOS Intel are included in each relevant section.

## 2) Before you start

- You must have your own `Codex.dmg` from the official source.
- This repo does not include DMG/app binaries.
- If anything does not match your machine, check `docs/TROUBLESHOOTING.md`.

## 3) Requirements

Primary:
- Ubuntu/Linux x64

Additional supported path:
- macOS Intel (`x86_64`)
- `bash`
- `git`
- `node` + `npm` + `npx`
- `7z` (to extract DMG contents)
- `codex` CLI

Install dependencies on Ubuntu:

```bash
sudo apt update
sudo apt install -y p7zip-full git curl
```

Install dependencies on macOS Intel (Homebrew):

```bash
brew install p7zip git curl
```

Verify your environment:

```bash
node -v
npm -v
npx -v
codex --version
```

## 4) Clone this repository

```bash
git clone https://github.com/Mina-Sayed/codex-dmg-linux-bridge.git
cd codex-dmg-linux-bridge
chmod +x scripts/codex-dmg-linux-launcher.sh
```

## 5) Get `Codex.dmg`

Download `Codex.dmg` from your official account source, then place it on your target machine.

Example:

```bash
mkdir -p "$HOME/codex-dmg-attempt-latest"
cp /path/to/Codex.dmg "$HOME/codex-dmg-attempt-latest/Codex.dmg"
```

## 6) Prepare the runtime payload from DMG

The launcher expects a workdir containing:

- `asar-unpacked/`
- `tools/node/runtime/bin/npx`

### 6.1 Extract DMG

Linux flow:

```bash
cd "$HOME/codex-dmg-attempt-latest"
7z x Codex.dmg -oextract
```

macOS Intel flow (preferred on macOS):

```bash
cd "$HOME/codex-dmg-attempt-latest"
hdiutil attach Codex.dmg -nobrowse -readonly
```

### 6.2 Extract `app.asar` into `asar-unpacked`

Find `app.asar`:

```bash
APP_ASAR="$(find extract /Volumes -type f -name app.asar 2>/dev/null | head -n 1)"
echo "$APP_ASAR"
```

If the path is valid, extract it:

```bash
npx --yes @electron/asar extract "$APP_ASAR" asar-unpacked
```

### 6.3 Ensure `tools/node/runtime/bin/npx` exists

If missing, symlink to your system `npx`:

```bash
mkdir -p tools/node/runtime/bin
ln -sf "$(command -v npx)" tools/node/runtime/bin/npx
```

## 7) Pick the correct Codex CLI binary

Check all installed Codex binaries:

```bash
which -a codex
codex --version
```

If you have multiple binaries, use the newest one (example):

- Linux: `/home/linuxbrew/.linuxbrew/bin/codex`
- macOS Intel: `/usr/local/bin/codex`

## 8) Authenticate Codex CLI

```bash
codex login
```

## 9) First launch

From this repo directory:

Linux example:

```bash
CODEX_DMG_WORKDIR="$HOME/codex-dmg-attempt-latest" \
CODEX_CLI_PATH="/home/linuxbrew/.linuxbrew/bin/codex" \
./scripts/codex-dmg-linux-launcher.sh
```

macOS Intel example:

```bash
CODEX_DMG_WORKDIR="$HOME/codex-dmg-attempt-latest" \
CODEX_CLI_PATH="/usr/local/bin/codex" \
./scripts/codex-dmg-linux-launcher.sh
```

## 10) Validate that it works

Quick app-server validation:

```bash
codex debug app-server send-message-v2 "reply with one word: ok"
```

Expected final response contains: `ok`.

## 11) Optional: install a global launcher command

```bash
mkdir -p ~/.local/bin
cp scripts/codex-dmg-linux-launcher.sh ~/.local/bin/codex-dmg-linux
chmod +x ~/.local/bin/codex-dmg-linux
```

Run it from anywhere:

Linux example:

```bash
CODEX_DMG_WORKDIR="$HOME/codex-dmg-attempt-latest" \
CODEX_CLI_PATH="/home/linuxbrew/.linuxbrew/bin/codex" \
~/.local/bin/codex-dmg-linux
```

macOS Intel example:

```bash
CODEX_DMG_WORKDIR="$HOME/codex-dmg-attempt-latest" \
CODEX_CLI_PATH="/usr/local/bin/codex" \
~/.local/bin/codex-dmg-linux
```

## 12) Common errors (quick map)

- `Missing app payload at .../asar-unpacked`
  - `app.asar` was not extracted correctly.
- `Missing Node runtime at .../tools/node/runtime/bin`
  - Create the `npx` symlink step in section 6.3.
- `Unable to locate the Codex CLI binary`
  - Set `CODEX_CLI_PATH` to a valid `codex` binary.
- `model_not_found`
  - Usually wrong/old CLI binary or incompatible model configuration.

More details: `docs/TROUBLESHOOTING.md`.

If you mounted the DMG with `hdiutil`, optionally detach after extraction:

```bash
MOUNT_POINT="$(hdiutil info | awk '/Codex Installer/{print $1}' | head -n 1)"
[[ -n "$MOUNT_POINT" ]] && hdiutil detach "$MOUNT_POINT"
```

## 13) Important files in this repo

- `scripts/codex-dmg-linux-launcher.sh`: main bridge launcher
- `docs/SETUP.md`: additional setup notes
- `docs/TROUBLESHOOTING.md`: fixes for common issues

## License

MIT (see `LICENSE`).
