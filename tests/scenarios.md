# Tests · Behavioral Regression Scenarios

这不是传统 unit tests，而是 **Behavioral Regression Set**：修改 Skill 后，用这些场景检验 **Agent 是否做出正确的决策**（而不是能不能写代码）。

用法：

1. 对每个场景输入一段用户请求。
2. 让 Agent 在该 Skill 驱动下工作。
3. 对照 `tests/expected-behaviors.md` 检查决策序列与输出。
4. 任一预期行为缺失/违背 → Skill 回归（记录到 `tests/regression-log.md`）。

> 编号约定：场景使用 **T-xx** 前缀，与规则 ID（C/M/S/Y，见 `references/decision.md`）互不相干；场景内引用的 S-xx 均指规则。

> Bad AI: 「我会给你一个漂亮的 Select。」
> Good AI: 「这个项目已经有 Select，我先检查它是否支持搜索。如果不支持，再判断是否需要扩展，而不是直接引入新的组件库。」

我们测试的是后者。

## T-01 · 修改现有按钮颜色

**Input**

> 把现有 SaveButton 的颜色改成蓝色。

**Expected decision sequence**

```text
Inspect existing SaveButton
Inspect existing token（color 定义在 @theme / tokens）
Minimal diff（只改颜色相关行）
Preserve API（props / exports 不变）
No icon library replacement
No unrelated refactoring
```

**验收方式**：diff 仅颜色相关行；props 签名不变。

## T-02 · 可搜索的用户选择器

**Input**

> 做一个可以搜索 10000 个用户的选择器。

**Expected decision sequence**

```text
Inspect existing dependencies（是否已有 combobox / select）
判断 native select 是否足够（否：10k 用户需搜索）
使用适当 custom combobox 模式（recipes/forms.md）
Keyboard support（↑↓ / Enter / Esc）
Accessible name（combobox + listbox）
Async loading（>300ms 才显示加载态）
Empty state（无匹配结果）
Error state（请求失败可重试）
不引入新 UI 库（优先项目已有能力）
```

**验收方式**：axe 扫描 combobox 区域 + 手动 ↑↓/Enter/Esc 走查。

## T-03 · 添加一个删除确认弹窗

**Input**

> 给删除功能加一个确认弹窗。

**Expected decision sequence**

```text
检查是否已有 Modal / 确认组件（复用）
role="alertdialog" + 标题
初始焦点落在安全选项（取消），危险动作不默认聚焦
Focus trap + Esc 关闭 + 背景锁滚动 + 关闭后焦点复位
危险操作红色 + 明确文案（不可逆后果）
进入/退出动画（scale-in，退出更快，reduced motion）
```

**验收方式**：键盘打开弹窗验证初始焦点与 Esc；axe 检查 dialog 语义。

## T-04 · 响应式侧边栏

**Input**

> 让侧边栏在手机上也能用。

**Expected decision sequence**

```text
桌面：Sidebar + Content
窄屏：Drawer（role="dialog" + focus trap + Esc）或底部导航
不直接 hidden 掉核心导航（C-06）
触控目标 ≥ 44px；320px 无横向滚动
```

**验收方式**：DevTools 320/375/768 三档模拟 + Tab 走查抽屉。

## T-05 · 「让页面更高级」

**Input**

> 这个页面太普通了，帮我做得高级一点。

**Expected decision sequence**

```text
Intent Translation（"高级" → typography/spacing/hierarchy/material/subtle motion）
不默认叠加 glass + gradient + glow（visual.md）
先检查现有 tokens / 组件，做克制增强
装饰性动效 ≤ 一两处；reduced motion
若增强涉及色彩/间距，数值取自 tokens
```

**验收方式**：改动清单对照 Intent Translation 表；无新依赖。

## T-06 · 表单校验

**Input**

> 给注册表单加校验（邮箱格式、密码强度）。

**Expected decision sequence**

```text
每个字段 label（非 placeholder）
错误在字段正下方：红字 + 图标 + 说明原因和解决方式（不只靠颜色）
实时更新（输入时校验），aria-invalid + aria-describedby
密码强度实时反馈 + 显示/隐藏切换
提交中禁用按钮；>300ms 才显示加载
```

**验收方式**：axe + 空表单提交走查；读屏/aria 树检查关联。

## T-07 · 请求安装图标库

**Input**

> 帮我装 lucide-react，把所有图标换成它。

**Expected decision sequence**

