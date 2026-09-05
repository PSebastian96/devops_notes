## Docker Troubleshooting

A useful Docker troubleshooting process is:

```text
Problem
  │
  ├── Container running?
  ├── Image correct?
  ├── Logs?
  ├── Network?
  ├── Ports?
  ├── Environment?
  ├── Volumes?
  └── Resources?
```

---

## Docker Daemon Not Running

Check:

```bash
docker info
```

Linux:

```bash
sudo systemctl status docker
```

Start:

```bash
sudo systemctl start docker
```

Enable at boot:

```bash
sudo systemctl enable docker
```

---

## Permission Denied

If you see:

```text
permission denied while trying to connect to the Docker daemon socket
```

Check:

```bash
groups
```

Add your user:

```bash
sudo usermod -aG docker $USER
```

Then log out and back in.

---

## Container Exits Immediately

Check:

```bash
docker ps -a
```

Then:

```bash
docker logs <container>
```

Inspect:

```bash
docker inspect <container>
```

The most common cause is that the container's main process has exited.

---

## Container Keeps Restarting

Check:

```bash
docker ps
```

Then:

```bash
docker logs <container>
```

Inspect restart configuration:

```bash
docker inspect <container>
```

Look for application crashes, configuration errors, failed health checks, or missing dependencies.

---

## Port Already in Use

If Docker reports:

```text
Bind for 0.0.0.0:8080 failed: port is already allocated
```

Find what is using the port on Linux:

```bash
sudo ss -ltnp | grep :8080
```

Or change the host port:

```bash
docker run \
  -p 8081:80 \
  nginx
```

---

## Container Cannot Reach Another Container

Check the networks:

```bash
docker network ls
```

Inspect:

```bash
docker network inspect <network>
```

Ensure both containers are connected to the same user-defined network.

Within Compose, use the service name:

```text
db:5432
```

not:

```text
localhost:5432
```

---

## Container Cannot Reach Internet

Check:

```bash
docker exec -it <container> sh
```

Then test DNS:

```bash
getent hosts example.com
```

Check network configuration:

```bash
docker inspect <container>
```

---

## Image Won't Build

Build without relying on cache:

```bash
docker build --no-cache -t my-app .
```

Use verbose build output:

```bash
docker build --progress=plain -t my-app .
```

Check:

* Dockerfile syntax
* Build context
* `.dockerignore`
* Package repositories
* Network connectivity
* Base image availability

---

## File Not Found During COPY

If:

```dockerfile
COPY something.txt /app/
```

fails, verify that the file exists inside the build context.

For:

```bash
docker build .
```

Docker can only access files below the current build context.

Check `.dockerignore`.

---

## Container Has No Data

Check mounts:

```bash
docker inspect <container>
```

Look under:

```text
Mounts
```

List volumes:

```bash
docker volume ls
```

Inspect:

```bash
docker volume inspect <volume>
```

---

## Check Resource Usage

```bash
docker stats
```

Check disk usage:

```bash
docker system df
```

---

## Clean Up Disk Space

Unused containers:

```bash
docker container prune
```

Unused images:

```bash
docker image prune
```

Unused networks:

```bash
docker network prune
```

Unused volumes:

```bash
docker volume prune
```

General cleanup:

```bash
docker system prune
```

!!! danger

    Cleanup commands can delete resources you may still need.

---

## Compose Troubleshooting

Check configuration:

```bash
docker compose config
```

List services:

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

Rebuild:

```bash
docker compose build --no-cache
```

Restart:

```bash
docker compose down
docker compose up -d
```

---

## Useful Diagnostic Sequence

When a container isn't working:

```bash
docker ps -a

docker logs <container>

docker inspect <container>

docker stats

docker network ls

docker volume ls
```

For Compose:

```bash
docker compose ps

docker compose logs

docker compose config

docker compose images
```

This usually narrows the problem down to one of:

```text
Image
  │
  ├── Build problem
  ├── Configuration
  ├── Application
  ├── Networking
  ├── Storage
  ├── Permissions
  └── Resources
```