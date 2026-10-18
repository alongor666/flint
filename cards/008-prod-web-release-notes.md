# 008 · 生产网页更新发布（每日首开见更新 + 历史结构化浏览）

- 状态: clarifying
- 目标仓: chexian-api（前端路由 + 服务端；数据链可能涉 auto_loadbi / 发布机）
- 类型: 功能（owner 表述「向生产端网页发布、面向用户的更新」）
- 录入: 2026-10-18
- 关联: 007（周更发布说明工作流）——本卡是 007 产物「用户版」的网页产品化延伸；007 管内容生产，本卡管内容呈现。

## 原文

> 向生产端网页发布、面向用户的更新应该是怎样的，如果发布在网页，每日首次打开即可看到前一天的更新，历史更新也放一个链接给大家能结构化看到会如何？该如何设计？先充分探索历史和现状，包含已经归档的历史pr，你可通过20轮以内的auq来探索我的需求

## 澄清（AUQ）

- （待答：更新锚点 / 内容生产链 / 首开展示形态 / 历史页归属，见讨论段分叉清单）

## 讨论

owner 诉求拆解：① 面向用户的更新发布到生产网页（而非只发 IM/文档）；② 每日首次打开即见前一日更新；③ 历史更新一个链接结构化浏览；④ 要求先探索历史与现状（含归档历史 PR）。

与 007 分工：007（alongor666-skills 目标仓）管「周更发布说明怎么产出」（三层流水线，已实测 W42）；本卡管「产出后如何在生产网页呈现与触达」。

### 探索 #1（2026-10-18）

- 探查锚: chexian-api-dev @ 11aa3887（origin/main，2026-10-18）
- 方法: 只读 grep/ls/head，未改任何文件

**现状事实（可复核）**：

1. **两类"更新"并存，节奏不同**：
   - 数据更新：`scripts/auto-release-daily.mjs` 每日两批自动发布（早批 01 签单+05 理赔 ~07:40、晚批 02 报价+03 维修+04 厂牌 ~12:00，launchd 每 15 分钟 tick），带飞书/企微告警通道。每日都有。
   - 代码发布：`scripts/generate-release-metadata.mjs` 构建期写 release.json（releaseSha+builtAt）入前端 dist 与 server/dist；`server/src/config/release.ts` 校验加载；#2130 规划提过「周日晚发布后/周一体检推送前」窗口惯例——代码按周部署，非每日。
   - ⇒ **PR 合并 ≠ 生产上线**：main 每日 ~20+ PR 合并，但用户系统里看到的功能只随周部署生效。「每日展示前一日 PR」会把未上线内容当更新展示。
2. **前端已能感知两类版本**：`GET /api/data/version`（server/src/routes/data.ts:568）返回 etlDate + contentVersion（保单内容指纹）+ releaseSha（发布指纹）+ serverStartTime；`src/app/etlVersionPoller.ts` 页面侧 5 分钟轮询、localStorage 基线 `chexian:last-etl-date`、变化即清 SW 缓存+失效 React Query（注释载明 SW 内检查双死的教训）。
3. **"更新感知"UI 已有家族**：`src/features/data-freshness/`（Host/Panel/Ring 三件套 + useDataFreshness，7 域 × 预计点/数据日期/状态档，全员可见）——用户已习惯在此看数据更新。
4. **"每日首次打开"的既有模式**：`src/features/first-run-guide/FirstRunGuide.tsx` —— per-username localStorage 存档（`firstrun-guide-v1:${username}`）、scriptId 版本失配即作废重放、App 组合根全局挂载、「重看」入口经 slot 注入 /guide 页尾。是公告已读存档的现成参照（非拦截、零感知闸）。
5. **路由纪律**：`src/shared/config/routeRegistry.ts` canonical 路由注册（navigationDomain 分组）；guide 域已有 /guide 系统指引、/chart-ledger 图表指引、/metric-atlas 指标图谱、/page-links 页面引用——历史更新页自然归属 guide 域。
6. **无现成公告/广播组件**：grep Broadcast/公告/Announce 前端零命中（danan SLA「站内广播」为其域内功能非通用件）；无 changelog/更新日志页面。
7. **IM 通道并行存在**：auto-release 告警走飞书机器人+企微 webhook；operating-loop 有 decision-digest-service 推送——「更新告知」已有 IM 通道，网页化是补充非替代。

**真分叉（AUQ 待问）**：
- F1 更新锚点：部署事实 vs 合并事实 vs 双栏混排（数据每日+功能按部署批）
- F2 内容生产链：007 人审产物入仓为源 vs 全自动机器生成 vs 机器预生成+人审放行（生产网页=公开面，内容安全必人审是硬约束）
- F3 首开形态：非阻断（首页卡片/导航红点，仿 FirstRunGuide 已读键控）vs 阻断弹窗 vs 顶部通知条
- F4 历史页形态：guide 域独立 canonical 路由 vs /guide 页内 tab vs 首页区块

- 转正映射建议: 单 issue（前端路由+组件+数据源接线+构建打包）或按 F2 答案拆 2 PR（数据源管线 / 前端呈现）；待 AUQ 后定
- 验收: 走查-可查标签 + 新路由出现在 routeRegistry + 生产构建含 release-notes 数据文件

## 开工记录

（未开工）
