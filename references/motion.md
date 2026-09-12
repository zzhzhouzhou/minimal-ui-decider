# Motion — 动效

Motion 服务功能，不是纯装饰。动效应该服务于：

```text
feedback（反馈） · state transition（状态转换） · hierarchy（层级） · focus（聚焦）
· opening / closing（开合） · page transition（页面转场）
```

## 规则

- **只对 transform / opacity 做动画**（位置、大小、透明度），不要对 width / height / color 等属性动画——否则卡顿。**例外**：小面积 disclosure（accordion / details 类展开收起）可用 `grid-template-rows: 1fr ↔ 0fr` 过渡；判断标准是 layout 动画的**面积与频率**——小而低频可接受，大面积/高频（列表、页面级）不行。
- **subtle / purposeful / interrupt-free**：动画快速、克制、可被打断（interrupt-free），新动画到来时立即接管。
- 出现用「先快后慢」节奏（cubic-bezier 或 ease-out）；**退出比进入快约 20%**，界面才干脆。
- 装饰性动效要克制：一个页面最多一两处。
- 避免「页面打开时所有元素同时从不同方向飞入」。
- 所有动效尊重 `prefers-reduced-motion`（见 `accessibility.md` §Reduced motion）：CSS 全局降级 + JS 侧用 `matchMedia('(prefers-reduced-motion: reduce)')` 读取并监听变化，为 true 时跳过帧循环直接呈现终值。

## 时长（token，见 references/tokens.md）

```text
hover / press / color change   150ms
menu / dialog 出现             200–300ms（scale-in + fade）
退出                           比进入快 ~20%
页面转场                       400–500ms（仅大面积变化）
```

## 复用现成动画令牌（真实项目 @theme）

```tsx
<div className="animate-scale-in">…</div>          {/* 弹窗/菜单进入 */}
<div className="animate-slide-up">…</div>          {/* 底部面板 */}
<div className="animate-fade-up">…</div>           {/* 淡入上移 */}
<div className="animate-ripple">…</div>            {/* 涟漪点击 */}
<div className="animate-marquee">…</div>           {/* 跑马灯 */}
<div className="animate-shimmer">…</div>           {/* 微光/骨架 */}
```

`--animate-*` 与 `@keyframes` 定义集中在 `@theme`（真实项目 index.css），不要在组件里重复定义同一动画。

## 进入 / 退出模式

```tsx
{/* 覆盖层：进入 scale-in + fade；退出更快 */}
<div
  data-state={open ? "open" : "closed"}
  className={
    open
      ? "animate-scale-in"
      : "pointer-events-none opacity-0 scale-95 transition-[opacity,transform] duration-150"
  }
>
```

退出用 CSS transition 更快（150ms）或缩短 duration；关闭动画结束再卸载（或保持 DOM + pointer-events-none）。

**关闭期防护（closing state）**：退出动画进行中（~120–150ms）必须防止——

- 触发器被再次点击导致重复打开；
- 焦点被 focus trap / 自动聚焦逻辑抓回正在卸载的弹层。

常用做法：设置 `closing` 标记/类，打开函数检测到 `closing` 时先完成关闭再重开；焦点复位在动画结束后执行一次，避免与 trap 竞争。

## 页面转场（View Transitions）

- `document.startViewTransition` 可用于主题切换/页面转场（Y-12，见 `recipes/advanced-interactions.md`）。
- 真实项目关闭默认淡入淡出，交给 clip-path 动画；仅在用户明确想要或转场有信息价值时使用。

## 检查

动效走查并入统一清单 `references/checklist.md`（全局清单「视觉与动效」组），本文件不再单独维护清单。