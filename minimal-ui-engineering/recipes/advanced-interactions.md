# Recipes · Advanced Interactions — 高级交互与视觉效果

对应 Handbook 章节 07（指针交互与背景）与 08（文字、滚动与高级模式）。
这里的所有效果**不是默认开启**——属于 Y（MAY），使用前必须通过复杂度测试（`references/decision.md`）：

1. 它是否服务当前任务？
2. 它是否降低了其他复杂度？
3. 删除它是否会明显损害 usability / accessibility / 用户明确要求的视觉结果 / 信息层级？

三个都是 No ⇒ 不要加。用户没有明确要求视觉结果时，默认不加装饰性效果。

## 通用实现纪律（所有高级效果）

- **Pointer Events 而非 mouse events**：`pointerdown/move/up` + `setPointerCapture`，桌面与触屏一致。`setPointerCapture` 用 try/catch 包裹——个别触屏环境会抛异常，失败时降级为 `document.elementFromPoint` 跟踪（见 `counterexamples.md` E-14）。
- **rAF 中只写 transform/opacity**；effect 卸载时 `cancelAnimationFrame` / 移除监听（不留泄漏，见 `counterexamples.md` E-13）。
- **尊重 reduced motion**：关闭时静态呈现最终状态；JS 驱动的效果读取 `matchMedia('(prefers-reduced-motion: reduce)')` 并监听变化（模式见 `accessibility.md` §Reduced motion）。
- **不承载核心功能**：效果失效时界面仍完整可用（键盘/触屏可完成全部任务）。
- 尽量复用项目现成动画令牌与工具类（`tokens.md`：marquee/shimmer/beam/float/dot/ripple/scale-in/slide-* 及 `.bg-dots` `.bg-grid` `.noise`）。

## Y 编号对照表

> Y 规则的 ID 在 `references/decision.md` §Y 声明，**本表是「效果 ↔ Y 编号 ↔ 等级」的 canonical 对照**；实现细节看下方各节。基础级增强（countup / reveal / swipe / confetti 等）超出原 Y-01…Y-12 名录，按 Y-13 起顺延编号，同样属 MAY，使用前跑复杂度测试。

| 效果 | Y 编号 | 等级 |
| --- | --- | --- |
| Spotlight（bg / card） | Y-01 | 高级 |
| Magnetic | Y-02 | 进阶 |
| Tilt | Y-03 | 进阶 |
| Particles | Y-04 | 高级 |
| Custom cursor | Y-05 | 高级 |
| Parallax | Y-06 | 进阶 |
| Glass | Y-07 | 基础 |
| Noise | Y-08 | 基础 |
| Scramble | Y-09 | 进阶 |
| Beam | Y-10 | 高级 |
| Knob / Stack（3D 类） | Y-11 | 进阶 |
| Theme toggle w/ View Transition | Y-12 | 高级 |
| Mesh gradient | Y-13 | 进阶 |
| Ripple | Y-14 | 基础 |
| Marquee | Y-15 | 基础 |
| Typewriter | Y-16 | 基础 |
| Count up | Y-17 | 基础 |
| Reveal / Word reveal | Y-18 | 基础/进阶 |
| Infinite scroll | Y-19 | 进阶 |
| Sortable（拖拽排序） | Y-20 | 进阶 |
| Swipe（左滑删除） | Y-21 | 高级 |
| Confetti | Y-22 | 进阶 |
| Gallery / Resizable | Y-23 | 进阶 |
| Visualizer | Y-24 | 高级 |
| Text highlight | Y-25 | 进阶 |

## 指针交互与背景（章节 07）

### Ripple（涟漪点击）— 基础
点击位置生成扩散圆（`animate-ripple`）+ 移除；键盘激活也触发（focus-visible 反馈）。

### Glass（毛玻璃）— 基础
```tsx
<div className="backdrop-blur-md bg-white/60 border border-white/40 rounded-xl">…</div>
```
浅底上慎用；背景有内容时才可感知；保证对比度。

### Magnetic（磁性按钮）— 进阶
指针靠近时按钮向指针偏移（`translate3d` 插值）；触屏禁用；纯增强。

### Knob（旋钮）— 进阶
`role="slider"` + 方向键；Pointer 旋转拖动；触屏可用。

### Parallax（鼠标视差）— 进阶
背景层随指针位移（transform + rAF 插值）；移动端无指针→禁用或静态。

### Tilt（3D 倾斜卡片）— 进阶
`perspective` + `rotateX/rotateY` 随指针；`will-change: transform` 克制使用；reduced motion 时关闭。

### Spotlight BG（背景随鼠标移动）— 高级
径向渐变跟随指针（CSS 变量 + `radial-gradient`）；桌面增强。

### Spotlight Card（边框聚光卡片）— 高级
边框高光跟随指针位置（CSS 变量定位 + mask）；纯装饰。

