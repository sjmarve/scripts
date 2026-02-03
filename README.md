# Professional Laravel Automation Suite

A high-performance shell automation suite designed for Laravel developers using Git worktrees and Laravel Valet. These scripts eliminate manual configuration when switching between features.

---

## 📋 Prerequisites
- **Shell**: Zsh (preferred) or Bash.
- **Tools**: `git`, `composer`, `npm`/`yarn`, `valet`.
- **Environment**: macOS with Laravel Valet installed.

## ⚙️ Installation
Add these to your `~/.zshrc` to ensure directory changes persist in your session:
```bash
alias scaffold='source ~/.scripts/scaffold'
alias teardown='source ~/.scripts/teardown'
```

---

## 🚀 Scaffold (`~/.scripts/scaffold`)
Automates the transition into a new feature branch or the preparation of an existing directory.

### Commands & Options
- `scaffold`: Sets up the current directory.
- `scaffold -b <branch>`: Creates a new worktree for `<branch>`, `cd`s into it, and starts setup.
- `scaffold -s <subdomain>`: Forces a specific Valet link (e.g., `brand-feature.test`).
- `scaffold -v`: Verbose mode (shows full command output).

### Technical Logic & "Under the Hood":
1.  **Worktree Management**: If `-b` is used, the script checks if the branch exists. If it does, it runs `git worktree add`. If not, it runs `git worktree add -b`. It then automatically re-evaluates the environment for the new path.
2.  **Environment Syncing**: 
    - Searches for a `.env` in the main git common directory (parent).
    - If found, it copies it local to preserve your global configuration.
    - If missing, it falls back to `.env.example`.
3.  **Bootstrapping**: 
    - Automatically creates missing `storage/framework/{cache,sessions,views}` directories.
    - Generates app keys if a new `.env` was created.
4.  **Performance Optimization**: 
    - Temporarily disables **Xdebug** for Composer/Yarn installs to maximize speed.
    - Suppresses PHP deprecation warnings globally during the script to keep output clean.
5.  **Smart Valet Hijacking**: 
    - Scans `~/.config/valet/Sites` for any symlinks pointing to the *parent project directory*.
    - Unlinks them and re-links them to your *current worktree*.
    - Secures (SSL) all links automatically.
    - Skips sites already pointing to the current folder to save execution time.

---

## 🌪️ Teardown (`~/.scripts/teardown`)
Safely destroys a worktree and returns your environment to a stable "fallback" state.

### Technical Safeguards:
1.  **Strict Termination**: Uses a `smart_exit` function that detects if the script is sourced. It terminates execution immediately if a critical error occurs, preventing accidental commands from running.
2.  **Protected Branches**: Hardcoded block for `master` and `develop`. It is physically impossible to teardown these branches via this script.
3.  **Uncommitted Changes**:
    - **[m]ove**: Stashes changes with a descriptive label ("Teardown auto-stash from [branch]") so they can be popped on the fallback branch.
    - **[t]rash**: Continues without stashing (changes will be LOST).
    - **[a]bort**: Stops everything.
4.  **Intelligent Restoration**: 
    - The script identifies which Valet sites are currently pointing to the worktree being deleted.
    - It selects a fallback branch based on priority: `develop` → `master` → `main`.
    - It unlinks the sites from the "dying" worktree and automatically links them to the fallback branch before deleting the folder.
5.  **Forceful Cleanup**: Runs `git worktree remove --force` to ensure stubborn processes or locks don't block the deletion.

---

## � User Experience Features
- **Braille Spinner**: A non-blocking animation (`⠋⠙⠹...`) runs for all long-running background tasks (Composer, Yarn, Valet).
- **Silent Job Control**: Uses `setopt no_monitor` (Zsh) and `&!` (disown) to hide system messages like `[1] 12345` and `Done...`.
- **Status Replacement**: The spinner animation is automatically replaced by a checkmark or `Done.` message on the same line to prevent terminal clutter.
- **Sourcing Aware**: Checks `ZSH_EVAL_CONTEXT` to determine whether to `exit` (script run) or `return` (sourced into terminal).
