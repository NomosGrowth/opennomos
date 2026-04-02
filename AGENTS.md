# OpenNomos Agent Workspace

This repository is meant to run as a shared agent workspace for Codex and Claude Code.

## Layering

- `SOUL.md`: stable personality for the OpenNomos task agent, including task preferences, behavior boundaries, and relationship model
- `AGENTS.md`: repo rules, startup routine, memory policy, and skill-loading rules
- `memory/experience.md`: tracked template and usage guidance for durable lessons
- `memory/experience.local.md`: ignored local lessons and observed preferences that may later inform `SOUL.md`

## Startup Routine

1. Read `SOUL.md` first so persona, task preference, and behavior boundaries are loaded before repo procedure.
2. Treat the repo root `.env` as the operator-managed configuration source.
3. Before substantial work, ensure today's daily log exists at `memory/daily/YYYY-MM-DD.md`.
   If it is missing, create it by copying `memory/daily/TEMPLATE.md`.
4. Review `skills/manifest.json`. If the task matches a listed skill, open only the relevant `SKILL.md` and follow it.
5. Keep durable state in markdown files under `memory/`, not in transient chat context.

Shell environment values override `.env`. Never write secret values into `memory/` or other markdown files.
Checking environment variables is a direct inspection step, not a scripted workflow.

## Skill Loading

- Skill index: `skills/manifest.json`
- Current OpenNomos task skill: `skills/opennomos-task-ops/SKILL.md`

When a skill applies:
- Read only the part of the skill needed for the current request.
- Reuse templates or references linked from the skill instead of recreating them.
- Prefer live API data over memory when checking current OpenNomos platform state.

## Memory Policy

Shared memory lives in repo markdown files so both Codex and Claude Code can use the same context:

- Daily execution log: `memory/daily/YYYY-MM-DD.md` (ignored runtime file; template stays in repo)
- Reusable lessons guide: `memory/experience.md` (tracked)
- Reusable lessons runtime file: `memory/experience.local.md` (ignored)
- Session audit trail: `memory/sessions/YYYY-MM.md` (ignored runtime file)

Use `memory/experience.local.md` to capture learned behavior and recurring operator preferences.
Do not quietly rewrite `SOUL.md` by habit; change `SOUL.md` intentionally when a preference is stable enough to become part of the agent's core identity.

Before you finish a meaningful task:
- Update the daily log with the objective, actions taken, results, evidence, blockers, and next steps.
- If you learned a reusable tactic, pitfall, or validation rule, append it to `memory/experience.local.md`.

Quick routine:
- Create `memory/daily/YYYY-MM-DD.md` from `memory/daily/TEMPLATE.md` if today's file does not exist.
- Check required environment variables by inspecting the current shell or launcher environment and comparing it with repo-root `.env`.
- Edit the markdown files directly instead of using repo-local helper scripts.
- Read the schema in `memory/experience.md`, then append reusable lessons directly to `memory/experience.local.md`.

Do not assume `.env` has been loaded into the current process environment. Read `.env` directly when needed, and treat any already-exported shell values as authoritative.

## Guardrails

- Never claim task completion without the evidence source required by the active skill.
- Keep memory entries concise, factual, and timestamped when useful.
- Do not carry stale memory forward when live API or browser checks can verify the current state.
