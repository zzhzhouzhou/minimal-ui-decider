# Recipes · Data Display — 数据展示

对应 Handbook 章节 06（card · table · accordion · timeline · stats · code · tree · kanban · chat · heatmap · carousel · compare · bento · masonry · description-list · blockquote · virtual-list · transfer）。

## 数据展示通则（Handbook）

- **数字右对齐 + 等宽数字**（`font-feature-settings: "tnum"`）；文字左对齐。
- **表格去掉竖线**，只保留极淡的横向分隔。
- 空数据显示空状态（图标 + 说明 + 行动按钮），不白屏。
- 数据组件覆盖 loading / empty / error 状态。
- 列表/表格在窄屏转换为卡片列表。

## Card

- 基础。表面 `bg-white dark:bg-zinc-900`；圆角用卡片档 12px（Tailwind 类名 `rounded-xl`，映射见 `tokens.md` §圆角）；阴影极淡、多层叠加，悬停升至 hover-card 档（层级 10）。
- **Card 是分组工具，不是默认容器**：先扁平结构（内容 / 区块 / 控件 / 数据），分组需要时才引入容器；不要 Card 套 Card、不要为了「像 dashboard」给每个指标一个 Card（见 `references/minimal.md`）。
- 卡片内再套元素：内圆角 = 外圆角 − 间距（`tokens.md`）。

## Table

- 等级：进阶。数字列右对齐 + tnum；`<th scope="col">`；行 hover 高亮（极淡）；仅横向分隔线。
- 响应式：小屏表格 → 卡片列表（`references/responsive.md` 布局转换模式）。
- 排序/选择需键盘可达（按钮 + `aria-sort`）。

## Accordion

- 基础。`<button aria-expanded>` + `aria-controls` 面板；`region` 面板；键盘 Enter/Space 展开收起，方向键可移动（可选）。
- 动画优先 transform/opacity；小面积展开收起可用 `grid-template-rows: 1fr ↔ 0fr` 过渡（见 `references/motion.md` 例外），避免大面积/高频 layout 动画。

## Timeline

- 基础。时间线点 + 内容；竖线用边框；时间用等宽数字/固定宽度对齐。

## Stats（指标卡片 / 迷你图）

- 等级：进阶。大数字 + 标签；趋势变化用色 + 箭头文字（不只靠颜色）；迷你图用 inline SVG（path + transform），可 `animate-countup`。
- 不要为了展示数据而画图：先问这个图是否降低了数据理解成本，否则用数字 + 趋势文字表达。

## Code Block / Kbd

- 基础。代码块 `pre/code` + mono 字体 + 复制按钮（`aria-label="复制代码"` + 复制成功提示）；Kbd 用 `<kbd>` 样式化（如 `Ctrl+S`）。
- 语言标注（如 `tsx`）；语法高亮避免重依赖（真实项目用内置轻量分词器）。

## Tree（树形视图）

- 等级：进阶。节点展开/收起（`aria-expanded`）+ `role="tree" / treeitem / group`；键盘：→ 展开、← 收起、↑↓ 移动、Home/End；焦点管理 roving tabindex。

## Kanban（看板拖放）

- 等级：高级。基于 Pointer Events 的拖放（桌面与触屏一致）；`aria-grabbed`/乱序提示；键盘替代：用按钮「左移/右移」或菜单操作（拖放不是唯一路径）。

## Chat（聊天气泡）

- 基础。左右气泡 + 时间；新消息区域 `aria-live="polite"`；输入框 label 在上方。

## Heatmap（日历热力图）

- 等级：进阶。色块矩阵；每格 `title`/`aria-label`（日期 + 数值）——不只靠颜色表达数值大小；今天有描边标记。

## Carousel

- 基础。上一张/下一张按钮（`aria-label`）+ 指示点（`aria-current`）；自动播放要 pausable（hover/焦点暂停）且尊重 reduced motion；键盘方向键可选。

## Compare（图片对比滑块）

- 等级：进阶。滑块拖拽（Pointer Events，触屏一致）+ 键盘（左右方向键）；`role="slider"` + aria 值。

## Bento（网格）

- 等级：进阶。大小块拼合；小屏回退为单列堆叠；卡片可含迷你数据。

## Masonry（瀑布流）

- 等级：进阶。CSS `columns` 最简单；图片懒加载 + 固定尺寸防跳动；加载更多用无限滚动模式（见 `advanced-interactions.md`）。

## Description List（描述列表）

- 基础。`<dl><dt><dd>` 语义；标签定宽，值左侧/下方；长文本换行。

## Blockquote（引用块）

- 基础。`<blockquote>` + 左边框/引号；cite 可附 `aria-label`。

## Virtual List（虚拟滚动列表）

- 等级：高级。固定行高的列表用窗口化渲染（只渲染可视行 + 缓冲区）；`role="list"` + 每行 `role="listitem"`；滚动容器有 `tabIndex` 保证键盘可达。
- 数据量小（<100）时直接用普通列表——虚拟化本身是复杂度（复杂度测试）。

## Transfer（穿梭框）

- 等级：进阶。左右两栏 + 移动按钮；每个选项 checkbox / `aria-selected`；键盘可达：Tab 到选项、空格选择、Enter 移动；窄屏上下堆叠。

## 检查

数据展示走查并入统一清单 `references/checklist.md`（§数据展示附加项），本文件不再单独维护清单。