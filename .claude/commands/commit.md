---
description: Stage and commit current changes (no push)
argument-hint: "[optional: commit message hint]"
allowed-tools: Bash(git status:*), Bash(git diff:*), Bash(git log:*), Bash(git add:*), Bash(git commit:*), Bash(git branch:*)
---

Create a git commit for the current changes. Do **not** push.

**User-provided commit message hint (if any):** $ARGUMENTS

## Steps

Run these commands in parallel to gather context:
- `git status` — show changed / untracked files
- `git diff` — show unstaged diff
- `git diff --cached` — show staged diff
- `git log --oneline -5` — inspect recent commit style of the repo

## Analyze and act

1. **No changes at all** (working tree clean): tell the user and stop.

2. **There are changes:**
   - Analyze the diff to classify the change (feat / fix / refactor / chore / docs / style / test)
   - Draft a commit message matching the **repo's existing style** (inferred from `git log`) — concise, focused on the "why"
   - If the user provided a hint in `$ARGUMENTS`, follow that direction
   - **DO NOT** stage sensitive files: `.env`, `credentials.json`, `*.pem`, `*.key`, or anything containing secrets
   - Stage specific files via `git add <specific files>` (avoid `git add -A` / `git add .`)
   - Create the commit using a HEREDOC to keep formatting correct. Do **not** add any Co-Authored-By / attribution trailer.
   - Run `git status` after to confirm the commit landed.

3. **If a pre-commit hook fails:**
   - **Do not** bypass with `--no-verify`
   - Read the error, fix the root cause, re-stage, and create a NEW commit (do not amend)

## Final output

Brief report:
- Commit SHA (short)
- Commit subject line
- Files included
