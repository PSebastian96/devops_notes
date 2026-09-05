# Remote Repositories

Remote repositories allow Git repositories to be shared between machines and developers.

## List Remotes

```bash
git remote -v
```

## Add a Remote

```bash
git remote add origin <url>
```

## Change a Remote URL

```bash
git remote set-url origin <url>
```

## Fetch Changes

Downloads information about changes from a remote repository without automatically integrating those changes into your current branch.

This is useful when you want to inspect remote changes before deciding whether to merge or otherwise integrate them.

```bash
git fetch
```

Fetch a specific remote:

```bash
git fetch origin
```

## Pull Changes

```bash
git pull
```

Equivalent conceptually to:

```bash
git fetch
git merge
```

## Push Changes

Uploads local commits to a remote repository.

```bash
git push
```

When pushing a new branch for the first time:

```bash
git push -u origin feature-login
```

Push a branch and establish its upstream:

The `-u` option establishes an upstream relationship, making future `git push` and `git pull` commands simpler.

```bash
git push -u origin main
```

After the upstream has been configured:

```bash
git push
```

## Clone a Repository

```bash
git clone <url>
```

Clone into a specific directory:

```bash
git clone <url> <directory>
```

Copies an existing remote repository.

```bash
git clone https://github.com/example/my-project.git
```

Then move into the repository:

```bash
cd my-project
```

!!! tip "Clone vs. init"
    Use `git clone` when a repository already exists remotely. Use `git init` when you are starting a new repository locally.


!!! note
    `git fetch` downloads remote information without changing your current branch. `git pull` fetches and then integrates the changes.
