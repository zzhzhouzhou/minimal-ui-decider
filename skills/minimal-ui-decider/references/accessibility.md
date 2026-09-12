# Accessibility — 无障碍（硬底线）

Accessibility 不是附加项，是 C 级底线（C-01…C-06）。任何实现都从这里的检查开始。

## Semantic HTML

优先使用原生元素，禁止用 `<div>` 模拟原生交互：

```html
<button> <a> <nav> <main> <form> <label> <input> <select> <textarea>
```

错误：

```tsx
<div onClick={handleClick} role="button">…</div>
```

正确：直接 `<button>`。

## Keyboard（C-01）

- 核心任务必须能仅用键盘完成。
- 焦点必须清晰可见（focus-visible 外框），不要把 `outline: none` 留在生产代码里。
- 覆盖层（Modal / Drawer / Menu / Combobox）：
  - 打开时焦点移入。
  - Tab 焦点圈在覆盖层内（focus trap）。
  - Esc 关闭。
  - 关闭后焦点回到触发器。
  - **初始焦点**：普通 dialog 落在首个可交互元素；`alertdialog`（危险确认）默认落在**安全选项**（如「取消」）——危险动作（删除/清空）永不默认聚焦，防止键盘用户一次 Enter 触发不可逆操作。实现上把 `data-autofocus` / 初始 focus 放在安全按钮。
- 自定义组件遵循原生模式：Tabs 用 roving tabindex + 方向键；Combobox 用 ↑↓ 移动选项、Enter 选择、Esc 收起；Switch 用空格切换。控件类型 → 键盘模式速查见 `recipes/forms.md` §键盘模式速查。

## Accessible name（C-02）

Icon-only 元素必须有稳定名称：

```tsx
<button aria-label="关闭" onClick={close}>
  <svg>…</svg>
</button>
```

- 名称来自可见文字、`aria-label` 或 `aria-labelledby`，并保持稳定。
- 有可见文字的按钮不需要重复 aria-label。

## Form Label（C-04）

placeholder 不是 label：

```tsx
{/* 错误 */}
<input placeholder="邮箱" />

{/* 正确 */}
<label htmlFor="email">邮箱</label>
<input id="email" placeholder="you@example.com" />
```

- 错误信息用 `aria-describedby` 关联到字段，并用 `aria-invalid` 标记。

## Non-color information（C-05）

error / success / warning / selected / required 不能只靠颜色表达，要配合：

- 文字（「邮箱格式不正确」）
- 图标（⚠ / ✓）
- 结构（边框、背景同时有文字）
- 状态指示（`aria-invalid`、`aria-selected`）

## Contrast（canonical 定义）

对比度要求的 **canonical 来源是本节**，其他文件只引用不重复定义：

| 内容 | 最低亮度差 |
| --- | --- |
| 正文小字 | 4.5 : 1 |
| 大字（约 24px 以上） | 3 : 1 |
| 图标、边框、开关等控件 | 3 : 1 |
| 正文加强版 | 7 : 1 |

深浅模式分别检查；色觉障碍用户不依赖颜色信息。自动化核对：axe-core 的 `color-contrast` 规则（见 `checklist.md` §自动化检查映射）。

## Focus

- 键盘焦点对所有可交互元素可见。
- 覆盖层聚焦管理见上。
- 锚点跳转留出头部空间：`scroll-padding-top`。

## Reduced motion

CSS 层——全局降级：

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

JS 层——JS 驱动的动画（count-up / 涟漪 / 拖拽倾斜 / 粒子等）必须读取同一偏好并直接呈现最终状态：

```ts
const mq = window.matchMedia("(prefers-reduced-motion: reduce)");
let reduced = mq.matches;                    // 初始化时读一次
mq.addEventListener("change", (e) => {       // 用户运行中切换偏好时跟随
  reduced = e.matches;
});
```

- `reduced` 为 true 时：跳过动画帧循环，直接设置终值/最终状态。
- 用户关闭动效时界面应立即呈现最终状态，不要等下一次刷新。

## Overlays / Dialog 要点

- 对话框标注角色与标题：`role="dialog"` + `aria-modal="true"` + `aria-labelledby`；危险确认用 `role="alertdialog"`。
- 打开时背景不可滚动（锁 body 滚动）；关闭恢复。
- Esc 关闭；点击遮罩关闭需可预测。
- 关闭动画期间：防止再次打开 / 焦点落入正在卸载的弹层（`closing` 状态防护，见 `motion.md` §进入/退出模式）。
- Toast / 重要变化要主动告知（`role="status"` / `aria-live="polite"`；错误用 `role="alert"`）。

## Touch target（来自 Handbook）

- 可点面积 ≥ 44 × 44 CSS px；图标 16–24px 时周围补透明区撑到 44px。
- 相邻可点元素间隔 ≥ 8px。

> 走查：全局清单见 `checklist.md`（本文件不单独维护清单）。
