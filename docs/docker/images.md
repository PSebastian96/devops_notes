# Docker Images

Docker images are immutable templates used to create containers.

---

## List Images

```bash
docker image ls
```

---

## Pull an Image

```bash
docker pull nginx
```

Specific tag:

```bash
docker pull nginx:1.27
```

!!! note

    Avoid relying on `latest` in production. Explicit version tags make deployments more predictable.

---

## Image Tags

An image reference commonly looks like:

```text
registry/repository:tag
```

Example:

```text
docker.io/library/nginx:1.27
```

Private registry:

```text
registry.example.com/team/my-app:1.0.0
```

---

## Tag an Image

```bash
docker tag my-app:latest my-app:1.0
```

Tag for a registry:

```bash
docker tag my-app:latest registry.example.com/team/my-app:1.0
```

---

## Push an Image

Login:

```bash
docker login registry.example.com
```

Push:

```bash
docker push registry.example.com/team/my-app:1.0
```

---

## Inspect an Image

```bash
docker image inspect nginx
```

Show image history:

```bash
docker history nginx
```

---

## Remove an Image

```bash
docker image rm nginx
```

Force:

```bash
docker image rm -f nginx
```

---

## Image Digest

Tags can be moved to different image versions.

Digests identify a specific image content.

Example:

```text
nginx@sha256:<digest>
```

For highly reproducible deployments, immutable digests can be preferable to mutable tags.

---

## Image Workflow

```text
Dockerfile
     │
     ▼
docker build
     │
     ▼
   Image
     │
     ├── docker run
     │
     └── docker push
             │
             ▼
          Registry
```