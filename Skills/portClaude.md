# Migrating Claude Code Sessions Between Machines

A complete guide to transferring Claude Code session context from a remote server (source) to a local machine (target) so you can resume conversations locally.

## Prerequisites

- SSH access to the source machine
- Claude Code installed on both machines
- `rsync` available on both machines

## Overview

Claude Code stores all state under `~/.claude/`. Migrating sessions requires:

1. Copying the right files
2. Making the code repository available locally
3. Fixing path mismatches between source and target

---

## Step 1: Copy Claude Code Data (Append, Not Overwrite)

Use `--ignore-existing` so local sessions are not replaced by remote ones.

```bash
REMOTE="user@remote-ip"

# Session index files
rsync -av --ignore-existing $REMOTE:~/.claude/sessions/ ~/.claude/sessions/

# Project data (includes sessions + memory)
rsync -av --ignore-existing $REMOTE:~/.claude/projects/ ~/.claude/projects/

# History — append, not overwrite
ssh $REMOTE 'cat ~/.claude/history.jsonl' >> ~/.claude/history.jsonl

# Optional: file history, tasks
rsync -av --ignore-existing $REMOTE:~/.claude/file-history/ ~/.claude/file-history/
rsync -av --ignore-existing $REMOTE:~/.claude/tasks/ ~/.claude/tasks/
```

**Skip these (ephemeral/regenerated):** `cache/`, `paste-cache/`, `shell-snapshots/`, `session-env/`, `downloads/`, `backups/`

**Skip `settings.json`** if your local config is already how you want it.

### Blocker: `rsync` fails with "No such file or directory"

Some directories (e.g., `plans/`) may not exist on the remote. This is safe to ignore — it just means the remote never used that feature.

### Blocker: SSH permission denied

If `rsync` fails with "Permission denied", ensure your SSH keys are set up or enter the password interactively. Run the command directly in your terminal (not through Claude Code) so you can enter credentials:

```bash
! rsync -av --ignore-existing user@remote-ip:~/.claude/sessions/ ~/.claude/sessions/
```

---

## Step 2: Make the Code Repository Available Locally

The remote working directory (e.g., `/remote/path/to/my_project`) won't exist on your local machine. Copy the code:

```bash
mkdir -p ~/workspace/projects
rsync -avL user@remote-ip:/remote/path/to/my_project ~/workspace/projects/
```

Use `-L` to follow symlinks on the remote in case the source directory is a symlink.

---

## Step 3: Fix the Project Directory Name

This is the most critical step. Claude Code derives a project directory name from your working directory path by replacing **all non-alphanumeric characters (including underscores and slashes) with hyphens**.

### How the mapping works

| Working directory | Project directory name |
|---|---|
| `/remote/path/to/my_project` | `-remote-path-to-my-project` |
| `/home/user/workspace/projects/my_project` | `-home-user-workspace-projects-my-project` |

**Note:** Underscores in folder names like `my_project` become hyphens: `my-project`.

### Blocker: Symlinks to match the old path don't work

You might try creating a symlink so the old path exists locally:

```bash
sudo mkdir -p /remote/path/to
sudo ln -s ~/workspace/projects/my_project /remote/path/to/my_project
```

**This does not work.** Claude Code resolves symlinks to the physical path (`pwd -P`), so it still derives the project name from the real path, not the symlink path.

### Blocker: Symlinks in `~/.claude/projects/` don't work either

```bash
ln -s ~/.claude/projects/-remote-path-to-my-project ~/.claude/projects/-home-user-workspace-projects-my-project
```

Claude Code does not follow symlinks in the projects directory.

### Solution: Copy and rename the project directory

Copy the remote project directory to match the local path's derived name:

```bash
cp -r ~/.claude/projects/-remote-path-to-my-project \
      ~/.claude/projects/-home-user-workspace-projects-my-project
```

**Do this for every subdirectory that had its own sessions:**

```bash
# If you also had sessions in /remote/path/to/my_project/subdir
cp -r ~/.claude/projects/-remote-path-to-my-project-subdir \
      ~/.claude/projects/-home-user-workspace-projects-my-project-subdir
```

### How to find the correct local project name

Start a throwaway session from the target directory to see what Claude Code creates:

