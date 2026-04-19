# claude-skill

A small toolkit for [Claude Code](https://docs.claude.com/claude-code):

- **Slash commands** for day-to-day git workflows (`/commit`, `/push`, `/diff`).
- **[`CLAUDE.md`](CLAUDE.md)** — a drop-in set of behavioral guidelines to reduce common LLM coding mistakes (think-before-coding, simplicity, surgical changes, goal-driven execution).

## Commands

| Command | Description |
| --- | --- |
| [`/commit`](.claude/commands/commit.md) | Stage and commit current changes (no push). |
| [`/push`](.claude/commands/push.md) | Stage, commit, and push to the current remote branch. |
| [`/diff`](.claude/commands/diff.md) | Review staged + unstaged changes and report issues by severity. |

Each command accepts an optional free-form hint as `$ARGUMENTS`, e.g.:

```
/commit fix null check in cart trigger
/push
/diff focus on security
```

## `CLAUDE.md`

A project-level instruction file Claude Code auto-loads from the repo root. It covers:

1. **Think Before Coding** — surface assumptions, ask when unclear, don't pick silently between interpretations.
2. **Simplicity First** — minimum code that solves the problem; no speculative abstractions or error handling for impossible cases.
3. **Surgical Changes** — touch only what the task requires; don't "improve" adjacent code; match existing style.
4. **Goal-Driven Execution** — define verifiable success criteria, then loop until green.

Use it as-is, or append project-specific rules below the provided sections. See [CLAUDE.md](CLAUDE.md) for the full text.

## Install

### Per user (available in every project)

```bash
git clone git@github.com:phamdung2209/claude-skill.git ~/.claude-skill

# slash commands
mkdir -p ~/.claude/commands
cp ~/.claude-skill/.claude/commands/*.md ~/.claude/commands/

# user-level behavioral guidelines (optional)
cp ~/.claude-skill/CLAUDE.md ~/.claude/CLAUDE.md
```

### Per project

```bash
# from the project root
mkdir -p .claude/commands

# slash commands
curl -fsSL https://raw.githubusercontent.com/phamdung2209/claude-skill/master/.claude/commands/commit.md -o .claude/commands/commit.md
curl -fsSL https://raw.githubusercontent.com/phamdung2209/claude-skill/master/.claude/commands/push.md   -o .claude/commands/push.md
curl -fsSL https://raw.githubusercontent.com/phamdung2209/claude-skill/master/.claude/commands/diff.md   -o .claude/commands/diff.md

# behavioral guidelines (optional — merge with your existing CLAUDE.md if any)
curl -fsSL https://raw.githubusercontent.com/phamdung2209/claude-skill/master/CLAUDE.md -o CLAUDE.md
```

Restart Claude Code (or run `/help`) and the commands appear in the slash-command list.

## Conventions enforced by these commands

- Commit messages follow the repo's **existing style** (inferred from `git log`) — no hard-coded Conventional Commits prefix.
- **No** `Co-Authored-By` / attribution trailers.
- **No** `git add -A` / `git add .` — files are staged explicitly to avoid leaking `.env`, keys, or credentials.
- **No** `--no-verify` — pre-commit hook failures are fixed at the root, then a new commit is created (never `--amend`).
- `/push` refuses to push to `master` / `main` without an explicit confirmation and never force-pushes unless asked.

## Layout

```
.claude/
  commands/
    commit.md
    diff.md
    push.md
  settings.local.json
CLAUDE.md
LICENSE
README.md
```

## License

[MIT](LICENSE) © PHAM VAN DUNG
