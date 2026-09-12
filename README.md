# Minimal UI Decider

> 面向 AI Agent 的 UI 设计与工程决策 Skill。它为「AI 写界面」这件事提供一套可引用、可测试的决策规则：先检查现有项目、能复用就复用、守住无障碍底线、按需控制复杂度。

核心目标：**让 AI 在 UI 设计与实现过程中做出更少、更合理、更可维护的决策，保证 UI 简洁有效，以易用性和简洁性为主。**

配套项目：[形色场 UI Handbook](https://zzhzhouzhou.github.io/UI-handbook/)（React 19 + TypeScript 5.9 + Tailwind CSS 4，10 章 / 115+ 个组件，全部手写实现，无 UI 组件库；组件计数约定见下文「维护」一节）。

## 解决什么问题

让 AI 写 UI 代码时，常见的问题有几类：

| 常见问题 | 本 Skill 对应的约束 |
| --- | --- |
| 不检查现有项目，直接新建重复组件、安装新依赖（为一个图标装 lucide-react） | M-01 复用现有组件 · M-03 已有依赖优先 |
| 把「好看」理解为堆效果：毛玻璃、渐变、发光一起上 | `visual.md` Intent Translation + 复杂度测试 |
| placeholder 当 label、`<div onClick>` 当按钮、错误状态只有颜色 | C-03 / C-04 / C-05（CRITICAL 底线） |
| 用户只要改个颜色，它顺手重构半个项目 | M-06 最小 diff |
| 把「简洁」执行成删说明文字、全灰、去掉所有边框和动效 | Minimal Style Preset + 复杂度测试 |
| 声称「完成了」但没跑过任何检查 | 工作流第 10 步：交付前出示机械验证证据 |

本 Skill 的做法：在 Agent 动手之前，给出一套决策顺序和规则编号；在交付之前，给出一份统一的走查清单。规则全部落在 Markdown 文件里，Agent 按任务加载，不依赖特定平台。

## 规则体系

所有规则带稳定编号，分四级：

```text
C-xx = CRITICAL   底线：键盘可用、可访问名、语义元素、表单标签、非颜色信息、核心响应式。
                  用户需求可以调整 S / Y，但不能无条件突破 C。
M-xx = MUST       工程默认：复用优先、样式类静态可分析、不装不必要的依赖、
                  保持公共 API、最小 diff、安全渲染用户输入。
S-xx = SHOULD     推荐默认：移动优先、动效有目的、100ms 内反馈、空态可行动等，可按项目调整。
Y-xx = MAY        可选增强：spotlight / glass / particles / 3D 等 25 种效果，
                  使用前必须通过复杂度测试。
```

规则之间、规则与用户需求发生冲突时，按固定优先级处理：

```text
CRITICAL floor → User goal → Information architecture → Usability → Responsive
    → Consistency → Performance → Motion → Decoration
```

**复杂度测试**是 Y 级效果（以及任何多余实现）的准入门槛，依次回答三个问题：

1. 它是否服务当前任务？
2. 它是否降低了其他复杂度？
3. 删掉它是否会损害可用性、无障碍、信息层级，或用户明确要求的视觉结果？

三个答案都是「否」，就删除。

## 工作方式

Agent 接到 UI 任务后执行 12 步工作流，核心顺序：

```text
理解目标 → Inspect 现有项目（组件 / token / 依赖 / 模式）
→ 识别可复用能力（Inspect → Reuse → Adapt → Extend → Create new）
→ 应用 CRITICAL 底线 → 实现
→ 机械验证（构建 / lint / axe）+ 统一走查清单 → 删除测试 → 交付
```

`SKILL.md` 是入口文件（精简的 router，避免写死行数），包含工作流、决策速查表和一张按语义分类的路由表。具体规则按任务加载对应文件，避免一次性读入全部内容——例如改表单时加载 `recipes/forms.md`，审查无障碍时加载 `references/accessibility.md`。路由按语义判断：用户说「让页面更高级」，即使没提色彩和字体，也应加载视觉规范。

三层分工：

```text
Skill        = How to decide        （决策规则、优先级、工作流）
Handbook     = What good implementation looks like（references/handbook.md + 线上 UI Handbook）
Source Code  = What actually works  （真实项目：优先复用既有实现）
Tests        = Whether the Agent behaves correctly（tests/，行为回归）
```

## 目录结构

```text
minimal-ui-decider/                   ← 仓库根
├── skills/
│   └── minimal-ui-decider/          ← Agent Skill 包（安装时复制 / gh skill 安装的就是这个目录）
│       ├── SKILL.md                 ← 入口：frontmatter、12 步工作流、决策速查、按需加载路由表
│       ├── references/              ← 规则与规范（按需加载）
│       │   ├── decision.md          ← C/M/S/Y 规则全文（Rule → Check → Fail signal）、豁免、冲突处理、决策矩阵
│       │   ├── checklist.md         ← 唯一的统一走查清单（全局项 + 领域附加项 + 自动化检查映射）
│       │   ├── accessibility.md     ← C-01…C-06 无障碍底线展开、对比度 canonical 定义
│       │   ├── visual.md            ← 视觉层级、Intent Translation、Minimal Style Preset（默认视觉基线 + Style Matrix）
│       │   ├── tokens.md            ← 色彩 / 字阶 / 间距 / 圆角 / 层级 / 动效 / 断点的唯一定义处
│       │   ├── interaction.md       ← 交互状态全集与反馈时机（100ms / 300ms / 3s）
│       │   ├── responsive.md        ← 移动优先、布局转换、320px 底线
│       │   ├── motion.md            ← 动效规则与动画令牌
│       │   ├── engineering.md       ← 工程默认 + 非 React 项目的 Stack Adaptation 映射表
│       │   ├── handbook.md          ← UI Handbook 事实来源 + 组件计数约定
│       │   └── counterexamples.md   ← E-01…E-20 反例（Wrong / Why / Right / Root cause）+ 规则↔反例索引
│       ├── recipes/                 ← 按组件家族的实现模式：basic-components / forms / navigation /
│       │                              feedback-overlays / data-display / advanced-interactions
│       └── tests/                   ← 行为回归：T-01…T-13 场景 + 预期行为 + 回归记录
├── LICENSE · README.md · CHANGELOG.md
```

## 安装与使用

### 方式一：`gh skill` 安装（推荐，需 GitHub CLI ≥ 2.90.0）

```bash
gh skill install zzhzhouzhou/minimal-ui-decider minimal-ui-decider
```

执行后会交互式选择目标 Agent（Copilot / Claude Code / Cursor / Codex / Gemini CLI 等）和作用范围（项目级 / 用户级）。也可以用参数一次指定：

```bash
# 安装到 Claude Code，用户级
gh skill install zzhzhouzhou/minimal-ui-decider minimal-ui-decider --agent claude-code --scope user
```

需要固定版本时，在 skill 名后附加标签（或用 `--pin` 阻止 `gh skill update` 自动更新）：

```bash
gh skill install zzhzhouzhou/minimal-ui-decider minimal-ui-decider@v1.4.0
```

之后用 `gh skill update` 跟随上游修订。

### 方式二：手动安装（任意支持 Agent Skills 标准的平台）

克隆仓库，把 `skills/minimal-ui-decider/`（含 `SKILL.md` 的目录）复制到 Agent 的 skill 目录：

```bash
git clone https://github.com/zzhzhouzhou/minimal-ui-decider.git
cp -r minimal-ui-decider/skills/minimal-ui-decider ~/.agents/skills/
```

用户级目录（如 `~/.agents/skills/`）或项目级目录（`.claude/skills/`、`.github/skills/`、`.agents/skills/`）均可；也可以在配置中把该目录加入自定义 skill 根（如 `customSkillDirs`）。

### 使用

1. 在任何 UI 任务中（新建页面、改组件、加表单、做响应式、审查无障碍等），Agent 会加载 `SKILL.md`，并按任务语义加载相关文件。
2. 使用时无需记忆规则编号；规则通过决策速查表和路由表进入上下文，需要完整定义时再查 `decision.md`。

规则是否生效，直接观察 Agent 的行为即可：

- 给已有项目一个「改按钮颜色」任务——正确响应是先查看现有组件与 token，只改相关行，props 签名不变。
- 让它「装个图标库」——正确响应是先检查项目已有的图标能力，说明依赖成本，而不是直接安装。

`tests/scenarios.md` 收录了 13 个这样的标准场景（T-01…T-13），每个场景附验收方式和禁止项。

## 技术栈说明

决策规则（C/M/S/Y、复杂度测试、复用纪律）与技术栈无关。示例代码与默认 token 基于 React 19 + TypeScript 5.9 + Tailwind CSS 4；其他技术栈按 `references/engineering.md` §Stack Adaptation 的映射表转换实现载体（例如 `@theme` ↔ CSS 自定义属性、`dark:` ↔ 属性选择器），行为层规范（焦点管理、Pointer Events、reduced motion、触控目标）在各栈下保持一致。

## 为什么这样设计

| 设计 | 目的 |
| --- | --- |
| SKILL.md 保持精简，详细内容按需加载 | Agent 只在需要时读入相关规范，节省上下文 |
| 规则带稳定编号（C/M/S/Y） | 反例、测试、回归都能引用到具体条目，改动可定位到文件 |
| Existing Project First（Inspect → Reuse → Adapt → Extend → Create） | 优先复用已有组件与依赖，避免重复造轮子和无谓的包体积 |
| 无障碍放在 C 级 | 键盘、可访问名、非颜色信息、表单标签、核心响应式始终执行 |
| Y 级效果需要通过复杂度测试 | 效果可以加，但要以任务和层级为先 |
| 统一走查清单集中在一处 | 所有检查项只在 `checklist.md` 维护，多份清单会逐渐分歧 |
| 反例 + 行为回归 | 回归检验的对象是 Agent 的决策序列，同时检验代码结果 |

## 维护

- **修改规则后跑行为回归**：对 `tests/scenarios.md` 的每个场景输入请求，对照 `expected-behaviors.md` 检查决策序列与禁止项，记录到 `tests/regression-log.md`。`scenarios.md` 提供了半自动化运行建议：fixture 项目 + Playwright + axe 断言机械行为，人工评述决策序列。
- **新增反例**：遵循 `counterexamples.md` 的统一结构（Wrong / Why / Right / Root cause / Related rules），并同步更新文末「规则 ↔ 反例索引」。
- **Handbook 新增组件时**：按 `handbook.md` §组件计数约定同步五处快照数字（handbook §事实、SKILL.md、README、engineering.md、CHANGELOG）。对外表述一律写「115+」，`+` 表示持续新增；数字不一致时以 handbook 清单实际 id 数为准。
- **走查清单只改一处**：所有检查项集中在 `checklist.md`，各 reference / recipe 只引用，避免多份清单逐渐分歧。
- **Skill 目录名保持 `skills/minimal-ui-decider/`**：`gh skill` 按「目录名 = frontmatter `name`」识别 skill，改名会破坏安装与更新。
- 版本记录在 `CHANGELOG.md` 与 Git tags，不写入 frontmatter。

## 相关链接

- UI Handbook 在线阅读：<https://zzhzhouzhou.github.io/UI-handbook/> · <https://uihandbook.zzhzhou2026.workers.dev>
- UI Handbook 仓库：<https://github.com/zzhzhouzhou/UI-handbook>
- 作者：zzh_zhou · Garbage Human Studio

## License

[MIT](./LICENSE)
