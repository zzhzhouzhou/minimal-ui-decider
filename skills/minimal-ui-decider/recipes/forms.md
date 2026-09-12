# Recipes · Forms — 表单与输入

对应 Handbook 章节 03（input · search · password · floating-label · selection · slider · otp · rating · combobox · select · datepicker · dropzone · color-swatches · multi-select · date-range · form-validation）。

## 表单结构通则（Handbook）

输入框四部分：**标签（永远在上方）→ 输入区 → 帮助文字 → 错误提示**。

- 错误提示：字段正下方，红字 + 图标（不只靠颜色，C-05），随输入实时更新。
- 关联：`<label htmlFor>` + `aria-describedby`（帮助/错误）+ `aria-invalid`。
- 选项 4–10 个用 `<select>`；>10 或需搜索用 combobox。
- 密码框必须提供显示/隐藏切换；强度在输入时反馈。
- 加载 >300ms 才显示提交中状态。

## Input

```tsx
function Field({ label, error, hint, id, ...props }: FieldProps) {
  return (
    <div className="flex flex-col gap-1.5">
      <label htmlFor={id} className="text-sm font-medium text-zinc-900">{label}</label>
      <input
        id={id}
        aria-invalid={!!error || undefined}
        aria-describedby={error ? `${id}-error` : hint ? `${id}-hint` : undefined}
        className={cn(
          "h-9 rounded-md border bg-white px-3 text-sm transition-colors",
          "focus-visible:outline-2 focus-visible:outline-offset-1 focus-visible:outline-zinc-900",
          error ? "border-red-500" : "border-zinc-300",
        )}
        {...props}
      />
      {hint && !error && <p id={`${id}-hint`} className="text-xs text-zinc-500">{hint}</p>}
      {error && (
        <p id={`${id}-error`} role="alert" className="flex items-center gap-1 text-xs text-red-600">
          <svg aria-hidden="true" className="size-3.5">…</svg>
          {error}
        </p>
      )}
    </div>
  );
}
```

## Search

- 输入框 + 放大镜图标（inline SVG）+ 清除按钮（`aria-label="清除"`）。
- 提交语义：`<form role="search">`；无结果给空状态（说明 + 下一步）。

## Password

- 结构同 Input + 右侧「显示/隐藏」切换按钮（`aria-pressed`）。
- 强度反馈在输入时实时给出（如三档弱/中/强，配合文字，不只靠颜色）。

```tsx
<button type="button" aria-label={show ? "隐藏密码" : "显示密码"} aria-pressed={show} onClick={() => setShow(!show)}>
  <EyeIcon className="size-4" />
</button>
```

## Floating Label（浮动标签）

- 等级：进阶。label 常驻在输入框内，聚焦/有值时上浮到边框上。
- 无障碍注意：浮动 label 仍然是 label——用 `<label>` + 位置切换，或 `aria-label` 保持稳定名称；placeholder 不得承担 label 职责（C-04）。

## Selection（Checkbox / Radio / Switch）

- 优先原生控件 + 样式化；保持语义与键盘（空格切换）。
- Checkbox 用 `role="checkbox"` + `aria-checked`；Radio 用 `role="radio"` + 方向键；Switch 用 `role="switch"` + 空格切换 + `aria-checked`。
- 选中态配合文字/勾选图标，不只靠颜色。

```tsx
<button role="switch" aria-checked={on} onClick={() => setOn(!on)} className={cn("h-6 w-11 rounded-full transition-colors focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-zinc-900", on ? "bg-zinc-900" : "bg-zinc-300")}>
  <span className={cn("block size-5 translate-x-0.5 rounded-full bg-white transition-transform", on && "translate-x-[22px]")} />
</button>
```

## Slider

- 等级：进阶。`role="slider"` + `aria-valuemin/max/now`；方向键调整；基于 Pointer Events 拖动（触屏一致）。
- 拖动时 `setPointerCapture`；禁用态视觉降级。

## OTP（验证码输入）

