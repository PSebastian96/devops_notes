# Staging & Commits

Git separates editing files from recording changes.

## Check Status

```bash
git status
```

## Stage a File

```bash
git add file.txt
```

## Stage Multiple Files

```bash
git add file1.txt file2.txt
```

## Stage Everything

```bash
git add .
```

Or:

```bash
git add -A
```

!!! tip "Review before committing"
    Use `git status` after staging to confirm that the intended files are in the staging area.

```bash
git status
```

Git can report:

- Modified files
- Untracked files
- Staged changes
- The current branch
- Whether your branch is ahead of or behind its remote branch

## Commit

```bash
git commit -m "Add configuration"
```

Good commit messages briefly describe **what the commit changes**.

Examples:

```bash
git commit -m "Fix navigation links"
git commit -m "Add installation instructions"
git commit -m "Update API documentation"
```

!!! tip "Keep commits focused"
    Small, focused commits are generally easier to review, understand, and revert.


## Stage and Commit Tracked Files

```bash
git commit -am "Update configuration"
```

!!! warning
    `git commit -am` does not include new, untracked files. New files must be staged with `git add` first.

## Amend the Last Commit

```bash
git commit --amend
```

Change only the commit message:

```bash
git commit --amend -m "Correct message"
```

!!! danger
    Avoid amending commits that have already been shared with other developers unless you understand the consequences of rewriting history.

