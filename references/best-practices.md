# Agent 项目最佳实践

> 本文件记录从优秀 agent 项目中提炼的设计模式，作为 agent-pm 审查其他项目时的评估参考。
>
> 来源：从多个优秀 agent 项目中提炼的最佳实践
> 提炼日期：2026-05-21
>
> 这些模式不是强制要求，而是**加分项**——项目缺少它们不一定是 bug，但具备它们说明设计成熟度更高。
> 审查时作为 P2/P3 建议输出，除非缺少某项直接导致了功能逻辑问题（那才是 P0/P1）。

---

## BP-001: Iron Law 模式

### 模式描述

每个核心流程定义一条**不可违反的底线规则**，用一句话表达，放在文件最显眼的位置。Iron Law 是 agent 在执行过程中遇到任何 rationalization 时的最终判断锚点。

### 优秀案例

- TDD skill: `NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST`
- Debugging skill: `NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST`
- Verification skill: `NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE FIRST`

### 审查检查点

- [ ] 核心流程是否有明确的"不可违反"底线规则？
- [ ] 该规则是否以足够醒目的方式呈现（如代码块、加粗、文件开头）？
- [ ] 规则表述是否一句话可理解，不存在"看情况"？

### 修复建议

如果项目缺少 Iron Law，在主文件开头添加一条，格式：
```
[动词] [核心动作] WITHOUT [前置条件] FIRST
```

---

## BP-002: Red Flags + Rationalization Table

### 模式描述

预判 agent 在执行过程中可能出现的**自我欺骗想法**，用两张表显式列出：
- **Red Flags**：agent 脑中出现某些想法时 = 立刻 STOP，回到流程
- **Rationalization Table**：常见借口 vs 现实（如 "Should work now" → "RUN the verification"）

### 优秀案例

TDD skill 的 Red Flags：
| Thought | Reality |
|---------|---------|
| "Too simple to test" | Simple things become complex. Use it. |
| "I'll test after" | Tests prove the code works. Write them first. |

Debugging skill 的 Rationalization Table：
| Excuse | Reality |
|--------|---------|
| "Issue is simple, don't need process" | Simple issues have root causes too. |
| "Emergency, no time for process" | Systematic debugging is FASTER than thrashing. |

### 审查检查点

- [ ] 关键流程（调试、验证、实现）是否有 Red Flags 表？
- [ ] Red Flags 是否覆盖了该流程中最容易走捷径的心理路径？
- [ ] 每条 Red Flags 是否有明确的"正确做法"对照？

### 修复建议

为项目的 2-3 个核心流程各添加一张 Red Flags 表，每张 4-6 条，聚焦该流程最常见的 rationalization。

---

## BP-003: Process Flow Diagram

### 模式描述

用 `digraph`（Graphviz DOT 语法）画出工作流的完整流程图，包括判断节点、分支、循环、终止状态。流程图让 agent 能快速定位自己在流程中的位置，减少"我现在该做什么"的困惑。

### 优秀案例

brainstorming skill 的流程图包含：探索上下文 → 询问 → 提出方案 → 呈现设计 → 用户批准 → 写设计文档 → 自检 → 用户审核 → 调用 writing-plans。每个判断节点都有明确的 yes/no 分支。

### 审查检查点

- [ ] 多步工作流（3 步以上）是否有可视化流程图？
- [ ] 流程图是否包含所有判断节点和分支？
- [ ] 流程图是否标明了循环和终止条件？
- [ ] 流程图与文字描述是否一致（不矛盾）？

### 修复建议

为项目的主工作流添加 digraph 流程图，放在工作流章节的开头。格式：
```dot
digraph workflow_name {
    rankdir=TB;
    node [shape=box];
    start [label="..." shape=doublecircle];
    // ... 节点和边
    end [label="..." shape=doublecircle];
}
```

---

## BP-004: 可复用 Prompt 模板作为独立文件

### 模式描述

将 agent 调用 subagent 或生成报告时使用的 prompt 模板提取为**独立文件**，主文件只引用它们。好处：
- 主文件瘦身，降低 token 消耗
- 模板可独立更新，不影响主逻辑
- 多处引用同一模板时保持一致

### 优秀案例

subagent-driven-development skill 将 3 个 prompt 模板分离为独立文件：
- `implementer-prompt.md` — 实现者 subagent 的 prompt
- `spec-reviewer-prompt.md` — 规格审查者 subagent 的 prompt
- `code-quality-reviewer-prompt.md` — 代码质量审查者 subagent 的 prompt

