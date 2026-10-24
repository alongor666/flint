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

### 第二轮裁定（2026-10-24 owner）

- **落点切换推迟**：半成品不当门面——等 overview-v2 成熟再切；首页经营体检**打算融合进** overview-v2（融合属新范围，成熟门槛之一，待明确后独立处理）。手风琴裁定未被推翻、维持原议。
- 更新触达降权不是顾虑（正合 owner 意向，见 015 卡）；403 风险 owner 要求量化角色清单（下方已探明）。

### 成熟度评估（2026-10-24 探查，main 快照）

- **数据面已远超 MCP 注册表旧描述**（`server/src/config/operating-overview.ts:143-150` 现行矩阵）：八卡主值 **7 卡可算**（签单保费/保费收入/收入进度/收入同比/变动成本率/获取成本率/保单年赔付率），仅「历年制赔付率」主值缺 G3+G5；收入族比较/实际变化已通（#2387 D1 当前数据重算口径）；成本族比较经账面基期通道已通（#2435，2026-10-22），G3 只剩成本族「较上月末」列；改善影响度全族挂 G4 但已接线（#2389 D2，有实例出数无实例留空）。
- **功能面成型**：报告导出（#2299 C2）、八卡趋势图补全（#2403）、结论体/预警登记/聚焦重开等 15 个测试文件；组件含 ImpactWorkspace/CardFace/ConclusionBody/ReportExportBar。
- **未成熟项**：①历年制赔付率卡——G3（真实历史时点快照）+G5（总公司原件对账）依赖数据发布方/业务侧，非代码可解，短期无解；②首页体检融合——owner 新范围，未开工；③#2392 八卡推送通道——方案 10-23 刚出未实施（外围，不阻塞门面）。
- **判定**：接近成熟未到。切换门槛建议＝体检融合完成＋历年制卡定处置（隐藏或缺项展示）。

### 403 拒绝面（探明）

- deniedModules 负面清单仅三页（apps-hub/access-control/user-management，`preset-users.ts:136-144`），与 /dashboard 无关；真正拦截来自 allowedRoutes 白名单。
- **唯一被拒角色 = org_user（三级机构账号）**：默认白名单 `ORG_USER_DEFAULT_ALLOWED_ROUTES`（前端 organizations.ts + 后端 ORG_ROLE_ALLOWED_ROUTES 镜像）含 /home 不含 /dashboard。其余角色全通：branch_admin/sx_manager/双电销/双金融同业=豁免或无白名单条目放行；sx_yeguan 白名单显式含 /dashboard。
- 将来切落点的正解：getDefaultRoute 内加能力判断（有 /dashboard 权限落 overview-v2，org_user 回落融合后的体检页）——与「判据 100% 委托 getDefaultRoute」精神一致。**体检融合与 org_user 白名单是同题**：融合后要么给 org_user 开 /dashboard（连带放开整个 KPI 看板族，权限面扩大），要么 overview-v2 挂独立守卫路径，融合设计时必裁。

### 跨组切换兼顾（探明）

- 现成零成本覆盖：NavSearch（与展开状态零耦合，搜页名直达，本身就是跨组最快路径）+ strongAnchor（收起组含当前页强锚回跳）+ 折叠 rail 态（64px 图标条常驻，collapsed=侧栏整体隐藏，PR-2a 语义；未实证有无悬浮子菜单，grep 无 flyout）。
- 可选增强（探索期细化）：rail 组图标 hover 悬浮子菜单（VSCode activity bar 式一击直达任意页）；不推荐「双击/长按例外多开」（破坏手风琴纯度，回退半步）。

### 影响评估补记（2026-10-24 owner 问「坏影响与变化」时探明）

- **前后端镜像改动面（初评遗漏）**：`getDefaultRoute()` 为恒值实现（organizations.ts:779-781 直接返回 `DEFAULT_LANDING_ROUTE='/home'`:387），但该常量与后端 `server/src/config/preset-users.ts` 的 `DEFAULT_LANDING_ROUTE` 同值镜像，`/me` 仍回传 defaultRoute 字段（前端不消费、契约仍在），两侧各挂单测锁字面量（前端 `organizations.test.ts`/后端 `preset-users.test.ts`）——改落点=2 源码+2 单测+ADR 登记共 5 处。
- **权限被拒角色落地撞墙（探索期待办）**：overview-v2 守卫挂父路径 /dashboard 拒绝清单（deniedModules 精确匹配）；恒值落点下被拒角色根地址落地即被 RouteAccessGuard 拦，现状 /home 无此面。实施时须守卫侧回退或 getDefaultRoute 内保留能力判断，且不违反 CX-ADR-14e573「判据 100% 委托 getDefaultRoute」精神。
- **副作用面**：①008 卡「首开=铃铛红点+首页摘要卡」的摘要卡曝光随默认落点离开首页而失去主路径（只剩铃铛）；②首页经营体检从每日必经变冷门页；③跨组高频切换用户每次多一击；④旧用户 localStorage 多元素展开集首读收敛为一组（一次性感知）；⑤overview-v2 为 #2274 半成品态（八卡七缺项），默认落点=每天首屏直视缺项占位；⑥生产闸 E2E 实测过「落地到首页 /home」，须同步改。
- NavSearch 无 expandedGroups 直接联动（grep 零命中），跳转走路由+strongAnchor（L440 收起组含当前页强锚）机制，手风琴天然适配。
