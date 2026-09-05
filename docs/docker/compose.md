# Docker Compose

Docker Compose defines multi-container applications using YAML.

The modern command is:

```bash
docker compose
```

---

## Basic Compose File

Create:

```text
compose.yaml
```

Example:

```yaml
services:
  app:
    build: .
    ports:
      - "8000:8000"

  db:
    image: postgres:17
    environment:
      POSTGRES_PASSWORD: example
```

Start:

```bash
docker compose up
```

Background:

```bash
docker compose up -d
```

Stop and remove:

```bash
docker compose down
```

---

## Services

Each entry under `services` defines a container/service.

```yaml
services:
  app:
    image: my-app

  database:
    image: postgres
```

---

## Build

```yaml
services:
  app:
    build: .
```

Build:

```bash
docker compose build
```

Build and start:

```bash
docker compose up --build
```

---

## Ports

```yaml
ports:
  - "8000:8000"
```

Format:

```text
host:container
```

---

## Environment Variables

```yaml
services:
  app:
    environment:
      APP_ENV: production
      LOG_LEVEL: info
```

Use an `.env` file for local configuration:

```text
APP_ENV=development
```

---

## Volumes

```yaml
services:
  db:
    image: postgres
    volumes:
      - postgres-data:/var/lib/postgresql/data

volumes:
  postgres-data:
```

---

## Networks

```yaml
services:
  app:
    networks:
      - backend

  db:
    networks:
      - backend

networks:
  backend:
```

---

## Dependencies

```yaml
services:
  app:
    depends_on:
      - db

  db:
    image: postgres
```

`depends_on` controls startup ordering but does not by itself guarantee that the dependency is ready to accept connections.

Applications should generally handle connection retries or use appropriate health checks.

---

## Health Checks

Example:

```yaml
services:
  db:
    image: postgres
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
```

---

## Common Commands

Start:

```bash
docker compose up
```

Start detached:

```bash
docker compose up -d
```

Stop:

```bash
docker compose stop
```

Restart:

```bash
docker compose restart
```

View services:

```bash
docker compose ps
```

View logs:

```bash
docker compose logs
```

Follow logs:

```bash
docker compose logs -f
```

View one service:

```bash
docker compose logs -f app
```

Execute command:

```bash
docker compose exec app sh
```

Build:

```bash
docker compose build
```

Pull images:

```bash
docker compose pull
```

Remove application:

```bash
docker compose down
```

Remove volumes too:

```bash
docker compose down -v
```

!!! danger

    `docker compose down -v` removes the Compose-managed volumes.

    This can permanently delete database data.


---

## Compose Workflow

```text
compose.yaml
      │
      ▼
docker compose up
      │
      ├── app
      ├── database
      └── cache
             │
             ▼
         Network
             │
             ▼
          Volumes
```
