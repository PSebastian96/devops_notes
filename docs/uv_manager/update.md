## Updating uv

If uv was installed using the standalone installer:

```bash
uv self update
```

If installed through a package manager, update it through that package manager.

Example:

### Linux (Ubuntu)

```bash
uv self update
```

### `pip` manager

```bash
python3 -m pip install --upgrade uv
```

### `pipx` manager

```bash
pipx upgrade uv
```

### Homebrew

```bash
brew upgrade uv
```

### WinGet

```powershell
winget upgrade --id=astral-sh.uv -e
```

The standalone install supports `uv self update`; package-manager installations should generally be updated through their package manager.