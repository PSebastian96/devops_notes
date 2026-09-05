# Stash

`git stash` temporarily stores uncommitted changes so you can work on a clean working directory.

## Stash Changes

```bash
git stash
```

Include untracked files:

```bash
git stash -u
```

## List Stashes

```bash
git stash list
```

## Apply a Stash

```bash
git stash apply
```

Apply a specific stash:

```bash
git stash apply stash@{1}
```

## Apply and Remove

```bash
git stash pop
```

## Delete a Stash

```bash
git stash drop stash@{0}
```

## Delete All Stashes

```bash
git stash clear
```

## Named Stash

```bash
git stash push -m "Work in progress"
```

!!! tip
    `git stash pop` applies the stash and removes it from the stash list. `git stash apply` leaves the stash available.

