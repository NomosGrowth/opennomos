---
name: opennomos-task-ops
description: Coordinate OpenNomos project-task workflows end to end. Use when the user wants to discover live OpenNomos tasks, classify them into full-auto or human-in-the-loop buckets, generate content packages for social or proof tasks, execute browser flows, and validate completion through event stream data.
---

# OpenNomos Task Ops

Use this skill to run OpenNomos tasks with a repeatable workflow instead of handling each project ad hoc.

Always pair this skill with:
- `opennomos-agent-mcp` for live project, task, and event stream data (https://github.com/NomosGrowth/opennomos-mcp-skills)
- `actionbook` for browser automation on project sites

## Browser Runtime Baseline

When this skill needs Playwright for browser work, pin the runtime to `playwright@1.58.2`.

- `playwright@1.58.2` maps to Chromium revision `1208`
- the current machine already has `~/Library/Caches/ms-playwright/chromium_headless_shell-1208`
- do not silently upgrade to `1.59.x` unless you also refresh the local Playwright browser cache, because `1.59.x` expects Chromium `1217`
- if a temporary Node workspace is needed, prefer installing `playwright@1.58.2` there instead of introducing a new repo dependency just to run the browser flow

## Required Inputs

Collect the smallest set that can unblock the current task:
- `nk_` API key for live OpenNomos data
- OpenNomos account credentials if browser login is required
- Human checkpoint support for captcha, 2FA, email verification, or wallet signing
- For content tasks, platform requirements from user input or `.env`, plus any required links, tags, screenshots, or mentions
- For ownership tasks, access to the relevant site, social account, or admin surface

Do not store secrets inside the skill files. Resolve credentials in this order:
- `OPENNOMOS_MCP_KEY`
- `OPENNOMOS_ACCOUNT_EMAIL`
- `OPENNOMOS_ACCOUNT_PASSWORD`
- if any required value is missing, ask the user to provide it in chat

Only use these environment variables for this skill. Do not invent alternate names.
- `OPENNOMOS_MCP_BASE_URL`
- `OPENNOMOS_MCP_KEY`
- `OPENNOMOS_ACCOUNT_EMAIL`
- `OPENNOMOS_ACCOUNT_PASSWORD`
- `OPENNOMOS_CONTENT_PLATFORMS`
- `OPENNOMOS_CONTENT_DEFAULT_LANGUAGE`
- `OPENNOMOS_CONTENT_PLATFORM_<PLATFORM_KEY>_NAME`
- `OPENNOMOS_CONTENT_PLATFORM_<PLATFORM_KEY>_LANGUAGE`
- `OPENNOMOS_CONTENT_PLATFORM_<PLATFORM_KEY>_CHAR_LIMIT`
- `OPENNOMOS_CONTENT_PLATFORM_<PLATFORM_KEY>_URL_LENGTH`
- `OPENNOMOS_CONTENT_PLATFORM_<PLATFORM_KEY>_REQUIRES_IMAGE`
- `OPENNOMOS_CONTENT_PLATFORM_<PLATFORM_KEY>_IMAGE_COUNT`
- `OPENNOMOS_CONTENT_PLATFORM_<PLATFORM_KEY>_NOTES`

If the user already has a logged-in browser session, prefer using that over logging in again, but still use the environment variables first when checking what credentials are available.

For recurring `C` tasks, treat the repo-root `.env` as the operator-managed default content config, with shell environment values overriding `.env` and direct user instructions overriding both.
For recurring daily `C` tasks inside this repo, use a two-layer planning model:
- on Mondays, create or refresh `artifacts/content/weeks/YYYY-Www.md` before the daily content package pass
- on other days, read the current week's plan first and generate today's packages against that plan unless live tasks or operator instructions require a change

## Workflow

### 1. Discover

Use `opennomos-agent-mcp` first. Default to `https://api.opennomos.com`.

Credential resolution for this phase:
- use `OPENNOMOS_MCP_KEY` first
- if it is missing, ask the user for the `nk_` key in chat

Pull only what is needed:
- `GET /api/v1/mcp/projects`
- `GET /api/v1/mcp/explore/projects`
- `GET /api/v1/mcp/projects/:project_id/tasks`
- `GET /api/v1/mcp/projects/:project_id/event-stream`

Treat `404` on `/tasks` as "project rule not found", not as a transport failure.

### 2. Classify

Put each task into one bucket:

- `A: full-auto`
  - Browserable product actions after login
  - Examples: open tool pages, daily-use flows, basic in-product interaction
- `B: half-auto`
  - Automation can reach the checkpoint, but the user must complete one step
  - Examples: captcha, email code, wallet signature, 2FA
- `C: user publishes, agent prepares`
  - The user performs the real post or external submission
  - Examples: X, Xiaohongshu, Discord, Telegram, form-based proof
- `D: user-owned surface`
  - The user controls the asset; the agent provides process support
  - Examples: SEO edits, sitemap submission, internal links, broken-link fixes, real invitations

Rank tasks by:
- points or expected upside
- time to execute
- automation fit
- dependence on external human action
- likelihood the task actually records points

### 3. Execute

For `A` tasks:
- Use `actionbook`
- Follow the manual-first rule for each page type
- If the manual is missing or broken, fall back to a page snapshot
- Prefer short deterministic runs over broad exploratory browsing

For `B` tasks:
- use `OPENNOMOS_ACCOUNT_EMAIL` and `OPENNOMOS_ACCOUNT_PASSWORD` first when login is required
- if either value is missing, ask the user in chat before starting the login flow
- Advance the flow until the user must take over
- State the checkpoint clearly and wait
- Resume immediately after the user confirms completion

For `C` tasks:
- Do not post from the user's social account unless the user explicitly asks
- Produce a content package using the format in [templates.md](references/templates.md)
- Read target platforms from `OPENNOMOS_CONTENT_PLATFORMS` when it is set; otherwise use the platforms explicitly requested by the user or implied by the task
- Treat `OPENNOMOS_CONTENT_PLATFORMS` as a comma-separated list of platform ids that become draft filenames under `artifacts/content/YYYY-MM-DD/<project>/<platform>.md`
- Resolve per-platform settings from `OPENNOMOS_CONTENT_PLATFORM_<PLATFORM_KEY>_*`, where `<PLATFORM_KEY>` is the uppercased platform id such as `X`, `WECHAT`, or `XIAOHONGSHU`
- Save reusable markdown content packages under `artifacts/content/YYYY-MM-DD/<project>/` when working inside this repo
- Also create or refresh `artifacts/content/YYYY-MM-DD/<project>/queue.md` inside each project folder
- If working inside this repo and today is Monday, also create or refresh the current week's planning artifact at `artifacts/content/weeks/YYYY-Www.md`
- If the current week's planning artifact exists, use it to vary daily angles and avoid rewriting the same content shape every day
- Optimize for real, credible content instead of task spam
- Respect per-platform constraints such as language, character limit, fixed URL length, image requirement, and image count
- If a platform requires images, include image guidance or a shot list in the package; do not claim the images already exist unless they were actually produced
- If the operator configured multiple platforms, treat those packages as reusable publishing assets, not as proof that the user has completed multiple public-posting tasks
- Treat the queue file as the handoff surface for later project-side reporting: default entries to `pending_submit`, let the operator fill public links and short English descriptions, and when asked to submit, use the current day's queue files and only act on entries whose `Public URL` is non-empty
- Treat package creation as the default stopping point unless the user explicitly asks for publishing or submission
- When the user explicitly asks to publish or push content to a social platform, load the `media-operator` skill (`skills/media-operator/SKILL.md`) and follow its workflow to fill the content into the target platform and save as draft. The `media-operator` skill uses `agent-browser` for browser automation — ensure it is installed (`npm install -g agent-browser`) before invoking

For `D` tasks:
- Produce an SOP, proof checklist, and validation plan
- Separate what the user must really change from what can be templated
- Do not claim completion before the owned surface is actually updated

### 4. Validate

Do not treat clicks or submissions as success. Check the data.

After each batch, re-check:
- project event stream when you need to know whether the task event was ingested

Mark outcomes as:
- `recorded`: event-stream shows a matching event for the current user
- `pending`: action completed but no matching event is visible yet
- `unclear`: nearby events exist but there is no confident match for the task
- `failed`: no evidence or clear rejection

Keep "confirmed by API" separate from any inference.

Validation rules:
- Use `GET /api/v1/mcp/projects/:project_id/event-stream` to answer “did my task event enter OpenNomos?”
- Stop the validation there unless the user explicitly asks about points or ledger

### 5. Maintain a Project Task Map

For each project, keep a lightweight operating view:
- which tasks exist
- which tasks are worth attempting
- which tasks require human checkpoints
- which tasks appear untracked or delayed
- what proof is required

Use the template in [templates.md](references/templates.md) when the user wants a reusable tracker.

## Output Rules

When planning:
- lead with the recommended task order
- show the class for each task
- show the human checkpoints
- show the evidence needed

When preparing content:
- provide one main version and two alternates
- include tags, hooks, and proof text if needed
- tune tone to the platform instead of reusing one generic draft
- surface the platform constraints used, especially language, character limit, and image requirements
- if working in this repo and preparing the week's Monday pass, write a weekly outline first under `artifacts/content/weeks/YYYY-Www.md`
- if working in this repo, prefer writing the package to `artifacts/content/YYYY-MM-DD/<project>/` instead of leaving it only in chat
- if working in this repo, also prepare the project-local `queue.md` file with placeholder entries for later submission reporting

When validating:
- show exact dates or timestamps when relevant
- separate confirmed outcomes from assumptions

## Guardrails

- Do not fabricate task completion, engagement, invites, or SEO work
- Do not assume a task event was recorded unless `/api/v1/mcp/projects/:project_id/event-stream` shows it
- Do not require points or ledger changes to validate task execution
- Do not use memory for current platform state when live API data is available
- Stop and ask for help only at true human checkpoints
