# Installation

uv can be installed without having Python or Rust installed first. The official standalone installer is the recommended cross-platform approach, while package managers are also available.

## Linux

### Ubuntu / Debian

The most portable option is the official installer:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

If `curl` isn't available:

```bash
wget -qO- https://astral.sh/uv/install.sh | sh
```

Restart your shell after installation if required.

```bash
source ~/.bashrc
```

or:

```bash
source ~/.zshrc
```

!!! note

```
The official installer may modify your shell profile so that the `uv` executable is available on your `PATH`.
```

### Fedora

You can use the official installer:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Or, if `uv` is available through your configured Fedora repositories:

```bash
sudo dnf install uv
```

Verify:

```bash
uv --version
```

### Arch Linux

If `uv` is available in your configured Arch repositories:

```bash
sudo pacman -S uv
```

Alternatively, use the official installer:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

---

## macOS

### Homebrew

If Homebrew is installed:

```bash
brew install uv
```

Verify:

```bash
uv --version
```

The official uv documentation lists Homebrew as a supported installation method.

### Standalone Installer

Alternatively:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

---

## Windows

### WinGet

Using Windows Package Manager:

```powershell
winget install --id=astral-sh.uv -e
```

Then open a new terminal and verify:

```powershell
uv --version
```

GitHub-style PowerShell installation can also be performed using the official installer:

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Both methods are documented by the uv project.

---

## Verify Installation

Regardless of operating system:

```bash
uv --version
```

Example:

```text
uv 0.x.x
```

Check where the executable is located:

Linux/macOS:

```bash
which uv
```

Windows:

```powershell
where.exe uv
```

Display help:

```bash
uv --help
```

Display help for a specific command:

```bash
uv help add
```

---

