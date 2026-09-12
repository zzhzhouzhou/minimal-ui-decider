# Tokens — Canonical Token Definitions

> 本文件是 **canonical token definitions 的唯一集中地**。其他文件引用 token 名称与这里的数值，不在多处重复完整定义。
> 注意：这不禁止其他文档出现规则性数字（如「触控 ≥ 44px」），需要集中的是 canonical definitions。

## 色彩

| Token | 浅色 | 深色 | 用途 |
| --- | --- | --- | --- |
| background | `zinc-50 / white` | `zinc-950`（非纯黑） | 页面背景 |
| surface | `white` | `zinc-900/80`（比背景略亮） | 卡片背景 |
| border / divider | `zinc-200`（比背景深一级） | `zinc-800`（比背景亮一级） | 分割线、边框 |
| foreground | `zinc-900`（近黑非纯黑） | `zinc-100`（近白非纯白） | 主要文字 |
| muted | `zinc-500` | `zinc-400` | 次要文字 |
| primary | 品牌色（如把 `zinc-900` 换成品牌色即成为你的设计系统） | 同左（深浅模式可微调） | 强调、主操作 |
| success | 绿 | 绿（微调亮度） | 成功 |
| warning | 黄 | 黄 | 警告 |
| danger | 红 | 红 | 错误、危险 |

- 灰阶约占界面 90%，强调色约 10%，语义色少量。
- 深色模式不是简单反色；层级越高背景越亮。
- 对比度：正文 ≥ 4.5:1，大字/图标/控件 ≥ 3:1，加强版 ≥ 7:1（深浅分别检查）。
  > Canonical 对比度定义与检查表见 `accessibility.md` §Contrast；本行仅为速记。

Tailwind 4 定义示例（@theme）：

```css
@theme {
  --color-background: var(--color-zinc-50);
  --color-surface: var(--color-white);
  --color-foreground: var(--color-zinc-900);
  --color-muted: var(--color-zinc-500);
  --color-primary: var(--color-zinc-900);
  --color-danger: var(--color-red-600);
  --color-success: var(--color-green-600);
  --color-warning: var(--color-amber-500);
}
```

## 字号与行高（成对使用，不单独调整）

| 层级 | 字号 | 行高（约倍数） | 用途 |
| --- | --- | --- | --- |
| display（特大标题） | 48px | 1.1 | 落地页主标题 |
| h1 | 32px | 1.25 | 页面标题 |
| h2 | 24px | 1.33 | 区块标题 |
| h3 | 20px | 1.4 | 卡片标题 |
| body | 16px | 1.6–1.75 | 正文 |
| caption | 12px | 1.5 | 时间、备注（可全部大写） |

- 数字用等宽数字：`font-feature-settings: "tnum"`。
- 字体栈（参考）：`Inter, PingFang SC, Hiragino Sans GB, Microsoft YaHei, system-ui`；代码：`JetBrains Mono, ui-monospace, Menlo`。
- 中文正文行长 30–40 字/行；标题字距略微收紧。

## 间距（只使用 4 的倍数）

```text
4 · 8 · 12 · 16 · 24 · 32 · 48 · 64   (px)
```

| 场景 | 建议 |
| --- | --- |
| 组件内部（文字与边框） | 4–12px |
| 组件与组件之间 | 16–24px |
| 区块与区块之间 | 48–96px |

## 圆角

| 元素 | 语义档 | 圆角 | Tailwind 4 类名 |
| --- | --- | --- | --- |
| 小元素（开关、小按钮、tag） | sm | 6px | `rounded-md` |
| 卡片 | md | 12px | `rounded-xl` |
| 弹窗 | lg | 16px | `rounded-2xl` |

