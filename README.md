# Automation Scripts

A collection of utility scripts to streamline Laravel development with Git worktrees and Laravel Valet.

## Installation

To use these scripts, add the following aliases to your `~/.zshrc` (or equivalent):

```bash
alias scaffold='source ~/.scripts/scaffold'
alias teardown='source ~/.scripts/teardown'
```

Then reload your shell:
```bash
source ~/.zshrc
```

---

## 🚀 Scaffold

The `scaffold` script automates the setup of a new or existing Laravel worktree.

### Usage
```bash
scaffold [-b branch_name] [-s subdomain] [-v]
```

### Options
- `-b branch_name`: **(Automated Worktree)** Creates a new Git worktree for the specified branch, switches to it, and starts the scaffolding.
- `-s subdomain`: **(Custom Subdomain)** Specifies a custom subdomain for Valet linking.
- `-v`: **(Verbose)** Shows all output from Composer, Yarn, and other commands. Standard mode uses a clean spinner.

### Features
- **Smart Worktree Creation**: Automatically detects if a branch exists locally and handles worktree creation accordingly.
- **Dependency Management**: Detects and runs `composer install` and `yarn install` / `npm install`.
- **Valet Hijacking**: Automatically identifies all Valet sites pointing to the parent project and "hijacks" them to point to your *current* worktree.
- **Environment Setup**: Automatically finds and copies the best `.env` file (from the main repo or `.env.example`).
- **Storage Prep**: Ensures all necessary `storage` and `bootstrap/cache` folders are present with correct permissions.

---

## 🌪️ Teardown

The `teardown` script safely cleans up a worktree and restores your environment.

### Usage
```bash
teardown
```

### Features
- **Safety Guards**: Blocks execution on `master` or `develop` branches.
- **Uncommitted Changes Detection**: Prompts you to [m]ove changes to a fallback branch (via stash), [t]rash them, or [a]bort.
- **Valet Restoration**: Automatically unlinks sites from the worktree being deleted and re-links them to your fallback worktree (e.g., `develop`).
- **Clean Cleanup**: Force-removes the Git worktree and switches your terminal back to the parent/fallback directory.
- **Confirmation Prompt**: Requires explicit `[y/n]` confirmation before executing destructive actions.

---

## 🛠 Features in Common
- **Braille Spinners**: High-quality visual feedback for background tasks.
- **Silent Mode**: Suppresses Job IDs and "Done" messages from Zsh for a premium look.
- **Sourcing Aware**: Designed to be sourced, allowing `cd` commands to affect your current shell session.
