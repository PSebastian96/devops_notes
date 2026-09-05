# Using Git in VSCode

## 1. Git with VSC

### 1.1 Loading a Project
1. Open VSCode.
2. Go to `File -> Open Folder` and select your project folder.
3. VSCode will automatically detect if it's a Git repository.

### 1.2 Git Panel
- Click the **Source Control** icon on the left sidebar (or `Ctrl+Shift+G`).
- You can see modified files, staged files, and commit history.

### 1.3 Basic Operations in VSCode
- **Stage Changes:** Click `+` next to files or `Stage All Changes`.
- **Commit Changes:** Enter a commit message and click the checkmark `✔`.
- **Push/Pull:**
  - Click the `...` menu in the Source Control panel.
  - Select `Push` to upload commits to remote.
  - Select `Pull` to fetch changes from remote.
- **Switch Branches:** Click the branch name in the bottom left corner and select or create a branch.
- **View History:** Right-click a file → `Open Timeline` or use extensions like `GitLens` for enhanced tracking.
- **Prevent Secrets from Being Committed:**`.gitignore` → Add files that wll be ignored by git (env variables, API keys).

### 1.4 Cloning a Repository in VSCode
1. Press `Ctrl+Shift+P` (or `Cmd+Shift+P` on macOS) to open the command palette.
2. Type `Git: Clone` and select it.
3. Enter the repository URL.
4. Select a local folder for the cloned project.
5. Open the folder once cloning is complete.

### 1.5 Extensions for Git
- **GitLens** – advanced Git insights and history tracking.
- **Git Graph** – visual representation of branches and commits.