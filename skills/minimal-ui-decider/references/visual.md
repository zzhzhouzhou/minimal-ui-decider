# Visual — 视觉设计

Visual 关注 **hierarchy（层级），而不是装饰数量**。数值令牌见 `references/tokens.md`；这里讲如何决策。

## 总原则（Handbook）

一套界面看起来「高级」，取决于底层规范是否统一，而不是某个组件做得多漂亮：

1. **克制用色**：界面绝大部分面积是黑、白、灰；彩色只留给最重要的动作和信息。强调色出现得越少越有力量。
2. **一切尺寸有出处**：字号、间距、圆角、高度从 token 表取值，不随手填写。
3. **照顾所有人**：对比度达标、触控面积够大、键盘可用、尊重 reduced motion。

> 极简 ≠ 灰度。不要为了「极简」强制所有 UI 使用黑白灰；颜色承担语义职责（background / foreground / muted / primary / danger / success）。完整误读清单见 `references/minimal.md`。

## Intent Translation（用户意图 → 设计策略）

用户通常不用设计系统术语。把意图转译为设计策略，而不是直接套效果：

| User intent | Design interpretation |
|---|---|
| 极简 / 简洁 / clean | 按 `references/minimal.md`（Minimal Style Preset）执行：减少视觉决策，保留必要信息与反馈 |
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

> 「极简 / 简洁」类意图的完整规范（Decision Strategy + Visual Baseline）见 `references/minimal.md`——Minimal 是本 Skill 的默认视觉基线。

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

视觉走查并入统一清单 `references/checklist.md`（全局清单「视觉与动效」组 + §视觉附加项），本文件不再单独维护清单。核对时对照上方 Intent Translation 表与 tokens 数值；极简任务另对照 `references/minimal.md`。