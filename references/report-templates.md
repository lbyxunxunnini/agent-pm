# 报告模板

## 技术审查报告

```
## 技术审查报告

### ReviewSpec
- project_type: skill / CLAUDE.md / MCP server
- mode: discovery
- enabled_dimensions: [...]
- excluded_dimensions: [...]
- max_findings: 8
- severity_threshold: P1
- product_review_gate: pending_after_tech_review / pending_after_fix / not_requested_in_this_round / completed / skipped_by_user

### [维度名]
- `APM-WORKFLOW-001` **[P1 必须改]** status: open
  - 证据：具体位置和原文/行为
  - 影响：为什么会导致不收敛或错误执行
  - 建议：修改方向
  - 验收条件：修复后如何判断通过
```

## 修复计划

```
## 修复计划（共 N 项）

### 必须修复
1. APM-WORKFLOW-001 [P1] 问题描述 → 依赖 APM-SAFETY-002
   - 验收条件：...
2. APM-SAFETY-002 [P0] 问题描述 → 无依赖（先修）
   - 验收条件：...

### Backlog（本轮不阻塞）
4. APM-DESIGN-003 [P2] 问题描述 → status: deferred
5. APM-OUTPUT-004 [P3] 问题描述 → status: deferred

→ 确认修复顺序？(Y/n/调整)
```

## 修复验证报告

```
## 修复验证报告

### ReviewSpec
- mode: verification
- inherited_from: 本轮 discovery ReviewSpec
- product_review_gate: pending_after_fix / completed / skipped_by_user

### 修复结果
- APM-WORKFLOW-001 [P1] —— fixed
  - 验收条件：全部通过
- APM-SAFETY-002 [P0] —— partially_fixed
  - 未通过条件：...
- APM-DESIGN-003 [P2] —— deferred，本轮不阻塞

### 本次 diff 引入的 P0/P1 回归
- 无 / [列出直接由本次修改引入的问题]

### Backlog
- [仅记录 unrelated P2/P3，不阻塞]

### 产品评审交接
- status: pending_after_fix / completed / skipped_by_user
- next_prompt: "修复验证完成，要继续做产品评审吗？" / "无"
```

## 产品评审

```
## 产品评审

- status: completed
- triggered_after: tech_review / fix_verification

### 价值评定
...
```

### 价值评定

评分锚点：3 分 = "行业平均水平，能用但无亮点"。低于 3 分需要改进，高于 3 分值得强调。

- **痛度**：它解决了什么问题？这个问题有多痛？用户现在怎么解决的？
  - 评分：1-无感 2-有点烦 3-影响效率 4-严重阻碍 5-无法忍受
- **技术质量**：架构是否健壮、可维护、可扩展
  - 评分：1-脆弱 2-能跑但难改 3-基本健壮 4-设计良好 5-优秀
- **差异化**：和现有方案相比有什么独特优势？有没有竞品？
  - 评分：1-完全重复 2-微小改进 3-有差异 4-明显优势 5-不可替代
- **ROI**：做了多少工作，产出了多少价值？是否值得继续投入？
  - 评分：1-投入远大于产出 2-勉强平衡 3-合理 4-高回报 5-极高杠杆

### 发展方向

- 输出短/中/长期路线图草案，每阶段 2-3 个关键动作
- 标注依赖关系和前置条件

### 用户接受策略

- **定位**：目标用户是谁、核心卖点是什么、一句话介绍怎么说
- **行动清单**：列出"你可以做的 5 件事"——面向该项目的**目标用户**，让他们能立刻上手体验并感受到价值

## 文件保存报告

```
# Agent PM 审查报告

- 审查日期：YYYY-MM-DD
- 项目路径：/path/to/project
- 项目类型：skill / CLAUDE.md / MCP server
- 产品评审状态：pending_after_tech_review / pending_after_fix / completed / skipped_by_user / not_requested_in_this_round

## 技术审查
（按维度组织，同对话报告格式，包含 ReviewSpec、issue_id、severity、status、acceptance_criteria）

## 修复记录（如有，未执行则标注"未执行"）
- 修复项数：N
- 成功：X / 失败：Y
- 详细修改：[文件列表及变更摘要]
- 验证模式结果：[fixed / partially_fixed / failed]
- Backlog：[deferred issue 列表]

## 产品评审（如有，未执行则标注"未执行"）
- 状态：[completed / skipped_by_user / not_requested_in_this_round]
- 触发时机：[tech_review / fix_verification / -]
- 若未执行，记录原因：[用户跳过 / 本轮未进入该阶段（not_requested_in_this_round） / -]
- 若已执行，（同对话报告格式）

## 自定义规则更新（如有）
- 新增候选规则：CRXXX - 规则描述（来源：项目名，日期）
- 正式采纳规则：RXXX - 规则描述（来源：项目名，日期）
- 新增候选修复模板：[模板描述]
- 淘汰候选规则：RXXX - 规则描述（淘汰日期）
```
