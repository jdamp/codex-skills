---
name: git-worktree-manager
description: Create and manage Git worktrees for parallel feature development, then open the new worktree in VS Code or start Codex CLI there for a second session. Use when users ask to work on another feature simultaneously, spin up a parallel workspace, or open a separate coding session even if they do not explicitly mention worktrees.
---

# Git Worktree Manager

Use this skill to run multiple active branches without cloning the repository multiple times.

## Workflow

1. Confirm repository context:
   - Run `git rev-parse --show-toplevel` and `git status -sb`.
   - If refs are stale, run `git fetch --all --prune`.
2. Create or attach worktrees:
   - Use `scripts/worktreectl create <branch>` to create a new local branch from `main`.
   - Use `scripts/worktreectl create <branch> --remote` when the branch already exists on `origin`.
   - Use `scripts/worktreectl create <branch> --base <base-branch>` for non-`main` start points.
   - Use `scripts/worktreectl create <branch> --open` to immediately open the new worktree in VS Code.
3. Inspect active worktrees:
   - Run `scripts/worktreectl list`.
4. Open an existing worktree for a parallel session:
   - Run `scripts/worktreectl open <path-or-branch>`.
   - Choose editor explicitly with `--editor code`.
   - Use `--print-open-cmd` when you need a dry-run command preview.
5. Start second Codex session:
   - In the worktree terminal, run Codex CLI using the user's normal launch flow.
   - Treat each worktree as an isolated task context.
6. Remove and clean up:
   - Use `scripts/worktreectl remove <path-or-branch>` after confirming status is clean.
   - Use `scripts/worktreectl prune` to remove stale metadata.
7. Recover metadata problems:
   - Use `scripts/worktreectl repair`.

## Safety Rules

- Refuse destructive cleanup if uncommitted or unpushed work may be lost.
- Never remove the primary worktree (repository root).
- Keep one branch checked out in only one worktree at a time.
- Prefer explicit paths when branch names are ambiguous.
- Ask before running GUI launch commands (`code`) if user approval is required.
- Show the exact command before execution when the user asks for confirmation.

## Script Reference

`scripts/worktreectl` provides consistent wrappers for the most common operations:

- `create <branch> [--base <base>] [--path <path>] [--remote] [--open] [--editor <auto|code>] [--print-open-cmd]`
- `list`
- `open <path-or-branch> [--editor <auto|code>] [--print-open-cmd]`
- `remove <path-or-branch> [--force]`
- `prune`
- `repair`

If the script is unavailable, fall back to direct `git worktree` commands.

## Examples

- "Set up a new worktree for `feature/billing-redesign`."
- "Spin up another workspace for a second feature and open it in VS Code."
- "Create a worktree for an existing remote branch `fix/login-loop`."
- "Open the `feature/billing-redesign` worktree in VS Code and start a second Codex CLI session."
- "List all worktrees and tell me which branch each one tracks."
- "Remove a completed worktree and clean stale entries."
- "Repair worktree metadata after I moved directories."

## Additional Reference

Read `references/worktree-playbook.md` for branch naming conventions, lifecycle patterns, and troubleshooting.