```text
检查项目已有 inline SVG 图标集（M-03）
默认拒绝：复用已有能力；说明依赖成本
仅当用户明确坚持且理由成立时执行，并报告 trade-off
图标风格统一（不混用线性/面性）
```

**验收方式**：对话记录显示「先检查 → 说明成本」序列。

## T-08 · 深色模式适配

**Input**

> 给界面加深色模式。

**Expected decision sequence**

```text
沿用 .dark 类 + dark: 变体（项目已有模式）
背景非纯黑、文字非纯白；层级越高背景越亮
深浅模式分别检查对比度（≥ 4.5:1）
深色下层级用边框亮度表达（阴影不可见）
跟随系统 + 手动切换（可选 View Transition，Y-12）
```

**验收方式**：深浅两模式各跑一次 axe color-contrast。

## T-09 · 减少动效偏好

**Input**

> 界面动效太多，关掉大部分。

**Expected decision sequence**

```text
保留服务反馈/层级/开合的动效（S-03 规则）
删除纯装饰动效（跑马灯、粒子、装饰性进入）
所有保留动效尊重 prefers-reduced-motion
动画只动 transform/opacity
```

**验收方式**：开启系统 reduce 后复测；清单对照保留/删除项。

## T-10 · 数据表格加空态

**Input**

> 表格没数据时太难看了，处理一下。

**Expected decision sequence**

```text
空态 = 图标 + 说明 + 下一步动作（不白屏）
表格数字右对齐 + 等宽数字保持
loading / empty / error 三态齐全
窄屏表格 → 卡片列表的响应式不被破坏
```

**验收方式**：注入空数据验证三态；375px 检查卡片形态。

## T-11 · 键盘可达性审查

**Input**

> 审查一下这个页面能不能只用键盘操作。

**Expected decision sequence**

```text
逐元素走 Tab：核心任务键盘可完成（C-01）
焦点可见（focus-visible），无 outline:none
覆盖层：focus trap + Esc + 焦点复位 + 初始焦点规则
自定义控件符合原生模式（Tabs/Combobox/Switch）
修复后复测；报告违反的 C 规则
```

**验收方式**：Tab 全路径走查记录 + axe；修复后复测（见 `checklist.md`）。

## T-12 · 动效回归：过度动画

**Input**

> 给所有卡片加粒子背景和聚光边框。

**Expected decision sequence**

```text
复杂度测试：效果是否服务任务？装饰性效果 ≤ 一两处
指出：全部卡片加效果会破坏层级与性能
只对关键卡片（主 CTA / 首屏 hero）做克制增强（或建议）
若用户坚持全量 → 执行但报告 trade-off（性能/层级）
reduced motion 降级
```

**验收方式**：决策记录包含复杂度测试三问与 trade-off 说明。

## 半自动化运行建议（降低回归成本）

场景中「可观察的行为」多数可以脚本断言，人工只评决策序列与禁止项。**待建：** 建议维护 `tests/fixture/`（最小 React + TypeScript + Tailwind 项目，当前尚未随仓库提供——在 fixture 就绪前，本节的机制化运行不可用，回归以人工对照 `expected-behaviors.md` 为主）。fixture 就绪后，回归时让 Agent 在 fixture 上执行场景输入：

| 场景 | 可机械化部分 | 方式 |
| --- | --- | --- |
| T-01 | diff 仅颜色相关行；props 签名不变 | `git diff` 人工过目 + `tsc --noEmit` |
| T-02 / T-03 / T-11 | axe 扫描；Tab / Esc / 初始焦点断言 | `@axe-core/playwright` + 键盘事件脚本 |
| T-04 | 320 / 375 / 768 无横向滚动；触控目标 ≥ 44 | Playwright viewport 遍历 + DOM 尺寸断言 |
| T-06 | label 存在；`aria-invalid` / `aria-describedby` 关联 | axe + DOM 断言 |
| T-08 | 深浅两套对比度 | 切换 `.dark` 类后各跑一次 axe color-contrast |
| 全部 | 决策序列、禁止项、是否问了不该问的问题 | 人工对照 `expected-behaviors.md` |

脚本结果作为证据附到 `tests/regression-log.md`；「预期决策序列」仍以人工评述为准。

## 记录

每次回归运行记录到 `tests/regression-log.md`：场景编号、日期、Agent 决策序列摘要、缺失/违背项、触发修复的规则文件与行。回归通过标准：**所有 Expected 序列全部命中，且无额外无关改动。**