- 圆角种类一个界面不超过三种。
- 嵌套元素：里面圆角 = 外面圆角 − 两者间距。
- **注意**：本表 sm / md / lg 是语义档名，与 Tailwind 内置档位（`rounded-sm` 4px / `rounded-md` 6px / `rounded-lg` 8px / `rounded-xl` 12px / `rounded-2xl` 16px）**不同名不同值**。写类名时以上表映射列为准，禁止按档名直觉套用（如弹窗写 `rounded-lg` 会得到 8px，违反 16px 档）。

## 尺寸（控件高度）

| 元素 | 高度 |
| --- | --- |
| 密集工具栏按钮 | 32px |
| 常规按钮 / 输入框 | 36px |
| 主要按钮 / 大按钮 | 44px |

- 密集 / 常规高度（32 / 36px）仅适用于鼠标精确输入的桌面场景；触屏可达的控件一律 ≥ 44×44（不足时补透明区撑大）。
- 触控目标 ≥ 44 × 44 CSS px（图标 16–24px 时补透明区）。
- 相邻可点元素间隔 ≥ 8px。
- 图标 16 / 20 / 24px；头像 24 / 32 / 40 / 56px。

## Elevation（z-index，全站仅 6 档）

| 层级 | 数值 | 典型元素 |
| --- | --- | --- |
| content | 0 | 普通内容 |
| hover-card | 10 | 悬停浮起的卡片 |
| popover | 20 | 下拉菜单、气泡 |
| sticky | 40 | 粘性头部、侧边栏 |
| overlay | 50 | 遮罩 + 弹窗 |
| toast | 60 | 全局提示，永远最上 |

- 用变量统一管理，禁止随手写大数字。
- 半透明遮罩出现时，比它层级低的内容不可点击。

## Motion 时长

| 场景 | 时长 |
| --- | --- |
| hover / press / color change | 150ms |
| menu / dialog 出现 | 200–300ms（从小到大 + 淡入） |
| 退出 | 比进入快约 20% |
| 页面转场 | 400–500ms（仅大面积变化） |

- 只对 transform / opacity 做动画。
- 尊重 `prefers-reduced-motion`。

## 断点（canonical，本表为唯一定义处）

> 本表是断点的 canonical 来源（五档）；`responsive.md` 只引用不重复定义。640–767px（tablet-sm）专指平板竖屏/大屏手机横屏，与 ≥768px 的 tablet 区分。

| 名称 | 宽度 | 典型设备 |
| --- | --- | --- |
| mobile | < 640px | 竖屏手机 |
| tablet-sm（平板竖屏） | ≥ 640px | 平板、大屏手机横屏 |
| tablet（平板横屏） | ≥ 768px | 平板 |
| laptop（笔记本） | ≥ 1024px | 笔记本、小显示器 |
| desktop（桌面） | ≥ 1280px | 台式机 |

- 移动优先；320px 不允许横向滚动。
- 内容宽度：阅读类约 672px，应用类约 1152px。
- 列数：手机 1 列、平板 2 列、桌面 3–4 列。

## 动效令牌（复用的动画 token，来自真实项目 @theme）

```text
marquee 24s linear infinite        跑马灯
shimmer 1.6s linear infinite       微光扫描（骨架屏）
spin-slow 6s linear infinite       慢旋转
beam 4s linear infinite            边框光束
float 4s ease-in-out infinite      上下浮动
dot 1.2s ease-in-out infinite      加载点
ripple 0.6s ease-out forwards      涟漪
fade-up 0.5s ease-out both         淡入上移
caret 1s step-end infinite         光标闪烁
scale-in 0.18s ease-out both       缩放进入（弹窗/菜单）
slide-in-right 0.28s cubic-bezier(0.32, 0.72, 0, 1) both
slide-in-left  0.28s cubic-bezier(0.32, 0.72, 0, 1) both
slide-up       0.28s cubic-bezier(0.32, 0.72, 0, 1) both
```

背景纹理工具类（真实项目）：`.bg-dots`（点阵）、`.bg-grid`（网格）、`.noise`（噪点）、`.skeleton`（骨架微光），深浅模式各有变体。