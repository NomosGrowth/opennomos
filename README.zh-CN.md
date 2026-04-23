# OpenNomos Task Ops Agent

<p align="right">
  <a href="./README.md">English</a> | <strong>简体中文</strong>
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

一个面向 OpenNomos 任务运营的生产级 agent 工作区，可同时配合 Codex 与 Claude Code 使用。

这个仓库面向需要稳定、可持续工作流的操作者：以 prompt 驱动任务，以仓库内分层指令约束行为，以 markdown 共享记忆，并且不依赖仓库本地脚本。

## 这个仓库的目标

- 标准化 OpenNomos 任务的发现、执行、验证与归档方式。
- 通过分层指令保持 agent 行为稳定，而不是依赖脆弱的聊天上下文。
- 用 markdown 保存可复用的运行经验，让 Codex 和 Claude Code 共享同一工作区。

## 特点

- 同时支持 Codex 与 Claude Code。
- 基于 prompt、模板和仓库说明文件的无脚本工作流。
- 面向 OpenNomos 的任务执行模型，强调证据与验证。
- 使用仓库内存储的 daily log、经验沉淀和可选 session audit。
- 在 `skills/` 下提供可复用的工作流技能封装。

## 快速开始

1. 创建或编辑仓库根目录下的 `.env`。
2. 填入你需要的 OpenNomos 配置与凭据。
3. 从仓库根目录启动 `codex` 或 `claude`。
4. 让 agent 直接读取 `SOUL.md`、`AGENTS.md`、`skills/` 和 `memory/`。

`.env` 示例：

```bash
OPENNOMOS_MCP_BASE_URL=https://api.opennomos.com
OPENNOMOS_MCP_KEY=
OPENNOMOS_ACCOUNT_EMAIL=
OPENNOMOS_ACCOUNT_PASSWORD=
```

配置规则：

- 真实密钥只放在 `.env` 中，不写进 markdown 文件。
- Shell 环境变量优先于 `.env` 中的值。
- 可用 [`.env.example`](.env.example) 作为安全模板。
- 环境检查刻意保持手动，不依赖仓库内脚本。

## 工作模型

### 指令分层

| 文件 | 作用 |
| --- | --- |
| `SOUL.md` | 定义稳定的人格、任务偏好与行为边界 |
| `AGENTS.md` | 定义仓库流程、启动顺序、memory 策略与 skill 加载规则 |
| `memory/experience.md` | 可复用经验的受版本控制模板 |
| `memory/experience.local.md` | 被忽略的本地经验与操作者偏好 |

### 核心原则

- 证据优先于感觉。
- 共享记忆优先于临时上下文。
- 可复用流程资产优先于一次性脚本。
- 对高风险或公开动作设置明确边界。

## 主要工作流

### OpenNomos 日常任务检查

使用 [prompts/opennomos-daily.md](prompts/opennomos-daily.md) 作为标准日常执行 prompt。

建议做法：

- 开始前先从 `memory/daily/TEMPLATE.md` 创建当天日志。
- 始终从这个仓库根目录运行 agent。
- 将动作、结果、证据、阻塞和下一步直接写入 markdown。
- 遇到 captcha、2FA、钱包签名、公开发帖等人工检查点时停止。

### 每周内容规划

使用 [prompts/opennomos-weekly-content.md](prompts/opennomos-weekly-content.md) 作为标准周计划 prompt。

建议做法：

- 在一周首次日常内容草稿投放前执行。
- 将周计划写入 `artifacts/content/weeks/YYYY-Www.md`。
- 后续每日运行在通过 `media-operator` 填写平台草稿前先读取该周计划。

## 仓库结构

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

## 共享记忆

两个 agent 共享同一套仓库内 memory 模型：

| 路径 | 用途 |
| --- | --- |
| `memory/daily/YYYY-MM-DD.md` | 被忽略的运行时日记 |
| `memory/daily/TEMPLATE.md` | 每日日志模板 |
| `memory/experience.md` | 经验模板与 schema |
| `memory/experience.local.md` | 被忽略的运行时经验 |
| `memory/sessions/YYYY-MM.md` | 可选的被忽略会话审计文件 |

建议流程：

1. 如果当天日志不存在，先创建它。
2. 在 markdown 中直接更新 activity、results、evidence 和 next steps。
3. 需要时对比当前环境变量与 `.env`。
4. 将可复用经验追加到 `memory/experience.local.md`。

## 技能复用

当前可复用的工作流 skill 是：

- `opennomos-task-ops`

如果你想把仓库中的 skills 复制到全局 Codex skills 目录：

```bash
mkdir -p "$CODEX_HOME/skills"
cp -R skills/* "$CODEX_HOME/skills/"
```

验证方式刻意保持与工具链解耦。请使用你的 agent 运行环境本身支持的校验方式，而不是依赖仓库内包装脚本。

## GitHub 指标

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
