# OpenNomos Daily Prompt

Run the repo-local OpenNomos daily task workflow for today.

Requirements:
- Work from the current repository root.
- Follow `AGENTS.md`, `skills/manifest.json`, and the `opennomos-task-ops` skill.
- Confirm the current actor first by reading `OPENNOMOS_MCP_KEY` and checking `/api/v1/mcp/me/points` before comparing any event-stream results.
- Discover live accessible projects, their task rules, and recent event-stream data.
- Prioritize worthwhile A-class tasks that do not require a human checkpoint today.
- If today is Monday, generate or refresh the current ISO week's content plan at `artifacts/content/weeks/YYYY-Www.md` before staging any daily platform drafts.
- If today is not Monday, read the current week's plan from `artifacts/content/weeks/YYYY-Www.md` when it exists, and use it as the default guide for today's content angles and platform mix unless live task state has changed.
- For worthwhile C-class content tasks, load `media-operator` and stage platform-ready drafts directly in the user's logged-in browser session after the A-class pass.
- Do not create standalone markdown drafting artifacts. Use the weekly plan, live task requirements, platform config, and target editor state as the working context.
- When later project-side reporting will be needed, create or refresh `artifacts/content/YYYY-MM-DD/<project>/queue.md` with default `pending_submit` entries so the operator can later fill public links and English submission blurbs.
- For OpenNomos-controlled first-party sites, prefer Playwright snapshot-first. For third-party sites, use actionbook manual-first when it adds value.
- If a product-side usage API exists, capture the request and response as intermediate evidence before event-stream validation.
- Validate success only with project event-stream data.
- Do not click final publish or submit public posts unless the user explicitly asks; stop at draft saved or the last true human checkpoint.
- If a task needs captcha, 2FA, wallet signing, unavailable browser handoff, or any other human checkpoint, stop at that point and record the blocker instead of waiting for input.
- Update `memory/daily/YYYY-MM-DD.md` with objective, actions, results, evidence, blockers, and next actions.

Final response format:
- Recorded tasks with exact timestamps
- Pending or failed tasks with the blocking reason
- Any follow-up needed from the operator
