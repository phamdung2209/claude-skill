---
description: Review current code changes (staged + unstaged) and report issues
argument-hint: "[optional: scope hint e.g. 'focus on security' or a file path]"
allowed-tools: Bash(git status:*), Bash(git diff:*), Bash(git log:*), Bash(git branch:*), Read, Grep, Glob
---

Review the pending code changes in the working tree and provide a concise code review.

**User focus hint (if any):** $ARGUMENTS

## Steps

Run these in parallel to understand scope:
- `git status` — list changed files
- `git diff` — unstaged changes
- `git diff --cached` — staged changes
- `git branch --show-current` — current branch

If `$ARGUMENTS` names a specific file or path, narrow `git diff` to that path.

For each non-trivial changed file, Read the file to see the change in context (diff alone can mislead).

## What to review

Go through the diff and flag issues in these categories. Skip categories that don't apply.

1. **Correctness** — logic bugs, off-by-one, wrong conditionals, unhandled null/undefined, async/await misuse, stale closures, missing `await`
2. **Regressions** — removed code that seemed load-bearing, changed function signatures used elsewhere (use Grep to verify call sites)
3. **Security** — XSS, injection, secrets in code, unsafe `dangerouslySetInnerHTML`, missing auth/permission checks, exposed env vars
4. **Performance** — unnecessary re-renders (missing `memo`/`useMemo`/`useCallback` where it matters), N+1 queries, large bundle additions, blocking operations in render
5. **React-specific** — incorrect hook deps, mutating state, missing keys, context value identity instability, event handler recreation
6. **Style & consistency** — mismatched naming/conventions vs surrounding code, dead code, leftover `console.log` / debugger, commented-out blocks
7. **Test coverage** — new logic without tests (only if the repo has tests; don't invent them otherwise)

## Output format

Start with a **one-line verdict**: `LGTM` / `Minor nits` / `Needs changes` / `Blocker`.

Then group findings by severity:

### Blockers
(must fix — bugs, security, broken behavior)

### Suggestions
(should consider — quality, clarity, minor correctness)

### Nits
(optional polish)

For each finding, include:
- File and line reference using markdown link: `[path/file.ts:42](path/file.ts#L42)`
- One-sentence issue description
- One-sentence suggested fix (code snippet only if meaningfully short)

End with **what's good**: 1–2 bullets calling out well-done parts (keep it real, skip if nothing stands out).

**Do not** edit any files or run any write commands. Review only.
