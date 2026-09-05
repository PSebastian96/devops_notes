# Docker Registries

A Docker registry stores and distributes container images.

---

## Common Registries

| Registry                  | Example                                        |
| ------------------------- | ---------------------------------------------- |
| Docker Hub                | `docker.io/user/app`                           |
| GitHub Container Registry | `ghcr.io/user/app`                             |
| GitLab Container Registry | `registry.gitlab.com/group/project`            |
| Amazon ECR                | `<account>.dkr.ecr.<region>.amazonaws.com/app` |
| Google Artifact Registry  | `<region>-docker.pkg.dev/...`                  |
| Azure Container Registry  | `<registry>.azurecr.io/app`                    |

---

## Login

Docker Hub:

```bash
docker login
```

Private registry:

```bash
docker login registry.example.com
```

---

## Tag an Image

```bash
docker tag my-app:latest \
  registry.example.com/team/my-app:1.0.0
```

---

## Push

```bash
docker push \
  registry.example.com/team/my-app:1.0.0
```

---

## Pull

```bash
docker pull \
  registry.example.com/team/my-app:1.0.0
```

---

## Complete Workflow

```bash
docker build -t my-app:1.0.0 .

docker login registry.example.com

docker tag my-app:1.0.0 \
  registry.example.com/team/my-app:1.0.0

docker push \
  registry.example.com/team/my-app:1.0.0
```

On another machine:

```bash
docker login registry.example.com

docker pull \
  registry.example.com/team/my-app:1.0.0
```

---

## Image Naming

A complete image reference can contain:

```text
registry/repository:tag
```

Example:

```text
registry.example.com/team/backend:1.4.2
```

Components:

```text
registry.example.com
        │
        └── Registry

team/backend
        │
        └── Repository

1.4.2
        │
        └── Tag
```

---

## Tags

Common release tags:

```text
1.0.0
1.0
1
latest
```

For production deployments, immutable version tags or digests are preferable to mutable tags such as `latest`.

---

## Image Digests

A digest identifies specific image content:

```text
my-app@sha256:<digest>
```

This allows deployments to reference an exact image rather than relying on a mutable tag.

---

## CI/CD Registry Workflow

```text
Git Push
   │
   ▼
CI Pipeline
   │
   ├── Test
   │
   ├── Build
   │
   ├── Scan
   │
   └── Push
         │
         ▼
      Registry
         │
         ▼
     Deployment
```