SKILL.md 只写：`./implementer-prompt.md - Dispatch implementer subagent`

### 审查检查点

- [ ] 项目是否有重复使用的模板/格式定义（报告模板、prompt 模板等）？
- [ ] 这些模板是否内联在主文件中导致主文件过长？
- [ ] 模板是否可以提取为独立 reference 文件？

### 修复建议

将主文件中超过 10 行且被多处引用的模板提取到 `references/` 目录下，主文件用一句链接引用。

---

## BP-005: Skill/Mode 类型标注（Rigid vs Flexible）

### 模式描述

将 skill 或工作流模式明确标注为两种类型之一：
- **Rigid**：严格遵循，不允许 adapt、不允许"看情况"简化、不允许 agent 自行判断跳过
- **Flexible**：可以适应上下文，agent 可以根据实际情况调整执行方式

### 优秀案例

优秀项目的分类：
- Rigid: TDD、debugging、verification（纪律性流程，一步都不能少）
- Flexible: brainstorming、patterns（需要根据上下文灵活调整）

### 审查检查点

- [ ] 项目中的关键流程是否标注了 Rigid/Flexible 类型？
- [ ] 标注是否准确（应 Rigid 的没标 Flexible，应 Flexible 的没标 Rigid）？
- [ ] agent 是否知道 Rigid 流程不可 adapt、Flexible 流程可以调整？

### 修复建议

在每个关键流程/模式的标题处添加 `[Rigid]` 或 `[Flexible]` 标注，并在首次出现时解释含义。

---

## BP-006: Description Field 纪律

### 模式描述

skill 的 description 字段**只描述触发条件**（什么时候用），**不描述工作流**（用了之后做什么）。原因：测试表明，description 中包含工作流摘要会导致 agent 走捷径——看到 description 就"知道"流程，从而跳过实际读取 skill 内容的步骤。

### 优秀案例

正确：`Use when implementing any feature or bugfix, before writing implementation code`
错误：`Use when implementing features - guides through RED-GREEN-REFACTOR cycle, writes failing tests first, then minimal code, then refactors`

### 审查检查点

- [ ] skill 的 description 是否只描述触发条件？
- [ ] description 中是否包含了工作流摘要、步骤列表、执行方式等非触发信息？
- [ ] description 是否以"Use when..."或等效的触发条件开头？

### 修复建议

精简 description 为纯触发条件，去除所有工作流描述。工作流内容放在 skill 正文中。

---

## BP-007: 连续执行 + 智能暂停

### 模式描述

agent 在执行多步任务时，采用"连续执行"模式：
- **默认不暂停**：不在每步之间征求用户确认，连续执行所有步骤
- **智能暂停点**：只在特定条件触发时暂停（失败、阻塞、需要用户决策、发现回归）

这减少了用户在批量操作中的交互摩擦，同时保留了安全暂停机制。

### 优秀案例

subagent-driven-development 的连续执行规则：
- 不暂停：任务之间不征求用户确认（"Should I continue?" 浪费时间）
- 暂停：BLOCKED 状态无法自行解决、ambiguity 阻止进展、所有任务完成

agent-pm Mode B 的连续修复模式：
- 不暂停：逐个修复项之间不征求确认
- 暂停：修复失败、发现 P0/P1 回归、L3 引用检查命中

### 审查检查点

- [ ] 多步执行流程是否默认连续执行而非逐步确认？
- [ ] 是否有明确的智能暂停条件（什么时候必须暂停）？
- [ ] 暂停条件是否覆盖了安全关键点（失败、回归、阻塞）？

### 修复建议

将逐步确认改为连续执行 + 智能暂停：定义 3-5 个暂停条件，其他情况自动继续。

---

## BP-008: Token 效率指导

### 模式描述

根据文件的**加载频率**控制文件大小，避免每次调用都加载大量不必要的 token：
- 每次加载的文件（bootstrap）：< 200 words
- 频繁加载的文件（常用 skill）：< 500 words
- 按需加载的文件（reference）：可以较长，但应只在需要时加载

拆分策略：主文件只保留**选择逻辑**（选哪个模式/流程），详细流程放在独立 reference 文件中按需加载。

### 优秀案例

优秀项目的规定：
- getting-started workflows < 150 words
- frequently-loaded skills < 200 words
- other skills < 500 words

