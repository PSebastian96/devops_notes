# Docker

Docker is a platform for building, packaging, distributing, and running applications as **containers**.

A container packages an application together with the dependencies and configuration required to run it, while sharing the host operating system's kernel.

Docker is commonly used for:

* Local development
* Application packaging
* CI/CD
* Microservices
* Testing
* Reproducible environments
* Deployment
* Development tooling

---

## Docker at a Glance

A typical Docker workflow looks like:

```text
Dockerfile
    │
    │ docker build
    ▼
 Docker Image
    │
    │ docker run
    ▼
 Docker Container
    │
    ├── Volumes
    ├── Networks
    └── Environment variables
```

For multi-container applications:

```text
             Docker Compose
                   │
       ┌───────────┼───────────┐
       ▼           ▼           ▼
    App           DB          Redis
 Container      Container    Container
       │           │           │
       └───────────┼───────────┘
                   │
               Network
```

---

## Core Docker Components

| Component     | Purpose                                       |
| ------------- | --------------------------------------------- |
| Docker CLI    | Command-line interface                        |
| Docker Engine | Runs containers                               |
| Image         | Read-only application template                |
| Container     | Running instance of an image                  |
| Dockerfile    | Instructions for building an image            |
| Volume        | Persistent Docker-managed storage             |
| Network       | Communication between containers              |
| Registry      | Stores and distributes images                 |
| Compose       | Defines and runs multi-container applications |

---

## Typical Development Workflow

```bash
# Build image
docker build -t my-app .

# Run container
docker run -p 8000:8000 my-app

# Check running containers
docker ps

# View logs
docker logs <container>

# Stop container
docker stop <container>
```

For a Compose project:

```bash
docker compose up -d
```

---

## Docker and Git

A Docker project will commonly contain:

```text
project/
├── .git/
├── Dockerfile
├── .dockerignore
├── compose.yaml
├── src/
└── README.md
```

Docker configuration should normally be committed to Git.

Generated data, secrets, local configuration, and unnecessary build artifacts should not be committed.

---

## Docker and CI/CD

Docker is commonly used in CI/CD to:

1. Build an application image
2. Run tests
3. Tag the image
4. Push it to a registry
5. Deploy the image

Example:

```text
Git Push
   │
   ▼
CI Pipeline
   │
   ├── Test
   ├── Build
   ├── Scan
   └── Push
         │
         ▼
      Registry
         │
         ▼
      Deployment
```

---

## Quick Reference

| Task               | Command                          |
| ------------------ | -------------------------------- |
| Check Docker       | `docker version`                 |
| Docker information | `docker info`                    |
| List images        | `docker images`                  |
| List containers    | `docker ps`                      |
| Build image        | `docker build -t <name> .`       |
| Run container      | `docker run <image>`             |
| Stop container     | `docker stop <container>`        |
| Remove container   | `docker rm <container>`          |
| Remove image       | `docker rmi <image>`             |
| View logs          | `docker logs <container>`        |
| Open shell         | `docker exec -it <container> sh` |
| Start Compose      | `docker compose up -d`           |
| Stop Compose       | `docker compose down`            |