# Diffs

`git diff` shows differences between versions of your files.

## Working Directory vs Staging

```bash
git diff
```

Shows unstaged changes.

## Staging vs Last Commit

```bash
git diff --staged
```

Also:

```bash
git diff --cached
```

## Compare Commits

```bash
git diff <commit1> <commit2>
```

## Compare Branches

```bash
git diff main feature/login
```

## Diff a Specific File

```bash
git diff -- path/to/file
```

## Statistics

```bash
git diff --stat
```

!!! note

    `git diff` by itself does **not** show changes that have already been staged. Use `git diff --staged` to inspect the staging area.

!!! tip "Useful review step"
    Reviewing `git diff --staged` before committing lets you verify exactly what will be included in the commit.