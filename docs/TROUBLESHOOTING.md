# Troubleshooting

## Error: model_not_found / requested model does not exist

Cause:

- app is pointing to an older Codex CLI binary
- model config references a model not visible to that binary/account state

Fix:

1. Verify active CLI:

```bash
which -a codex
codex --version
```

2. Force launcher to modern CLI:

Linux path example:

```bash
CODEX_CLI_PATH="/home/linuxbrew/.linuxbrew/bin/codex"
```

macOS Intel path example:

```bash
CODEX_CLI_PATH="/usr/local/bin/codex"
```

3. Check available models via app-server `model/list`.

## Message send does nothing in UI

Cause:

- backend turn fails (often model mismatch)

Fix:

- fix model + CLI path
- restart app fully
- create a new conversation

## Native module architecture mismatch (`ERR_DLOPEN_FAILED`)

Cause:

- payload contains ARM64 `.node` native module from DMG
- Electron runtime is x86_64 on macOS Intel

Fix:

- rebuild the failing native module for `electron@40` + `x64`
- replace only the affected `.node` binary in `asar-unpacked`

## DBus warning: UnitExists

`org.freedesktop.systemd1.UnitExists` is typically a warning on Linux and not the root cause of message failures.

## DeprecationWarning: url.parse

Non-blocking warning from runtime dependency path. Safe to ignore for normal usage.

## MCP context7 failed to start

Non-blocking for core chat flow. Install missing dependency if you need that MCP only.
