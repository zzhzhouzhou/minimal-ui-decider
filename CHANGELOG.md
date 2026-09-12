# Changelog

本项目版本通过 CHANGELOG 与 Git tags 维护（不写入 SKILL.md frontmatter）。

## v1.4.0 — 2026-09-12

适配 `gh skill` 安装。**Skill 内容无变更。**

- 仓库布局调整为 `gh skill` / Agent Skills 识别的标准多 skill 布局：Skill 包移入 **`skills/minimal-ui-decider/`**（`skills/<skill-name>/SKILL.md`，skill 名与目录名一致）；README、LICENSE、CHANGELOG 留在仓库根，不进入安装产物。
- README 安装说明改为两条路径：`gh skill install zzhzhouzhou/minimal-ui-decider minimal-ui-decider`（GitHub CLI ≥ 2.90.0，支持 `--agent` / `--scope` / `@版本` 固定）与手动复制（克隆后复制 `skills/minimal-ui-decider/`）。
- 维护约定补充：Skill 目录名必须与 frontmatter `name` 一致，否则 `gh skill` 无法识别。

## v1.3.0 — 2026-09-12

更名、仓库结构扁平化与复审修复。**无规则 ID 语义变更。**

**更名与结构**

- Skill 更名 **minimal-ui-decider**（与仓库名一致）：SKILL.md frontmatter `name` 同步；文档与示例中的旧名清理。
- 仓库结构扁平化：**仓库根 = Agent Skill 包本体**（SKILL.md、references/、recipes/、tests/ 位于仓库根目录），克隆后整个目录即可作为 skill 安装；README 目录树同步。
- SKILL.md：决策速查表补充维护约定（修改 decision.md §Decision Matrix 时同步核对摘录）；路由表将数据展示拆分为独立「小」档（与其他 recipes 分列）。

**复审修复**

- E-04 关联规则收敛为 M-03（移除与依赖反例无关的 Y 层引用）。
- `decision.md` §Decision Matrix 增加与 SKILL.md §决策速查 的双向同步注记。
- Switch / Segmented / Tabs 示例补 `focus-visible` 外框——示例代码是 Agent 最常照抄的部分，须自身满足 C-01 焦点可见。
- `demo/` 走查页头注释同步更名（demo 保持本地维护，不随仓库发布）。

## v1.2.0 — 2026-09-12

一致性与缺口修复 + 组件计数治理。**无规则 ID 语义变更**；编号变化仅新增反例 E-15…E-17。

**引用与示例修复**

- 断链修复：`recipes/data-display.md` 中 `recipes/responsive.md` → `references/responsive.md`。
- 圆角 token 增加语义档名 ↔ Tailwind 类名映射列（6px→`rounded-md`、12px→`rounded-xl`、16px→`rounded-2xl`），并注明 Tailwind 内置档位不同名不同值；`feedback-overlays.md` Modal 示例 `rounded-lg`（8px）→ `rounded-2xl`（16px 档）；`data-display.md` Card 描述同步。
- 动效规则澄清例外：`motion.md` 明确小面积 disclosure 可用 `grid-template-rows: 1fr ↔ 0fr` 过渡（判断标准是面积与频率）；`data-display.md` Accordion 与 `advanced-interactions.md` Gallery 措辞与之一致，移除「用 transform/width 实现」的自相矛盾。
- `basic-components.md` CountdownButton 示例补卸载清理（与 E-13、自身提示一致）。
- `engineering.md` §Dependencies 改为「先 Inspect 当前项目」，Handbook 参考路径（`src/components/primitives.tsx`、`useCopy` 等）显式标注为 Handbook 仓库路径，避免误认成用户项目文件。
- README 安装说明移除 `.dsh/skills/` 笔误路径。

**组件计数治理（handbook 持续新增场景）**

- `handbook.md` 新增 §组件计数约定（防漂移）：canonical 计数处 = §章节与组件清单的 id 实际数量；对外统一「10 章 / N+」表述（N 为快照，当前 115）；新增/删除组件时同步五处快照（handbook §事实、SKILL.md、README、engineering.md、CHANGELOG）。
- 同步更新 SKILL.md / README / engineering.md 中的「115 个组件」为「115+」表述；README 维护 checklist 的「组件总数核对」改为按约定流程核对。

**内容补齐**

