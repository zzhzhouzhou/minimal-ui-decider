# Responsive — 响应式

Responsive 不是简单写 `sm:` `md:` `lg:`，而是思考：**用户在不同空间（屏幕/输入方式/上下文）下如何完成任务。** 内容放不下时才切换布局，不默记设备型号。

## 原则

- **mobile-first**：先从手机设计，保证小屏可用，再逐级向大屏增强。
- **touch-friendly**：触控目标 ≥ 44×44，相邻 ≥ 8px。
- **content priority**：核心内容与核心操作在窄屏优先可见可达。
- **layout transformation**（布局转换，不是简单隐藏）：

| 桌面端 | 手机端 |
| --- | --- |
| 多列表格 | 卡片列表 |
| 侧边栏 | 抽屉 / 底部导航（入口 ≤ 5 个） |
| 居中弹窗 | 底部面板（bottom sheet） |
| 悬停展开 | 点击展开 |
| 横向导航 | 底部标签栏 或 汉堡菜单 |

- **hover 不得成为唯一交互方式**：mobile 无 hover 时核心功能仍然成立。
- 必要时使用 container queries（容器尺寸而非视口）。

## 断点

**Canonical 定义见 `references/tokens.md` §断点（五档）**，此处只列应用方式，数值不在此重复：

```text
mobile   < 640px          基线设计，320px 为底线
tablet-sm / tablet ≥ 640 / 768   两列布局、横向空间开始可用
laptop   ≥ 1024px         侧边栏常驻、多列数据
desktop  ≥ 1280px         内容宽度收口（应用类 ~1152px）
```

## 底线

- **320px 宽度不允许横向滚动。**
- 触屏没有 hover：所有 hover 交互必须有点按替代。
- 桌面端：`Sidebar + Content`；Mobile 端：`Drawer / Sheet + Content`——而不是把侧边栏 `hidden` 掉导致核心功能不可达（违反 C-06）。

## 实现模式

```tsx
{/* 表格 → 卡片：小屏卡片列表，大屏表格 */}
<div className="grid gap-4 md:block">
  <DesktopTable className="hidden md:block" />
  <MobileCards className="md:hidden" />
</div>
```

注意：`hidden md:block` 用于**渐进增强的展示层**没问题，但禁止把核心功能直接隐藏（C-06）。

> 走查：全局清单见 `checklist.md`；响应式特有项见 `checklist.md` §响应式附加项。
