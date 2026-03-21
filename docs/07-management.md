# 07 - Management & Operations

This guide covers day-2 operations: startup/shutdown, updates, backups, and NanoBot operations.

## 1. Stack Management

### Starting & Stopping
- Start core stack (prod): `./scripts/up prod`
- Start core + NanoBot gateway: `./scripts/up prod ai`
- Stop stack: `./scripts/down prod`
- Restart one service:
  ```bash
  docker compose -f compose/compose.yml -f compose/compose.prod.yml restart plane-api
  ```

### Updates
1. Pull updated images:
   ```bash
   docker compose -f compose/compose.yml pull
   ```
2. Restart:
   ```bash
   ./scripts/up prod
   ```

## 2. Bootstrapping Plane (Superuser)

Create the first admin user:

- Web: `https://plane.example.com/god-mode/`
- CLI:
  ```bash
  docker compose -f compose/compose.yml -f compose/compose.prod.yml --env-file env/.env.prod exec plane-api python manage.py createsuperuser
  ```

### Database Migrations
```bash
docker compose -f compose/compose.yml -f compose/compose.prod.yml --env-file env/.env.prod run --rm plane-api python manage.py migrate
```

## 3. NanoBot Operations (Slack-first)

Auto-init + start gateway:
```bash
./scripts/up prod ai
```

Run arbitrary NanoBot CLI command:
```bash
./scripts/nanobot-cli prod status
```

## 4. Backups

All critical data lives in `/data` on host:
```bash
/data/plane/postgres
/data/plane/minio
/data/n8n
/data/nanobot
```

Recommended DB dump:
```bash
docker exec -t plane-db pg_dumpall -c -U plane > /backups/plane_$(date +%F).sql
```

## 5. Monitoring & Logs

```bash
docker compose -f compose/compose.yml logs -f --tail=100
docker ps
```
