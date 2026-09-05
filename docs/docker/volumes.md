# Docker Volumes

Containers are ephemeral (lasting for short time) by design.

If application data needs to survive container replacement, use persistent storage.

---

## Named Volumes

Create:

```bash
docker volume create app-data
```

List:

```bash
docker volume ls
```

Inspect:

```bash
docker volume inspect app-data
```

Remove:

```bash
docker volume rm app-data
```

---

## Mount a Volume

```bash
docker run \
  -v app-data:/data \
  my-app
```

Format:

```text
volume_name:container_path
```

---

## Bind Mount

A host directory can be mounted:

```bash
docker run \
  -v $(pwd)/data:/data \
  my-app
```

This is useful for development.

---

## Read-only Mount

```bash
docker run \
  -v $(pwd)/config:/config:ro \
  my-app
```

---

## Volume vs Bind Mount

| Volume                               | Bind Mount                       |
| ------------------------------------ | -------------------------------- |
| Managed by Docker                    | Managed by user                  |
| Docker storage location              | Explicit host path               |
| Good for persistent application data | Good for development/source code |
| Easier to move/manage                | Direct host filesystem access    |

---

## Database Example

```bash
docker volume create postgres-data

docker run -d \
  --name postgres \
  -v postgres-data:/var/lib/postgresql/data \
  postgres
```

Deleting the container:

```bash
docker rm -f postgres
```

does not delete the volume.

The data remains in:

```text
postgres-data
```

---

## Volume Cleanup

List volumes:

```bash
docker volume ls
```

Remove unused volumes:

```bash
docker volume prune
```

!!! danger

    Removing a volume permanently removes the data stored in that volume.

    Ensure backups exist before deleting production volumes.
