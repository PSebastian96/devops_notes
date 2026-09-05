# Configuration

Git configuration is stored at different levels:

| Level  | Scope              | Command               |
| ------ | ------------------ | --------------------- |
| System | All users          | `git config --system` |
| Global | Current user       | `git config --global` |
| Local  | Current repository | `git config --local`  |

## User Identity

Configure the name associated with commits:

```bash
git config --global user.name "Your Name"
```

Configure the email:

```bash
git config --global user.email "you@example.com"
```

Check the configuration:

```bash
git config --global --list
```

## Default Branch

Set the default initial branch name:

```bash
git config --global init.defaultBranch main
```

## Editor

Configure the default Git editor:

```bash
git config --global core.editor vim
```

## Useful Configuration

```bash
git config --global init.defaultBranch main
git config --global core.autocrlf input
git config --global pull.rebase false
```

!!! tip
    Repository-specific configuration overrides global configuration.
