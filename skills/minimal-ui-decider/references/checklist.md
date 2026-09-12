# Checklist — 全局走查清单 + 自动化检查映射

> 本文件是**唯一的统一走查清单**。各 reference / recipe 不再重复维护自己的清单；领域特有检查项以「附加项」形式集中在这里。
> 用法：每次任务走「全局清单」；按涉及的领域加挂「附加项」；有条件时跑「自动化映射」中的工具并把结果作为证据。

## 一、全局清单（每次 UI 任务必查）

**任务与架构**

- [ ] 解决用户的实际任务（不是只把页面做漂亮）
- [ ] 先 Inspect 再动手：组件 / token / 依赖 / 模式被检查过（M-01、M-03）
- [ ] 使用现有项目模式；复用优先于新建（M-01）
- [ ] 最简适当架构；无不必要抽象（复杂度测试）
- [ ] 未默认引入新依赖（M-03）
- [ ] 产生最小、可维护的 diff；无无关重构（M-06）
- [ ] 公共 API 尽量保持（M-04）
- [ ] 用户输入安全渲染（M-07）

**CRITICAL floor**

- [ ] 核心任务可仅用键盘完成，焦点可见（focus-visible）（C-01）
- [ ] 每个可交互元素有 accessible name（C-02）
- [ ] 语义元素表达交互，无 `<div onClick>`（C-03）
- [ ] 表单控件有持久 label，placeholder 不替代 label（C-04）
- [ ] 状态不只靠颜色传达：有文字/图标/结构/状态指示（C-05）
- [ ] 核心移动交互不依赖 hover；320px 无横向滚动（C-06）

**视觉与动效**

- [ ] 数值取自 tokens（`tokens.md`），不随手填
- [ ] 页面层级清晰：一个 h1、不跳级、主按钮每屏一次（S-05）
- [ ] 无未说明理由的视觉特效；装饰性动效 ≤ 一两处
- [ ] 动效有目的（S-03）且尊重 `prefers-reduced-motion`（S-06）
- [ ] 深浅模式分别检查（对比度、层级、阴影降级为边框）

**收尾**

- [ ] 覆盖适用交互状态（M-05）：按 `interaction.md` 状态表核对
- [ ] 声称完成前运行了可行的机械验证并出示证据（见下节）
- [ ] 删除测试：还能删掉什么而不损失？

## 二、领域附加项（按涉及领域加挂）

**表单（`recipes/forms.md`）**

- [ ] 错误在字段正下方：红字 + 图标 + 原因与解决方式；`aria-invalid` + `aria-describedby`
- [ ] 实时校验（输入时更新），提交中禁用按钮；>300ms 才显示加载
- [ ] 4–10 项用 `<select>`；>10 或需搜索用 combobox
- [ ] 密码框可切换可见性 + 强度实时反馈

**导航（`recipes/navigation.md`）**

- [ ] 当前位置 = 颜色 + 字重（不加彩色高亮块）；移动端入口 ≤ 5
- [ ] 弹层（菜单/面板/命令面板）：焦点圈 + Esc + 焦点复位 + 外点关闭
- [ ] 未读/计数类标记有数字（不只靠颜色）

**覆盖层（`recipes/feedback-overlays.md`）**

- [ ] `role="dialog"`（危险确认用 `alertdialog`）+ `aria-modal` + 可访问标题
- [ ] 打开锁滚动、关闭恢复；初始焦点规则（危险对话框默认落在安全选项）
- [ ] Toast：成功 ~3s 消失、错误留存；层级 50/60 正确

**数据展示（`recipes/data-display.md`）**

- [ ] 数字右对齐 + 等宽数字；表格去竖线只留横向分隔
- [ ] loading / empty / error 三态齐全；空态 = 图标 + 说明 + 行动
- [ ] 排序/选择/拖拽有键盘替代；窄屏有卡片形态

**高级效果（`recipes/advanced-interactions.md`）**

- [ ] 每个效果落在 Y 级并通过复杂度测试（用户明确要求且通不过测试时：可执行，但必须先报告 trade-off，见 T-12）
- [ ] Pointer Events（触屏一致）；rAF / 监听 / timer 已清理
- [ ] 效果失效时核心功能仍完整（不承载核心功能）

**视觉（`references/visual.md`）**

- [ ] 重要信息在视线上方（左上到右下视线动线）
- [ ] 同类元素尺寸、圆角、间距一致
- [ ] 图标风格统一（全部线性或全部面性）
- [ ] 按钮文案用动词，语气一致
- [ ] 强调色 ≤ ~10% 面积；三个红色按钮会毁掉主次
- [ ] Minimal / 极简任务：对照 `visual.md` §Minimal Style Matrix 逐维检查；必要信息与反馈未删减（E-18 / E-19）

**响应式（`references/responsive.md`）**

- [ ] 布局转换而非隐藏：表格→卡片、侧栏→抽屉/底部栏、弹窗→底部面板
- [ ] 触控目标 ≥ 44×44，相邻可点元素 ≥ 8px

## 三、自动化检查映射（有条件必跑，结果作为完成证据）

| 检查项 | 工具 / 方式 | 覆盖规则 |
|---|---|---|
| 语法 / 构建 | `tsc --noEmit`、`node --check`、`vite build`、项目 lint | 交付前最低门槛 |
| 可达性静态扫描 | axe-core（浏览器扩展 / `vitest-axe` / `@axe-core/playwright`） | C-01 C-02 C-03 C-05、对比度 |
| JSX a11y 静态检查 | `eslint-plugin-jsx-a11y`（React 项目标配：alt-text / aria-props / click-events-have-key-events / label-has-associated-control 等） | C-01 C-02 C-03 C-04 |
| 对比度 | axe（color-contrast 规则）/ token 文件人工核对深浅两套 | C-05、`tokens.md` 对比度 |
| 键盘走查 | 手动 Tab / Shift+Tab 全路径 + Esc 关闭弹层 | C-01 |
| 样式类可分析性 | ESLint（自定义规则）/ code review 检查动态拼接 | M-02 |
| 依赖体积 | `npm ls`、bundle analyzer | M-03 |
| 公共 API | 组件 props 类型 diff（tsc） / API 变更记录 | M-04 |
| XSS / 外链 | grep `innerHTML`、`dangerouslySetInnerHTML`；检查 `target="_blank"` 是否带 `rel="noopener"` | M-07 |
| 320px / 响应式 | DevTools 设备模拟 320 / 375 / 768 / 1280 | C-06 |
| reduced motion | 系统/DevTools 开启 `prefers-reduced-motion: reduce` 后复测 | S-06 |

> 无法运行某项（如纯静态文件无构建环境）时，在交付说明中写明「该项未运行及原因」，改为人工走查代替。

## 四、走查顺序建议

```text
机械验证（能跑的先跑，拿到证据）
  → 全局清单逐项
  → 领域附加项
  → 删除测试
  → 报告：通过项 / 违反项 / 未运行项
```
