# GitHub Authentication & Repository Setup

## Overview

After installing Git, the next step is to authenticate with GitHub and connect a local project to a GitHub repository.

There are two common ways to authenticate Git with GitHub:

| Method                 | Recommended for                | Authentication               |
| ---------------------- | ------------------------------ | ---------------------------- |
| **HTTPS + GitHub CLI** | Most developers                | Browser-based authentication |
| **SSH**                | Developers who prefer SSH keys | SSH key pair                 |

!!! tip "Recommended setup"
    For a new development machine, **GitHub CLI + HTTPS** is a simple option. GitHub CLI can authenticate through the browser and store the credentials used by Git.

---

## 1. Authenticate with GitHub

Verify Git

Before installing GitHub CLI, verify that Git is installed:

```bash
git --version
```

Example:

```bash
git version 2.51.0
```

### Linux

The installation method depends on your Linux distribution.

Debian / Ubuntu

For Debian and Ubuntu systems, GitHub provides an apt installation method.

```bash
sudo apt update
sudo apt install gh
```

Verify the installation:
```bash
gh --version
```

Example:

```bash
gh version 2.x.x
```

Fedora / RHEL

On Fedora and compatible RHEL-based distributions:

```bash
sudo dnf install gh
```

Then:

```bash
gh --version
```

GitHub lists dnf as the installation method for Fedora/RHEL systems.

openSUSE / SUSE

For openSUSE and SUSE systems:

```bash
sudo zypper install gh
```

Then:

```bash
gh --version
```

GitHub also provides package downloads for distributions where a package manager is not available.

### macOS

Homebrew

The simplest method on macOS is Homebrew:

```bash
brew install gh
```

Verify:

```bash
gh --version
```

GitHub officially documents Homebrew as the macOS installation method.

If Homebrew is not installed, you can install it separately, then run:

brew install gh

!!! tip
    If you already use Homebrew for development tools such as Git, Python, Node.js, Docker, or Terraform, installing `gh` through Homebrew keeps your command-line tooling in one package manager.

### Windows

WinGet

On modern Windows installations, the easiest terminal-based installation is Windows Package Manager:

```bash
winget install --id GitHub.cli
```

After installation, open a new terminal and verify:

```bash
gh --version
```

GitHub lists WinGet as the Windows package-manager installation method.

Windows Terminal

If you're using Windows Terminal, PowerShell, or Command Prompt:

```bash
gh --version
```

should work after installation.

!!! note
    If `gh` is not recognised immediately after installation, close and reopen your terminal so that the updated `PATH` is loaded.

Verify the Installation

Regardless of operating system:

```bash
gh --version
```

You can also display the available commands:

```bash
gh help
```

For example:
```bash
gh auth
gh repo
gh pr
gh issue
gh workflow
```

- `gh auth` is particularly important because it is used to authenticate your GitHub account.

---

## 2. Verify Git Installation

Check that Git is installed:

```bash
git --version
```

Example:

```text
git version 2.51.0
```

Configure your Git identity:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

Check the configuration:

```bash
git config --global --list
```

!!! note "Git identity vs GitHub authentication"

    `user.name` and `user.email` identify the author of your commits.

    They do **not** authenticate you with GitHub.

    GitHub authentication determines whether you are allowed to clone, pull, or push to a repository.

---

### Option A — GitHub CLI + HTTPS

Install the GitHub CLI (`gh`) for your operating system.

Verify it:

```bash
gh --version
```

Authenticate:

```bash
gh auth login
```

Follow the prompts.

Choose:

```text
? What account do you want to log into?
> GitHub.com

? What is your preferred protocol for Git operations?
> HTTPS

? Authenticate Git with your GitHub credentials?
> Yes

? How would you like to authenticate GitHub CLI?
> Login with a web browser
```

GitHub CLI will provide a one-time code and open a browser where you can authenticate to your GitHub account.

Check your authentication:

```bash
gh auth status
```

You should see information indicating that you are logged into GitHub.

!!! tip
    With HTTPS authentication through GitHub CLI, you do not need to manually create and copy a personal access token for normal Git operations. GitHub CLI can store the Git credentials for you.

---

### Option B — SSH Authentication

SSH is another common way to authenticate with GitHub.

Check whether you already have an SSH key:

```bash
ls ~/.ssh
```

Look for files such as:

```text
id_ed25519
id_ed25519.pub
```

If you don't have a key, create one:

```bash
ssh-keygen -t ed25519 -C "you@example.com"
```

Start the SSH agent:

```bash
eval "$(ssh-agent -s)"
```

