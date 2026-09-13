---
name: minimal-ui-decider
description: Make design and implementation decisions for UI work (React + TypeScript + Tailwind examples; decision rules are stack-agnostic). Use when building, modifying, reviewing, or refactoring UI components, layouts, forms, navigation, responsive behavior, interaction states, accessibility, motion, or visual design — 构建/修改/审查 UI、组件、表单、布局、无障碍、响应式、动效时使用. Prefer the simplest solution that satisfies the user's goal while preserving existing project architecture and accessibility.
---

# Minimal UI Engineering

面向 Agent 的 UI 设计与工程决策系统，配套 [形色场 UI Handbook](https://zzhzhouzhou.github.io/UI-handbook/)（React 19 + TypeScript + Tailwind CSS 4）。

核心哲学：

> **Minimalism is not the absence of visual elements. It is the absence of unnecessary decisions.**
> 极简不是没有视觉元素，而是没有不必要的决策。

所以本 Skill 不是 UI 教科书，也不是视觉禁令：**它是决策与工程宪法（Decision / Engineering Constitution）**。
具体组件长什么样由 `references/handbook.md` 与 `recipes/*` 负责；**先做决策，再查规范，再写代码，最后验证。**

## 三层分工

```text
Skill         → Decide whether / why / how much   （本 Skill）
Handbook      → Decide how supported components are designed（references/handbook.md）
Source Code   → Verify actual implementation      （真实项目，优先复用）
```

不要在 Skill 里重写 Handbook；不要重新发明组件分类体系。现有 Handbook（10 章 / 115+ 个组件——快照计数以 `references/handbook.md` §组件计数约定为准；等级：基础 / 进阶 / 高级）是事实来源。

> **项目适配**：现有项目文档（AGENTS.md / CLAUDE.md / 设计系统文档）的明确约定优先于本 Skill 的 S/Y 与 Visual Baseline 默认值（`references/minimal.md`）；M 级按 `decision.md` §Exemptions 处理；C 层是 floor，不可被项目约定绕过。

## 规则等级（稳定 ID）

```text
C-xx = CRITICAL   基本可用性 / Accessibility / 安全底线。违反 ⇒ 核心任务可能无法完成。
M-xx = MUST       工程质量 / 维护性 / 一致性默认要求。违反 ⇒ 工程质量下降。
S-xx = SHOULD     推荐默认实践，可根据用户目标与项目约束调整。
Y-xx = MAY        可选增强（spotlight / glass / particles / 3D / parallax / custom cursor …）。
```

完整规则见 `references/decision.md`。冲突时按此优先级（CRITICAL 是 floor，不是普通条目）：

```text
CRITICAL floor → User goal → Information architecture → Usability → Responsive
    → Consistency → Performance → Motion → Decoration
```

用户需求可以改变 S / Y，但不能无条件突破 C。

> 注意：行为回归测试场景使用独立前缀 **T-xx**（见 `tests/scenarios.md`），与 S 规则互不相干。

## Agent 工作流（每次 UI 任务都执行）

```text
 1. 理解用户目标（Intent Translation，见 references/visual.md §Intent）
 2. Inspect 现有项目：components / tokens / dependencies / patterns / architecture
 3. 识别可复用能力：Inspect → Reuse → Adapt → Extend → Create new
 4. 选择满足目标的最简方案
 5. 应用 CRITICAL floor（键盘、可访问名、语义、非颜色信息、表单标签、核心响应式）
 6. 应用工程默认（复用、可分析样式类、最小 diff、保持公共 API）
 7. 按需加载 Handbook / Recipe 得到具体设计与实现
 8. 实现
 9. 走查：响应式、适用交互状态、键盘与无障碍、动效与 reduced motion、视觉层级
10. Verify：运行可行的机械验证并出示证据（构建 / 语法检查 / lint / axe 等自动化检查，
    见 references/checklist.md §自动化映射）；无法运行的项说明原因。声称完成前先有证据。
11. 运行删除测试，移除不必要复杂度
12. 返回实现
```

## 何时询问用户

> **If ambiguity materially changes implementation → Ask.**
> 缺失信息会实质改变实现方案时才问；否则 **Make a reasonable default and proceed.**

例如「做一个选择器」可以直接判断 native select 是否足够；「做一个能搜索 10000 个用户的选择器」方案已改变，才需要询问。

## 决策速查（高频情况）

> 本表是 `references/decision.md` §Decision Matrix 的速查摘录，两处不一致时以 decision.md 为准。多数任务看这里即可起步；涉及规则冲突、豁免、复杂决策时再读 decision.md 全文。
> **维护约定**：修改 `decision.md` §Decision Matrix 时，必须同步核对本节速查表并保持一致，防止两处漂移。

| 情况 | 默认决策 |
|---|---|
| 现有组件满足需求 / 90% 满足 | Reuse（M-01）/ Adapt·Extend 并保持 API（M-04） |
| 现有依赖有能力 | 不装新依赖（M-03），inline SVG / 现有 hook / 原生 API |
| 用户要「高级一点」 | Intent Translation → typography / spacing / hierarchy / subtle motion |
| 用户要「简洁 / 极简」 | Minimal Style Preset（minimal.md）：减少视觉决策，保留必要信息与反馈 |
| 用户要特定高级效果 | 允许（Y），先跑复杂度测试 |
| 选项 4–10 个 / >10 或需搜索 | native `<select>` / combobox |
| 列表数据 < 100 条 | 普通列表（虚拟化本身是复杂度） |
| 展示型内容切换 / 平级视图切换 | accordion 优先 / tabs·segmented |
| 异步时长不确定 / 已知短操作 | 骨架屏（>300ms 才显示）/ 按钮内 loading |
| 全局跳转频繁 | command palette（Ctrl+K） |
| 表格行 > 6 列且窄屏常用 | 同时提供卡片形态 |
| 没说清且方案会变 | Ask（一问，不搞问卷） |
| 涉及状态 / 语义 | 先核对 C-01…C-06 是否满足 |

## 路由（按语义与任务判断，不做机械关键词匹配）

| 当任务涉及…… | 加载 | 体量 |
|---|---|---|
| 规则全文 / 豁免 / 冲突处理（简单任务用上方「决策速查」即可） | `references/decision.md` | 大 |
| 全局走查清单 + 自动化检查映射 | `references/checklist.md` | 中 |
| Accessibility / 无障碍 | `references/accessibility.md` | 中 |
| 视觉 / 层级 / 字体 / 色彩 / 品牌感（如「更高级」） | `references/visual.md`、`references/tokens.md` | 小 + 中 |
| 极简 / 默认视觉基线（如「更简洁」「极简一点」） | `references/minimal.md` | 中 |
| 交互状态 | `references/interaction.md` | 小 |
| 响应式 / 断点 / 布局转换 | `references/responsive.md` | 小 |
| 动画 / 动效 | `references/motion.md` | 小 |
| React / Tailwind / 依赖 / 性能 / 工程 / 其他技术栈 | `references/engineering.md` | 中 |
| 具体组件规范与事实来源 | `references/handbook.md` | 小 |
| 组件家族实现（基础/表单/导航/反馈/高级） | `recipes/basic-components.md`、`recipes/forms.md`、`recipes/navigation.md`、`recipes/feedback-overlays.md`、`recipes/advanced-interactions.md` | 中 |
| 数据展示实现 | `recipes/data-display.md` | 小 |
| 复杂决策 / 常见错误 | `references/counterexamples.md` | 大 |
| 行为回归测试 | `tests/scenarios.md`、`tests/expected-behaviors.md` | 中 |

> 体量档位：小 ≤100 行 · 中 100–250 行 · 大 >250 行，仅供加载成本参考，不要求精确同步（避免行数标注随修订漂移）。

路由按**语义**判断：用户说「让页面更高级」即使没提色彩/间距/字体，也应加载 visual guidance。

## 必须避免的高频错误

- 用 placeholder 代替 label（违反 C-04）。
- 用 `<div onClick>` 模拟原生交互（违反 C-03）。
- 只靠颜色表达 error / selected / required（违反 C-05）。
- 默认安装 Lucide / Radix / 其他 UI 库（违反 M-03）；优先 inline SVG 与已有能力。
- 把「极简」执行成全灰、删必要信息、边框/圆角/动效一刀切或泛模板（违反 `references/minimal.md`，见 E-18…E-21）。
- 把所有数字全部禁止在 token 文件之外出现（只集中 canonical token definitions，规则数字正常存在）。
- 把 Existing Project First 理解成「旧代码永远不能改」（CRITICAL 无障碍问题必须指出并处理）。
- 每次 UI 任务都询问用户。
- 为了「规范」做无关重构（违反 M-06）。
- 未转义渲染用户输入 / 交互输出（违反 M-07）。
- 机械关键词路由。

完整反例见 `references/counterexamples.md`（E-01 … E-21，Wrong / Why / Right / Root cause / Related rules）。

## 最终质量标准

走查用统一清单 `references/checklist.md`（全局清单 + 各领域附加项 + 自动化检查映射），不要逐文件重复检查。

最后执行删除测试：

> **Can anything be removed without meaningful loss?** 如果可以，移除它。
