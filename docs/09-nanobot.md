# 09 - NanoBot (Slack-first)

NanoBot is integrated as an optional profile and sourced from the git submodule at `external/nanobot`.

## 1. Requirements
- Valid OpenAI API key.
- Slack app credentials:
  - Bot token (`xoxb-...`)
  - App-level token (`xapp-...`)

## 2. Configure environment
Populate these keys in your env file (`env/.env.local` or `env/.env.prod`):
- `NANOBOT_PROVIDER`
- `NANOBOT_MODEL`
- `NANOBOT_REASONING_EFFORT`
- `NANOBOT_MAX_TOKENS`
- `NANOBOT_CONTEXT_WINDOW_TOKENS`
- `NANOBOT_TEMPERATURE`
- `NANOBOT_OPENAI_API_KEY`
- `NANOBOT_SLACK_BOT_TOKEN`
- `NANOBOT_SLACK_APP_TOKEN`
- `NANOBOT_SLACK_ALLOW_FROM`
- `NANOBOT_SEND_PROGRESS` (set `false` to suppress interim "working" messages)
- `NANOBOT_SEND_TOOL_HINTS` (set `false` to suppress tool-hint chatter)
- `NANOBOT_STATE_DIR`
- `NANOBOT_OAUTH_DIR`

Directory ownership note:
- The user running `./scripts/up` must be able to write both `NANOBOT_STATE_DIR` and `NANOBOT_OAUTH_DIR`.
- On a fresh VPS, run `./scripts/setup prod` first, or fix ownership manually with:
  - `sudo mkdir -p /home/deploy/agents/.alex /home/deploy/agents/.oauth`
  - `sudo chown -R deploy:deploy /home/deploy/agents`

Recommended coding defaults:
- `NANOBOT_PROVIDER=openai`
- `NANOBOT_MODEL=openai/gpt-5.3-codex`
- `NANOBOT_REASONING_EFFORT=low`

Provider note:
- `openai` uses `NANOBOT_OPENAI_API_KEY`.
- `openai_codex` is OAuth-based in NanoBot (not API-key based) and requires provider login.

## 3. Auto-bootstrap from env
StartupStack auto-initializes NanoBot config when you start with the `ai` profile:
```bash
./scripts/up prod ai
```

This creates/updates config in `NANOBOT_STATE_DIR` and uses `NANOBOT_OAUTH_DIR` for shared OAuth sessions, then starts the gateway.

## 4. Start NanoBot gateway
```bash
./scripts/up prod ai
```

## 5. Useful commands
```bash
./scripts/nanobot-cli prod status
./scripts/nanobot-cli prod agent
```
