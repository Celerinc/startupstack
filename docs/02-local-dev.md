# 02 - Local Development

StartupStack is designed to run locally with minimal friction. This is perfect for testing configuration changes or evaluating the included software.

> **Note**: Unlike Production (which requires a VPN), Local Development **does not** need a VPN. We simply expose the services on your local ports (e.g., `localhost:8080`).


## Setup

1.  **Clone the Repository**:
    ```bash
    git clone https://github.com/Celerinc/startupstack.git
    cd startupstack
    ```

2.  **Configure Environment**:
    Create a local environment file. The default example `env/.env.example` is pre-configured with dummy values relevant for local dev.
    ```bash
    cp env/.env.example env/.env.local
    ```
    > You likely don't need to change anything in `.env.local` for basic testing.

## Starting the Stack

We provide a helper script to launch the stack in "local mode". This mode uses `compose.local.yml` which exposes ports directly to `localhost`.

```bash
./scripts/up local
```

This command runs:
```bash
docker compose -f compose/compose.yml -f compose/compose.local.yml --env-file env/.env.local up -d
```

## Accessing Services

Once all containers are healthy (refer to `docker ps`), you can access them directly:

- **Plane**: [http://localhost:8080](http://localhost:8080)
- **n8n**: [http://localhost:5678](http://localhost:5678)
- **Rocket.Chat**: [http://localhost:3000](http://localhost:3000)
- **MinIO Console**: [http://localhost:9001](http://localhost:9001) (User: `minioadmin`, Pass: `change-me`)

## Stopping

To stop the stack and remove containers:

```bash
./scripts/down
```
Or manually:
```bash
docker compose -f compose/compose.yml -f compose/compose.local.yml down
```

## Troubleshooting

- **Port Conflicts**: If port `8080` or `3000` is in use, edit `compose/compose.local.yml` to map to a different host port (e.g., `"8081:8000"`).
- **Data**: Local data is stored in Docker volumes. To wipe everything, use `./scripts/down -v` (if applicable, or `docker compose down -v`).
