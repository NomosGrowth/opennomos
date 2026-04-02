# Memory Bank

This repository keeps shared memory in markdown so Claude Code and Codex can see the same operating history.

## Files

- `daily/YYYY-MM-DD.md`: day-level execution log, kept local and ignored by Git
- `daily/TEMPLATE.md`: copy this when a new day starts
- `experience.md`: tracked schema and guidance for lesson entries
- `experience.local.md`: reusable lessons, pitfalls, and proven patterns kept local and ignored by Git
- `sessions/YYYY-MM.md`: optional manual session audit trail kept local and ignored by Git

## Logging Rules

- Keep entries factual and short.
- Record outcomes and evidence, not just intentions.
- Do not store secrets, raw credentials, or access tokens here.
- If live API state can answer a question, treat memory as historical context only.
- Read `experience.md` for the schema, then add lessons directly to `experience.local.md`; no helper script is required.

## Suggested Daily Flow

1. Ensure the current day file exists by copying `daily/TEMPLATE.md` if needed.
2. Append major actions to `## Activity Log`.
3. Summarize validated outcomes in `## Results` and `## Evidence`.
4. Check any required environment variables manually by comparing the current environment with repo-root `.env`.
5. Move reusable insights into `experience.local.md`.
6. If you maintain session audits, append them manually to `sessions/YYYY-MM.md`.
