# 02 - Local Development

StartupStack runs locally with minimal friction for fast iteration.

## Setup
1. Clone:
   ```bash
   git clone https://github.com/Celerinc/startupstack.git
   cd startupstack
   git submodule update --init --recursive
   ```
2. Configure env:
   ```bash
   cp env/.env.example env/.env.local
   ```

## Start
```bash
./scripts/up local
```

Equivalent command:
```bash
docker compose -f compose/compose.yml -f compose/compose.local.yml --env-file env/.env.local up -d
```

## Access services
- Plane: http://localhost:8080
- n8n: http://localhost:5678
- MinIO Console: http://localhost:9001

## Optional: NanoBot locally
```bash
./scripts/up local ai
```

## Stop
```bash
./scripts/down local
```

## Troubleshooting
- Port conflicts: update mappings in `compose/compose.local.yml`.
- Data reset: `docker compose -f compose/compose.yml -f compose/compose.local.yml down -v`.
