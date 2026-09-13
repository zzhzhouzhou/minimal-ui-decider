# Minimal — 默认视觉基线与决策策略

> 本文件是 Minimal 的 **canonical 规范**：极简在本 Skill 中既是默认视觉语言（Visual Baseline），也是一套决策策略（Decision Strategy）。其他文件引用这里，不重复定义。

## 正式定义

> **Minimal UI is a controlled system for reducing unnecessary complexity — visual, informational, interaction, and systemic — while preserving clarity, accessibility, discoverability, and task completion.**
>
> 极简 UI 是一套受控的复杂度削减系统：减少不必要的视觉、信息、交互与系统复杂度，同时保留清晰性、无障碍、可发现性与任务完成能力。

SKILL.md 核心哲学的展开：**Minimalism is not about having less. It is about making fewer unnecessary decisions.** 对于必要但复杂的东西，不要机械删除——组织它、排序它、渐进披露它，让它变得容易理解。

### 四种极简（Effective Complexity = Visual + Cognitive + Interaction + System）

要降低的是**有效复杂度**，不是单看视觉复杂度——「视觉变干净但更难用」就是坏极简：

| 层 | 减少什么 | 手段 |
| --- | --- | --- |
| Visual Minimalism | 装饰、竞争表面、任意颜色、多余边框 / 阴影 / 动效 | token 复用、效果默认关闭 |
| Information Minimalism | 用户必须**同时处理**的信息量（不是数据本身） | hierarchy、grouping、summary、渐进披露 |
| Interaction Minimalism | 不必要的步骤、打断、确认、跳转、重复操作 | 合并路径、按需确认 |
| System Minimalism | 任意数值、一次性例外、重复模式、组件变体膨胀 | tokens、组件复用、角色化 |

三个概念不要混用：**Minimalism**（视觉 / 系统克制）≠ **Simplicity**（交互 / UX 清晰）≠ **Consistency**（系统可预测）。

## 何时使用（Intent · 三种状态）

| 用户输入 | 处理 |
| --- | --- |
| 明确指定风格（如「紫色渐变 Cyberpunk」） | 用户风格优先于本文件，仅 CRITICAL floor 不可突破（红底绿字这类低对比组合仍须指出风险） |
| 模糊审美意图（如「高级一点」） | Intent Translation（`visual.md`）：restrained palette / hierarchy / typography / spacing / subtle motion，而非自动堆紫渐变 + glow |
| 未指定风格且项目无主导风格 | 启用本文件：Minimal 作为默认视觉基线 |

- **项目适配**：现有项目文档（AGENTS.md / CLAUDE.md / 设计系统文档）的明确约定优先于本文件的默认值（Visual Baseline 与 S/Y）；M 级按 `decision.md` §Exemptions 处理；C 层是 floor，不可被任何项目约定绕过。
- 优先级：**CRITICAL floor > 用户明确风格 > Intent Translation > 本 Baseline > 通用 S/Y 默认**。

## Decision Strategy（遇到冲突怎么做）

```text
 1. Identify what the task needs.
 2. Remove unnecessary decisions.
 3. Preserve necessary context.
 4. Organize necessary complexity.
 5. Expose important actions.
 6. Defer low-frequency complexity（渐进披露）.
 7. Establish hierarchy.
 8. Reduce visual competition（先降竞争，再谈删信息）.
 9. Validate discoverability.
10. Validate task completion.
```

判据：**Effective Complexity 下降才算赢**。视觉元素变多但认知成本下降（加标签、加状态信息、加语义色）可以是更好的极简——视觉复杂度 ↓ 与认知复杂度 ↑ 完全可能同时发生（反例 E-21）。

## Visual Baseline（默认长什么样）

一句话：**Neutral-first + Semantic color + Intentional accent**——以中性为基础、以克制为倾向、以系统为约束、以目的为依据（Default to restraint; opt into emphasis）。

### Color

- Base / Surface / Border / 主次文字默认取**中性色阶**；颜色留给：主操作、选中态、状态、反馈、品牌、数据可视化。
- Accent **有意使用**（层级 / 交互 / 状态 / 品牌），不在界面上均匀铺开（不要每个 Card、每个 Section、每个按钮一个颜色）。
- 语义色含义全站稳定（success / warning / danger 各司其职）。

### Effects（Default Off，不是禁止）

渐变、glow、glassmorphism、backdrop blur、大面积高饱和背景、装饰纹理属于 **atmosphere 层**，默认不进入基础 UI（按钮、卡片、输入框、导航、表格、弹窗）。提供品牌 / 氛围 / 层级价值时才 opt-in；装饰性动效 ≤ 一两处（S-03）。

### 分离与表面（Separation priority）

```text
spacing → surface 对比 → border → shadow → 强效果
```

低层手段够用就不叠加高层手段：两个区域已靠 spacing / 背景对比区分时，不再叠 border + shadow + glow。**Prefer structural separation over decorative elevation.**

### Composition（反 Cardification）

- **Card 是分组工具，不是默认容器**：页面 = 内容 + 区块 + 控件 + 数据 的扁平结构，分组需要时才引入容器（不要 Card 套 Card）。
- Image / illustration 必须承担语义 / 品牌 / 情绪角色；只为填空白 → 质疑它。

### Typography 与层级数量

- **定义角色而非零散属性**：Text（primary / secondary / muted）、Surface（background / elevated / interactive）、Border（default / strong）；角色数量少而稳定，取值见 `tokens.md`。
- 字重有限集（regular / medium / semibold 足够），不为了「高级感」默认引入个性 display 字体。

