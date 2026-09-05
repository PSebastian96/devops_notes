# Docker Installation

## Installation

Docker installation differs slightly depending on the operating system.

For Linux, Docker Engine is the primary component.

For Windows and macOS, Docker Desktop provides the Docker Engine together with the Docker CLI and supporting components.

---

## Ubuntu

The recommended approach is to install Docker Engine from Docker's official APT repository rather than relying on an old distribution package.

Remove conflicting packages if present:

```bash
sudo apt remove docker.io docker-doc docker-compose podman-docker containerd runc
```

Install prerequisites:

```bash
sudo apt update

sudo apt install ca-certificates curl
```

Create the keyring directory:

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

Add Docker's signing key:

```bash
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  -o /etc/apt/keyrings/docker.asc

sudo chmod a+r /etc/apt/keyrings/docker.asc
```

Add the Docker repository:

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] \
  https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Install Docker:

```bash
sudo apt update

sudo apt install docker-ce docker-ce-cli containerd.io \
  docker-buildx-plugin docker-compose-plugin
```

---

## Verify Installation

Check the Docker version:

```bash
docker --version
```

More detailed information:

```bash
docker version
```

Check the daemon:

```bash
sudo systemctl status docker
```

Run the test image:

```bash
sudo docker run hello-world
```

---

## Run Docker Without sudo

By default, Docker's Unix socket is accessible to root.

Add your user to the `docker` group:

```bash
sudo usermod -aG docker $USER
```

Then log out and back in.

Alternatively:

```bash
newgrp docker
```

Test:

```bash
docker run hello-world
```

!!! danger

    Membership of the `docker` group effectively grants root-level control over the host through Docker.

    Only add trusted users to this group.

---

## macOS

The standard approach is Docker Desktop.

Using Homebrew:

```bash
brew install --cask docker
```

Start Docker Desktop from Applications.

Verify:

```bash
docker version
```

Test:

```bash
docker run hello-world
```

---

## Windows

Docker Desktop is the standard installation for Windows.

Using WinGet:

```powershell
winget install Docker.DockerDesktop
```

Start Docker Desktop and allow it to configure the required WSL 2 integration if prompted.

Verify from PowerShell:

```powershell
docker version
```

Test:

```powershell
docker run hello-world
```

---

## Docker Compose

Modern Docker installations provide Compose as a Docker CLI plugin.

Verify:

```bash
docker compose version
```

Use:

```bash
docker compose up
```

rather than the older:

```bash
docker-compose up
```

!!! note "Modern syntax"

    The recommended modern command is:

    ```bash
    docker compose
    ```

    Older installations may use the separate `docker-compose` command.