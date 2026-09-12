# Recipes · Basic Components — 基础组件

对应 Handbook 章节 02（button · segmented · badge · avatar · tooltip · tag-input · number-input · countdown-button · split-button）。
数值令牌见 `references/tokens.md`；交互状态见 `references/interaction.md`。

## Button

- 等级：基础。尺寸：密集 32px / 常规 36px / 主要 44px（`h-8 / h-9 / h-11`）。
- 变体：primary（实心）、secondary（浅底）、outline（描边）、ghost、danger（红 + 二次确认）。
- 状态：hover / focus-visible / active（`active:scale-[0.98]`）/ disabled / loading。
- 主按钮每屏一次；loading 禁用 + spinner + 禁止重复点击。

```tsx
type ButtonProps = React.ButtonHTMLAttributes<HTMLButtonElement> & {
  variant?: "primary" | "outline" | "ghost" | "danger";
  size?: "sm" | "md" | "lg";
  loading?: boolean;
};

const sizeMap = {
  sm: "h-8 px-3 text-sm",
  md: "h-9 px-4 text-sm",
  lg: "h-11 px-5 text-base",
};
const variantMap = {
  primary: "bg-zinc-900 text-white hover:bg-zinc-700",
  outline: "border border-zinc-300 text-zinc-900 hover:bg-zinc-100",
  ghost: "text-zinc-900 hover:bg-zinc-100",
  danger: "bg-red-600 text-white hover:bg-red-500",
};

export function Button({ variant = "primary", size = "md", loading, disabled, children, ...props }: ButtonProps) {
  return (
    <button
      type="button"
      disabled={disabled || loading}
      aria-busy={loading || undefined}
      className={cn(
        "inline-flex items-center justify-center gap-2 rounded-md font-medium transition-colors duration-150",
        "focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-zinc-900",
        "active:scale-[0.98] disabled:pointer-events-none disabled:opacity-50",
        sizeMap[size],
        variantMap[variant],
      )}
      {...props}
    >
      {loading && <Spinner className="size-4 animate-spin" />}
      {children}
    </button>
  );
}
```

## Segmented（分段控制器）

- 等级：进阶。单选互斥组；用 `role="radiogroup"` + 方向键左右切换；选中态不得只用颜色（C-05）——同时用字重/实心底 + `aria-checked`。
- 窄屏允许横向滚动，但保证 44px 可点高度。

```tsx
<div role="radiogroup" aria-label="视图" className="inline-flex rounded-md bg-zinc-100 p-1">
  {options.map((o, i) => (
    <button
      key={o.value}
      role="radio"
      aria-checked={value === o.value}
      className={cn("h-8 rounded px-3 text-sm", value === o.value ? "bg-white font-medium shadow-sm" : "text-zinc-500 hover:text-zinc-900")}
      onClick={() => onChange(o.value)}
    >
      {o.label}
    </button>
  ))}
</div>
```

## Badge / Tag

- 等级：基础。纯展示用 `<span>`；若可关闭（Tag 带删除），用 `<button aria-label="移除 xxx">`。
- 色彩语义 + 文字/图标双通道；圆角 `rounded-md`（6px）或更小。

```tsx
<span className="inline-flex items-center gap-1 rounded-md bg-zinc-100 px-2 py-0.5 text-xs text-zinc-700">
  未发布
</span>
```

## Avatar

- 等级：基础。尺寸 24 / 32 / 40 / 56px；无图时显示姓名首字 + `aria-label` 兜底。

```tsx
<img src={src} alt={name} className="size-10 rounded-full object-cover" />
```

## Tooltip

- 等级：基础。悬浮/聚焦显示；触屏无法 hover——trigger 同时支持 focus，或用点击/长按替代。
- 无障碍：trigger `aria-describedby` 指向 tooltip；tooltip 定位用 `fixed`/绝对定位 + 层级 20（popover）。

```tsx
<span className="group relative inline-flex" tabIndex={0} aria-describedby="tip-save">
  保存
  <span id="tip-save" role="tooltip" className="pointer-events-none absolute bottom-full left-1/2 mb-2 -translate-x-1/2 rounded-md bg-zinc-900 px-2 py-1 text-xs text-white opacity-0 group-hover:opacity-100 group-focus-visible:opacity-100">
    快捷键 Ctrl+S
  </span>
</span>
```

## Tag Input（标签输入）

- 等级：进阶。输入框 + Enter 生成 tag + 退格删除最后一项 + 删除按钮（`aria-label`）。
- 键盘：Enter 提交、Backspace 删尾、焦点在输入框即可操作。

## Number Input（数字步进器）

- 等级：进阶。左减右加；支持键盘↑↓；`aria-valuemin/valuemax/valuenow` 或直接用原生 `<input type="number">` + 自定义按钮。
- 点击步进按钮时不要让焦点丢失。

## Countdown Button（倒计时按钮）

- 等级：进阶。点击后禁用并倒计时（如「重新发送 (59s)」）；倒计时结束恢复并重新聚焦。注意组件卸载时清理 timer。

```tsx
function CountdownButton() {
  const [left, setLeft] = useState(0);
  const active = left > 0;
  useEffect(() => {
    if (!active) return;
    const t = setInterval(() => setLeft((s) => (s <= 1 ? 0 : s - 1)), 1000);
    return () => clearInterval(t); // 卸载/结束都清理，不留泄漏（E-13）
  }, [active]);
  return (
    <Button onClick={() => setLeft(60)} disabled={active}>
      {active ? `重新发送 (${left}s)` : "发送验证码"}
    </Button>
  );
}
```

## Split Button（分割按钮）

- 等级：进阶。主按钮 + 箭头下拉（`aria-haspopup="menu"` + `aria-expanded`）；下拉菜单焦点管理（Esc 关闭、焦点回触发器）。

## 检查

基础组件走查并入统一清单 `references/checklist.md`（全局清单 + 表单/覆盖层附加项），本文件不再单独维护清单。