agent-pm 的拆分：SKILL.md（~160 行）只保留身份、原则和模式选择，Mode A/B/C 详细流程（各 100-180 行）放在 references/ 下按需加载。

### 审查检查点

- [ ] 主文件（bootstrap/入口文件）是否控制在合理大小（< 200 行）？
- [ ] 详细流程/模板是否分离为独立文件按需加载？
- [ ] 是否有文件超过 500 行且可以拆分？
- [ ] 是否有内容被重复加载（主文件和 reference 都内联了同一段内容）？

### 修复建议

- 主文件 > 500 行：将详细流程提取到 references/ 目录
- 模板 > 10 行且多处引用：提取为独立文件
- 主文件只保留：身份定义、核心原则、模式选择逻辑、关键约束

---

## BP-009: 设计先行门控（Design-First Gate）

### 模式描述

在任何实现代码之前，必须先完成**设计探索并获得用户批准**。这是一个 HARD-GATE——不通过就不能写代码，无论任务看起来多简单。

设计先行的价值：
- "简单"任务是未检验假设导致返工的重灾区
- 先设计能暴露需求歧义，避免写完代码才发现理解偏差
- 设计文档是后续实现计划和代码审查的参照基准

### 优秀案例

brainstorming skill 的 HARD-GATE 规则：
> Do NOT invoke any implementation skill, write any code, scaffold any project, or take any implementation action until you have presented a design and the user has approved it. This applies to EVERY project regardless of perceived simplicity.

工作流：探索上下文 → 逐个提问 → 提出 2-3 个方案 → 分段呈现设计 → 用户批准 → 写设计文档 → 自检 → 用户审核 → 调用 writing-plans

### 审查检查点

- [ ] 项目是否有"先设计后实现"的明确流程？
- [ ] 该流程是否有 HARD-GATE 阻断机制（不通过设计就不能写代码）？
- [ ] 设计阶段是否包含需求澄清（提问）和方案比较（多选一）？
- [ ] 设计文档是否有自检环节（检查占位符、矛盾、歧义）？

### 修复建议

在实现流程前插入设计阶段，用 HARD-GATE 标签阻断。即使简单任务也要走设计流程（可以很短，几句话即可）。

---

## BP-010: 两阶段 Review（先规格合规，再代码质量）

### 模式描述

代码审查分为**两个独立阶段**，顺序不可颠倒：
1. **规格合规审查**（Spec Compliance）— 代码是否实现了设计/需求中要求的所有东西？有没有多做或少做？
2. **代码质量审查**（Code Quality）— 代码写得好不好？命名、结构、测试覆盖、性能等。

先审规格再审质量的原因：如果先审质量，agent 可能把精力花在"把错误的事情写得很漂亮"上。规格合规确保"做了对的事"，代码质量确保"事做得好"。

### 优秀案例

subagent-driven-development 的两阶段 review 流程：
- 每个 task 完成后，先派 spec reviewer 检查规格合规性
- 规格通过后，再派 code quality reviewer 检查代码质量
- 任何一个 reviewer 发现问题 → implementer 修复 → re-review → 直到通过
- Red Flag: `Start code quality review before spec compliance is ✅`（错误顺序）

### 审查检查点

- [ ] 项目是否有代码审查/质量检查流程？
- [ ] 审查是否区分了"规格合规"和"代码质量"两个维度？
- [ ] 两个维度的审查顺序是否正确（先规格后质量）？
- [ ] 审查发现问题后是否有修复→re-review 的闭环？

### 修复建议

将单一 review 拆为两阶段：先检查"做对了没有"（spec compliance），再检查"做好了没有"（code quality）。用两个独立的 checklist 或 prompt 模板分别执行。

---

## BP-011: 小任务 + 新鲜上下文

### 模式描述

将大任务拆分为**小任务**（每个 2-5 分钟可完成），每个小任务用**全新的 subagent** 执行，不继承前一个任务的上下文。

好处：
- 避免上下文污染（前一个任务的错误/假设不会带到下一个）
- 每个 subagent 只需要理解当前任务的上下文，prompt 更精准
- 并行安全（独立 subagent 之间不干扰）
- 失败隔离（一个任务失败不影响其他任务）

### 优秀案例

subagent-driven-development 的核心原则：
- 读取计划文件一次，提取所有 task 的完整文本
- 每个 task dispatch 一个新 subagent，提供完整任务文本 + 上下文
- subagent 不读取计划文件（由 controller 提供完整信息）
- Red Flag: `Make subagent read plan file`（应该提供全文而不是让 subagent 自己去读）

