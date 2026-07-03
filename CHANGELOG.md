# Changelog

## 3.0.11 - 2026-07-03

- **统一错误输出格式**：SKILL.md 新增"错误输出规范"——错误信息必须自解释（原因 + 可执行动作），文档链接仅作补充，禁止让用户自行查文档理解错误
- **反模式聚合手册**：新增 `references/anti-patterns.md`，将分散在 SKILL.md / mode-a / mode-b / mode-d / report-templates / faq 中的 8 类反模式和歧义消解规则聚合为单文件，方便用户一次性了解所有注意事项
- **大文件场景处理策略**：mode-d.md D-Step 2a 从硬编码"最多 5 个文件"改为按优先级读取策略（入口 → 主指令 → 关键 reference），可标注"未完整读取"，上下文充分性优先于数量限制
- **跨轮次状态恢复完整性校验**：mode-d.md D-Step 1b 新增恢复前 4 项校验（目录存在、issue 有效、摘要非空、上下文完整），校验失败自动回退到询问模式并给出自然语言原因

## 3.0.10 - 2026-07-03

- **产品评分升级为一等模式**：新增模式 D（`references/mode-d.md`），产品评审/评分不再仅是审查或修复后的附属阶段，用户可直接说"产品评分""打分""--score"独立进入
- **意图优先级**：SKILL.md 新增意图优先级规则——产品评分 > 创建新 skill > 修复 > 审查，支持组合意图（A→D、B→D、A→B→D）
- **模式 A/B 重构产品评审交接**：mode-a.md 阶段二和 mode-b.md B-Step 7 从内联产品评审改为显式交接到模式 D，避免"修复后进不去评分""用户主动要求评分被上层流程挡住"
- **report-templates.md 新增 ProductReviewSpec**：独立评分规格，包含 `triggered_by`、`source_snapshot`、`unresolved_blockers_policy` 等字段
- **report-templates.md 产品评审报告增强**：增加维度评分明细表、总体评分、ProductReviewSpec 输出
- **issue-ledger.md 新增可恢复字段**：`pending_action`、`source_review_id`、`source_project_path`、`last_context_summary`，支持跨轮次上下文恢复，用户说"进入评分"时不再重新分类到 A/B
- **用户文档更新**：README.md、quick-start.md（三种→四种用法）、faq.md（新增"如何只做产品评分"）
- **歧义消解反例体系**：SKILL.md 新增 6 条歧义消解规则——顺序连接词优先于关键词匹配、"打分"在非产品评审上下文不触发、无目标项目不触发、历史查询≠新评分、"是，但是…"不截断、模糊表述不进 D。mode-d.md 不触发情况从 3 条扩展到 6 类 20+ 条反例

## 3.0.9 - 2026-06-24

- 新增场景维度"生态适配性"（面向国内用户），评估语言支持、网络依赖、本地化程度
- 新增 references/quick-start.md 快速开始指南，降低上手门槛
- 新增 references/faq.md 常见问题解答，覆盖使用类和理解类问题
- 新增 references/examples/ 目录，包含三个完整示例：
  - example-review-output.md：技术审查报告示例
  - example-fix-flow.md：修复流程示例
  - example-product-review.md：产品评审示例
- SKILL.md 新增"辅助资源"章节，引用新文件

## 3.0.7 - 2026-06-18

- 产品评审维度框架重构：从固定 5 维度改为三层可配置框架（核心维度 + 工具类型维度 + 场景维度）
- 新增核心必评维度：逻辑闭环性、一致性、清晰度、具体性，解决原有框架缺少逻辑正确性检查的问题
- 新增工具类型维度：Skill（指令清晰度、边界明确性、组合性、token 效率）、Agent（推理能力、规划能力、工具调用能力、错误恢复）、MCP Server（Tool schema 正确性、资源管理、安全性、性能）
- 新增场景维度：Loop 成熟度、易用性、可组合性、安全性、痛度、差异化、ROI，按项目特点选择
- 综合评级增加透明评分过程：必须输出维度评分明细表，每个维度评分必须引用支持方/反方具体条目编号
- 反方观点约束：禁止"没有实战验证""用户是否会遵守"等模型不可验证的批评，聚焦逻辑正确性、可执行性、一致性、完整性、退出条件、资源消耗
- 文件精简优化：核心文件从 1640 行减少到 855 行（-48%），详细内容移到 references/extended/ 目录按需加载
- checklist.md 修复方法表格移到 extended/checklist-fix-methods.md
- best-practices.md 精选 6 个核心模式，10 个扩展模式移到 extended/best-practices-extended.md
- report-templates.md Rubric 评分标准移到 extended/rubric-scoring.md
- mode-a.md 详细流程图和 ProjectContext 结构移到 extended/mode-a-details.md

