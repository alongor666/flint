# 016 · 前日更新/历史更新入口降权

- 状态: scouted
- 目标仓: chexian-api（先探已定：008 卡产物——首页更新摘要卡+铃铛红点在 `src/features/` updates 域与 guide 域 /updates 历史页）
- 类型: 优化（信息架构/入口位置）
- 录入: 2026-10-24

## 原文

（owner 2026-10-24，在 014 影响评估讨论中带出）

> 更新我原本也不打算，因为它只是系统的更新，并不是数据分析。因为我们的主要用户还是数据分析者，怎么更新的并不是最核心的功能，所以这一点也没有关系。甚至本来我都想把前日更新及历史更新放到不那么显眼的地方。

## 先探结论

- 动因定位：用户观——主要用户是数据分析者，系统更新日志非核心功能；008 卡「首开=铃铛红点+首页摘要卡」的主路径设计与该定位冲突，摘要卡属过度曝光。
- 与 014 的耦合：014 落点切走首页后更新曝光自然下降，两卡同向；但降权是独立诉求（即便落点不动也可做）。

## 澄清（AUQ）

- Q1 降权形态: A（推荐）撤首页摘要卡、铃铛保留（红点未读提示仍在，点开见前日更新）/ B（推荐外）铃铛也撤，更新历史只在 guide 域 /updates 页可达（入口藏进设置或页脚）/ C 降为首页角落小字链接 → **owner 答: A（2026-10-26 AUQ 补答）**
- Q2 「前日更新」的每日触达是否保留推送/提醒能力: A（推荐）保留铃铛红点（想知道的人有信号）/ B 完全静默，只有主动访问 → **owner 答: A（2026-10-26 AUQ 补答）**

## 讨论

范围很小（前端入口调整，008 产物已有 per-username 已读键与历史页骨架，撤摘要卡+挪铃铛即可）。等 owner 对 Q1/Q2 表态或直接说「探索 015」。

## 探索 #1（2026-10-26）

- 探查锚: chexian-api @ origin/main 198e818a7（静态核实，`git show origin/main:<path>`，行号可复核）
- 防撞车（两法同证）: ①gh——开放 issue 搜「更新入口」「摘要卡」命中的 #2335/#2544/#2595/#1751/#1752/#1629/#2392 均无关，开放 PR 搜 updates 零命中；②git——近 30 日 `src/features/updates/` 改动全属 #2208 特性线（9a022e157 建、19cbc6327 测试存根、c7caff6eb 返修），无降权/撤卡工作。
- 实施要点（改动面＝净删约 5 文件，零新增）:
  - `src/features/pages/LandingHomePage.tsx:6,49-50`：删 import 与 `<HomeUpdatesCard />`（连同 :49 注释行）；同容器 CoefficientMonitorSummaryCard / HealthCheckSection 不动
  - 删 `src/features/updates/HomeUpdatesCard.tsx` 与 `__tests__/HomeUpdatesCard.test.tsx`
  - `src/features/updates/index.ts:3-4`：删导出行，头注释「首页摘要卡」措辞同步
  - `src/features/INDEX.md:40`：updates/ 行去掉摘要卡描述与文件清单项（目标仓 AGENTS.md §7 多文件改动同步索引）
  - **孤儿导出（先删后加红线）**：`readState.ts:52 latestUnreadDay` 仅摘要卡消费（铃铛用 hasUnreadDays/latestDayDate、页面用 latestDayDate，`git grep` 实证），删卡后成死导出须同删，`readState.test.ts` 对应用例一并清
  - 零语义副作用：卡片不写已读态（纯消费 useUpdatesRead），已读水位推进在铃铛「全部标已读」与 /updates 页浏览——删卡不改红点行为；e2e 仅 `tests/e2e/13-a11y-axe.spec.ts:151-159` 注释提及（铃铛计数不含卡片，不破 a11y 基线）
- 与 014 的关系：落点段挂起中、LandingHomePage 仍是全员落地页（页头注释 CX-ADR-14e573），删卡与落点切换正交、先后皆可
- 转正映射建议: 1 卡 → 1 issue（type/feature）→ 1 PR；与 014 无文件冲突可并行
- 验收: ①`bun run typecheck` 绿（无悬空 import）；②`bun run test --run src/features/updates src/features/pages` 绿；③未读态下首页不再渲染「系统更新」卡（`data-testid="home-updates-card"` 消失——注意无未读时本就零足迹，验收前须清 `updates-read-v1:*` localStorage 造未读）；④铃铛红点/下拉/全部标已读与 /updates 页行为不变；⑤`tests/e2e/13-a11y-axe.spec.ts` 铃铛计数断言不动仍绿

## 开工记录

（待「开工 016」）
