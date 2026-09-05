# Branching

Branches allow development to happen independently.

!!! note "Why use branches?"
    Branches allow you to develop features, fixes, or experiments without making changes directly on the `main` branch.

## List Branches

```bash
git branch
```

Include remote branches:

```bash
git branch -a
```

## Create a Branch

```bash
git branch feature/login
```

## Switch Branch

### Switch

```bash
git switch feature/login
```
### Checkout

```bash
git checkout feature/login
```

## Create and Switch

### New Syntax

```bash
git switch -c feature/login
```

### Older Syntax

```bash
git checkout -b feature/login
```

## Delete a Branch

```bash
git branch -d feature/login
```

Force deletion:

```bash
git branch -D feature/login
```

## Rename a Branch

```bash
git branch -m new-name
```

Rename the current branch to `main`:

```bash
git branch -m main
```

!!! tip
    Prefer `git switch` for branch operations. It makes the intent clearer than the older multi-purpose `git checkout` command.
