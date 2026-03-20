# AGENTS.md

Instructions for all Codex/OpenAI agents working in this repository.

## Purpose
- Keep StartupStack secure and operable.
- Treat this repository as infrastructure code with sensitive local config.

## Hard Security Rules
- Never read, print, cat, grep, or summarize secret files by default.
- Secret files include, at minimum:
  - `env/.env.prod`
  - `env/.env.local`
  - `.env`
  - `*.key`
  - `*.pem`
  - private key material or tokens in any path
- Access to secret files is allowed only when the user explicitly requests that exact file and confirms the need.
- When secret access is authorized, read the minimum necessary lines and redact sensitive values in all outputs.
- Never copy secret values into commits, docs, plans, or chat responses.

## Subagent Rules
- Subagents inherit all rules in this file.
- Before spawning a subagent, provide constraints: "Do not read secrets/env/key files."
- If a subagent accesses secrets without explicit authorization, stop and report immediately.

## Repo Workflow Rules
- Prefer `rg` for search and `rg --files` for file listing.
- Make minimal, targeted edits. Do not reformat unrelated files.
- Do not run destructive git commands (`reset --hard`, checkout revert) unless explicitly asked.
- If unexpected changes appear, pause and ask the user.

## Directory-Specific Policy
- If a deeper `AGENTS.md` exists, follow the closest one for that directory in addition to this root policy.

## Memory Policy
- Public, durable project context belongs in tracked docs under `docs/agentic/`.
- Private operator notes belong in `AGENTS.override.md` or `docs/agentic/private-memory.md` (ignored by git).
- Never store secrets in tracked memory files.
