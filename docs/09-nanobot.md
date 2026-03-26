# 09 - NanoBot (Slack-first)

NanoBot is integrated as an optional profile and sourced from the git submodule at `external/nanobot`.

## 1. Requirements
- Valid OpenAI API key.
- Slack app credentials:
  - Bot token (`xoxb-...`)
  - App-level token (`xapp-...`)
- Local audio transcription dependencies are bundled in the NanoBot image (`ffmpeg` + local Whisper package), so no manual container installs are required after normal image build.

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
- `NANOBOT_GITHUB_TOKEN` (PAT, optional but required for autonomous repo creation)
- `NANOBOT_GITHUB_OWNER` (default target owner/org, e.g. `celerinc`)
- `NANOBOT_GITHUB_DEFAULT_VISIBILITY` (`private` or `public`, default `private`)
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

GitHub automation note:
- If `NANOBOT_GITHUB_TOKEN` is set, `scripts/nanobot-init` bootstraps `gh` auth inside NanoBot using `GH_CONFIG_DIR=/root/.nanobot/gh` (persisted by `NANOBOT_STATE_DIR` volume).
- Default behavior is safe:
  - owner comes from `NANOBOT_GITHUB_OWNER`
  - visibility defaults to `private`
  - collisions (`owner/repo` already exists) must be treated as stop-and-report in agent policy.

### GitHub PAT setup (important)

Use a token for an account that can create repositories in the target owner/org.

Fine-grained PAT (recommended):
- Resource owner: set to your org/user target (for example `Celerinc`).
- Repository access: `All repositories` (or selected repositories if your policy allows it).
- Configure permissions in the **Repositories** section for this workflow.
- You typically do **not** need extra permissions in the **Organizations** section for normal create/push/PR repo workflows.
- Minimum practical permissions:
  - `Administration`: Read and write
  - `Contents`: Read and write
  - `Pull requests`: Read and write

Classic PAT (fallback):
- Use `repo` scope when fine-grained restrictions/policies block your flow.
- If your org uses SSO, authorize the token for the org after creation.

## 3. Auto-bootstrap from env
StartupStack auto-initializes NanoBot config when you start with the `ai` profile:
```bash
./scripts/up prod ai
```

This creates/updates config in `NANOBOT_STATE_DIR` and uses `NANOBOT_OAUTH_DIR` for shared OAuth sessions, then starts the gateway.
If GitHub token is configured, it also ensures `gh auth status` is ready for autonomous repo creation.

## 4. Start NanoBot gateway
```bash
./scripts/up prod ai
```

## 5. Useful commands
```bash
./scripts/nanobot-cli prod status
./scripts/nanobot-cli prod agent
docker compose -f compose/compose.yml -f compose/compose.prod.yml --env-file env/.env.prod exec nanobot-gateway gh auth status --hostname github.com
```
