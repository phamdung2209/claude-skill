---
description: Stage, commit, and push code to the current remote branch
argument-hint: "[optional: commit message hint]"
allowed-tools: Bash(git status:*), Bash(git diff:*), Bash(git log:*), Bash(git add:*), Bash(git commit:*), Bash(git push:*), Bash(git branch:*), Bash(git rev-parse:*)
---

Run the push flow for the current repository.

**User-provided commit message hint (if any):** $ARGUMENTS

## Steps

Run these commands in parallel to gather context:
- `git status` — show changed / untracked files
- `git diff` — show unstaged diff
- `git diff --cached` — show staged diff
- `git log --oneline -5` — inspect recent commit style of the repo
- `git branch --show-current` — identify the current branch

## Analyze and act

1. **No changes at all** (working tree clean & no unpushed commits): tell the user and stop.

2. **There are uncommitted changes:**
   - Analyze the diff to classify the change (feat / fix / refactor / chore / docs / style / test)
   - Draft a commit message matching the **repo's existing style** (inferred from `git log`) — concise, focused on the "why"
   - If the user provided a hint in `$ARGUMENTS`, follow that direction
   - **DO NOT** stage sensitive files: `.env`, `credentials.json`, `*.pem`, `*.key`, or anything containing secrets
   - Stage specific files via `git add <specific files>` (avoid `git add -A` / `git add .`)
   - Create the commit using a HEREDOC to keep formatting correct. Do **not** add any Co-Authored-By / attribution trailer.

3. **Push to remote:**
   - If the branch has no upstream: `git push -u origin <current-branch>`
   - If it already has an upstream: `git push`
   - **NEVER** use `--force` / `--force-with-lease` unless the user explicitly asks
   - **NEVER** push directly to `master` / `main` — warn the user if on that branch

4. **If a pre-commit / pre-push hook fails:**
   - **Do not** bypass with `--no-verify`
   - Read the error, fix the root cause, re-stage, and create a NEW commit (do not amend)

## Final output

Brief report:
- Branch pushed
- Number of commits pushed
- PR URL (if it can be inferred from the remote)
