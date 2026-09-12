# Interaction — 交互状态

组件需要覆盖**适用**的交互状态，但不是每个组件都必须实现所有状态。只实现适用的（applicable states）。

## 状态全集

```text
default · hover · focus · active · disabled · loading · error · empty · success · no-permission
```

> `no-permission`（无权限/禁用页面）是中后台必备态：给出「无权限」说明 + 联系管理员/切换账号的下一步动作（构成空态模式），不白屏、不只藏按钮。

| 组件家族 | 适用状态 |
| --- | --- |
| Button / 可点击 | hover · focus · active · disabled · loading |
| Input / 表单控件 | focus · error · disabled（+ required） |
| Data display（列表/表格/卡片） | loading · empty · error |
| 反馈类（Toast/Alert） | success · error · info |
| 选择类（Select/Combobox） | open · hover · focus · selected · disabled · loading · empty · error |

## 反馈时机（来自 Handbook）

- 所有操作 **100ms 内**有视觉反馈（按下变色/缩放）。
- 异步操作 **超过 300ms** 才显示加载态，避免一闪而过。
- 加载中禁止重复点击（按钮 loading 态 + disabled）。
- 成功轻提示约 3s 自动消失；错误提示要等用户处理，不自动消失。

## 指针与触屏一致（Pointer Events）

- 拖拽、滑动手势基于 Pointer Events（pointerdown/pointermove/pointerup + setPointerCapture），桌面与触屏行为一致。
- hover 不得是唯一交互方式；触屏上 hover 效果要有 tap 替代。
- 触摸目标 ≥ 44×44，相邻 ≥ 8px；32 / 36px 密集尺寸仅限桌面鼠标精确输入场景，触屏可达控件一律 ≥ 44（见 `tokens.md` §尺寸）。

## 通用状态实现要点

### hover / focus / active

```tsx
<button className="bg-zinc-900 text-white hover:bg-zinc-700 focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-zinc-900 active:scale-[0.98] disabled:opacity-50 disabled:pointer-events-none">
```

- 按下轻微缩小（如 `active:scale-[0.98]`）。
- focus 用 `focus-visible` 而非 `focus`，避免鼠标点击也出现外框。
- disabled 用 `pointer-events-none` + 视觉降级，且不被聚焦。

### loading

- 按钮 loading：禁用 + spinner + 文案不变或「提交中…」。
- 数据区 loading：骨架屏（`.skeleton`）优先于转圈；加载 >300ms 才显示。

### error

- 错误信息出现在字段正下方，红字 + 图标（不只用颜色，见 C-05）。
- 随输入实时更新；说明原因和解决方式。
- 字段 `aria-invalid` + `aria-describedby` 关联错误文本。

### empty

- 空状态 = 图标 + 说明 + 下一步动作，不白屏（`recipes/data-display.md` Empty State）。

## 键盘交互（核心任务）

- 自定义控件遵循原生键盘模式：Tabs 方向键 + roving tabindex；Combobox ↑↓ / Enter / Esc；Switch 空格；Modal 焦点圈 + Esc 关闭。
- 焦点可见（focus-visible 外框）。

## 检查

交互状态走查并入统一清单 `references/checklist.md`（全局清单「覆盖适用交互状态」项 + 领域附加项），本文件不再单独维护清单。核对时对照上方「组件家族 × 适用状态」表。