## 3.0.6 - 2026-06-18

- 模式 A 引入子 Agent 读取机制，将文件读取委托给独立子 Agent，避免主线程上下文膨胀导致死循环
- 新增 ProjectContext 结构化定义，子 Agent 输出 files/workflow/decision_points/constraints/preliminary_findings，主线程后续阶段基于此工作不再回读原文件
- 新增 preliminary_findings 预判机制，子 Agent 对措辞敏感检查项（无歧义表述、约束具体、触发条件、优先级）做预判，主线程只处理 warn 二次确认和 fail 纳入候选
- Step 1.2 从"通读所有文件"改为"验证 ProjectContext 完整性"，只补充缺失部分，消除无边界循环
- Step 2.5 验证阶段改为按需读取，限定范围（1-2 个文件 + 行号定位），禁止扇出
- 新增降级策略：子 Agent 不可用时串行读取硬限制 8 个核心文件，读完立即生成 ProjectContext 后续不再回读

## 3.0.5 - 2026-06-10

- 产品评审新增「三方评估法」：先扮演支持方列出优点、再扮演反方列出缺点、最后综合双方观点给出客观评级，确保评分公正而非凭印象打分
- 产品评审 5 个评分维度（痛度、技术质量、差异化、ROI、Loop 成熟度）新增量化 Rubric，每个维度 1-5 分各有明确判定条件
- 修复 SKILL.md description 违反自身 BP-006（Description Field 纪律）的问题，精简为纯触发条件
- 删除 references/harness_loop_improvement_plan.md（已实施的改进计划，属于历史遗留物）

## 3.0.3 - 2026-06-10

- 收紧维度九 G-001 严重程度升级规则：仅当缺少评估反馈直接阻断 G-003 迭代循环时，才从 P2 升级为 P1，并要求在修复计划中标注触发依赖
- 模式 C 的评估与迭代闭环改为按 skill 类型分级：复杂/开发类要求 Rubric 或分级检查清单，轻量工具类允许 checklist/binary，但必须说明适配理由和失败处理路径
- `SkillBrief.evaluation` 新增 `fit_reason` 字段，用于解释评估强度为什么适合当前 skill 类型
- 明确 G-001~G-006 是正式目标治理审查框架，CR-002~CR-005 仍是候选经验规则，不得仅凭候选规则自动升级正式标准

## 3.0.2 - 2026-06-10

- 新增维度九「目标治理层」（G-001~G-006），覆盖评估反馈体系、目标定义丰富度、迭代循环设计、执行者与评估者分离、过程控制与目标治理平衡、可观测性
- 新增最佳实践 BP-013~BP-016：Rubric 驱动评估、迭代循环与退出条件、品质锚定、目标治理优先
- 模式 A 新增 Step 2.8「跨维度综合发现」，在输出报告前做整体校验（共同根因、修复级联效应、与自诊断文档一致性）
- 模式 A 审查上下文读取阶段新增 archive 自诊断文档检查，审查目标从"从零发现"调整为"验证自诊断 + 补充遗漏"
- 模式 C 闭环条件从 8 个扩展为 9 个，新增「评估与迭代闭环」，SkillBrief 新增 `evaluation` 字段
- 产品评审新增 Loop 成熟度评分维度和目标治理综合分析（过程控制 vs 目标治理比例、最高杠杆改进方向、改进依赖关系）
- 报告模板为维度 9 类改进建议新增「实现提示」字段，区分"往哪改"和"怎么改"
- issue-ledger 新增 CR-002~CR-005 候选规则，涵盖评估反馈体系、线性执行流程、角色共享上下文和过程控制占比等系统性缺陷

## 3.0.1 - 2026-06-04

- 修复技术审查、修复验证和产品评审之间的流程断链
- 明确模式 A 在技术闭环结束后再进入产品评审询问
- 明确模式 B 在源自模式 A 时，修复验证完成后必须交接回产品评审
- 为报告模板新增 `product_review_gate` 和产品评审交接状态，避免多轮对话丢失评分阶段
- 为 `issue-ledger` 新增产品评审状态记录，显式追踪待评审、已完成和用户跳过等状态
- 对齐 `SKILL.md` 与引用文档中的版本号和主流程摘要