### Radius / Shadow

- 有限的 radius 档位（6 / 12 / 16px，见 `tokens.md` §圆角），禁止「胶囊化」默认（一切 rounded-full / 3xl）。
- Shadow 按 elevation 使用（`tokens.md` §Elevation 六档），不作为默认装饰。

### Motion

低存在感：短、有目的、可被打断。**动效解释变化（explain change），而不是装饰静止（decorate stillness）**（见 `motion.md`）；页面级装饰动画默认关闭。

### 风格速览（Preset summary）

```yaml
style: minimal          # default baseline（用户明确风格 > 本文件 > 通用默认）
color:    neutral-first + semantic + intentional accent
effects:  gradient / glow / glass / texture = opt-in（default off）
surface:  quiet；card = grouping tool, not default container
shape:    radius scale 6/12/16，no pill-by-default
shadow:   elevation-only
type:     role-based, limited weights, no display font by default
motion:   low-intensity, functional only
density:  context-dependent；先降视觉竞争，再谈删信息
```

## Style Matrix（逐维倾向 + 决策问题）

| Dimension | Minimal 倾向 | Agent 应该问的问题 |
| --- | --- | --- |
| Hierarchy | 强、显式 | 用户第一眼应该看到什么？第二眼呢？ |
| Density | 低～中（允许高信息密度） | 哪些信息可以合并？高密度区靠层级与分组处理，不靠删功能 |
| Typography | 承担层级 | 能否靠字号 / 字重建立层级，而不加新表面？ |
| Spacing | 有意图 | 留白是在分组还是单纯撑空间？ |
| Color | 克制但有语义 | 这个颜色承担什么语义？删掉它层级还在吗？ |
| Surface | 少而明确 | 这个容器真的需要独立表面吗？ |
| Border | 谨慎保留 | 没有 border 会不会影响分组 / 边界 / 输入暗示？ |
| Shadow | 少量 | 阴影在表达层级还是装饰？ |
| Radius | 一致、克制 | 圆角有设计或交互作用吗？（不是越小越极简） |
| Decoration | 极少 | 去掉它会损失什么？（复杂度测试） |
| Motion | 克制但有 | 动画在反馈还是表演？反馈类动效不因极简而消失 |
| Icon | 功能优先 | 这个 icon 增加理解了吗？（含糊的 icon 不如明确的文字标签） |
| Complexity | 低 | 有没有更简单的表达？ |

## 常见误读（每一条都是失败模式）

- Minimal = 全灰 / 黑白 → 错。颜色承担层级、状态与身份（Stripe 界面色彩丰富，视觉决策依然克制）。
- Minimal = 删除说明文字 / label / 反馈 → 错。删必要信息是破坏可用性，不是简化。
- Minimal = 无限堆留白 → 错。留白服务层级与分组，padding 大小不等于高级感。
- Minimal = 全部去边框 → 错。border 承担分组、边界与输入暗示。
- Minimal = 圆角归零 → 错。那是 Brutalist 的语言，Minimal 不排斥 radius。
- Minimal = 不用动画 → 错。反馈 / 连续性动效要保留（S-03），删的是无目的 motion。
- Minimal = 所有页面套同一模板 → 错。那是 generic：极简可以有个性（低无意义复杂度 + 品牌语言）。

## 从真实产品提炼的决策规则

| 来源案例 | 提炼出的规则 |
| --- | --- |
| Apple | Simplicity ≠ Minimalism：有时增加上下文信息反而让体验更简单 |
| Linear | 极简与高信息密度兼容：靠层级、分组、命令化路径与克制的状态表达 |
| Vercel / Geist | Minimal 靠系统控制而非审美感觉：定义角色，不定义零散属性 |
| Stripe | 颜色可以丰富，视觉决策保持克制：Minimal ≠ monochrome |
| Notion | 功能多与界面克制并存：组织复杂度，而不是消灭复杂度 |
| Arc | 有性格的极简：个性来自品牌语言与布局，无意义复杂度保持低 |

> 研究方法：按场景（login / dashboard / settings / table / empty state…）观察多个真实产品如何做减法，按「观察 → 可迁移规则 → 反例」转化，不停留在「好看」。

## 边界自查表（看起来更极简 ≠ 结果更好）

| 设计决策 | 看起来更 Minimal？ | 真正结果 |
| --- | --- | --- |
| 隐藏全部导航 | 是 | 可能更难找到 |
| 删除 helper text | 是 | 可能增加理解成本 |
| 删除状态信息 | 是 | 可能增加猜测 |
| 全部 icon-only | 是 | 可能降低可发现性 |
| 大量堆留白 | 是 | 可能降低信息效率 |
| 所有内容套 Card | 是 | 可能破坏信息结构 |
| 黑白灰一统天下 | 是 | 可能破坏语义与品牌 |
| 高频功能塞进 More | 是 | 可能降低效率 |
| 增加少量上下文 | 否 | 可能降低认知成本 |
| 增加语义颜色 | 否 | 可能提高可读性 |
| 增加明确文字标签 | 否 | 可能提高可发现性 |

> 「看起来更极简」的项未必错——判断标准是 Effective Complexity 是否下降（Decision Strategy 第 9 / 10 步：可发现性与任务完成）。

## 走查

Minimal 任务走查并入统一清单 `checklist.md`（§视觉附加项），逐维对照本文件 Style Matrix 与 Visual Baseline。
