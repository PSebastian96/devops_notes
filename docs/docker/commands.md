# Common Docker Commands

## Docker Information

```bash
docker version
docker info
docker --help
```

---

## Images

List images:

```bash
docker images
```

or:

```bash
docker image ls
```

Pull an image:

```bash
docker pull nginx
```

Remove an image:

```bash
docker rmi nginx
```

Inspect an image:

```bash
docker image inspect nginx
```

---

## Build Images

Build from the current directory:

```bash
docker build -t my-app .
```

Specify a Dockerfile:

```bash
docker build -f Dockerfile.dev -t my-app:dev .
```

Build with a tag:

```bash
docker build -t my-app:1.0 .
```

---

## Containers

List running containers:

```bash
docker ps
```

List all containers:

```bash
docker ps -a
```

Run a container:

```bash
docker run nginx
```

Run in the background:

```bash
docker run -d nginx
```

Give the container a name:

```bash
docker run -d --name web nginx
```

Map a port:

```bash
docker run -d \
  --name web \
  -p 8080:80 \
  nginx
```

---

## Container Lifecycle

Start:

```bash
docker start <container>
```

Stop:

```bash
docker stop <container>
```

Restart:

```bash
docker restart <container>
```

Remove:

```bash
docker rm <container>
```

Force removal:

```bash
docker rm -f <container>
```

---

## Logs

View logs:

```bash
docker logs <container>
```

Follow logs:

```bash
docker logs -f <container>
```

Show timestamps:

```bash
docker logs -t <container>
```

---

## Execute Commands

Execute a command:

```bash
docker exec <container> <command>
```

Open an interactive shell:

```bash
docker exec -it <container> sh
```

For images containing Bash:

```bash
docker exec -it <container> bash
```

---

## Copy Files

Container → host:

```bash
docker cp <container>:/path/file .
```

Host → container:

```bash
docker cp ./file <container>:/path/
```

---

## Cleanup

Remove stopped containers:

```bash
docker container prune
```

Remove unused images:

```bash
docker image prune
```

Remove unused resources:

```bash
docker system prune
```

!!! danger

    `docker system prune` can remove unused containers, networks, images, and build cache.

    Review what will be removed before using aggressive cleanup commands.

---

## Simple Workflow

```bash
docker pull nginx

docker run -d \
  --name web \
  -p 8080:80 \
  nginx

docker ps

docker logs web

docker exec -it web sh

docker stop web

docker rm web
```
