# Counterexamples — 高价值反例

统一结构：**Wrong → Why → Right → Root cause → Related rules**。这些是最常见的高价值错误；遇到类似模式先停下来对照。

## E-01 · Placeholder replaces label

**Wrong**

```tsx
<input placeholder="邮箱" />
```

**Why**

- 输入内容后字段含义消失。
- placeholder 不是稳定的字段标签。
- 基础表单可用性被破坏。

**Right**

```tsx
<label htmlFor="email">邮箱</label>
<input id="email" placeholder="you@example.com" />
```

**Root cause**

把「视觉减少」误当成「认知负担减少」。

**Related rules**

- C-02 · C-04

## E-02 · div as button

**Wrong**

```tsx
<div onClick={submit} className="cursor-pointer">保存</div>
```

**Why**

- 不可聚焦、不可键盘操作、读屏不可见。
- 无 disabled 语义、无 loading 状态挂载点。

**Right**

```tsx
<button type="button" onClick={submit}>保存</button>
```

**Root cause**

用展示元素模拟原生交互，省掉了语义成本。

**Related rules**

- C-01 · C-03

## E-03 · Color-only status

**Wrong**

```tsx
<span className="text-red-500">错误</span>
```

**Why**

- 色觉障碍用户无法区分状态。
- 无 `aria-invalid` / 错误说明，读屏得不到原因。

**Right**

```tsx
<div id="email-error" role="alert" className="text-red-600">
  <svg aria-hidden="true">…</svg> 邮箱格式不正确
</div>
<input aria-invalid aria-describedby="email-error" />
```

**Root cause**

把状态表达完全交给颜色通道。

**Related rules**

- C-05 · S-08

## E-04 · Unnecessary dependency

**Wrong**

```tsx
npm install lucide-react   // 只为了一个 search 图标
```

**Why**

- 项目已有手写 inline SVG 图标集与图标组件。
- 新增依赖增大产物、提升维护成本，与任务无关。

**Right**

```tsx
// 复用项目 primitives 里的图标组件 / inline SVG
<SearchIcon className="size-4" />
```

**Root cause**

默认引入外部库而不是先检查已有能力（M-03）。

**Related rules**

- M-03

## E-05 · Unnecessary abstraction

**Wrong**

```tsx
// 仅一个使用点，为了「复用」抽象出带 6 个配置项的 HOC
withDropdown(openable)(MyList)
```

**Why**

- 抽象本身成为新的认知负担。
- 与「减少不必要决策」的原则相反。

**Right**

在组件内部直接实现；出现第二个真实需求时再抽取。

**Root cause**

为「复用」提前抽象，而非为真实需求抽象。

**Related rules**

- §Minimalism · 复杂度测试

## E-06 · Excessive animation

**Wrong**

```tsx
// 进入页面时每个元素各自从不同方向飞入
<div className="animate-slide-in-right">…</div>
<div className="animate-slide-in-left">…</div>
<div className="animate-fade-up">…</div>
```

**Why**

- 分散注意力，破坏层级与阅读节奏。
- 装饰性动效过多，违反「一个页面最多一两处」。

**Right**

单个克制的进入动画（如 `animate-fade-up` 统一节奏），或完全不用。

**Root cause**

把动效当装饰堆砌，而不是服务层级与反馈。

**Related rules**

- S-03 · S-06 · §Motion

## E-07 · Mobile hover dependency

**Wrong**

```tsx
{/* 唯一入口在 hover 上 */}
<nav className="hidden hover:block">…</nav>
```

**Why**

- 触屏没有 hover，核心导航不可达（C-06）。

**Right**

```tsx
// 小屏：抽屉/底部导航/点击展开
<button onClick={toggleDrawer} aria-expanded={open}>菜单</button>
<Drawer open={open} onClose={close} />
```

**Root cause**

用桌面交互模型假设所有设备。

**Related rules**

- C-06 · §Responsive

## E-08 · Unnecessary visual effects

**Wrong**

```tsx
// 一个普通的保存按钮，叠加 glass + glow + 粒子
<button className="glass glow-pulse particles">保存</button>
```

**Why**

- 效果不服务任务，反而稀释主操作。
- 增加渲染成本与维护成本。

**Right**

```tsx
<button className="bg-zinc-900 text-white hover:bg-zinc-700 rounded-md px-4 py-2">保存</button>
```

**Root cause**

把「高级」理解成叠加效果，而不是 typography / spacing / hierarchy / subtle motion。

**Related rules**

- Y 类（可选用，但必须通过复杂度测试）· visual.md Intent Translation

## E-09 · Duplicate existing component

**Wrong**

```tsx
// 项目已有 <Avatar>，又新建一个结构几乎相同的组件
function ProfilePhoto({ src, name }) { … }
```

**Why**

- 两处维护同一能力，风格漂移。
- 违反 M-01。

