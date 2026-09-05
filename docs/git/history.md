# History

## View History

```bash
git log
```

Compact format:

```bash
git log --oneline
```

Graph view:

```bash
git log --oneline --graph --decorate --all
```

## Show a Commit

```bash
git show <commit>
```

## Search Commit Messages

```bash
git log --grep="keyword"
```

## Show a Specific File's History

```bash
git log -- path/to/file
```

## Show Who Changed Each Line

```bash
git blame <file>
```

!!! tip
    A useful general-purpose history command is:

    ```bash
    git log --oneline --graph --decorate --all
    ```

    It provides a compact view of commits, branches, and tags.