# Recipes · Navigation — 导航

对应 Handbook 章节 04（navbar · tabs · breadcrumb · pagination · sidebar · stepper · dropdown · command · contextmenu · dock · scroll-progress · bottom-tabs · notification-panel · scroll-spy-toc）。

## 导航通则（Handbook）

- 导航的极简 = **降低认知混淆**，不是减少入口数量；先组织层级再谈删减（`references/minimal.md`）。
- 当前位置用**颜色 + 字重**区分，不加彩色高亮块。
- 层级 >4 用面包屑；步骤 <6 用步骤条。
- 移动端底部导航入口 ≤ 5 个；侧边栏在窄屏转抽屉/底部导航（C-06，不能直接 hidden 掉核心功能）。
- 通知入口带未读标记（数字 + 视觉提示，不只靠颜色）。

## Navbar

- 基础。顶部栏 + 紧凑内部间距（高度约 56–64px）；窄屏折叠为菜单按钮（`aria-expanded`）或底部导航。
- 未读标记：`aria-label="通知，3 条未读"` + 角标数字。

## Tabs

- 等级：进阶。键盘：方向键切换 + roving tabindex；`role="tablist" / tab / tabpanel` + `aria-selected` + `aria-controls`。
- 选中态 = 字重/颜色 + 下划线/下底，不只靠颜色。
- 窄屏可横向滚动。

```tsx
<div role="tablist" aria-label="设置分类" className="flex gap-1 border-b border-zinc-200">
  {tabs.map((t) => (
    <button
      key={t.id}
      role="tab"
      id={`tab-${t.id}`}
      aria-selected={active === t.id}
      aria-controls={`panel-${t.id}`}
      tabIndex={active === t.id ? 0 : -1}
      className={cn("h-9 px-3 text-sm focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-zinc-900", active === t.id ? "font-medium text-zinc-900 border-b-2 border-zinc-900" : "text-zinc-500 hover:text-zinc-900")}
    >
      {t.label}
    </button>
  ))}
</div>
```

## Breadcrumb

- 基础。`<nav aria-label="面包屑">` + 用 `aria-current="page"` 标记当前页。
- 层级 >4 时用；分隔符用文字「/」或 aria-hidden 图标。

## Pagination

- 基础。前后页按钮（`aria-label="上一页/下一页"`）+ 页码 + `aria-current="page"`；窄屏压缩为「上一页 · 第 3/12 页 · 下一页」。

## Sidebar

- 等级：进阶。桌面常驻；窄屏抽屉（`role="dialog"` + focus trap + Esc + 遮罩点击关闭；关闭后焦点回开关）。
- 目录项滚动高亮用 scroll spy（见 Scroll Spy TOC）。

## Stepper

- 基础。步骤 <6 时用；当前步 `aria-current="step"`；已完成步打勾；状态配文字（不只靠颜色）。

## Dropdown（下拉菜单）

- 等级：进阶。trigger `aria-haspopup="menu"` + `aria-expanded`；菜单 `role="menu"` + `menuItem`；↑↓ 移动、Enter 选择、Esc 关闭、焦点回 trigger。
- 层级 20（popover）；点击外部关闭。

## Command Palette（命令面板 ⌘K）

- 等级：高级。全局快捷键 ⌘K / Ctrl+K 打开（无冲突监听，输入框聚焦时不拦截字符键）；作为对话框处理：focus trap + Esc 关闭 + 背景锁滚动。
- **两阶段选择**：第一阶段输入过滤（combobox 语义）→ 第二阶段 ↓/↑ + Enter 执行。
- 结果渲染：按分组（`页面 / 操作 / 最近`）展示，组头小字；高亮项用 `aria-selected="true"` 同步（`aria-activedescendant` 指向当前项），hover 与键盘高亮共用同一状态，避免双高亮。
- 无结果给「无匹配命令」空态；执行后关闭面板、清空输入、焦点回触发器（或按命令语义跳转后聚焦目标）。
- 列表较长时高亮项滚动到可视区（`scrollIntoView({ block: "nearest" })`）。
- 何时需要：全局跳转频繁、页面 > 5 个、操作不可尽数暴露在导航里（Decision Matrix）。

## Context Menu（右键菜单）

- 等级：进阶。`contextmenu` 事件唤起；触屏无右键——同时提供长按或显式「更多」按钮入口。
- 键盘：Shift+F10 触发；菜单焦点管理同 Dropdown。

## Dock（macOS Dock）

- 等级：高级。放大效果基于指针位置插值（rAF 中只写 transform，unload 清理）；触屏退化为普通图标栏；纯增强（Y），不承载核心导航。

## Scroll Progress（阅读进度 / 收缩头部）

- 等级：进阶。顶部细进度条（`transform: scaleX` + `transform-origin: left`，可配合 `prefers-reduced-motion` 静态显示）。
- 收缩头部：滚动时头部高度/阴影变化，保持内容不被遮挡（`scroll-padding-top`）。

## Bottom Tab Bar

- 等级：进阶。移动端主导航，入口 ≤ 5；`role="tablist"` 或导航链接 + `aria-current`；固定底部 + 安全区 padding。

## Notification Panel（通知面板）

- 等级：进阶。入口带未读角标；面板为弹出层（层级 20）+ 焦点管理；未读条目有强视觉提示；可一键已读。

## Scroll Spy TOC（页内目录 · 滚动侦测）

- 等级：高级。监听滚动位置（`IntersectionObserver` 或滚动计算）高亮当前目录项；高亮用颜色 + 字重。
- 真实项目可参考 `useScrollSpy`：按阅读线判定当前章节/组件；用户悬停/操作目录时暂停跟随，不与用户抢滚动。

## 检查

导航走查并入统一清单 `references/checklist.md`（§导航附加项），本文件不再单独维护清单。