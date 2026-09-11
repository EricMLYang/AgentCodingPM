---
name: claude-sync
description: Keep CLAUDE.md as a thin Claude Code bridge to the repo's AGENTS.md and .agents assets. Use after AGENTS.md changes, agent-asset migrations, or a request to verify Claude synchronization.
---

# Claude bridge sync

Keep `AGENTS.md` as the single source of truth. `CLAUDE.md` is an entry point, not a second copy of the instructions.

## Scope

- Default to the current repo.
- When the user asks to synchronize all repos, include the current repo and the child repos listed by its maintained project registry or index.
- Derive each bridge from that repo's own `AGENTS.md`; never copy role content across repos.

## Workflow

For each repo in scope:

1. Confirm `AGENTS.md` exists.
2. Ensure `CLAUDE.md` identifies the repo, states that `AGENTS.md` is authoritative, points reusable assets to `.agents/`, and includes `@AGENTS.md`.
3. Check active instructions, indexes, and Claude settings for stale `.github/` paths or references to `copilot-instructions.md`.
4. Preserve repo-specific `.claude/` settings unless a path changed because of the migration.

Report repos updated, already synchronized, or missing `AGENTS.md`. Do not duplicate the full contents of `AGENTS.md` in `CLAUDE.md`.
