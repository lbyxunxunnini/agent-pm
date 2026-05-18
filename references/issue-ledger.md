# Agent PM Issue Ledger

> 此文件用于记录 agent-pm 审查问题的生命周期，防止同一项目在多轮审查中反复发现、反复修复、反复引入新问题。
>
> 使用规则：
> - 每轮审查开始前先读取本文件，复用已有 issue_id 和 rejected/deferred 状态。
> - 每轮修复结束后更新本文件。
> - 候选规则只能记录在这里，不能立即污染 checklist。

---

## 当前项目问题台账

| issue_id | 项目 | 维度 | severity | status | first_seen | last_seen | 证据位置 | 验证结果 |
|----------|------|------|----------|--------|------------|-----------|----------|----------|
| 暂无 | - | - | - | - | - | - | - | - |

### 状态定义

- `open`：已发现，但用户尚未确认是否处理
- `accepted`：用户确认需要修复
- `fixed_pending_verification`：已修改，等待验证
- `fixed`：验收条件已通过
- `partially_fixed`：部分验收条件未通过
- `failed`：修复失败或引入阻塞回归
- `rejected`：用户拒绝处理，后续不得重复报告
- `deferred`：进入 backlog，本轮不阻塞

---

## 本轮 ReviewSpec 记录

```yaml
review_id:
date:
project_path:
project_type:
mode: discovery
enabled_dimensions: []
excluded_dimensions: []
max_findings: 8
severity_threshold: P1
checklist_version:
```

---

## Backlog

| issue_id | severity | 问题摘要 | 延后原因 | 重新触发条件 |
|----------|----------|----------|----------|--------------|
| 暂无 | - | - | - | - |

---

## 候选规则

候选规则不得在当前审查闭环内生效。只有满足跨项目证据、非重复、用户确认后，才能进入 `checklist.md` 的正式自定义规则。

### CR-001: 候选规则标题

- **状态**: candidate / accepted / rejected
- **来源项目**:
- **首次发现日期**:
- **重复出现项目**:
- **关联 issue_id**:
- **规则描述**:
- **检查方式**:
- **不采纳风险**:
- **采纳条件**:

---

## 候选修复模板

### CF-001: 候选模板标题

- **状态**: candidate / accepted / rejected
- **适用问题模式**:
- **修改范围**:
- **验收条件**:
- **成功项目**:
- **失败项目**:
- **失败原因**:

