# Containers

A container is an isolated process created from a Docker image.

---

## Create and Run

```bash
docker run nginx
```

Detached:

```bash
docker run -d nginx
```

Named:

```bash
docker run -d --name web nginx
```

---

## Port Mapping

A container port is not automatically accessible from the host.

```bash
docker run -d \
  -p 8080:80 \
  nginx
```

The format is:

```text
host_port:container_port
```

So:

```text
localhost:8080
      │
      ▼
container:80
```

---

## Environment Variables

```bash
docker run \
  -e APP_ENV=development \
  my-app
```

Multiple variables:

```bash
docker run \
  -e APP_ENV=production \
  -e LOG_LEVEL=info \
  my-app
```

---

## Container Names

```bash
docker run --name web nginx
```

Then:

```bash
docker stop web
docker start web
docker logs web
docker rm web
```

---

## Interactive Containers

```bash
docker run -it ubuntu bash
```

---

## Detached Containers

```bash
docker run -d nginx
```

---

## Restart Policies

Always restart:

```bash
docker run \
  --restart always \
  nginx
```

Restart unless explicitly stopped:

```bash
docker run \
  --restart unless-stopped \
  nginx
```

---

## Resource Limits

Memory:

```bash
docker run \
  --memory 512m \
  my-app
```

CPU:

```bash
docker run \
  --cpus 1 \
  my-app
```

---

## Inspect

```bash
docker inspect <container>
```

Container processes:

```bash
docker top <container>
```

Resource usage:

```bash
docker stats
```

---

## Container Lifecycle

```text
Created
   │
   ▼
Running
   │
   ├── stop ──► Stopped
   │
   └── crash ─► Exited
                   │
                   ▼
                 Removed
```

A stopped container still exists until removed.

---

## Container vs VM

| Container          | Virtual Machine            |
| ------------------ | -------------------------- |
| Shares host kernel | Has guest OS/kernel        |
| Lightweight        | Heavier                    |
| Starts quickly     | Usually slower             |
| Process isolation  | Hardware/OS virtualization |
| Smaller footprint  | Larger footprint           |