# Docker Networking

Docker networking allows containers to communicate with one another and with external systems.

---

## List Networks

```bash
docker network ls
```

Inspect:

```bash
docker network inspect <network>
```

---

## Default Networks

Docker normally provides networks such as:

```text
bridge
host
none
```

---

## Create a Network

```bash
docker network create app-network
```

---

## Connect Containers

Run containers on the same network:

```bash
docker run -d \
  --name db \
  --network app-network \
  postgres
```

```bash
docker run -d \
  --name app \
  --network app-network \
  my-app
```

The application can communicate with the database using:

```text
db
```

as the hostname.

---

## Container DNS

Docker provides DNS resolution between containers on user-defined networks.

Example:

```text
app
 │
 │ database connection
 ▼
db:5432
```

The hostname is the container/service name.

---

## Port Publishing

Publish a container port:

```bash
docker run \
  -p 8080:80 \
  nginx
```

This exposes the container's port 80 through host port 8080.

---

## Port Binding

Bind only to localhost:

```bash
docker run \
  -p 127.0.0.1:8080:80 \
  nginx
```

This prevents direct access through other host interfaces.

---

## Network Isolation

Containers on different isolated networks generally cannot communicate directly.

This can be useful for separating application tiers.

Example:

```text
frontend-network
      │
      ▼
   frontend

backend-network
      │
      ├── API
      └── Database
```

---

## Compose Networking

Docker Compose creates a project network automatically.

Example:

```yaml
services:
  app:
    image: my-app

  db:
    image: postgres
```

The application can connect to:

```text
db:5432
```

rather than using `localhost`.

!!! warning

    Inside a container, `localhost` refers to the current container, not another container and not necessarily the host machine.
