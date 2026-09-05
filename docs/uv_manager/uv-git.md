# uv and Git

uv works very well with Git because the important project files are text-based and should be committed:

```text
project/
├── pyproject.toml
├── uv.lock
├── .python-version
├── .gitignore
├── README.md
└── src/
```

Do **not** commit:

```text
.venv/
```

A typical `.gitignore`:

```gitignore
.venv/
__pycache__/
*.py[cod]
.pytest_cache/
.mypy_cache/
.ruff_cache/
```

!!! tip "Commit the lockfile"
    `uv.lock` should normally be committed to Git.

    It provides reproducible dependency resolution across machines and CI environments.

Typical workflow:

```bash
uv add requests

git status

git add pyproject.toml uv.lock

git commit -m "Add requests dependency"

git push
```

On another machine:

```bash
git pull

uv sync
```

The second machine will recreate the project environment from the project configuration and lockfile.

---