# uv and Docker

uv can be used directly inside Docker images.

A simple example:

```dockerfile
FROM python:3.12-slim

COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

WORKDIR /app

COPY pyproject.toml uv.lock ./

RUN uv sync --locked --no-install-project

COPY . .

RUN uv sync --locked

CMD ["uv", "run", "my-app"]
```

The important files are:

```text
pyproject.toml
uv.lock
```

These should be copied before the rest of the source code where possible so Docker can cache the dependency installation layer.

!!! danger
    Do not copy your local `.venv` into a Docker image.

    A virtual environment is platform-specific. The Docker image should create its own environment. Add `.venv` to `.dockerignore`.

Example `.dockerignore`:

```text
.venv/
.git/
__pycache__/
*.pyc
```

---