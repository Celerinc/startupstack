# 07 - Management & Operations

This guide covers "Day 2" operations: managing the stack, backups, updates, and service-specific tasks.

## 1. Stack Management

### Starting & Stopping
- **Start (Prod)**: `./scripts/up prod`
- **Stop**: `./scripts/down`
- **Restart a single service**:
  ```bash
  docker compose -f compose/compose.yml -f compose/compose.prod.yml restart plane-api
  ```

### Updates
To update the stack (e.g., new Plane version):
1.  Pull new images:
    ```bash
    docker compose -f compose/compose.yml pull
    ```
2.  Restart the stack:
    ```bash
    ./scripts/up prod
    ```

## 2. Bootstrapping Plane (Superuser)

After the first install, you need to create a superuser for Plane.

**Option A: Web (God Mode)**
Visit `https://plane.example.com/god-mode/` to create the first admin user via the UI.

**Option B: CLI (Manual)**
If God Mode is disabled or you prefer CLI:
```bash
docker compose -f compose/compose.yml -f compose/compose.prod.yml --env-file env/.env.prod exec plane-api python manage.py createsuperuser
```

### Database Migrations
If Plane is stuck or shows migration errors:
```bash
docker compose -f compose/compose.yml -f compose/compose.prod.yml --env-file env/.env.prod run --rm plane-api python manage.py migrate
```

## 3. Backups

All critical data is stored in the `/data` directory on the host.

### Strategy
1.  **Filesystem Snapshot**:
    Backup the entire `/data` directory.
    ```bash
    /data/plane/postgres  # Database
    /data/plane/minio     # File uploads
    /data/n8n             # Workflow data
    /data/rocketchat      # Chat history
    ```

2.  **Database Dumps (Recommended)**
    Create a cron job to dump the Postgres database.
    ```bash
    docker exec -t plane-db pg_dumpall -c -U plane > /backups/plane_$(date +%F).sql
    ```

## 4. Monitoring & Logs

**View Logs**:
```bash
docker compose -f compose/compose.yml logs -f --tail=100
```

**Check Health**:
```bash
docker ps
```
Ensure all containers are `Up` (and `healthy` where applicable).
