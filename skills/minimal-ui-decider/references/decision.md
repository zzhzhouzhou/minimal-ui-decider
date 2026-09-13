# Decision — 核心决策规则

本文件是 Skill 的核心：**每次 UI 任务先读这里。** 规则按 C / M / S / Y 分级，使用稳定 ID，格式统一为 Rule → Check → Fail signal。

> 引用约定：规则 ID（C/M/S/Y）指向本文件；回归测试场景使用 **T-01…T-13**（见 `tests/scenarios.md`），两套编号互不相干。

## 规则层级与优先级

```text
CRITICAL floor
    ↓
User goal
    ↓
Information architecture
    ↓
Usability
    ↓
Responsive
    ↓
Consistency
    ↓
Performance
    ↓
Motion
    ↓
Decoration
```

> CRITICAL 是 floor，不是普通 priority item。用户需求可以改变 S / Y，但不能无条件突破 C。

## C — CRITICAL（底线）

### C-01 · Keyboard completion

**Rule**

核心任务必须能仅用键盘完成（Tab / Enter / Esc / 方向键）。

**Check**

逐一点击界面中每个可交互元素，验证是否存在键盘等价路径；覆盖层必须能获得焦点并能关闭。

**Fail signal**

存在只有鼠标才能完成的核心操作；焦点不可见或无法到达；弹窗无法用 Esc 关闭。

### C-02 · Accessible name

**Rule**

每个可交互元素都必须有稳定的 accessible name。

**Check**

Icon-only 元素检查 `aria-label` / 可见文字；读屏上下文可理解其用途。

**Fail signal**

`<button><svg /></button>` 无任何名称；aria-label 随状态变化导致名称不稳定。

### C-03 · Semantic interaction

**Rule**

用原生语义元素表达交互：`<button>`、`<a>`、`<input>`、`<select>`、`<textarea>`、`<label>`、`<form>`、`<nav>`、`<main>`。

**Check**

可点击元素是否使用 button/a；表单控件是否使用原生控件或其可访问替代。

**Fail signal**

`<div onClick>` 模拟按钮/链接；用 span 模拟 checkbox。

### C-04 · Persistent form label

**Rule**

表单控件必须有持久可见或可访问的 label；placeholder 不能代替 label。

**Check**

每个输入控件都有 `<label htmlFor>` 或等价的 aria-label/aria-labelledby。

**Fail signal**

`<input placeholder="邮箱" />` 无 label；label 在输入后消失。

### C-05 · Non-color information

**Rule**

error / success / warning / selected / required 等状态不能只靠颜色表达。

**Check**

状态是否同时有 text / icon / structure / state indicator。

**Fail signal**

红色文字无图标无说明；选中项仅通过背景色区分。

### C-06 · Core responsive access

**Rule**

核心功能在最小屏幕（320px）可用，不得依赖 hover，不得横向滚动。

**Check**

在 320px 宽度验证核心任务路径；触屏验证所有 hover 交互的替代方案。

**Fail signal**

移动端侧边栏直接 `hidden` 导致功能不可达；触屏无法触发 hover 才能用的操作。

## M — MUST（工程默认）

### M-01 · Reuse existing components

**Rule**

现有项目组件能满足任务时，必须复用，不新建重复组件。

**Check**

创建新组件前先 Inspect 相关组件与组件清单。

**Fail signal**

新组件无任务特定理由地复制了已有能力。

### M-02 · Prefer static Tailwind classes

**Rule**

Tailwind class 尽量静态、可分析；避免不必要的运行时 class 生成与动态拼接。

**Check**

条件样式是否可用静态变体（`hover:`、`dark:`、`disabled:`）表达；`cn()` 只用于组合可枚举的 class 集。

**Fail signal**

字符串模板拼 class；运行时根据任意值生成 class 名。

> Stack-agnostic 版本：任何技术栈下，样式组合都应「可枚举、可静态分析」，不运行时拼字符串（见 `engineering.md` §Stack Adaptation）。

### M-03 · Existing dependency first

**Rule**

优先使用已有 dependency 的能力；不默认安装新依赖（不默认 Lucide / Radix / UI 库）。

**Check**

新增依赖前检查是否已有能力（含 inline SVG 图标、现有 hook、原生 API）。

**Fail signal**

为一个小需求引入大依赖；替换项目已有的 inline SVG 图标体系。

### M-04 · Preserve public APIs

**Rule**

修改组件时保持已有 props / exports / public API 兼容。

**Check**

改动是否改变了组件的对外签名；是否为破坏性变更。

**Fail signal**

重命名 props、删除导出、改变受控/非受控行为且无迁移路径。

### M-05 · Cover applicable interaction states

**Rule**

组件覆盖其**适用**的交互状态（见 `references/interaction.md`），但不是所有状态都必须实现。

**Check**

Button：hover/focus/active/disabled/loading；Input：focus/error/disabled；数据展示：loading/empty/error。

**Fail signal**

组件在适用状态下无视觉反馈；异步组件无 loading / error / empty。

### M-06 · No unrelated refactoring

**Rule**

用户只要求小改动时，不做无关重构，保持最小 diff。

**Check**

diff 中是否只包含任务相关的变更。

**Fail signal**

为了「规范」顺手重命名、重排、替换与任务无关的代码。

### M-07 · Render user input safely

**Rule**

用户输入与外部数据必须转义/安全渲染后输出；不做 DOM 注入裸拼接。

**Check**

拼接 HTML 前是否转义（或使用框架默认的安全渲染）；外链是否带 `rel="noopener noreferrer"`；表单是否声明合理的 `autocomplete`。

**Fail signal**

`innerHTML = userInput`；评论/昵称等用户内容直接进模板；新窗口外链无 noopener。

