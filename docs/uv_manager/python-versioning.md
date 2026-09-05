# Python Versioning

## Install a Specific Python Version

Install Python 3.12:

```bash
uv python install 3.12
```

Install a specific patch release:

```bash
uv python install 3.12.3
```

Install multiple versions:

```bash
uv python install 3.11 3.12 3.13
```

uv keeps these Python installations separate from the system Python installation.

---

## Select the Python Version for a Project

Inside your project:

```bash
uv python pin 3.12
```

This creates:

```text
.python-version
```

containing the selected Python version.

Example:

```text
3.12
```

uv uses this file to determine the project's default Python version.

Then recreate/synchronize the environment:

```bash
uv sync
```

Verify:

```bash
uv run python --version
```

Example:

```text
Python 3.12.x
```

The project is now using its own Python environment:

```text
System Python
     │
     ├── /usr/bin/python3
     │
     └── unaffected
     
uv-managed Python
     │
     └── Python 3.12
             │
             ▼
          .venv/
```

---

## Change the Python Version

Suppose the project currently uses:

```text
Python 3.12
```

and you want Python 3.13.

First install the version if necessary:

```bash
uv python install 3.13
```

Pin the project:

```bash
uv python pin 3.13
```

Synchronize the environment:

```bash
uv sync
```

Verify:

```bash
uv run python --version
```

You should now see:

```text
Python 3.13.x
```

---

## Downgrade Python

The same process works in reverse.

For example, change from Python 3.13 to Python 3.12:

```bash
uv python install 3.12
uv python pin 3.12
uv sync
```

Verify:

```bash
uv run python --version
```

The system Python is not changed.

!!! note "Project-level Python version"

    The important distinction is:

    ```bash
    uv python install 3.12
    ```

    installs a Python version managed by uv.

    While:

    ```bash
    uv python pin 3.12
    ```

    tells a particular project which Python version to use.

    Then:

    ```bash
    uv sync
    ```

    synchronizes the project's `.venv` with that configuration.

---

## Python Version Requirements

A project can also specify the Python versions it supports through `pyproject.toml`.

For example:

```toml
[project]
requires-python = ">=3.12"
```

Or:

```toml
[project]
requires-python = ">=3.12,<3.14"
```

This is different from `.python-version`.

### `.python-version`

Specifies the project's preferred Python version.

```text
3.12
```

### `requires-python`

Specifies which Python versions the project supports.

```toml
requires-python = ">=3.12,<3.14"
```

Think of it as:

```text
.python-version
    │
    └── "Use this Python by default"

requires-python
    │
    └── "These Python versions are supported"
```

uv uses both when resolving and running the project.

---