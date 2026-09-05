# Common Git Commands

## Command Table

| Command                    | What it does                                                  |
| -------------------------- | ------------------------------------------------------------- |
| `git init`                 | Creates a new Git repository                                  |
| `git clone <url>`          | Copies a remote repository to your computer                   |
| `git status`               | Shows the current state of the working directory              |
| `git add <file>`           | Stages a file for the next commit                             |
| `git add .`                | Stages all changes in the current directory                   |
| `git commit -m "message"`  | Creates a commit containing staged changes                    |
| `git log`                  | Displays the commit history                                   |
| `git diff`                 | Shows changes that have not been staged                       |
| `git branch`               | Lists or manages branches                                     |
| `git switch <branch>`      | Switches to another branch                                    |
| `git switch -c <branch>`   | Creates and switches to a new branch                          |
| `git checkout <branch>`    | **Older syntax:** switches to another branch                  |
| `git checkout -b <branch>` | **Older syntax:** creates and switches to a new branch        |
| `git merge <branch>`       | Merges another branch into the current branch                 |
| `git remote -v`            | Displays configured remote repositories                       |
| `git fetch`                | Downloads changes from a remote without merging them          |
| `git pull`                 | Downloads and integrates changes from a remote repository     |
| `git push`                 | Uploads local commits to a remote repository                  |
| `git restore <file>`       | Restores a file to a previous state                           |
| `git checkout -- <file>`   | **Older syntax:** restores a file to its last committed state |
| `git rm <file>`            | Removes a file and stages its deletion                        |
| `git mv <old> <new>`       | Moves or renames a file and stages the change                 |
| `git reset`                | Moves the current branch or changes staging state             |
| `git tag <name>`           | Creates a tag for a specific commit                           |

!!! note "Command syntax"
       Values written inside `<angle-brackets>` are placeholders. Replace them with the value appropriate for your repository.

!!! tip "Modern vs. older Git syntax"

       `git switch` and `git restore` were introduced to separate two different jobs that were historically handled by `git checkout`.

       **Modern:**

       - `git switch <branch>` → switch branches
       - `git switch -c <branch>` → create and switch branches
       - `git restore <file>` → restore files

       **Older:**

       - `git checkout <branch>` → switch branches
       - `git checkout -b <branch>` → create and switch branches
       - `git checkout -- <file>` → restore files

       You will still encounter `git checkout` frequently in older documentation, scripts, and tutorials.

---

## Simple Workflow

A basic workflow for making and committing changes to a Git repository looks like this:

### 1. Create a repository

```git
git init
```

### 2. Check the repository status

```git
git status
```

### 3. Make changes to your files

### 4. Review your changes
```git
git diff
```

### 5. Stage the changes
```git
git add .
```

### 6. Check what will be committed
```git
git status
```

### 7. Create a commit
```git
git commit -m "Add initial files"
```

### 8. View the commit history
```git
git log
```

### 9. Push changes to repository
```git
git push
```


!!! tip "The basic cycle is:"
    Edit → Diff → Add → Commit → Push

---