### 审查检查点

- [ ] 大任务是否被拆分为可独立执行的小任务？
- [ ] 每个小任务是否使用独立的执行上下文（而非共享 session 状态）？
- [ ] 任务间是否有明确的输入/输出边界（而非依赖隐式状态）？

### 修复建议

将大流程拆分为独立的小任务，每个任务有明确的输入、输出和验证标准。如果项目使用 subagent，确保每个 task 使用新 subagent。

---

## BP-012: 阶段间结构化交接

### 模式描述

工作流的每个阶段之间有**明确的交接条件和触发方式**，而不是靠 agent 自行判断"下一步该做什么"。

交接条件包括：
- 前置阶段必须完成的产出物（如设计文档、实现计划）
- 交接时的触发动作（如"调用 writing-plans skill"）
- 不满足交接条件时的处理（如"设计未批准，继续修改"）

### 优秀案例

典型工作流的结构化交接：
```
brainstorming 完成 → 设计文档已写入 + 用户已批准 → 触发 writing-plans
writing-plans 完成 → 计划文件已写入 + 用户选择执行方式 → 触发 subagent-driven-development 或 executing-plans
subagent-driven-development 完成 → 所有 task 已完成 + 最终 review 已通过 → 触发 finishing-a-development-branch
```

每个交接点都有明确的**前置条件**（必须满足什么）和**触发动作**（调用什么）。

### 审查检查点

- [ ] 工作流各阶段之间是否有明确的交接条件？
- [ ] 交接条件是否包含前置产出物和触发动作？
- [ ] 不满足交接条件时是否有明确的处理路径（而非死路）？
- [ ] agent 是否能自动判断交接条件是否满足（而非靠用户提醒）？

### 修复建议

为工作流的每个阶段定义：产出物 → 交接条件 → 下一阶段触发动作。写成表格或流程图，让 agent 可以自动判断何时交接。

---

### BP-013: Rubric 驱动评估（Rubric-Driven Evaluation）

项目为 Agent 配备结构化的评测框架，把"什么是好"拆成可独立判断、可量化加权的检查条目，而非仅靠二元 pass/fail 或 Agent 自我评估。

**核心要素**：
- 评测条目分级：Essential（必须满足）、Important（应当满足）、Optional（锦上添花）、Pitfall（必须规避的负向标准）
- 负向标准（Pitfall）与正向标准并列，识别常见或高风险错误
- 每条条目自包含可评判，不依赖外部上下文即可独立判断
- 评测条目在需求/规划阶段就生成，不在评估阶段临时发挥
- 评测结果可量化（加权总分），驱动后续决策

**审查检查点**：
- 项目是否有评测条目或量化评估机制？
- 条目是否分级（不是全部同一权重）？
- 是否有 Pitfall（负向标准）？
- 条目是否在需求阶段生成（而非评估时临时发挥）？

**修复建议**：引入四层 Rubric 框架（功能正确性 / 健壮性 / UI 呈现 / 交互体验），在需求确认阶段基于验收标准生成评测条目，每条标注级别和权重。

**对应检查项**：G-001（评估反馈体系）、G-004（信息隔离，间接）。审查维度 9 时以 G-001 为主线，此处提供设计要素和修复方案的详细参考。

---

### BP-014: 迭代循环与退出条件（Iteration Loop with Exit Conditions）

项目的执行流程包含"评估→反馈→改进"的闭环，且有明确的退出条件防止无限循环或过早结束。

**核心要素**：
- 评分不达标时回到执行阶段重做，不是线性走完就结束
- 最大轮次限制（防止无限循环）
- 评分阈值（达标即退出）
- 边际效益判断（连续多轮提升微弱时建议退出，避免为了改进而改进）
- 退出原因可追溯（记录是因为达标退出、轮次用尽、还是边际停滞）

**审查检查点**：
- 执行流程是线性的还是有循环？
- 循环有无退出条件？
- 有无边际效益判断？
- 循环结束后是否记录退出原因？

**修复建议**：在验证阶段后增加迭代循环逻辑，设评分阈值（如 4.0/5.0）和最大轮次（如 5 轮），增加边际效益检查（连续 2 轮提升 < 0.1 时警告）。

**对应检查项**：G-003（迭代循环设计）。审查维度 9 时以 G-003 为主线，此处提供循环设计要素和退出条件的详细参考。