- 等级：进阶。N 个输入位（常见 6 位）；自动前进焦点；退格回退；粘贴整段自动拆分。
- 无障碍：容器 `role="group"` + `aria-label="验证码，共 6 位"`；每格 `inputmode="numeric"` + `maxlength="1"`。
- 键盘：←→ 在格间移动（清空当前格），任意格输入后自动前进；粘贴从第一格重新分发。

## Rating

- 评分星。键盘：方向键调整 + 空格/Enter 选择；`role="radiogroup"` 语义；读屏读出当前值/总数（如「商品评分，当前 4 星，共 5 星」）。
- 选中态除颜色外有填充/描边差异（C-05）；分组下方给文字输出当前值。

## Select（下拉选择）

- 4–10 个选项：优先原生 `<select>`（无障碍与键盘免费获得）。

```tsx
<label htmlFor="city">城市</label>
<select id="city" className="h-9 rounded-md border border-zinc-300 px-3">
  {cities.map((c) => <option key={c} value={c}>{c}</option>)}
</select>
```

## Combobox（可搜索下拉）

- 等级：进阶。选项 >10 或需要搜索时使用。
- 结构：`role="combobox"` + `aria-expanded` + `aria-controls` 列表 + `role="listbox"` / `option` + `aria-activedescendant`。
- 键盘：↑↓ 移动高亮、Enter 选择、Esc 收起；支持 async 加载（loading/empty/error 态）、无结果空态。
- 参考 Handbook「searchable user selector」场景（`tests/scenarios.md` T-02）。

## Multi Select（多选下拉）

- 等级：进阶。combobox + checkbox 选项；已选项以 tag 展示并可从 tag 删除。
- 键盘与 combobox 一致；全选/清空可选。

## Date Picker / Date Range

- 等级：进阶 / 高级。日历默认定位到当前月份；今天有特殊标记（描边，不只靠颜色）。
- 键盘：方向键移动日期、Enter 选择、Esc 关闭；弹层聚焦管理（同 Modal）。
- Range：开始 → 结束选择流；结束早于开始时给出错误提示或自动交换；两个输入用 `aria-label` 区分开始/结束。

## Dropzone（文件上传区）

- 等级：进阶。拖放 + 点击选择双通道（触屏无拖放）；`role="button"` + 键盘 Enter/Space 打开选择器；限制与错误（大小/类型）用文字 + 图标。
- 拖放基于 Pointer/Drag Events 且提供降级路径。

## Color Swatches（颜色选择）

- 颜色块选择：每个 swatch `<button aria-label="颜色 #hex">` + `aria-pressed`；选中标记（勾/边框）不只靠颜色。

## Form Validation

- 提交时校验 + 输入时实时更新；错误字段正下方红字 + 图标 + 说明原因和解决方式。
- 成功/失败有明确反馈（Toast 或行内）；提交中禁用按钮。

## 键盘模式速查（自定义控件选型）

| 控件类型 | 焦点管理 | 方向键 | 确认/关闭 | 关键 ARIA |
| --- | --- | --- | --- | --- |
| Combobox | 输入框保持焦点 | ↑↓ 移动高亮 | Enter 选择 / Esc 收起 | `aria-activedescendant` + listbox/option |
| Tabs | roving tabindex | ←→（Home/End 可选） | 即选即用 | `aria-selected` + tablist/tab/tabpanel |
| Menu / Dropdown | DOM focus 移动 | ↑↓ 循环 | Enter 执行 / Esc 回触发器 | `role="menu"` + `aria-haspopup` |
| Radiogroup / Segmented / Rating | roving tabindex | ↑↓←→ 直接切换 | 空格可选（rating） | `aria-checked` + radiogroup/radio |
| Slider | 单焦点 | ←→ 步进，Shift 大步，Home/End 边界 | — | `aria-valuenow/min/max/text` |
| Switch | 单焦点 | — | 空格切换 | `role="switch"` + `aria-checked` |

> 走查：表单特有项见 `checklist.md` §表单附加项。

## 检查

表单走查并入统一清单 `references/checklist.md`（§表单附加项），本文件不再单独维护清单。