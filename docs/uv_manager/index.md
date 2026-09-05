# UV Project Management

## Overview

`uv` is a fast Python package and project manager developed by Astral.

It can manage:

* Python installations
* Virtual environments
* Project dependencies
* Lockfiles
* Python project metadata
* Command execution
* Python tools
* Package builds
* Workspaces

Unlike traditional workflows that combine `python`, `venv`, `pip`, `pip-tools`, and `pyenv`, uv can provide most of these capabilities through a single tool.

## Quick Reference

### Installation

| Platform        | Command                                            |
| --------------- | -------------------------------------------------- |
| Ubuntu / Debian | `curl -LsSf https://astral.sh/uv/install.sh \| sh` |
| Fedora          | `curl -LsSf https://astral.sh/uv/install.sh \| sh` |
| Arch            | `curl -LsSf https://astral.sh/uv/install.sh \| sh` |
| macOS           | `brew install uv`                                  |
| Windows         | `winget install --id=astral-sh.uv -e`              |

### Verification

| Task             | Command        |
| ---------------- | -------------- |
| uv version       | `uv --version` |
| uv location      | `which uv`     |
| Windows location | `where.exe uv` |
| Help             | `uv --help`    |

### Project

| Task                         | Command             |
| ---------------------------- | ------------------- |
| Virtual environment          | `uv venv`           |
| Create project               | `uv init <name>`    |
| Initialize current directory | `uv init`           |
| Sync environment             | `uv sync`           |
| Run command                  | `uv run <command>`  |
| Run Python                   | `uv run python`     |
| Show dependency tree         | `uv tree`           |
| Check lockfile               | `uv lock --check`   |

### Dependencies

| Task               | Command                                            |
| ------------------ | -------------------------------------------------- |
| Add dependency     | `uv add <package>`                                 |
| Add exact version  | `uv add "<package>==<version>"`                    |
| Add dev dependency | `uv add --dev <package>`                           |
| Remove dependency  | `uv remove <package>`                              |
| Update all         | `uv lock --upgrade`                                |
| Update one         | `uv lock --upgrade-package <package>`              |
| Update to version  | `uv lock --upgrade-package "<package>==<version>"` |
| Synchronize        | `uv sync`                                          |

### Python

| Task                      | Command                            |
| ------------------------- | ---------------------------------- |
| List Python versions      | `uv python list`                   |
| List installed versions   | `uv python list --only-installed`  |
| Install Python            | `uv python install 3.12`           |
| Install multiple versions | `uv python install 3.11 3.12 3.13` |
| Select project Python     | `uv python pin 3.12`               |
| Synchronize environment   | `uv sync`                          |
| Verify project Python     | `uv run python --version`          |

### Typical Workflow

```bash
# Create project
uv init my-project

cd my-project

# Select Python
uv python install 3.12
uv python pin 3.12

# Add dependencies
uv add requests
uv add --dev pytest ruff

# Create/sync environment
uv sync

# Run application
uv run python main.py

# Run tests
uv run pytest

# Check code
uv run ruff check .

# Update dependencies
uv lock --upgrade
uv sync

# Commit project configuration
git add pyproject.toml uv.lock .python-version
git commit -m "Update Python dependencies"
git push
```

!!! tip "Recommended project files"

    For a typical uv-managed Python project, commit:

    ```text
    pyproject.toml
    uv.lock
    .python-version
    ```

    and exclude:

    ```text
    .venv/
    ```

    This gives other developers and CI enough information to recreate the project's Python environment without committing the environment itself.