---

### BP-015: 品质锚定（Quality Anchoring）

项目在需求阶段就定义"做成什么样算好"，给 Agent 具象化的品质标杆，而非仅定义"做什么算对"。

**核心要素**：
- 品质层级定义（MVP / 精打磨 / 生产级），不同层级对应不同评估权重
- 正面参考（截图/URL + "好在哪"的注解）
- 反面参考（"为什么不好"的注解）
- 设计意图关键词（风格、密度、交互范式）
- 品质红线（不可低于此标准的底线）
- 品质定位传导到评估权重（不是悬空的声明）

**审查检查点**：
- 需求定义中是否有品质维度（不只是功能验收）？
- 是否有参考锚点（正面/反面）？
- 品质定位是否影响评估权重？

**修复建议**：在需求确认阶段增加品质锚定字段（quality_tier、positive_references、negative_references、design_intent、quality_redlines），品质层级直接传导到 Rubric 权重。

**对应检查项**：G-002（目标定义丰富度）。审查维度 9 时以 G-002 为主线，此处提供品质锚定要素和落地字段的详细参考。

---

### BP-016: 目标治理优先（Goal Governance over Process Control）

项目的规则/约束中，帮助 Agent 理解目标（"做什么""什么是好"）的规则应占相当比例，而非全部是限制 Agent 行为（"不许做什么""必须按这个步骤"）的规则。当过程控制远多于目标治理时，系统趋于僵硬。

**核心要素**：
- 每条规则可以问："是在帮 Agent 理解目标，还是在限制行为？"
- 目标治理类规则占比不低于 20-30%
- 不在过程控制上无限叠加，优先强化目标端
- 定期审查规则库，淘汰过时的过程控制规则

**审查检查点**：
- 统计项目中所有"必须/禁止/不得"类规则的数量
- 统计项目中"目标/验收/品质/评估"类规则的数量
- 比例是否严重失衡（如过程控制 > 80%）？

**修复建议**：审查现有规则，将纯过程控制类规则考虑精简或合并，增加目标治理类规则（评估标准、品质定义、迭代条件等）。

**对应检查项**：G-005（过程控制与目标治理平衡）。审查维度 9 时以 G-005 为主线，此处提供平衡判断标准和修复思路的详细参考。

---

## 审查报告中的最佳实践评估格式

在产品评审阶段（阶段二），可以输出最佳实践评估：

```
### 最佳实践评估

| 编号 | 模式 | 状态 | 说明 |
|------|------|------|------|
| BP-001 | Iron Law | ✅ 具备 | "NO X WITHOUT Y FIRST" 位于文件第 N 行 |
| BP-002 | Red Flags | ❌ 缺少 | 核心流程无 rationalization 预判表 |
| BP-003 | 流程图 | ⚠️ 部分 | 有主流程图但缺少判断分支 |
| BP-004 | 模板分离 | ✅ 具备 | prompt 模板已独立为文件 |
| BP-005 | 类型标注 | ❌ 缺少 | 未标注 Rigid/Flexible |
| BP-006 | Description 纪律 | ✅ 具备 | description 只含触发条件 |
| BP-007 | 连续执行 | ⚠️ 部分 | 有批量模式但默认逐步确认 |
| BP-008 | Token 效率 | ❌ 缺少 | 主文件 800+ 行未拆分 |
| BP-009 | 设计先行门控 | ❌ 缺少 | 无 HARD-GATE 阻断，直接写代码 |
| BP-010 | 两阶段 Review | ⚠️ 部分 | 有 review 但未区分规格合规和代码质量 |
| BP-011 | 小任务+新鲜上下文 | ✅ 具备 | 每个 task 独立 subagent，无上下文污染 |
| BP-012 | 阶段间结构化交接 | ❌ 缺少 | 阶段切换靠 agent 自行判断，无明确交接条件 |
| BP-013 | Rubric 驱动评估 | ❌ 缺少 | 无结构化评测条目，评估靠 agent 自由发挥 |
| BP-014 | 迭代循环与退出条件 | ❌ 缺少 | 线性执行流程，无评估→改进循环 |
| BP-015 | 品质锚定 | ❌ 缺少 | 需求只有功能验收，无品质标准定义 |
| BP-016 | 目标治理优先 | ⚠️ 部分 | 过程控制规则远多于目标治理规则 |

综合：4/16 具备，2/16 部分，10/16 缺少
```
