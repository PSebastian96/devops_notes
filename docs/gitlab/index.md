# Gitlab

## 1. Overview

GitLab is a DevOps platform that combines Git repositories, collaboration, CI/CD, issue tracking, package/container registries, and deployments in one platform.

For a typical project:

```text
Developer
   │
   ├── Git
   │    ├── commit
   │    ├── branch
   │    └── push
   │
   ▼
GitLab Repository
   │
   ├── Merge Request
   ├── Code Review
   ├── CI/CD Pipeline
   │
   ▼
Runner
   │
   ├── Test
   ├── Build
   ├── Package
   └── Deploy
   │
   ▼
Environment
   ├── Development
   ├── Staging
   └── Production
```

## 2. GIT vs Gitlab

Git and GitLab are not the same thing.

Git is the underlying version control system.

GitLab provides services around Git.

Git	GitLab
Version control system	DevOps platform
Runs locally	Hosted platform
Tracks commits	Hosts Git repositories
Manages branches	Provides merge requests
Provides history	Provides CI/CD
git commit	Pipelines
git push	Runners
git merge	Issues
Local repository	Container/package registries

## 3. Gitlab Project

A GitLab project normally contains:

```text
Project
├── Repository
├── Issues
├── Merge Requests
├── CI/CD
├── Packages & Registries
├── Deployments
├── Environments
└── Project Settings
```

A GitLab project is broadly equivalent to a repository on GitHub.

## 4. Create a Gitlab project

Create a project from the GitLab web interface.

Typical options:

1. Create blank project
2. Create from template
3. Import existing repository

For an existing local project:

```bash
cd my-project
git init
git add .
git commit -m "Initial commit"
```

Add the GitLab remote:

```bash
git remote add origin https://gitlab.com/<username>/<project>.git
```

Rename the default branch:

```bash
git branch -M main
```

Push:

```bash
git push -u origin main
```

Check the remote:

```bash
git remote -v
```

## 5. GitLab Authentication

GitLab supports several authentication methods.

Common approaches:

1. HTTPS + Personal Access Token
2. SSH keys
3. GitLab CLI / API authentication

For regular Git operations, SSH is convenient because you don't repeatedly enter credentials.

- SSH Authentication

Generate an SSH key if you don't already have one:

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

Start the SSH agent:

```bash
eval "$(ssh-agent -s)"
```

Add your key:

```bash
ssh-add ~/.ssh/id_ed25519
```

Display the public key:

```bash
cat ~/.ssh/id_ed25519.pub
```

Add the public key to GitLab under:

GitLab
→ Preferences
→ SSH Keys

Test:

```bash
ssh -T git@gitlab.com
```

Use the SSH repository URL:

```bash
git remote set-url origin git@gitlab.com:<username>/<project>.git
```
Verify:

```bash
git remote -v
```