# Undoing Changes

Git provides several ways to undo changes. The correct command depends on where the change currently exists and whether the commit has already been shared with others.

## Finding HEAD

`HEAD` refers to the commit currently checked out.

To display the current commit hash:

```bash
git rev-parse HEAD
```

Example:

```text
a1b2c3d4e5f6789012345678901234567890abcd
```

To display the shortened commit hash:

```bash
git rev-parse --short HEAD
```

Example:

```text
a1b2c3d
```

You can also see `HEAD` as part of the commit history:

```bash
git log --oneline --decorate -5
```

Example:

```text
a1b2c3d (HEAD -> main) Add documentation
7f8e9a0 Add Git commands
3c4d5e6 Initial commit
```

Here, `HEAD -> main` means that `HEAD` currently points to the latest commit on the `main` branch.

## Finding a Specific Commit

To find the commit hash you want to revert or reset to:

```bash
git log --oneline
```

Example:

```text
a1b2c3d Add new feature
7f8e9a0 Update configuration
3c4d5e6 Initial commit
```

The shortened hash can generally be used when referring to a commit:

```bash
git revert 7f8e9a0
```

or:

```bash
git reset 7f8e9a0
```

!!! tip
    You usually do not need to copy the entire 40-character commit hash. A sufficiently unique shortened hash, such as `7f8e9a0`, is normally enough.

## Discard Working Directory Changes

Modern syntax:

```bash
git restore <file>
```

This discards uncommitted changes to the specified file and restores it to the version in the index.

Older syntax:

```bash
git checkout -- <file>
```

!!! warning
    These commands discard the file's current uncommitted changes. Make sure you do not need them before running the command.


## Unstage a File

Remove a file from the staging area while keeping its changes in the working directory:

```bash
git restore --staged <file>
```

The changes are **not deleted**; they are simply removed from the staging area.

## Revert a Commit

`git revert` creates a **new commit** that reverses the changes introduced by an existing commit.

```bash
git revert <commit>
```

For example:

```bash
git revert 7f8e9a0
```

This is generally the safest approach for commits that have already been pushed to a shared remote repository because it does not rewrite existing history.

Example:

```text
A---B---C---D
        ^
        |
     revert C

A---B---C---D---E
                ^
                |
          E reverses C
```

!!! tip
    Use `git revert` when you want to undo a commit while preserving the existing project history.

## Reset to a Specific Commit

`git reset` moves the current branch to a different commit.

For example:

```bash
git reset <commit>
```

To move the current branch back to a specific commit:

```bash
git reset 7f8e9a0
```

The effect depends on the reset mode.

### Soft Reset

Keeps changes staged:

```bash
git reset --soft <commit>
```

Example:

```bash
git reset --soft 7f8e9a0
```

The branch moves to the specified commit, but the changes between the old `HEAD` and the target commit remain staged.

### Mixed Reset

Keeps changes in the working directory but unstages them:

```bash
git reset --mixed <commit>
```

This is the default:

```bash
git reset <commit>
```

Example:

```bash
git reset 7f8e9a0
```

The branch moves to the specified commit and the changes remain in the working directory as unstaged changes.

### Hard Reset

Moves the branch to the specified commit and discards changes from the working directory and staging area:

```bash
git reset --hard <commit>
```

Example:

```bash
git reset --hard 7f8e9a0
```

!!! danger
    `git reset --hard` can permanently discard uncommitted changes. Use it carefully.


## Reset Current Branch to HEAD

You can use `HEAD` as a reference instead of specifying a commit hash.

Unstage all staged changes:

```bash
git reset HEAD
```

Discard all local changes and return the working tree to `HEAD`:

```bash
git reset --hard HEAD
```

!!! warning
    `git reset --hard HEAD` discards all uncommitted changes in tracked files.


## Reset to the Previous Commit

Git supports relative references such as `HEAD~1`.

Move the current branch back one commit:

```bash
git reset --hard HEAD~1
```

Two commits:

```bash
git reset --hard HEAD~2
```

For example:

```text
A---B---C---D  HEAD
        ^
        |
      HEAD~1
```

Here:

* `HEAD` → `D`
* `HEAD~1` → `C`
* `HEAD~2` → `B`
* `HEAD~3` → `A`

!!! danger
    A hard reset changes the branch history. Avoid using it on commits that have already been pushed to a shared repository unless rewriting history is intentional.


## Revert vs Reset

The key distinction is whether you want to **preserve or rewrite history**.

| Goal                           | Command                      | History                 |
| ------------------------------ | ---------------------------- | ----------------------- |
| Undo a shared commit           | `git revert <commit>`        | Preserves history       |
| Move branch back locally       | `git reset <commit>`         | Rewrites branch history |
| Undo and keep changes staged   | `git reset --soft <commit>`  | Rewrites branch history |
| Undo and keep changes unstaged | `git reset --mixed <commit>` | Rewrites branch history |
| Undo and discard changes       | `git reset --hard <commit>`  | Rewrites branch history |

!!! tip "Rule of thumb"

    **Already pushed/shared?** Prefer:

    ```bash
    git revert <commit>
    ```

    **Local and not shared?** `git reset` may be appropriate.

## Quick Reference

| Situation                             | Command                       |
| ------------------------------------- | ----------------------------- |
| Show current HEAD                     | `git rev-parse HEAD`          |
| Show short HEAD                       | `git rev-parse --short HEAD`  |
| Find commits                          | `git log --oneline`           |
| Discard file changes                  | `git restore <file>`          |
| Unstage file                          | `git restore --staged <file>` |
| Undo shared commit                    | `git revert <commit>`         |
| Move branch to a commit               | `git reset <commit>`          |
| Move branch and keep changes staged   | `git reset --soft <commit>`   |
| Move branch and keep changes unstaged | `git reset --mixed <commit>`  |
| Move branch and discard changes       | `git reset --hard <commit>`   |
| Reset to previous commit              | `git reset --hard HEAD~1`     |
| Reset working tree to HEAD            | `git reset --hard HEAD`       |
