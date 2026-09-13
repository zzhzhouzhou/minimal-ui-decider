# Recipes · Feedback & Overlays — 反馈与覆盖层

对应 Handbook 章节 05（toast · modal · drawer · alert · progress · loading · empty · popover · fab）。
覆盖层（Modal / Drawer / Popover / Toast）有共同的焦点与层级规则，先读这部分。

## 覆盖层通则

- 层级：遮罩 + 弹窗 50；Toast 60（永远最上）；菜单/气泡 20（见 `references/tokens.md` Elevation）。
- 半透明遮罩出现时，比它层级低的内容不可点击。
- 打开时背景锁滚动；关闭恢复；按 Esc 关闭；焦点圈在弹层内；关闭后焦点回到触发器。
- **初始焦点**：普通 dialog 落在首个可交互元素；危险确认（`alertdialog`）默认落在**安全选项**（如「取消」），危险动作永不默认聚焦（见 `accessibility.md` §Keyboard）。
- **关闭期防护**：退出动画期间（~150ms）防止重复打开、防止焦点被抓回正在卸载的弹层（`closing` 标记，见 `motion.md` §进入/退出模式）。
- 进入：`animate-scale-in`（200–300ms）；退出更快（~150ms），可用 transition + `data-state`。
- 窄屏：居中弹窗 → 底部面板（`animate-slide-up`）。
- `role="dialog"` + `aria-modal="true"` + `aria-labelledby` 标题。

```tsx
{open && (
  <div className="fixed inset-0 z-50 flex items-center justify-end p-4 sm:items-center sm:justify-center">
    <div className="absolute inset-0 bg-black/50" onClick={onClose} aria-hidden="true" />
    <div
      role="dialog"
      aria-modal="true"
      aria-labelledby="dlg-title"
      className="relative z-10 flex h-full w-full flex-col rounded-2xl bg-white p-6 shadow-xl animate-slide-up sm:h-auto sm:max-w-md sm:animate-scale-in dark:bg-zinc-900"
    >
      <h2 id="dlg-title">标题</h2>
      …
      <button onClick={onClose} aria-label="关闭">×</button>
    </div>
  </div>
)}
```

## Modal（对话框 / 确认框）

- 基础。焦点圈（Tab 循环）、Esc 关闭、遮罩点击关闭（确认框可禁用）、背景锁滚动。
- 确认框：危险操作红 + 二次确认文案；`role="alertdialog"`。
- **小 ≠ 应该用 Modal**：Modal 的价值在于真的需要阻断当前上下文；任务有完整信息架构时倾向页面 / 抽屉，而不是无限增大的弹窗。
- 打开/关闭动画见通则；用 `useEffect` + `keydown` 监听（记得清理）。

## Drawer（抽屉 / 底部面板）

- 等级：进阶。`animate-slide-in-right`（右侧抽屉）或 `animate-slide-up`（底部面板）；窄屏默认底部面板。
- 同 Modal 的焦点与滚动管理。

## Toast（轻提示）

- 等级：进阶。层级 60；成功 ~3s 自动消失；错误等用户处理（可手动关闭）。
- 无障碍：成功 `role="status"`（polite），错误 `role="alert"`；多条堆叠右下角。
- 动画：进入 fade-up / 退出更快；尊重 reduced motion。

```tsx
function Toast({ kind, message, onClose }: { kind: "success" | "error"; message: string; onClose: () => void }) {
  return (
    <div
      role={kind === "error" ? "alert" : "status"}
      className={cn("pointer-events-auto flex items-center gap-2 rounded-md px-4 py-3 text-sm shadow-lg animate-fade-up",
        kind === "error" ? "bg-red-600 text-white" : "bg-zinc-900 text-white")}
    >
      <span aria-hidden="true">{kind === "error" ? "✕" : "✓"}</span>
      {message}
      <button onClick={onClose} aria-label="关闭" className="ml-2 opacity-70 hover:opacity-100">×</button>
    </div>
  );
}
```

## Alert（警告条 / 横幅）

- 基础。行内或页面顶部；成功/警告/错误/信息 + 图标 + 文字（不只靠颜色）；可带操作按钮与关闭；`role="alert"`（错误）或 `role="status"`。

## Progress（进度指示）

- 等级：进阶。确定进度：`role="progressbar"` + `aria-valuenow/min/max`；不确定：`role="progressbar"` + `aria-label`（如加载中）。
- 只动画 transform/opacity（`scaleX` + origin left）。

## Loading（加载态 / 骨架屏）

- 基础。>300ms 才显示；优先骨架屏（`.skeleton`）而不是转圈。
- 骨架屏形状贴近真实内容（行/块/图），避免布局跳动；`aria-busy` + 完成后的结果提示。

## Empty State（空状态）

- 基础。图标 + 说明 + 下一步动作按钮；不白屏。数字区/表格区空数据时同样处理。

```tsx
<div className="flex flex-col items-center gap-2 py-12 text-center">
  <InboxIcon className="size-10 text-zinc-300" />
  <p className="text-sm font-medium text-zinc-700">还没有项目</p>
  <p className="text-xs text-zinc-500">创建第一个项目开始使用</p>
  <Button size="sm" onClick={create}>新建项目</Button>
</div>
```

## Popover（气泡卡片）

- 基础。trigger 打开（点击/焦点）；`aria-expanded` + `aria-controls`；Esc/外部点击关闭；层级 20。
- 触屏可用（点击触发）；定位防溢出（视口内翻转）。

## FAB · Speed Dial（悬浮操作按钮）

- 等级：进阶。主 FAB 展开子操作（`aria-expanded` + `aria-controls` + Esc 收起）；展开动画 scale + 延迟交错（克制）；窄屏不遮挡核心内容与底部导航。

## 检查

覆盖层走查并入统一清单 `references/checklist.md`（§覆盖层附加项），本文件不再单独维护清单。