### Visualizer（音乐律动条）— 高级
AudioContext 分析 + rAF 绘制音量条；无音频源时显示空态；暂停/清理 on unmount。

### Cursor（自定义光标）— 高级
替换/增强光标（隐藏原生 cursor 不可取——必须保留可访问性：键盘焦点、点击目标不变；仅桌面增强，触屏禁用）。

### Particles（粒子连线背景）— 高级
Canvas 粒子 + 连线；`devicePixelRatio` 适配 + 数量上限（性能）；reduced motion 降级为静态或关闭。

### Beam（边框光束）— 高级
沿路径的光束（`offset-path` 动画，真实项目 `--animate-beam`）；装饰性边框效果。

### Mesh Gradient（流动渐变背景）— 进阶
多个径向渐变 + `hue-rotate`/位移动画（慢速）；大色块呼吸感；配合文字对比度检查。

## 文字、滚动与高级模式（章节 08）

### Marquee（无限跑马灯）— 基础
内容复制两份 + `translateX(-50%)` 循环（真实项目 `--animate-marquee`）；`prefers-reduced-motion` 时静止；可暂停。

### Typewriter（打字机）— 基础
定时逐字追加（`--animate-caret` 光标）；完成停止；卸载清理 timer；光标用 `aria-hidden` 装饰。

### Count Up（数字滚动）— 基础
`requestAnimationFrame` 插值到目标值；reduced motion 直接显示终值；用等宽数字防跳动；`aria-live="polite"` 或读屏友好（最终值可访问）。

### Scramble（文字乱码解码）— 进阶
字符随机替换 + 逐位收敛（rAF）；最终文字必须完整、语义不变；`aria-label` 提供最终文本。

### Reveal（滚动显现）— 基础
`IntersectionObserver` 进入视口加 `animate-fade-up`；reduced motion 直接可见；只动 transform/opacity。

### Word Reveal（滚动逐字点亮）— 进阶
同 Reveal，逐字/逐词点亮；克制节奏。

### Stack（堆叠卡片）— 进阶
多层叠卡 + 拖出（Pointer Events）；触屏可拖。

### Infinite Scroll（无限滚动）— 进阶
`IntersectionObserver` 哨兵加载下一页；loading / empty / error 态齐全；加载时避免布局跳动。

### Sortable（拖拽排序）— 进阶
Pointer 拖拽 + 占位/位移；键盘替代：上移/下移按钮或 ↑↓+Enter；`aria-label` 说明可排序。

### Swipe（左滑删除）— 高级
Pointer 水平拖动 + 阈值触发；必须有关闭/撤销路径；触屏与桌面一致；删除后 Toast + 可撤销（S 级建议）。

### Confetti（纸屑庆祝）— 进阶
Canvas 粒子庆祝；只在明确的事件（如完成任务）触发，不自动播放；reduced motion 忽略。

### Gallery（伸缩画廊）— 进阶
行内容器，某一项展开——小面积可用 `grid-template-rows` / flex 比例过渡（见 `motion.md` 例外），不要用 JS 逐帧改 width；键盘：方向键移动焦点、Enter 展开；`aria-expanded`。

### Resizable（可拖拽分栏）— 进阶
分隔条拖动（Pointer + min/max 限制）；键盘：分隔条 `role="separator"` + 方向键调整；窄屏回退为堆叠（不复用分栏）。

### Theme Toggle w/ View Transition（主题切换转场）— 高级
`document.startViewTransition(() => toggleDark())` + clip-path 圆形扩散（真实项目模式）；降级：不支持时直接切换；reduced motion 直接切换。

### Clock（时钟）— 基础
定时器每秒更新（文本毫秒舍入）；卸载清理 interval；`aria-live` 不必要（避免读屏打扰）——用 `role="timer"` 或纯装饰。

### Noise（噪点纹理）— 基础
SVG feTurbulence 纹理（真实项目 `.noise`）——纯 CSS class，零 JS。

### Text Highlight（搜索高亮）— 进阶
`<mark>` 包裹匹配文本（语义高亮）；实时输入重算时可防抖；计数/导航到下一个命中处（`aria-current` 或 focus 移动）。

## 选择效果时的快速判断

| 用户诉求 | 默认建议 |
| --- | --- |
| 想让卡片更精致 | hover 极淡阴影 + 边框 + 轻微 scale（不是 tilt/beam） |
| 想让品牌页有氛围 | 一个背景效果（mesh gradient / dots / noise）就够，不加多个 |
| 想让按钮更跟手 | press scale + 150ms 变色（不是 magnetic） |
| 想让文字有张力 | typography / spacing / count-up（不是 scramble） |
| 庆祝/成功 | confetti 仅事件触发一次 |
| 任何装饰效果 | 先跑复杂度测试（decision.md） |

## 检查

高级效果走查并入统一清单 `references/checklist.md`（§高级效果附加项），本文件不再单独维护清单。