```bash
cd ~/workspace/projects/my_project
echo "test" | claude -p "say hello"
ls -lt ~/.claude/projects/ | head -3
```

The newest directory is the one Claude Code expects.

---

## Step 4: Fix the `cwd` Field Inside Session Files

Each `.jsonl` session file contains `"cwd":"/remote/path/to/my_project"` embedded in every message. Claude Code may use this to validate sessions. Update it to match your local path:

```bash
sed -i 's|/remote/path/to/my_project|/home/user/workspace/projects/my_project|g' \
    ~/.claude/projects/-home-user-workspace-projects-my-project/*.jsonl
```

For subdirectories, be specific with the order (longer paths first to avoid partial matches):

```bash
# Fix subdirectory paths FIRST (longer match)
sed -i 's|/remote/path/to/my_project/subdir|/home/user/workspace/projects/my_project/subdir|g' \
    ~/.claude/projects/-home-user-workspace-projects-my-project-subdir/*.jsonl

# Then fix the parent path
sed -i 's|/remote/path/to/my_project|/home/user/workspace/projects/my_project|g' \
    ~/.claude/projects/-home-user-workspace-projects-my-project/*.jsonl
```

---

## Step 5: Resume the Session

```bash
cd ~/workspace/projects/my_project
claude --resume <session-id>
```

To list available sessions interactively:

```bash
claude -r
```

---

## Quick Reference: Complete Migration Script

Replace the variables at the top and run:

```bash
#!/bin/bash
REMOTE="user@remote-ip"
REMOTE_BASE="/remote/path/to/my_project"
LOCAL_BASE="/home/user/workspace/projects/my_project"

# Derive project directory names (replace non-alphanumeric with hyphens)
REMOTE_PROJECT=$(echo "$REMOTE_BASE" | sed 's|[^a-zA-Z0-9]|-|g')
LOCAL_PROJECT=$(echo "$LOCAL_BASE" | sed 's|[^a-zA-Z0-9]|-|g')

# 1. Copy Claude Code data
rsync -av --ignore-existing $REMOTE:~/.claude/sessions/ ~/.claude/sessions/
rsync -av --ignore-existing $REMOTE:~/.claude/projects/ ~/.claude/projects/
ssh $REMOTE 'cat ~/.claude/history.jsonl' >> ~/.claude/history.jsonl

# 2. Copy the code repository
mkdir -p "$(dirname $LOCAL_BASE)"
rsync -avL $REMOTE:$REMOTE_BASE $LOCAL_BASE/

# 3. Copy project directory with correct name
cp -r ~/.claude/projects/$REMOTE_PROJECT ~/.claude/projects/$LOCAL_PROJECT

# 4. Fix cwd in session files
sed -i "s|$REMOTE_BASE|$LOCAL_BASE|g" ~/.claude/projects/$LOCAL_PROJECT/*.jsonl

echo "Done. Try: cd $LOCAL_BASE && claude -r"
```

---

## Summary of Blockers

| Blocker | Root Cause | Solution |
|---|---|---|
| `rsync` "No such file or directory" | Directory doesn't exist on remote | Safe to ignore |
| SSH permission denied | No SSH key / wrong password | Run interactively with `!` prefix |
| "No conversation found" after copying | Project directory name mismatch | Copy to correctly named directory |
| Filesystem symlinks don't help | Claude Code resolves to physical path (`pwd -P`) | Must copy, not symlink |
| Project directory symlinks don't help | Claude Code doesn't follow symlinks in `~/.claude/projects/` | Must copy, not symlink |
| Underscores in folder names | Claude Code converts `_` to `-` in project dir names | Use throwaway session to discover correct name |
| Session found but won't load | `cwd` field inside `.jsonl` still points to remote path | `sed` replace the paths inside `.jsonl` files |
| `history.jsonl` overwritten | Used `>` instead of `>>` or rsync | Always append with `>>` |

---

## Important Notes

- **No live sync.** This is a one-time copy. Changes on either machine stay local.
- **Version differences.** Sessions created with older Claude Code versions (e.g., 2.1.81) can be resumed on newer versions (e.g., 2.1.132).
- **Session index files** in `~/.claude/sessions/` are keyed by PID and are informational — the actual session data lives in `~/.claude/projects/<project>/<session-id>.jsonl`.
