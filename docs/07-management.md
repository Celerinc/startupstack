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
- Restart Twenty server:
  ```bash
  docker compose -f compose/compose.yml -f compose/compose.prod.yml restart twenty-server
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
3. For existing deployments migrating Twenty from local storage to MinIO-backed storage:
   - No extra Twenty S3 credential is required.
   - Twenty uses existing `MINIO_ROOT_USER` and `MINIO_ROOT_PASSWORD`.
   - Keep `PLANE_BUCKET=plane` unchanged to preserve existing Plane uploads.

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

## 3. Bootstrapping Twenty (First Workspace Admin)

- Web: `https://twenty.example.com`
- In default single-workspace mode (`TWENTY_IS_MULTIWORKSPACE_ENABLED=false`), the first account created becomes workspace admin.

## 4. NanoBot Operations (Slack-first)

Auto-init + start gateway:
```bash
./scripts/up prod ai
```

Run arbitrary NanoBot CLI command:
```bash
./scripts/nanobot-cli prod status
```

## 5. Backups

All critical data lives in `/data` on host:
```bash
/data/plane/postgres
/data/minio
/data/twenty/postgres
/data/n8n
/data/nanobot
```

Object storage model:
- `plane` bucket: Plane attachments
- `twenty` bucket: Twenty attachments
- Both are hosted in shared MinIO under `/data/minio`

Recommended DB dump:
```bash
docker exec -t startupstack-plane-db-1 pg_dumpall -c -U plane > /backups/plane_$(date +%F).sql
docker exec -t startupstack-twenty-db-1 pg_dumpall -c -U postgres > /backups/twenty_$(date +%F).sql
```

## 6. Monitoring & Logs

```bash
docker compose -f compose/compose.yml logs -f --tail=100
docker ps
```
