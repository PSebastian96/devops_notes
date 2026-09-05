These tools work well together:

```text
                 Git Repository
                       │
          ┌────────────┴────────────┐
          │                         │
   pyproject.toml                uv.lock
          │                         │
          └──────────┬──────────────┘
                     │
                    uv
                     │
                  .venv
                     │
          ┌──────────┴──────────┐
          │                     │
       Makefile              Dockerfile
          │                     │
          │                     │
     local commands        container build
```

A typical development workflow:

```bash
git clone <repository>

cd project

uv sync

make test

make lint

git add .

git commit -m "Implement feature"

git push
```

CI/CD can then use the same dependency definition:

```bash
uv sync --locked
uv run pytest
```

Docker can use:

```bash
uv sync --locked
```

This gives you one source of truth for Python dependencies: `pyproject.toml` plus `uv.lock`.

---