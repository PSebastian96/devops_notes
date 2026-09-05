# Merging

Merging combines changes from one branch into another.

## Basic Merge

First switch to the branch that should receive the changes:

```bash
git switch main
```

Then merge:

```bash
git merge feature/login
```

## Fast-Forward Merge

If the target branch has not diverged:

```text
main
  \
   feature
```

Git can simply move the branch pointer forward.

## Merge Commit

When branches have diverged, Git may create a merge commit:

```text
A---B---C---M  main
     \     /
      D---E
```

## Abort a Merge

If conflicts occur and you want to cancel the merge:

```bash
git merge --abort
```

## Resolve Conflicts

Git attempts to combine the histories automatically. If the same part of a file has conflicting changes, Git may require you to resolve a **merge conflict** manually.

!!! warning "Merge conflicts"
    A merge conflict is not an error in Git. It means Git needs you to decide which changes should be kept before the merge can be completed.

Check conflicted files:

```bash
git status
```

After resolving a file:

```bash
git add <file>
```

Complete the merge:

```bash
git commit
```
