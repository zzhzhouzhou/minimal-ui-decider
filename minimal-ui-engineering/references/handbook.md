# Handbook — 形色场 UI Handbook 事实来源

> 本文件是 Skill 与 Handbook 的分工点：**这里记录 Handbook 的事实（章节、组件、设计规则），具体实现与完整代码请以线上 Handbook 与源码为准。**
> 不要在本 Skill 中重写 Handbook 的全部内容——它是 Concrete Design / Component Specification。

- 在线阅读（GitHub Pages）：<https://zzhzhouzhou.github.io/UI-handbook/>
- 在线阅读（Cloudflare）：<https://uihandbook.zzhzhou2026.workers.dev>
- 设计规范离线版：<https://github.com/zzhzhouzhou/UI-handbook/blob/main/docs/handbook.md>
- 仓库：<https://github.com/zzhzhouzhou/UI-handbook>
- 技术栈：React 19 + TypeScript 5.9 + Tailwind CSS 4 + Vite 7；运行时依赖仅 `clsx` + `tailwind-merge`；图标为手写内联 SVG；构建产物单文件。

## 事实（facts）

- **10 章 / 115+ 个组件**（`+` 表示仍在持续新增；canonical 计数 = 本文件 §章节与组件清单 的 id 实际数量，同步约定见下文 §组件计数约定），组件等级：`基础 / 进阶 / 高级`。
- 全部组件手写 React + Tailwind，无任何 UI 组件库。
- 实现基调：克制的黑白灰（`zinc-*`）——**把 `zinc-900` 换成品牌色，就是你的设计系统**。
- 深色模式：`.dark` 类 + `dark:` 变体；跟随系统并可手动切换。
- 拖拽/滑动基于 Pointer Events，桌面与触屏行为一致。
- 每个组件含可交互预览、用途说明、实现要点、无障碍细节、可复制核心代码。

## 三大总原则（全篇前提）

1. **克制用色**：绝大部分面积黑、白、灰；彩色只留给最重要的动作和信息。
2. **一切尺寸有出处**：字号、间距、圆角、高度从 token 表取值（`references/tokens.md`）。
3. **照顾所有人**：对比度、触控面积、键盘可用、尊重 reduced motion。

## 章节与组件清单

| 章节            | 组件（id）                                                                                                                                                                                    |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 01 设计基础       | color · typography · spacing · elevation · motion-tokens · states                                                                                                                         |
| 02 基础组件       | button · segmented · badge · avatar · tooltip · tag-input · number-input · countdown-button · split-button                                                                                |
| 03 表单与输入      | input · search · password · floating-label · selection · slider · otp · rating · combobox · select · datepicker · dropzone · color-swatches · multi-select · date-range · form-validation |
| 04 导航         | navbar · tabs · breadcrumb · pagination · sidebar · stepper · dropdown · command(⌘K) · contextmenu · dock · scroll-progress · bottom-tabs · notification-panel · scroll-spy-toc           |
| 05 反馈与覆盖层     | toast · modal · drawer · alert · progress · loading · empty · popover · fab                                                                                                               |
| 06 数据展示       | card · table · accordion · timeline · stats · code · tree · kanban · chat · heatmap · carousel · compare · bento · masonry · description-list · blockquote · virtual-list · transfer      |
| 07 指针交互与背景    | spotlight-bg · parallax · visualizer · magnetic · tilt · spotlight-card · cursor · particles · ripple · knob · glass · beam · mesh-gradient                                               |
| 08 文字、滚动与高级模式 | marquee · typewriter · scramble · countup · reveal · stack · infinite · sortable · confetti · swipe · theme-toggle · clock · gallery · word-reveal · resizable · noise · text-highlight   |
| 09 设计规范标准     | sizing · color-standard · type-standard · z-index · motion-standard · breakpoints · keyboard                                                                                              |
| 10 模式与原则      | layout · responsive · form-ux · darkmode · checklist · error-pattern                                                                                                                      |

组件等级示例：button/badge/avatar/tooltip 为基础；segmented/combobox/sidebar/toast/table 为进阶；command/date-range/kanban/virtual-list/cursor/particles 为高级。

### 组件计数约定（防漂移）

Handbook 会持续新增组件，为避免「清单加了、别处还写旧数字」，约定：

- **canonical 计数处**：本文件 §章节与组件清单 中组件 id 的实际数量。其他文件只引用快照数字，不独立计数。
- **对外表述**统一写「10 章 / **N+** 个组件」，N 为当前快照（2026-09 时为 115）。`+` 是承诺还会增长，因此别处禁止写死「115 个」这类不带 `+` 的说法。
- **新增 / 删除组件时必须同步快照 N** 到以下五处：
  1. 本文件 §事实
  2. `SKILL.md`（三层分工一节）
  3. `README.md`（开头配套说明 + 目录结构）
  4. `references/engineering.md` §项目结构参考（nav.ts 注释）
  5. `CHANGELOG.md`「事实对齐」小节记录
- README §维护与回归 的 Handbook 同步 checklist 按本约定核对；两处数字不一致时，**以本文件清单实际 id 数为准**。

## 常用组件设计规则（Handbook 摘要）

### 按钮类

- 主按钮每页只出现一次；其余用次要/描边。
- 按下轻微缩小；加载中禁止重复点击并显示转圈。
- 危险操作红色 + 二次确认。

### 输入类

- 输入框四部分：标签（永远在上方）、输入区、帮助文字、错误提示（字段正下方，红字+图标，实时更新）。
- 密码框提供显示/隐藏切换；强度在输入时反馈。
- 选项 4–10 个用下拉；超过 10 个或需搜索用可输入下拉（combobox）。
- 日历默认当前月份；今天有特殊标记。

### 导航类

- 当前位置用颜色+字重区分，不加彩色高亮块。
- 层级 >4 用面包屑；步骤 <6 用步骤条。
- 移动端底部导航入口 ≤ 5 个。
- 通知入口带未读标记。

### 反馈类

- 成功轻提示 ~3s 自动消失；错误要等用户处理。
- 加载 >300ms 才显示；空列表给解释 + 下一步动作。
- 弹窗打开时背景不可滚动；Esc 关闭。

### 数据展示类

- 数字右对齐 + 等宽数字；文字左对齐。
- 表格去竖线，只保留极淡横向分隔。
- 空数据显示空状态（图标 + 说明 + 行动按钮）。

## 使用方式

- 需要具体组件结构/代码 → 查线上 Handbook 对应组件，或 `recipes/*`。
- 需要设计规范数值 → `references/tokens.md`。
- 需要设计走查清单 → Handbook 第十一章 / `tests/expected-behaviors.md`。
- 需要反例 → `references/counterexamples.md`。

> 原则：**不要重新发明组件分类体系。现有 Handbook 与源码是事实来源。**
