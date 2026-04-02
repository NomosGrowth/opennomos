# OpenNomos Weekly Content Prompt

Run the repo-local OpenNomos weekly content planning workflow for the current ISO week.

Requirements:
- Work from the current repository root.
- Follow `AGENTS.md`, `skills/manifest.json`, and the `opennomos-task-ops` skill.
- Use live project/task data before writing the weekly plan.
- Write or refresh the current week's plan at `artifacts/content/weeks/YYYY-Www.md`.
- Use `artifacts/content/WEEKLY-PLAN-TEMPLATE.md` as the default structure.
- Keep the weekly plan aligned with current task fit, not just operator platform config.
- For `share_article`-only projects, prefer article-like platforms in the weekly plan unless live task state changes.
- Make the plan specific enough that daily content generation can follow it without re-deciding the whole week.
- Do not generate all daily platform drafts in the weekly planning pass unless the operator explicitly asks.
- Update `memory/daily/YYYY-MM-DD.md` with the planning action, results, evidence, and next actions.

Final response format:
- Current week plan path
- Key weekly priorities
- Any task-fit caveats that daily generation must respect
