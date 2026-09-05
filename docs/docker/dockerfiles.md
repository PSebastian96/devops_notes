# Dockerfiles

A Dockerfile contains instructions used to build a Docker image.

---

## Basic Dockerfile

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["python", "app.py"]
```

Build:

```bash
docker build -t my-app .
```

Run:

```bash
docker run -p 8000:8000 my-app
```

---

## Common Instructions

### FROM

Defines the base image:

```dockerfile
FROM python:3.12-slim
```

Every normal Dockerfile starts with a base image.

---

### WORKDIR

Sets the working directory:

```dockerfile
WORKDIR /app
```

---

### COPY

Copies files from the build context:

```dockerfile
COPY . .
```

Prefer copying only what is required where practical.

---

### RUN

Executes a command during image construction:

```dockerfile
RUN apt-get update
```

---

### ENV

Sets environment variables:

```dockerfile
ENV APP_ENV=production
```

Do not use `ENV` for secrets.

---

### EXPOSE

Documents the port the application listens on:

```dockerfile
EXPOSE 8000
```

It does not publish the port to the host.

Publishing is done with:

```bash
docker run -p 8000:8000 my-app
```

---

### CMD

Defines the default command:

```dockerfile
CMD ["python", "app.py"]
```

---

### ENTRYPOINT

Defines the executable for the container:

```dockerfile
ENTRYPOINT ["python"]
```

Arguments can then be supplied separately.

---

## Dockerfile Best Practices

### Use Small Base Images

For example:

```dockerfile
FROM python:3.12-slim
```

rather than unnecessarily large base images.

---

### Use `.dockerignore`

Example:

```text
.git
.venv
__pycache__
*.pyc
.env
```

---

### Avoid Secrets

Do not write:

```dockerfile
ENV DATABASE_PASSWORD=mysecret
```

Secrets should be provided at runtime or through an appropriate secret-management mechanism.

---

### Combine Related Package Installation

Instead of unnecessarily creating multiple layers:

```dockerfile
RUN apt-get update
RUN apt-get install -y curl
```

use:

```dockerfile
RUN apt-get update \
    && apt-get install -y --no-install-recommends curl \
    && rm -rf /var/lib/apt/lists/*
```

---

## Multi-stage Builds

Multi-stage builds allow build dependencies to be separated from the final runtime image.

```dockerfile
FROM golang:1.24 AS builder

WORKDIR /app

COPY . .

RUN go build -o app .

FROM debian:bookworm-slim

COPY --from=builder /app/app /app

CMD ["/app"]
```

The final image doesn't need the Go compiler.

---

## Build Arguments

```dockerfile
ARG APP_VERSION=dev

LABEL version=$APP_VERSION
```

Build:

```bash
docker build \
  --build-arg APP_VERSION=1.0 \
  -t my-app:1.0 .
```

Do not use build arguments for secrets.

---

## Build Context

When running:

```bash
docker build .
```

the current directory becomes the build context.

Docker can only `COPY` files from within the build context.

Use `.dockerignore` to prevent unnecessary files from being sent to the builder.