- 新增反例 E-15（运行时拼 class / M-02）、E-16（小改动破坏公共 API / M-04）、E-17（数据区只有 success 态 / M-05）；反例索引表同步，M-02/M-04/M-05 缺口补齐。
- `checklist.md` §自动化检查映射补 `eslint-plugin-jsx-a11y` 行（C-01…C-04 静态检查）；§高级效果附加项与 T-12 口径对齐（用户明确要求且已报告 trade-off 时可执行）。
- 触控目标与密集尺寸关系澄清（tokens / interaction / visual）：32 / 36px 仅限桌面鼠标精确输入，触屏可达控件一律 ≥ 44×44。
- `tests/scenarios.md` 新增 §半自动化运行建议（fixture 项目 + Playwright + axe 的机械化映射表），降低回归基线建立成本。

**结构优化**

- `demo/` 走查页调整为本地维护，不随仓库发布；`CHANGELOG.md` 保留在仓库（`handbook.md` §组件计数约定与回归基线策略引用它，是维护流程的一部分）。
- README 面向仓库首发（`minimal-ui-decider`）重写：新增「常见问题 ↔ 约束」对照、复杂度测试三问、12 步工作流摘要、安装与行为观察方法、维护流程；目录树根名更新为仓库名。
- `SKILL.md` 新增「决策速查」精简矩阵（canonical 仍在 `decision.md` §Decision Matrix）；路由表「约行数」列改为体量档位（小/中/大），避免行数标注随修订漂移；多数简单任务无需全文加载 decision.md。
- `demo/admin-dashboard.html` 头注释规则映射修正：token 纪律归入「tokens 总原则」，M-02 回归其本义（样式类静态可分析）；README 目录结构补 `demo/`。

## v1.1.0 — 2026-02-11

全面修订：一致性修复（P0）、结构优化（P1）、内容增强（P2）与实战反哺。**无破坏性规则 ID 变更**；唯一编号变化是测试场景前缀 `S-xx` → `T-xx`（避免与 S 规则撞车）。

**一致性修复**

- 规则编号撞车：回归场景 S-01…S-12 更名 **T-01…T-12**（`tests/*`），与 SHOULD 规则 S-01…S-10 彻底分离；场景内对规则的引用改写为「S-03 规则」等形式。
- 架构说明文档（`Minimal UI Engineering Skill 项目架构与实现说明.md`）标注为历史设计文档，其中「104 个组件」为初版数字，以 skill 本体的 115 为准。
- 断点：`tokens.md` §断点 为五档 canonical 定义；`responsive.md` 改为引用并补回 tablet-sm（640–767）档位说明。
- 对比度标准：canonical 定于 `accessibility.md` §Contrast；`tokens.md` 只留速记 + 引用。
- 补齐仓库 `LICENSE`（MIT）。

**结构优化**

- **Y 层补全**：`recipes/advanced-interactions.md` 新增「Y 编号对照表」（25 个效果 ↔ Y-01…Y-25 ↔ 等级，canonical 对照）；`decision.md` §Y 提供速查并指明事实来源。Y-13 起为基础级增强顺延编号。
- **Stack Adaptation**：`engineering.md` 新增非 React/非 Tailwind 项目的映射表（@theme ↔ CSS 变量、dark: ↔ 属性选择器、cn() ↔ 状态 class、effect 清理 ↔ removeEventListener 等）；决策规则声明为 stack-agnostic；SKILL.md frontmatter description 同步。
- **统一走查清单**：新建 `references/checklist.md`（全局清单 + 领域附加项 + 自动化检查映射 + 键盘模式速查）；13 份分散清单收敛于此，各 reference/recipe 的检查节改为指向 checklist。
- **验证步骤**：Agent 工作流插入第 10 步「Verify：运行可行的机械验证并出示证据（构建/语法/lint/axe）」；checklist 提供规则 → 工具映射（axe → C 层、ESLint → M-02、bundle → M-03、grep innerHTML → M-07…）。
- **回归基线机制**：新建 `tests/regression-log.md`（记录格式 + 基线策略 + 受影响场景重跑约定）；每个场景补「验收方式」一行。
- **初始焦点规则**：覆盖层初始焦点写入 `accessibility.md`（alertdialog 默认落安全选项，危险动作永不默认聚焦）、`feedback-overlays.md` 通则，并新增反例 E-11；T-03/T-11 预期同步。

**内容增强**

