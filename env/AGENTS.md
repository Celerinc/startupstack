# AGENTS.md (env/)

This directory contains sensitive environment configuration.

## Access Policy
- Do not read files in this directory by default.
- Never read `env/.env.prod` unless the user explicitly asks for that exact file and confirms the intent.
- If authorized, inspect only the minimum required lines and redact all secrets in outputs.
- Do not echo full env values, tokens, passwords, keys, or connection strings.

## Allowed Alternatives
- Prefer `env/.env.example` for structure/reference.
- Use placeholder values when creating or updating documentation.
