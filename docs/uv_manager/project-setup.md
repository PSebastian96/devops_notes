## Project Setup

uv's recommended project workflow is based around:

```text
pyproject.toml
uv.lock
.venv/
.python-version
```

The important distinction is:

```text
pyproject.toml
    │
    ├── Project metadata
    ├── Python requirement
    └── Dependency requirements
              │
              ▼
          uv.lock
              │
              ▼
           .venv/
```

`pyproject.toml` describes the project and its dependency requirements.

`uv.lock` records the exact resolved dependency versions.

`.venv` contains the project's isolated Python environment.

`.python-version` can specify the Python version used by the project.

---

## Initialize a Project

Create a virtual environment:

```bash
uv venv
```

Create a new project:

```bash
uv init my-project
```

Enter the project:

```bash
cd my-project
```

The project will contain files such as:

```text
my-project/
├── .python-version
├── README.md
├── pyproject.toml
└── src/
    └── my_project/
        └── __init__.py
```

uv's default project initialization creates an application-style project.

You can also initialize a project in the current directory:

```bash
mkdir my-project
cd my-project

uv init
```

---

## Project Environment

uv automatically manages a virtual environment for the project:

```text
my-project/
├── .venv/
├── pyproject.toml
└── uv.lock
```

The `.venv` directory is isolated from the system Python installation.

It should **not** be committed to Git.

uv automatically creates the environment when required by commands such as `uv run` or `uv sync`.

You can explicitly create/synchronize it:

```bash
uv sync
```

---

## Running Python

Instead of manually activating the environment, uv can execute commands inside the project environment:

```bash
uv run python
```

Run a script:

```bash
uv run python main.py
```

Run a module:

```bash
uv run python -m my_project
```

Check the Python version being used:

```bash
uv run python --version
```

This is useful because `uv run` ensures the project environment is up to date before running the command.

---

## Activating the Environment

You can also activate `.venv` manually.

### Linux / macOS

```bash
source .venv/bin/activate
```

### Windows PowerShell

```powershell
.venv\Scripts\Activate.ps1
```

### Windows CMD

```cmd
.venv\Scripts\activate
```

After activation:

```bash
python --version
```

and:

```bash
which python
```

Linux/macOS should point into:

```text
.venv/bin/python
```

Windows:

```text
.venv\Scripts\python.exe
```

!!! tip
    You don't have to activate the environment when using uv.

    This:

    ```bash
    uv run python main.py
    ```

    is often preferable to:

    ```bash
    source .venv/bin/activate
    python main.py
    ```

---

# Dependencies

## Add a Dependency

Add a package:

```bash
uv add requests
```

This updates:

```text
pyproject.toml
uv.lock
.venv/
```

uv resolves the dependency, updates the lockfile, and synchronizes the environment.

---

## Add a Specific Version

```bash
uv add "requests==2.32.4"
```

---

## Add a Version Constraint

```bash
uv add "requests>=2.30,<3"
```

---

## Add a Development Dependency

For development-only dependencies:

```bash
uv add --dev pytest
```

For example:

```bash
uv add --dev ruff
uv add --dev pytest
uv add --dev mypy
```

Development dependencies are stored in dependency groups rather than normal runtime dependencies.

---

# Remove Dependencies

Remove a dependency:

```bash
uv remove requests
```

Remove a development dependency:

```bash
uv remove pytest
```

uv updates the project configuration, lockfile, and environment.

---

# Install / Synchronize Dependencies

Synchronize the project:

```bash
uv sync
```

This installs the dependencies described by the lockfile.

It also removes packages that are no longer part of the locked environment by default.

A useful workflow after cloning a repository is:

```bash
git clone <repository>
cd <repository>

uv sync
```

---

# Update Dependencies

Update all dependencies:

```bash
uv lock --upgrade
```

Then synchronize:

```bash
uv sync
```

You can also combine the operations:

```bash
uv sync --upgrade
```

Update a specific dependency:

```bash
uv lock --upgrade-package requests
```

Then:

```bash
uv sync
```

Or update a package to a specific version:

```bash
uv lock --upgrade-package "requests==2.32.4"
```

Dependency upgrades remain constrained by the version requirements declared by the project.

---

# Dependency Categories

A typical project might contain:

```text
Runtime dependencies
├── requests
├── fastapi
└── pydantic

Development dependencies
├── pytest
├── ruff
└── mypy
```

Add runtime dependencies:

```bash
uv add requests
```

Add development dependencies:

```bash
uv add --dev pytest
uv add --dev ruff
```

Install only development dependencies:

```bash
uv sync --only-dev
```

Skip development dependencies:

```bash
uv sync --no-dev
```

uv supports dependency groups for separating development tooling from runtime requirements.

---

# Inspect Dependencies

Show the dependency tree:

```bash
uv tree
```

Check the project's configuration:

```bash
uv version
```

Check the lockfile:

```bash
uv lock --check
```

The `--check` option verifies that the lockfile is up to date without modifying it.

---