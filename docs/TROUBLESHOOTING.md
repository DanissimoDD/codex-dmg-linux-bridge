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

```bash
CODEX_CLI_PATH="/home/linuxbrew/.linuxbrew/bin/codex"
```

3. Check available models via app-server `model/list`.

## Message send does nothing in UI

Cause:

- backend turn fails (often model mismatch)

Fix:

- fix model + CLI path
- restart app fully
- create a new conversation

## DBus warning: UnitExists

`org.freedesktop.systemd1.UnitExists` is typically a warning and not the root cause of message failures.

## DeprecationWarning: url.parse

Non-blocking warning from runtime dependency path. Safe to ignore for normal usage.

## MCP context7 failed to start

Non-blocking for core chat flow. Install missing dependency if you need that MCP only.
