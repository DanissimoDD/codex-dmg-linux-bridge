# Setup Notes (Ubuntu)

This is the exact practical flow used for a successful run.

## 1) Extract and prepare payload

Use your own `Codex.dmg` and prepare a workdir that includes:

- `asar-unpacked/` (app source extracted from `app.asar`)
- `tools/node/runtime/bin/npx`

## 2) Choose the correct Codex CLI

Important: if multiple Codex CLI binaries are installed, always point launcher to the modern one.

Check:

```bash
which -a codex
codex --version
```

Recommended path on this setup:

- `/home/linuxbrew/.linuxbrew/bin/codex`

## 3) Configure model

Edit `~/.codex/config.toml`:

```toml
model = "gpt-5.3-codex"
```

If you previously mapped this model down to older ones, remove that specific migration rule.

## 4) Launch

```bash
CODEX_DMG_WORKDIR="$HOME/codex-dmg-attempt-latest" \
CODEX_CLI_PATH="/home/linuxbrew/.linuxbrew/bin/codex" \
~/.local/bin/codex-dmg-linux
```

## 5) Validate

```bash
codex exec --skip-git-repo-check --model gpt-5.3-codex "Reply with one word: ok"
```

Expected final word: `ok`.
