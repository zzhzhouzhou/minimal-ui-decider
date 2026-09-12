# Visual — 视觉设计

Visual 关注 **hierarchy（层级），而不是装饰数量**。数值令牌见 `references/tokens.md`；这里讲如何决策。

## 总原则（Handbook）

一套界面看起来「高级」，取决于底层规范是否统一，而不是某个组件做得多漂亮：

1. **克制用色**：界面绝大部分面积是黑、白、灰；彩色只留给最重要的动作和信息。强调色出现得越少越有力量。
2. **一切尺寸有出处**：字号、间距、圆角、高度从 token 表取值，不随手填写。
3. **照顾所有人**：对比度达标、触控面积够大、键盘可用、尊重 reduced motion。

> 极简 ≠ 灰度。不要为了「极简」强制所有 UI 使用黑白灰；颜色承担语义职责（background / foreground / muted / primary / danger / success）。完整误读清单见 §Minimal Style Preset。

## Intent Translation（用户意图 → 设计策略）

用户通常不用设计系统术语。把意图转译为设计策略，而不是直接套效果：

| User intent | Design interpretation |
|---|---|
| 极简 / 简洁 / clean | 按 §Minimal Style Preset 执行：减少不必要的视觉决策，保留必要信息与反馈 |
| 高级 / premium | typography / spacing / material hierarchy / subtle motion（**不是** glass + gradient + glow） |
| 未来感 | contrast / data geometry / purposeful motion |
| 炫酷 | 可以提高效果强度，但不能破坏 hierarchy |
| Apple-like | restrained surface / typography / spacing / depth / motion |
| 活泼 | softer geometry / stronger color / expressive microinteraction |
| Brutalist | strong contrast / typography，不强制默认 radius/shadow |

这些是转译（intent translation），不是固定模板。「高级」优先考虑：

```text
typography → spacing → hierarchy → material → subtle motion
```

## Minimal Style Preset（默认视觉基线）

极简是本 Skill 的默认视觉语言。它是一种可执行的决策策略，不是一种「少」的外观。

### 何时使用（Intent）

满足任一条件时按 Minimal 执行：

- 用户要求「简洁 / 干净 / 克制 / 安静 / 聚焦 / 极简 / clean / minimal」。
- 用户没有指定视觉风格，且现有项目没有占主导的既定风格（Existing Project First 优先于默认基线）。

### 核心原则

> **减少不必要的视觉决策，保留完成任务所必需的信息与反馈。**

「少」的对象是决策（颜色、表面、装饰、动效的数量），从来不是信息、功能或可访问性。

### Style Matrix（逐维倾向 + 决策问题）

| Dimension | Minimal 倾向 | Agent 应该问的问题 |
| --- | --- | --- |
| Hierarchy | 强、显式 | 用户第一眼应该看到什么？第二眼呢？ |
| Density | 低～中 | 哪些信息可以合并？高密度区靠层级与分组处理，不靠删功能 |
| Typography | 承担层级 | 能否靠字号 / 字重建立层级，而不加新表面？ |
| Spacing | 有意图 | 留白是在分组还是单纯撑空间？ |
| Color | 克制但有语义 | 这个颜色承担什么语义？删掉它层级还在吗？ |
| Surface | 少而明确 | 这个容器真的需要独立表面吗？ |
| Border | 谨慎保留 | 没有 border 会不会影响分组 / 边界 / 输入暗示？ |
| Shadow | 少量 | 阴影在表达层级还是装饰？ |
| Radius | 一致、克制 | 圆角有设计或交互作用吗？（不是越小越极简） |
| Decoration | 极少 | 去掉它会损失什么？（复杂度测试） |
| Motion | 克制但有 | 动画在反馈还是表演？反馈类动效不因极简而消失 |
| Icon | 功能优先 | 这个 icon 增加理解了吗？ |
| Complexity | 低 | 有没有更简单的表达？ |

