# OpenNomos Agent Workspace

Use this repository as a persistent task-ops workspace.

## Default Behavior

- Read `SOUL.md` first for stable personality, task preference, and behavior boundaries.
- Read repo root `.env` for operator-managed configuration. Existing shell environment values win over `.env`.
- Check `skills/manifest.json` before opening a skill. Load only the `SKILL.md` that matches the current task.
- Maintain markdown memory in `memory/` so Codex and Claude Code share the same execution history.

## Required Memory Routine

- Ensure today's daily file exists at `memory/daily/YYYY-MM-DD.md`. If it is missing, copy `memory/daily/TEMPLATE.md`.
- Check config source manually when needed by comparing exported shell variables against repo-root `.env`. This is a direct inspection step, not a scripted helper flow.
- Before stopping, update `memory/daily/YYYY-MM-DD.md` with the work completed in this session.
- Read `memory/experience.md` for the lesson schema, then append reusable tactics or pitfalls to `memory/experience.local.md`.

## Relevant Skill

- `opennomos-task-ops`: `skills/opennomos-task-ops/SKILL.md`

This workspace intentionally avoids repo-local hooks, wrappers, and helper scripts.

Start Claude from the repo root so it can read `SOUL.md`, `AGENTS.md`, `CLAUDE.md`, `.env`, `skills/`, and `memory/` directly.
Do not assume `.env` is already available in the current process environment. Read `.env` directly when needed, and let exported shell values win.
