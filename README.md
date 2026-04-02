# OpenNomos Task Ops Agent

<p align="right">
  <strong>English</strong> | <a href="./README.zh-CN.md">简体中文</a>
</p>

<p align="left">
  <a href="https://github.com/NomosGrowth/opennomos/stargazers">
    <img alt="GitHub stars" src="https://img.shields.io/github/stars/NomosGrowth/opennomos?style=for-the-badge&logo=github" />
  </a>
  <a href="https://github.com/NomosGrowth/opennomos/network/members">
    <img alt="GitHub forks" src="https://img.shields.io/github/forks/NomosGrowth/opennomos?style=for-the-badge&logo=github" />
  </a>
  <a href="https://github.com/NomosGrowth/opennomos/commits/main">
    <img alt="GitHub last commit" src="https://img.shields.io/github/last-commit/NomosGrowth/opennomos?style=for-the-badge&logo=github" />
  </a>
  <img alt="OpenNomos" src="https://img.shields.io/badge/OpenNomos-Task%20Ops-0F766E?style=for-the-badge" />
  <img alt="Codex Compatible" src="https://img.shields.io/badge/Codex-Compatible-111827?style=for-the-badge" />
  <img alt="Claude Code Compatible" src="https://img.shields.io/badge/Claude%20Code-Compatible-D97706?style=for-the-badge" />
  <img alt="Script Free Workflow" src="https://img.shields.io/badge/Workflow-Script--Free-2563EB?style=for-the-badge" />
  <img alt="Markdown Memory" src="https://img.shields.io/badge/Memory-Markdown-16A34A?style=for-the-badge" />
</p>

A production-style agent workspace for running OpenNomos task operations with Codex and Claude Code.

This repository is designed for operators who want a clean, durable setup: prompt-driven workflows, repo-local instruction layers, shared markdown memory, and no dependency on repo-local automation scripts.

## Why This Repo Exists

- Standardize how OpenNomos task work is discovered, executed, validated, and documented.
- Keep agent behavior durable through layered instructions instead of fragile chat context.
- Preserve reusable operational knowledge in markdown so Codex and Claude Code can share the same workspace.

## Highlights

- Shared workspace for both Codex and Claude Code.
- Script-free operating model built on prompts, templates, and repo instructions.
- OpenNomos-focused task workflow with clear evidence and validation expectations.
- Repo-local memory model for daily logs, reusable lessons, and optional session audits.
- Reusable skill packaging under `skills/` for portable task workflows.

## Quick Start

1. Create or edit repo-root `.env`.
2. Fill in the OpenNomos credentials you need.
3. Launch `codex` or `claude` from the repository root.
4. Let the agent load `SOUL.md`, `AGENTS.md`, `skills/`, and `memory/` directly.

Example `.env` values:

```bash
OPENNOMOS_MCP_BASE_URL=https://api.opennomos.com
OPENNOMOS_MCP_KEY=
OPENNOMOS_ACCOUNT_EMAIL=
OPENNOMOS_ACCOUNT_PASSWORD=
```

Setup rules:

- Keep real secrets in `.env`, not in markdown files.
- Shell environment variables override values defined in `.env`.
- Use [`.env.example`](.env.example) as the safe template.
- Environment inspection is manual by design.

## Operating Model

### Instruction Layers

| File | Role |
| --- | --- |
| `SOUL.md` | Stable agent personality, task preference, and behavior boundaries |
| `AGENTS.md` | Repo procedure, startup routine, memory policy, and skill-loading rules |
| `memory/experience.md` | Tracked schema for reusable lessons |
| `memory/experience.local.md` | Ignored runtime lessons and local operator preferences |

### Core Principles

- Evidence over vibes.
- Shared memory over transient context.
- Reusable workflow assets over one-off scripts.
- Explicit guardrails for risky or public actions.

## Main Workflows

### Daily OpenNomos Task Check

Use [prompts/opennomos-daily.md](prompts/opennomos-daily.md) as the canonical daily execution prompt.

Recommended behavior:

- Create today's file from `memory/daily/TEMPLATE.md` before starting.
- Run the agent from this repository root.
- Record actions, results, evidence, blockers, and next steps directly in markdown.
- Stop on human checkpoints such as captcha, 2FA, wallet signing, or public posting.

### Weekly Content Planning

Use [prompts/opennomos-weekly-content.md](prompts/opennomos-weekly-content.md) as the canonical weekly planning prompt.

Recommended behavior:

- Run it before the first daily content pass of the week.
- Store the weekly outline at `artifacts/content/weeks/YYYY-Www.md`.
- Let later daily runs read that weekly plan before generating project-level output.

## Repository Layout

```text
.
├── AGENTS.md
├── CLAUDE.md
├── README.md
├── README.zh-CN.md
├── SOUL.md
├── memory/
│   ├── README.md
│   ├── daily/
│   │   └── TEMPLATE.md
│   ├── experience.md
│   └── sessions/
├── prompts/
│   ├── opennomos-daily.md
│   └── opennomos-weekly-content.md
└── skills/
    ├── manifest.json
    └── opennomos-task-ops/
        ├── SKILL.md
        ├── agents/
        │   └── openai.yaml
        └── references/
            └── templates.md
```

## Shared Memory

Both agents use the same repo-local memory model:

| Path | Purpose |
| --- | --- |
| `memory/daily/YYYY-MM-DD.md` | Ignored runtime daily log |
| `memory/daily/TEMPLATE.md` | Daily log template |
| `memory/experience.md` | Tracked lesson schema |
| `memory/experience.local.md` | Ignored runtime lessons |
| `memory/sessions/YYYY-MM.md` | Optional ignored session audit |

Suggested routine:

1. Create today's daily log if it does not exist.
2. Update activity, results, evidence, and next steps in markdown.
3. Compare current environment variables with `.env` when required.
4. Append reusable lessons to `memory/experience.local.md`.

## Skill Reuse

The current reusable workflow skill is:

- `opennomos-task-ops`

If you want to copy repo skills into a global Codex skills directory:

```bash
mkdir -p "$CODEX_HOME/skills"
cp -R skills/* "$CODEX_HOME/skills/"
```

Validation is intentionally toolchain-specific. Use the validator your agent runtime already supports instead of relying on repo-local wrapper scripts.

## GitHub Metrics

<p align="left">
  <a href="https://github.com/NomosGrowth/opennomos">
    <img alt="GitHub repo" src="https://img.shields.io/badge/GitHub-NomosGrowth%2Fopennomos-181717?style=flat-square&logo=github" />
  </a>
  <a href="https://github.com/NomosGrowth/opennomos/issues">
    <img alt="GitHub issues" src="https://img.shields.io/github/issues/NomosGrowth/opennomos?style=flat-square&logo=github" />
  </a>
  <a href="https://github.com/NomosGrowth/opennomos/pulls">
    <img alt="GitHub pull requests" src="https://img.shields.io/github/issues-pr/NomosGrowth/opennomos?style=flat-square&logo=github" />
  </a>
</p>

[![Star History Chart](https://api.star-history.com/svg?repos=NomosGrowth/opennomos&type=Date)](https://star-history.com/#NomosGrowth/opennomos&Date)
