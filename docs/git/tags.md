# Tags

Tags are references to specific commits and are commonly used to identify releases.

## List Tags

```bash
git tag
```

## Create a Tag

```bash
git tag v1.0.0
```

## Annotated Tag

```bash
git tag -a v1.0.0 -m "Release v1.0.0"
```

## Show a Tag

```bash
git show v1.0.0
```

## Push a Tag

```bash
git push origin v1.0.0
```

Push all tags:

```bash
git push origin --tags
```

## Delete a Tag

Local:

```bash
git tag -d v1.0.0
```

Remote:

```bash
git push origin --delete v1.0.0
```

!!! tip
    Annotated tags are generally preferred for releases because they contain metadata such as the tagger, date, and message.
