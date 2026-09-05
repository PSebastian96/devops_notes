# Docker Concepts

Understanding the difference between an **image**, **container**, **volume**, **network**, and **registry** is fundamental to using Docker effectively.

---

## Image

An image is an immutable template used to create containers.

```text
Image
  │
  ├── Application
  ├── Runtime
  ├── Dependencies
  └── Configuration
```

Images are composed of layers.

---

## Container

A container is a running or stopped instance of an image.

```text
Image
  │
  ├── Container A
  ├── Container B
  └── Container C
```

Multiple containers can be created from the same image.

---

## Image vs Container

| Image                     | Container             |
| ------------------------- | --------------------- |
| Template                  | Instance              |
| Immutable                 | Has writable layer    |
| Used to create containers | Runs the application  |
| Stored locally/registry   | Exists on Docker host |

---

## Dockerfile

A Dockerfile describes how to build an image.

Example:

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "app.py"]
```

Build:

```bash
docker build -t my-app .
```

---

## Registry

A registry stores Docker images.

Examples include:

* Docker Hub
* GitHub Container Registry
* GitLab Container Registry
* Amazon ECR
* Google Artifact Registry
* Azure Container Registry

Typical workflow:

```text
Local Dockerfile
      │
      ▼
    Image
      │
      │ docker push
      ▼
   Registry
      │
      │ docker pull
      ▼
 Other Docker Host
```

---

## Volume

Volumes provide persistent storage outside the container's writable layer.

```text
Container
    │
    ▼
 Volume
    │
    ▼
Persistent data
```

If a container is deleted, its writable layer disappears.

A volume can remain.

---

## Network

Docker networks allow containers to communicate.

Example:

```text
app ───────► database
 │
 └─────────► redis
```

Compose automatically creates a network for services unless configured otherwise.

---

## Environment Variables

Configuration can be supplied using environment variables:

```bash
docker run \
  -e APP_ENV=production \
  my-app
```

Inside the application:

```text
APP_ENV=production
```

Avoid putting passwords and secrets directly into Dockerfiles.

---

## Docker Daemon

The Docker daemon manages:

* Containers
* Images
* Networks
* Volumes
* Builds

The CLI communicates with the Docker daemon.

```text
docker CLI
    │
    ▼
Docker Engine
    │
    ├── Containers
    ├── Images
    ├── Networks
    └── Volumes
```

---

## Docker Layers

Docker images are built from layers.

For example:

```text
Application layer
        │
Dependencies layer
        │
Runtime layer
        │
Base image
```

Docker can cache unchanged layers, making subsequent builds faster.

---

## Ephemeral Containers

Containers should generally be treated as disposable.

Instead of:

```text
Container
    │
    └── Important database data
```

prefer:

```text
Container
    │
    └── Application

Volume
    │
    └── Database data
```

This separates application lifecycle from data lifecycle.