**Right**

```tsx
<Avatar src={src} name={name} />
```

**Root cause**

不先 Inspect 组件清单就动手。

**Related rules**

- M-01

## E-10 · Unrelated refactoring

**Wrong**

```tsx
// 任务：改按钮颜色；顺手把整个文件改成箭头函数、重命名一堆变量
```

**Why**

- diff 膨胀、审查困难、引入回归风险。
- 违反 M-06。

**Right**

只改任务相关的行。

**Root cause**

把「规范感」凌驾于任务边界之上。

**Related rules**

- M-06

## E-11 · Danger-first focus in confirm dialog

**Wrong**

```tsx
// 删除确认弹窗的初始焦点落在红色「删除」按钮上
<AlertDialog autoFocusSelector="#btn-delete" />
```

**Why**

- 键盘用户打开弹窗后一次 Enter 就触发不可逆操作。
- alertdialog 的焦点管理错误，违反覆盖层焦点规则。

**Right**

```tsx
// 初始焦点落在安全选项（取消）；危险按钮永不默认聚焦
<AlertDialog initialFocus="#btn-cancel" />
```

**Root cause**

把「打开弹窗」当成单一动作，没有按对话框类型区分初始焦点策略。

**Related rules**

- C-01 · `accessibility.md` §Keyboard 初始焦点

## E-12 · Unescaped user input

**Wrong**

```ts
commentList.innerHTML += `<div>${user.nickname}: ${user.text}</div>`;
```

**Why**

- 用户输入未转义直接进 DOM，构成存储型 XSS。
- 评论/昵称等 UGC 内容是最常见的注入入口。

**Right**

```ts
const el = document.createElement("div");
el.textContent = `${user.nickname}: ${user.text}`; // textContent 天然安全
commentList.appendChild(el);
```

**Root cause**

用字符串拼接表达结构，绕过了安全渲染层。

**Related rules**

- M-07

## E-13 · Event listener / rAF leak

**Wrong**

```tsx
useEffect(() => {
  window.addEventListener("resize", onResize);
  const raf = requestAnimationFrame(loop); // 卸载时既不移除监听，也不 cancel
}, []);
```

**Why**

- 组件卸载后监听与动画循环仍在跑，内存泄漏 + 隐性错误。
- 移动端长期运行后性能劣化。

**Right**

```tsx
useEffect(() => {
  window.addEventListener("resize", onResize);
  const raf = requestAnimationFrame(loop);
  return () => {
    window.removeEventListener("resize", onResize);
    cancelAnimationFrame(raf);
  };
}, []);
```

**Root cause**

只写「开启」不写「关闭」，把 effect 当一次性初始化。

**Related rules**

- `engineering.md` §Performance · `recipes/advanced-interactions.md` §通用实现纪律

## E-14 · Pointer capture without fallback

**Wrong**

```ts
el.setPointerCapture(e.pointerId); // 部分触屏环境抛异常，拖拽直接失效
```

**Why**

- `setPointerCapture` 在个别浏览器/嵌入式 WebView 会抛 `NotFoundError`，无降级时整个拖拽交互中断。

**Right**

```ts
try { el.setPointerCapture(e.pointerId); } catch { /* 降级：跟随 elementFromPoint */ }
```

**Root cause**

把「必须 Pointer Events」当成无条件的 API 调用，忽略了环境容错。

**Related rules**

- `recipes/advanced-interactions.md` §通用实现纪律

## E-15 · Runtime class concatenation

**Wrong**

```tsx
// 类名在运行时拼接，Tailwind 无法静态分析
<div className={`bg-${tone}-500 text-${tone}-600`}>…</div>
```

**Why**

- `bg-${tone}-500` 不是完整的类名字面量，构建时扫描不到，生产构建直接缺样式。
- 任意字符串组合不可枚举，review 与测试无法覆盖所有分支。

**Right**

```tsx
// 枚举显式化，class 全部为静态字面量
const tones = {
  primary: "bg-zinc-900 text-white",
  danger: "bg-red-600 text-white",
} as const;
<div className={cn("rounded-md px-4 py-2", tones[tone])}>…</div>
```

**Root cause**

用字符串拼接表达枚举，而不是把枚举显式化为可枚举的 class 集。

**Related rules**

- M-02 · `engineering.md` §Tailwind

## E-16 · Breaking public API in a small change

**Wrong**

```tsx
// 任务只是改配色，顺手把受控组件改成非受控、删掉了 onLoadMore prop
function UserList({ items }: { items: Item[] }) { … }
// 原来：<UserList value={v} onChange={setV} onLoadMore={load} />
```

**Why**

- 所有调用方 silent break，类型错误在下游构建时才爆。
- 「小改动」的 diff 里混入破坏性变更，审查与回滚困难。

**Right**

