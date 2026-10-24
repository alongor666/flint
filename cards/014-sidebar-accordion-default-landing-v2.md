# 014 · 侧边栏手风琴 + 默认落点改经营分析新版

- 状态: discussed
- 目标仓: chexian-api（先探已定：导航展开记忆在 `src/components/layout/SidebarNavigation.tsx`，默认落点在 `src/app/App.tsx` HomeIndexRedirect → `getDefaultRoute()`（`src/shared/config/organizations.ts`））
- 类型: 优化（导航交互 + 默认路由）
- 录入: 2026-10-24

## 原文

（owner 2026-10-24，分两条消息送达，按序拼接）

> 现在导航栏默认是打开首页，并且会记住用户的情况。我希望进行以下调整：
>
> 我希望只展开一个一级目录，其他的一级目录都折叠起来。首次打开，进入经营分析新版

## 先探结论（零成本通道）

- 目标仓判定 chexian-api：「经营分析新版」= `/dashboard/overview-v2`（App.tsx:474，#2274，权限随 /dashboard）；quote-vision 无此页面。
- 现状一（展开记忆）：`SidebarNavigation.tsx:110` localStorage 键 `sidebar-expanded-groups` 存「展开集」（数组、可多组同开），持久化唯一落点 L250-268（卡 2026-09-13-claude-c2d134）；旧 key `sidebar-collapsed-groups`（存收起集）有迁移清理（L113/L241）。即 owner 说的「会记住用户的情况」。
- 现状二（默认落点）：App.tsx:380-388 `HomeIndexRedirect`，现行裁定 CX-ADR-14e573（2026-09-15）：全员固定落「经营总览 › 首页」，判据 100% 委托 `getDefaultRoute()`；HashRouter 下仅根地址/新标签触发，站内刷新不回首页。
- 现状三（落地联动）：C1 落地标记（`landing-state.ts`）会让 SidebarLayout 在落地时做一次导航复位（展开侧栏 + 展开当前页所属分组），App.tsx:383-385——手风琴改造须与该机制协同（落地展开当前组时收起其余组）。
- 需求映射：①一级目录改手风琴（同时至多展开一组，展开新组自动收起其他）；②默认落点从「经营总览›首页」改为「经营分析新版」。

## 澄清（AUQ）

- Q1 「首次打开进入经营分析新版」的落点语义: A（推荐）默认落点直接改为 overview-v2（根地址进入恒落该页，其余记忆行为不变）/ B（仅无记忆的首次落该页，之后记住用户上次最后访问页——需新增「上次页面」记忆，改动面大一截）→ **owner 答: A**（2026-10-24）
- Q2 手风琴下的展开记忆: A（推荐）保留记忆——唯一展开组刷新后恢复（沿用 storage key，值退化为 ≤1 元素）/ B 去掉记忆——每次打开全收起，仅展开当前页所属组 → **owner 答: A**（2026-10-24）

## 讨论

裁定后的需求定稿（两项，同一 PR 可承载）：

1. **手风琴**：`SidebarNavigation.tsx` 的 `expandedGroups` 由多值集合退化为「至多一组」——点开一组即收起其余；localStorage 键 `sidebar-expanded-groups` 沿用、值恒 ≤1 元素（旧多元素值首次读取时取当前页所属组或首元素收敛，不另起 key、不再加一轮迁移清理）。C1 落地复位（展开当前页所属组）天然满足手风琴，只需确认复位路径走同一 updater。
2. **默认落点**：`getDefaultRoute()` 返回值改为 `/dashboard/overview-v2`。**这是对 CX-ADR-14e573（2026-09-15「全员固定落经营总览›首页」）的替换裁定**，探索时须按目标仓 ADR 纪律补登新裁定/废止旧条，不能只改常量。overview-v2 权限与 /dashboard 同源，落点守卫无需新增分支。

有现成回归锁须同步改：`src/components/layout/__tests__/sidebar-navigation-collapsed.test.tsx`（展开表丢失更新 / 落地到首页）、`tests/default-landing-navigation.test.tsx`（源码结构锁）。待「探索 014」时核 `getDefaultRoute` 全部调用方、角色分支、以及 ADR 登记位置。
