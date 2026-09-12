# Engineering — 工程默认

默认技术栈（与 UI Handbook 一致）：

```text
React 19 · TypeScript 5.9 · Tailwind CSS 4 · Vite 7
运行时依赖只有 clsx + tailwind-merge；图标为手写内联 SVG
```

> **Stack-agnostic 声明**：本 Skill 的决策规则（C/M/S/Y、复杂度测试、复用纪律）与技术栈无关。
> 下列 React/Tailwind 实现是「模式参考」；非 React 项目按 §Stack Adaptation 映射，决策规则原样适用。

## 核心原则

### Reuse（M-01）

优先现有组件、现有 token、现有 hook。Inspect → Reuse → Adapt → Extend → Create new。

### Dependencies（M-03）

优先已有 dependency 的能力。新增依赖默认被拒绝——先 Inspect **当前项目**有什么：

- 图标：项目已有 inline SVG 图标集 / 图标组件则复用；没有就手写 inline SVG（Handbook 参考实现的图标集在 `src/components/primitives.tsx`——那是 Handbook 仓库的路径，不是当前项目的文件）
- 项目已有的 hook / 工具函数（Handbook 参考实现中有 `useCopy`、`useScrollSpy` 等；以 Inspect 结果为准）
- 原生 API（`document.elementFromPoint`、`IntersectionObserver`、`Pointer Events`、`startViewTransition`）

不默认安装 Lucide / Radix / 任何 UI 库。

### Tailwind（M-02）

保持 class **静态、可分析**。避免运行时 class 生成与任意值字符串拼接。

```tsx
// 错误：运行时拼接
<div className={`bg-${color}-500`}>

// 正确：静态变体或可枚举组合
const tones = { primary: "bg-zinc-900 text-white", danger: "bg-red-600 text-white" };
<div className={cn("rounded-md px-4 py-2", tones[variant])}>
```

`cn()` 用 `clsx` + `tailwind-merge`：

```ts
import { clsx, type ClassValue } from "clsx";
import { twMerge } from "tailwind-merge";
export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

### Tokens（Tailwind 4 @theme）

设计令牌与动画集中定义在 `@theme`，组件通过 token 名称引用，不在多处重复数值：

```css
@import "tailwindcss";
@custom-variant dark (&:where(.dark, .dark *));

@theme {
  --color-background: …;
  --color-foreground: …;
  --animate-scale-in: scaleIn 0.18s ease-out both;
  /* …见 references/tokens.md */
}
```

深色模式用 `.dark` 类 + `dark:` 变体（`@custom-variant dark`）。

### Public API（M-04）

修改组件保持 props / exports / public API 兼容；受控/非受控行为不变；破坏性变更需说明迁移。

### Minimal diff（M-06）

用户只要求小改动时不做无关重构。diff 只含任务相关变更。

### Performance

避免为视觉效果加入大量：

- JS（事件监听器、rAF 循环——用后清理）
- DOM（节点树膨胀）
- 依赖
- animation loops

数值右对齐用等宽数字（`tnum`）；图片懒加载且固定尺寸避免布局跳动；首屏无布局跳动。

## Stack Adaptation（非 React / 非 Tailwind 项目）

决策规则（C-01…C-06、M-01…M-07、S/Y、复杂度测试）**原样适用**；只有实现载体变化。常用映射：

| React/Tailwind 概念 | 其他栈的等价物 | 决策规则不变 |
| --- | --- | --- |
| `@theme` 设计令牌 | CSS 自定义属性（`:root { --token: … }`） | token 集中一处，数值有出处 |
| `dark:` 变体 + `.dark` 类 | `[data-theme="dark"]` 属性选择器 + 同一 `.dark`/属性切换逻辑 | 深色非纯黑纯白、层级更亮 |
| `focus-visible:` 前缀 | `:focus-visible` 原生选择器 | 焦点外框可见（C-01） |
| `cn()` 可枚举组合 | 静态 class 名 + 状态 class（`.is-open`、`.disabled`） | 不运行时拼字符串（M-02） |
| 条件渲染 + effect 清理 | `hidden` 属性 / 显隐切换 + `removeEventListener` / `cancelAnimationFrame` / `disconnect()` | 监听与循环不留泄漏 |
| JSX 语义元素 | 原生 HTML 元素 + ARIA | C-03 语义交互 |
| 组件复用（M-01） | 复用已有 CSS 组件类 / Web Component / 模板片段 | Inspect → Reuse 顺序不变 |
| 框架默认 XSS 防护 | 手动转义（`textContent` / escape 函数）后再插入 | M-07 安全渲染 |

> 无论什么栈：覆盖层焦点管理、Pointer Events、reduced motion、触控目标、等宽数字等**行为层规范完全一致**（见对应 reference）。

## 项目结构参考（UI Handbook 仓库）

```text
src/
├── App.tsx               # 应用外壳：搜索、滚动高亮、阅读进度、主题切换、命令面板
├── nav.ts                # 目录数据源（10 章 / 115+ 组件，快照以 references/handbook.md 为准）
├── index.css             # 设计令牌、动画、深色变体
├── components/           # Showcase、CommandPalette、primitives（原子组件 + SVG 图标）
├── hooks/                # useScrollSpy、useCopy
├── sections/             # 10 个章节文件
└── utils/                # cn / copy / highlight / scroll
```

新增组件三步（目录一致性校验）：nav.ts 加条目 → 章节加 `<Showcase id>` → build（prebuild 自动 check-nav）。

> 走查：全局清单见 `checklist.md`（本文件不单独维护清单）。