Add your private key:

```bash
ssh-add ~/.ssh/id_ed25519
```

Display your public key:

```bash
cat ~/.ssh/id_ed25519.pub
```

Copy the entire output.

In GitHub, add the public key under:

```text
Settings
→ SSH and GPG keys
→ New SSH key
```

GitHub requires the public key to be added to your account before the SSH key can authenticate you.

Test the connection:

```bash
ssh -T git@github.com
```

A successful authentication will produce a message indicating that GitHub has successfully authenticated your account.

!!! danger "Never share your private key"

    Never upload or share:

    ```text
    ~/.ssh/id_ed25519
    ```

    Only the `.pub` file should be added to GitHub.

---

## 3. Create a Repository on GitHub

Create a new repository on GitHub.

For example:

```text
Repository name: my-project
```

You will then have a remote repository such as:

```text
https://github.com/<username>/my-project.git
```

or:

```text
git@github.com:<username>/my-project.git
```

GitHub supports both HTTPS and SSH remote URLs.

!!! tip "Empty repository"

```
If you already have a local project that you want to push to GitHub, creating an **empty GitHub repository** is often the simplest approach.
```

---

## 4. Connect Your Local IDE Project

Open your project directory in your IDE.

For example:

```bash
cd ~/projects/my-project
```

You can use any IDE, such as VS Code, IntelliJ IDEA, PyCharm, or another Git-compatible editor.

The IDE is simply working with the Git repository on your filesystem.

The important relationship is:

```text
IDE
 │
 │ edits files
 ▼
Local Git Repository
 │
 │ git push
 ▼
GitHub Repository
```

---

## 5. Initialize the Local Repository

If the project does not already contain a Git repository:

```bash
git init
```

This creates a hidden `.git` directory:

```text
my-project/
├── .git/
├── src/
├── README.md
└── ...
```

Check the repository:

```bash
git status
```

You should see the files listed as untracked files.

!!! note
    `git init` creates the **local** repository. It does not connect the repository to GitHub yet.

---

## 6. Create a `.gitignore`

Before committing your files, create a `.gitignore` file.

For example:

```gitignore
# IDE
.vscode/
.idea/

# Environment variables
.env
.env.*

# Python
__pycache__/
*.pyc
.venv/

# Node
node_modules/

# Build output
dist/
build/
```

The `.gitignore` prevents files such as secrets, dependencies, IDE configuration, and build artifacts from being committed.

!!! danger

    Never commit secrets such as:

    ```text
    .env
    API keys
    passwords
    private SSH keys
    cloud credentials
    ```

---

## 7. Stage the Project

Check what Git sees:

```bash
git status
```

Stage the files:

```bash
git add .
```

Check the staging area:

```bash
git status
```

You should now see the files under:

```text
Changes to be committed
```

---

## 8. Create the First Commit

Create your initial commit:

```bash
git commit -m "Initial commit"
```

Check the history:

```bash
git log --oneline
```

Example:

```text
a1b2c3d (HEAD -> main) Initial commit
```

At this point:

```text
Working Directory
        │
        │ git add
        ▼
Staging Area
        │
        │ git commit
        ▼
Local Repository
```

The repository is still only local.

---

## 9. Connect the Repository to GitHub

Add the GitHub repository as a remote:

### HTTPS

```bash
git remote add origin https://github.com/<username>/my-project.git
```

### SSH

```bash
git remote add origin git@github.com:<username>/my-project.git
```

Check the remote:

```bash
git remote -v
```

Example:

```text
origin  https://github.com/<username>/my-project.git (fetch)
origin  https://github.com/<username>/my-project.git (push)
```

`origin` is simply the conventional name Git gives to the primary remote repository.

---

## 10. Set the Main Branch

Modern repositories commonly use `main` as the primary branch.

Set your local branch to `main`:

```bash
git branch -M main
```

Check:

```bash
git branch
```

You should see:

```text
* main
```

---

## 11. Push the Repository to GitHub

Push the local `main` branch:

```bash
git push -u origin main
```

The `-u` option establishes the upstream relationship between your local branch and the remote branch.

After this, future pushes can usually be performed with:

```bash
git push
```

The complete relationship is now:

```text
Local main
    │
    │ git push
    ▼
origin/main
    │
    ▼
GitHub
```

---

## 12. Verify the Remote

Check the configured remote:

```bash
git remote -v
```

Check the branch tracking relationship:

```bash
git branch -vv
```

Example:

```text
* main a1b2c3d [origin/main] Initial commit
```