### 常见误读（每一条都是失败模式）

- Minimal = 全灰 / 黑白 → 错。颜色承担层级、状态与身份（Stripe 界面色彩丰富，视觉决策依然克制）。
- Minimal = 删除说明文字 / label / 反馈 → 错。删必要信息是破坏可用性，不是简化。
- Minimal = 全部去边框 → 错。border 承担分组、边界与输入暗示。
- Minimal = 圆角归零 → 错。那是 Brutalist 的语言，Minimal 不排斥 radius。
- Minimal = 不用动画 → 错。反馈 / 连续性动效要保留（S-03），删的是无目的 motion。
- Minimal = 所有页面套同一模板 → 错。那是 generic：极简可以有个性（低无意义复杂度 + 品牌语言）。

### 从真实产品提炼的决策规则

| 来源案例 | 提炼出的规则 |
| --- | --- |
| Apple | 复杂度集中在有价值的地方——Minimal 不等于处处低视觉复杂度 |
| Linear | 极简与高信息密度兼容：靠层级、分组与克制的状态表达，不靠空 |
| Vercel | 单色系统 + 一致的边界与栅格：一致性本身就是极简 |
| Stripe | 颜色可以丰富，视觉决策保持克制：Minimal ≠ monochrome |
| Notion | 功能多与界面克制并存：降低每一时刻需要处理的视觉复杂度 |
| Arc | 有性格的极简：个性来自品牌语言与布局，无意义复杂度保持低 |

> 研究方法：按场景（login / dashboard / settings / table / empty state…）观察多个真实产品如何做减法，按「观察 → 可迁移规则 → 反例」转化，不停留在「好看」。

## Typography

- 关注 hierarchy / font size / weight / line height / letter spacing / readability。
- 字号与行高成对出现（见 tokens），不允许单独调整。
- 标题字距略微收紧，正文保持默认。
- 数字（金额、时间、表格）用等宽数字（`font-feature-settings: "tnum"`），否则数字变化时左右跳动。
- 中文不用斜体、不用下划线强调（下划线易被误认为链接）；强调用加粗或变色。
- 中文正文行长 30–40 字/行。

## Color

颜色承担语义职责：

```text
background   页面/卡片底色（浅：近白；深：近黑非纯黑）
foreground   主要文字（浅：近黑非纯黑；深：近白非纯白）
muted        次要文字 / 分割线
primary      品牌/强调色（最多 ~10% 面积）
danger       错误/危险
success      成功
warning      警告
```

- 灰阶约 90%，强调色约 10%，语义色少量。
- 一个页面里强调色出现得越少越有力量；三个红色按钮会毁掉主次。
- 深色模式不是简单反色：背景不用纯黑、文字不用纯白，层级越高背景越亮；阴影在深色下改用「稍亮的边框」表达层级。

## Spacing

- 间距远近代表关系远近：相关元素靠近，无关元素拉开。
- 组件内部 4–12px；组件之间 16–24px；区块之间 48–96px。

## Elevation（层级）

- 阴影「几乎看不见」：多层极淡黑色叠加，而不是一大坨深色；层级越高阴影越明显。
- 深色模式阴影不可见，用边框亮度表达层级。
- z-index 全站只允许 6 档（见 tokens），用变量统一管理，禁止随手写大数字。

## 控制尺寸

- 密集工具栏按钮 32px；常规按钮/输入框 36px；主要/大按钮 44px。
- 触控 ≥ 44×44（32 / 36px 密集尺寸仅限桌面鼠标场景，触屏一律 ≥ 44）；相邻 ≥ 8px 空隙。

## 最终走查（视觉部分）

视觉走查并入统一清单 `references/checklist.md`（全局清单「视觉与动效」组 + §视觉附加项），本文件不再单独维护清单。核对时对照上方 Intent Translation 表、§Minimal Style Matrix 与 tokens 数值。