> 该规则与栈无关：React 默认转义，但 `dangerouslySetInnerHTML`、富文本、markdown 渲染仍是风险点；vanilla / 模板引擎项目中这是必修项。

## S — SHOULD（推荐默认）

- S-01 · 移动优先：先保证小屏可用，再向大屏增强（`references/responsive.md`）。
- S-02 · 内容优先：布局与信息层级服务于用户完成任务（`references/visual.md`）。
- S-03 · 动效有目的：只对 feedback / state transition / hierarchy / focus / open-close 使用动效（`references/motion.md`）。
- S-04 · 反馈及时：操作 100ms 内有视觉反馈；加载超过 300ms 才显示加载态。
- S-05 · 主按钮每屏一次：一个页面只有一个 primary action。
- S-06 · 尊重 reduced motion：动画与转场尊重 `prefers-reduced-motion`。
- S-07 · 危险操作二次确认：删除类操作用红色并确认。
- S-08 · 错误信息说明原因与解决方式，出现在字段正下方。
- S-09 · 空态可行动：空状态 = 图标 + 说明 + 下一步动作，不白屏（`recipes/data-display.md`）。
- S-10 · 时间与数字可扫描：时间用等宽数字（tnum）；相对时间搭配绝对时间（title 或括号）。

可根据用户目标、项目约束、实际场景调整。

## Y — MAY（可选增强）

高级视觉与交互，默认不开启；使用前必须通过下面的复杂度测试。

> **「效果 ↔ Y 编号（Y-01…Y-25）↔ 等级」的完整对照是 canonical，定义在 `recipes/advanced-interactions.md` §Y 编号对照表；本文件不再重复编号速查，避免两处漂移。** 所有 Y 级效果都属于 MAY，使用前跑复杂度测试。

## 复杂度测试（Y 效果与任何多余实现都必须通过）

对每个候选元素/效果依次回答：

```text
1. Does it serve the current task?（它是否服务当前任务？）
2. Does it reduce another complexity?（它是否降低了其他复杂度？）
3. Does removing it harm usability, accessibility, maintainability, or the requested visual result?
   （删除它是否会明显损害可用性 / 无障碍 / 用户明确要求的视觉结果 / 信息层级？）
```

三个答案都是 No ⇒ **删除它。**

> 判定注记：第 3 问中的「用户明确要求的视觉结果」涵盖用户主动提出的增强诉求——用户说「加一些动效 / 加新功能」时，合理的动效与新功能本身即是被要求的视觉/功能结果，通过测试；仍需遵守 S-03（有目的）、S-06（reduced motion）与性能纪律，且装饰性动效 ≤ 一两处。

核心删除测试：

> **删掉它以后，用户还能完成任务吗？** 如果能，再问：**删掉它以后，用户失去了什么？** 如果答案是「几乎什么都没有」，则删除。

## Exemptions / 例外

### CRITICAL

不能通过普通 exemption 绕过。用户明确要求违反时：

```text
Report → Fix if in scope → If user explicitly rejects, state risk（明确说明风险）
```

### MUST

可以例外，但条件严格。核心条件：

> **遵守该规则会引入与用户当前任务无关的复杂度。**

还可因用户明确要求、现有项目约束、第三方平台约束、浏览器 / framework 限制而调整。禁止使用宽泛理由「为了简单所以可以忽略」——必须证明该复杂度与当前任务无关。

### S / Y

直接按照用户需求，但告知重要 trade-off。现有项目文档（AGENTS.md / CLAUDE.md / 设计系统文档）的明确约定优先于 S/Y 默认值与 Visual Baseline（`references/minimal.md`）；C 层不可被项目约定绕过。

## Conflict Reporting（规则与用户需求冲突时）

```text
C: Report → Fix if in scope → If user explicitly rejects, state risk
M: Report deviation / reason → Follow user if justified
S: Follow user directly
Y: Follow user directly
```

目标不是阻止用户，而是**让用户知道重要的 trade-off。**

## Decision Matrix（快速决策）

> `SKILL.md` §决策速查 是本表的精简摘录；修改本表时按 SKILL.md 的维护约定同步核对该摘录，防止两处漂移。

| 情况 | 默认决策 |
|---|---|
| 现有组件满足需求 | Reuse（M-01） |
| 现有组件 90% 满足 | Adapt / Extend，保持 API（M-04） |
| 需求超出已有能力 | Extend，或先判断原生控件是否足够 |
| 用户要「高级一点」 | Intent Translation → 先 typography/spacing/hierarchy/material/subtle motion（`references/visual.md`） |
| 用户要「简洁 / 极简」 | Minimal Style Preset（`references/minimal.md`）：减少视觉决策，保留必要信息与反馈 |
| 用户要特定高级效果 | 允许（Y），先跑复杂度测试 |
| 选项 4–10 个 | native `<select>` |
| 选项 >10 或需搜索 | combobox（`recipes/forms.md`） |
| 列表数据 < 100 条 | 普通列表（虚拟化本身是复杂度） |
| 展示型内容切换（FAQ/详情面板） | accordion 优先，跨大区块再考虑 tabs |
| 平级视图切换 | tabs / segmented |
| 异步加载不确定时长 | 骨架屏（>300ms 才显示） |
| 已知时长的短操作 | 按钮内 loading（禁重复点击） |
| 全局跳转频繁 | command palette（Ctrl+K，`recipes/navigation.md`） |
| 表格行 > 6 列且窄屏常用 | 同时提供卡片形态（布局转换） |
| 用户没说清楚且方案会变 | Ask（一问，不搞问卷） |
| 涉及状态/语义 | 检查 C-01…C-06 是否满足 |