保持 props 签名与受控/非受控行为不变；确需变更时新增 prop 并保留旧 prop 一个过渡期（deprecation），附迁移说明。

**Root cause**

把内部重构的冲动凌驾于调用方契约之上。

**Related rules**

- M-04 · M-06

## E-17 · Async section with only the success state

**Wrong**

```tsx
// 数据区只有「渲染成功」一种形态
{data && <Table rows={data} />}
```

**Why**

- loading：>300ms 的等待没有任何反馈（或骨架一闪而过）。
- empty：无数据时白屏，用户不知道该做什么。
- error：请求失败无提示、无重试路径。

**Right**

```tsx
{isLoading ? <Skeleton /> : error ? <ErrorState onRetry={refetch} /> :
  data.length === 0 ? <EmptyState /> : <Table rows={data} />}
```

loading / empty / error 三态齐全；空态 = 图标 + 说明 + 行动。

**Root cause**

只实现 happy path，把「数据总会正常回来」当成默认假设。

**Related rules**

- M-05 · `interaction.md` 状态全集 · S-09

## E-18 · Minimal misread as removing necessary information

**Wrong**

```tsx
// 「做得极简一点」→ 删掉 label、帮助文字和错误提示
<input placeholder="搜索" />
```

**Why**

- 删除必要信息不是简化，是破坏可用性：输入后字段含义消失（E-01）、错误不可见。
- 极简的对象是视觉决策，不是信息与反馈。

**Right**

保留 label / 帮助 / 错误 / 反馈；删除的是装饰、多余表面与无目的动效。

**Root cause**

把「视觉上少」当成目标本身，忘了目标是减少决策负担。

**Related rules**

- C-04 · C-05 · `visual.md` §Minimal Style Preset

## E-19 · Doctrinal stripping（教条式做减法）

**Wrong**

```tsx
// 「极简风」= 全灰 + 全去边框 + 圆角归零 + 不用动画
<div className="bg-neutral-100 text-neutral-800">…</div>
```

**Why**

- 颜色承担层级 / 状态 / 身份，全灰让错误与重点不可辨（C-05）。
- border 承担分组、边界与输入暗示，全去掉后结构不可读。
- 圆角归零是 Brutalist 的语言，与极简无关。
- 反馈类动效承担 feedback / continuity，删掉后操作失去回应。

**Right**

按 `visual.md` §Minimal Style Matrix 逐维决策：每个维度先问「它承担什么」，再决定去留；删的是无目的项。

**Root cause**

把风格词表（少颜色 / 少边框 / 少圆角 / 少动效）当成规则本身，而不是重新逐维做决策。

**Related rules**

- C-05 · S-03 · S-06 · `visual.md` §Minimal Style Preset 误读清单

## E-20 · One template for every page（泛模板化）

**Wrong**

```tsx
// 不看内容与场景，所有页面套同一套「白底 + 圆角卡片 + 灰色文字」
```

**Why**

- 这已经不是极简，而是 generic：层级、品牌与内容差异被模板抹平。
- 用户要求的是完成任务的结构，不是千篇一律的外观。

**Right**

从内容与任务出发建立层级与分组；个性允许来自品牌色、布局与文案节奏，无意义复杂度保持低。

**Root cause**

用「安全的主流样式」替代逐页的视觉决策，放弃了决策职责。

**Related rules**

- `visual.md` §Intent Translation · §Minimal Style Preset

---

## 反例索引（规则 ↔ 反例）

| 规则 | 直接反例 | 相关联的反例 |
| --- | --- | --- |
| C-01 键盘完备 | E-02 | E-11 |
| C-02 可访问名 | E-03（部分） | — |
| C-03 语义交互 | E-02 | — |
| C-04 表单标签 | E-01 | — |
| C-05 非颜色信息 | E-03 | — |
| C-06 核心响应式 | E-07（hover 依赖） | — |
| M-01 复用组件 | E-09 | E-04 |
| M-02 静态类 | E-15 | — |
| M-03 依赖优先 | E-04 | E-09 |
| M-04 公共 API | E-16 | E-10 |
| M-05 适用状态 | E-17 | — |
| M-06 无关重构 | E-10 | — |
| M-07 安全渲染 | E-12 | — |
| 动效纪律 | E-06 | E-08、E-13 |
| 资源清理 | E-13 | — |
| 环境容错 | E-14 | — |
| Minimal 误读（删必要信息） | E-18 | — |
| 教条式做减法 | E-19 | E-08 |
| 泛模板化 | E-20 | — |
| 复杂度测试 | E-05、E-08 | E-06 |

> 维护约定：新增反例后同步更新本表；某规则连续两版都没有专属反例时，优先在下次迭代补充。（M-02 / M-04 / M-05 的缺口已于 v1.2.0 补齐：E-15 / E-16 / E-17。）