- 新增 **M-07 安全渲染**（转义/安全渲染用户输入、外链 noopener、autocomplete）；新增反例 E-12。
- 新增反例 E-11（危险优先焦点）、E-13（监听/rAF 泄漏）、E-14（setPointerCapture 无容错）；E-01…E-14 统一结构。
- 新增**反例索引**（规则 ↔ 反例对照表，暴露 M-02/M-04/M-05 反例缺口）。
- 决策矩阵扩充（decision.md）：骨架屏 vs spinner、tabs vs accordion、命令面板时机、表格/卡片切换阈值等 15+ 新条目。
- S 规则扩至 S-01…S-10（新增 S-09 空态可行动、S-10 时间与数字可扫描）。
- `interaction.md` 状态全集补 `no-permission`（无权限态）。
- `recipes/forms.md`：OTP/Rating/DatePicker/Color Swatches 实现要点补全；新增「键盘模式速查」表（combobox/tabs/menu/radiogroup/slider/switch 的焦点管理与 ARIA 选型）。
- `recipes/navigation.md`：Command Palette 扩充（两阶段选择、分组渲染、hover 同步高亮、scrollIntoView、何时需要）。
- 实战反哺：JS 侧 reduced-motion 读取模式（matchMedia + change 监听）入 `accessibility.md`/`motion.md`；关闭动画期防护（closing state）入 `motion.md`/`feedback-overlays.md`；setPointerCapture try/catch 入 `advanced-interactions.md`；复杂度测试判定注记（用户主动要求增强视为第 3 问通过）入 `decision.md`。
- SKILL.md：路由表加约行数列；高频错误补 M-07 条目；description 双语化。
- `handbook.md` / 同步机制：README 维护节新增 Handbook 同步 checklist（组件总数/章节/等级/技术栈四项核对）。

## v1.0.0 — 2026-02-11

初始发布：完整的 Minimal UI Engineering Skill（面向 Agent 的 UI 设计与工程决策系统）。

**Skill 包（`minimal-ui-engineering/`）**

- `SKILL.md`：路由器（frontmatter + 规则等级 + Agent 工作流 + 语义路由 + 质量清单）。
- `references/`（10 个文件）：
  - `decision.md` — C / M / S / Y 规则（Rule → Check → Fail signal）、优先级、豁免、冲突报告、决策矩阵、复杂度/删除测试。
  - `accessibility.md` — C-01…C-06 展开（键盘、可访问名、语义交互、表单标签、非颜色信息、核心响应式）。
  - `visual.md` — 视觉层级 + Intent Translation（「高级」≠ glass+gradient+glow）。
  - `tokens.md` — canonical tokens：色彩（zinc 体系 + 语义色）、字阶（48/32/24/20/16/12）、间距（4 的倍数）、圆角（6/12/16）、控件高度（32/36/44）、触控（≥44×44）、z-index 六档（0/10/20/40/50/60）、动效时长、断点、可复用动画令牌。
  - `interaction.md` — 适用交互状态、反馈时机（100ms / 300ms / 3s）、Pointer Events。
  - `responsive.md` — 移动优先、布局转换、320px 底线、容器查询。
  - `motion.md` — 只动 transform/opacity、时长与退出更快、reduced motion、现成动画令牌复用。
  - `engineering.md` — React 19 / TS 5.9 / Tailwind 4 / clsx+tailwind-merge / inline SVG / `cn()` / 最小 diff / 性能纪律。
  - `handbook.md` — 形色场 UI Handbook 事实来源：10 章 115 组件清单、等级、常用组件设计规则、在线链接。
  - `counterexamples.md` — E-01…E-10 高价值反例（统一 Wrong / Why / Right / Root cause / Related rules 结构）。
- `recipes/`（6 个文件）：`basic-components`、`forms`、`navigation`、`feedback-overlays`、`data-display`、`advanced-interactions`（Y 级高级效果 + 复杂度测试门槛）。
- `tests/`（2 个文件）：`scenarios.md`（输入场景）+ `expected-behaviors.md`（逐项预期与禁止项）。

**事实对齐**

- 与 <https://zzhzhouzhou.github.io/UI-handbook/> 及 <https://github.com/zzhzhouzhou/UI-handbook> 仓库现状对齐：React 19、TypeScript 5.9、Tailwind CSS 4 @theme、Vite 7、运行时依赖仅 clsx + tailwind-merge、inline SVG、10 章 115 组件、zinc 黑白灰基调、`@custom-variant dark`、动效令牌（marquee/shimmer/beam/float/dot/ripple/scale-in/slide-* 等）。

**仓库级文件**

- `README.md` — 项目定位、目录结构、安装与回归使用方法、设计决策说明。
- `CHANGELOG.md` — 本文件。

## 未发布

- 行为回归基线：运行 `tests/scenarios.md`（T-01…T-12），首个全量通过记录为基线（`tests/regression-log.md`）。
