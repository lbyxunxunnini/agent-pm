# agent-pm

`agent-pm` 是一个面向 Claude Code / Codex skill、`CLAUDE.md` 指令文件和 MCP Server 项目的产品经理型审查 skill。它不做通用 code review，而是从产品、指令、工作流和收敛性角度识别 agent 项目中的高价值问题，并支持在同一闭环内完成修复与验证。

当前版本：`3.0.6`

## 核心能力

- 审查 skill、`CLAUDE.md`、MCP Server 项目的指令清晰度、逻辑完整性和设计质量
- 基于 `issue_id`、状态和验收条件管理问题生命周期
- 在修复阶段执行验证模式，避免重新开放式审查
- 在技术闭环结束后继续承接产品评审、多维度评分和发展方向建议
- 记录候选规则和候选修复模板，支持跨项目进化

## 使用方式

触发方式：

```text
/agent-pm [path]
```

- 不带路径时，先询问用户要审查哪个项目
- 审查模式优先进入模式 A
- 已确认问题的修复进入模式 B
- 新 skill 创建构思先进入模式 C 做澄清

## 工作流概览

1. 模式 A：技术审查，固定 `ReviewSpec`，逐维度扫描并通过执行验证筛选问题
2. 交互确认：将问题标记为 `accepted`、`rejected` 或 `deferred`
3. 模式 B：仅修复 `accepted` 的 P0/P1，并在验证模式中检查验收条件和直接回归
4. 产品评审：技术闭环结束后，询问是否继续做多维度评分、发展方向和用户接受策略
5. 台账更新：同步 issue 状态、产品评审状态、候选规则与候选修复模板

## 目录结构

```text
agent-pm/
├── SKILL.md
├── README.md
├── CHANGELOG.md
└── references/
    ├── best-practices.md
    ├── checklist.md
    ├── issue-ledger.md
    ├── mode-a.md
    ├── mode-b.md
    ├── mode-c.md
    ├── report-templates.md
    └── skill-brief-questions.md
```

## 关键文件

- `SKILL.md`：主入口、模式选择、核心原则和总体约束
- `references/mode-a.md`：审查阶段流程
- `references/mode-b.md`：修复与验证阶段流程
- `references/mode-c.md`：新 skill 创建前澄清流程
- `references/report-templates.md`：审查、修复验证、产品评审和保存报告模板
- `references/issue-ledger.md`：issue 生命周期、ReviewSpec 和产品评审状态记录
- `references/checklist.md`：逐维度审查清单与标准修复方法

## 版本说明

详见 [CHANGELOG.md](CHANGELOG.md)。