This means your local `main` branch is tracking `origin/main`.

---

## 13. Normal Development Workflow

Once everything is configured, your normal workflow becomes:

```bash
# Check current state
git status

# Create a branch for your work
git switch -c feature/my-change

# Edit files
# ...

# Review changes
git diff

# Stage changes
git add .

# Commit changes
git commit -m "Add my change"

# Push branch to GitHub
git push -u origin feature/my-change
```

The workflow is:

```text
                    GitHub
                       ▲
                       │
                    git push
                       │
                       │
IDE → Working Tree → Staging → Local Commit
        │               │           │
        │            git add     git commit
        │
      edit
```

---

## 14. Connecting an Existing Local Project

If you already have a project:

```text
my-project/
├── src/
├── README.md
├── package.json
└── ...
```

You can connect it directly:

```bash
cd my-project

git init

git branch -M main

git add .

git commit -m "Initial commit"

git remote add origin https://github.com/<username>/my-project.git

git push -u origin main
```

For SSH:

```bash
git remote add origin git@github.com:<username>/my-project.git
```

---

## 15. Cloning an Existing GitHub Repository

If the repository **already exists on GitHub** and you want to work on it locally, you normally do **not** use `git init`.

Instead, clone it:

### HTTPS

```bash
git clone https://github.com/<username>/my-project.git
```

### SSH

```bash
git clone git@github.com:<username>/my-project.git
```

Then:

```bash
cd my-project
```

Open the directory in your IDE.

For example, with VS Code:

```bash
code .
```

Check the repository:

```bash
git status
```

You can then make changes, commit, and push:

```bash
git add .
git commit -m "Update project"
git push
```

!!! tip "Clone vs init"

````
Use:

```bash
git clone <url>
```

when the repository **already exists remotely**.

Use:

```bash
git init
```

when you have an **existing local project** that you want to turn into a Git repository.
````

---

## 16. GitHub Authentication vs Repository Connection

These are two separate steps:

```text
1. Authenticate
       │
       ▼
   GitHub knows
   who you are
       │
       ▼
2. Configure remote
       │
       ▼
   Git knows where
   the repository is
       │
       ▼
3. Push / Pull
```

For example:

```bash
gh auth login
```

handles authentication.

While:

```bash
git remote add origin https://github.com/<username>/my-project.git
```

connects your local Git repository to the GitHub repository.

!!! note
    Authentication does not automatically connect your project to a repository.

    Likewise, adding a remote does not grant you permission to push. You need both a correctly configured remote and valid authentication/authorization.

---

## 17. Quick Reference

| Task                         | Command                                            |
| ---------------------------- | -------------------------------------------------- |
| Check Git                    | `git --version`                                    |
| Configure name               | `git config --global user.name "Your Name"`        |
| Configure email              | `git config --global user.email "you@example.com"` |
| Authenticate with GitHub CLI | `gh auth login`                                    |
| Check GitHub authentication  | `gh auth status`                                   |
| Create local repository      | `git init`                                         |
| Clone existing repository    | `git clone <url>`                                  |
| Check repository state       | `git status`                                       |
| Stage files                  | `git add .`                                        |
| Create commit                | `git commit -m "message"`                          |
| Add GitHub remote            | `git remote add origin <url>`                      |
| Check remote                 | `git remote -v`                                    |
| Rename branch to main        | `git branch -M main`                               |
| Push first time              | `git push -u origin main`                          |
| Push subsequent changes      | `git push`                                         |
| Download remote changes      | `git pull`                                         |

---

## Complete First-Time Setup

For an existing local project, the entire process can be reduced to:

```bash
# Authenticate with GitHub
gh auth login

# Configure Git identity
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Go to your project
cd my-project

# Initialize Git
git init

# Use main as the primary branch
git branch -M main

# Stage and commit
git add .
git commit -m "Initial commit"

# Connect to GitHub
git remote add origin https://github.com/<username>/my-project.git

# Push to GitHub
git push -u origin main
```

After the initial setup:

```bash
git add .
git commit -m "Describe your change"
git push
```

That's the basic **IDE → Git → GitHub** workflow.

!!! tip "Recommended mental model"
    Remember the difference between these four things:

    ```text
    GitHub Account
        │
        │ authentication
        ▼
    GitHub Access
        │
        │ remote URL
        ▼
    Local Git Repository
        │
        │ edited by
        ▼
    IDE / Working Directory
    ``
    Your IDE does not need a separate Git repository. It works with the `.git` repository in your project directory and uses Git's configured authentication to communicate with GitHub.