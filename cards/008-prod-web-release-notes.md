# 008 · 生产网页更新发布（每日首开见更新 + 历史结构化浏览）

- 状态: clarifying
- 目标仓: chexian-api（前端路由 + 服务端；数据链可能涉 auto_loadbi / 发布机）
- 类型: 功能（owner 表述「向生产端网页发布、面向用户的更新」）
- 录入: 2026-10-18
- 关联: 007（周更发布说明工作流）——本卡是 007 产物「用户版」的网页产品化延伸；007 管内容生产，本卡管内容呈现。

## 原文

> 向生产端网页发布、面向用户的更新应该是怎样的，如果发布在网页，每日首次打开即可看到前一天的更新，历史更新也放一个链接给大家能结构化看到会如何？该如何设计？先充分探索历史和现状，包含已经归档的历史pr，你可通过20轮以内的auq来探索我的需求

## 澄清（AUQ）

- **F1 更新锚点 → owner 已答（2026-10-18 插话「我是说新pr和commit」）**: 内容源 = 每日新合并的 PR/commit（合并事实锚），非部署事实锚。「合并≠上线」风险已知悉由 owner 接受——展示的是开发进展流。
- **F4 回溯范围 → owner 原文已含**: 「包含已经归档的历史pr」= 全历史回填，非增量。
- F2 内容生产链（AUQ 未答）→ **保守默认: 机器规则直出**（前缀+scope 白名单筛用户可见项，标题原文+类型徽标，全自动零人审；与「每日」节奏匹配、生产机纯 git 可跑无网络依赖）。升级路径: AI 改写人审版（007 工作流产物）替换当周条目。
- F3 首开展示形态（AUQ 未答）→ **保守默认: 非阻断**——导航更新铃铛（未读红点）+ 首页「昨日更新」摘要卡，per-username 已读存档仿 FirstRunGuide 键控模式；不做弹窗打断。
- F5 历史页归属（AUQ 未答）→ **保守默认: guide 域独立 canonical 路由 /updates**（符合 routeRegistry 纪律，铃铛/卡片/深链统一指向）。
- 假设清单（owner 纠偏即停）: ①用户版口径全员同一份（不按权限分层）；②团队版/工程细节不进生产网页（公开面内容安全）；③数据链走服务端运行时读 JSON（每日更新但每周构建，bundle 打包不可行）。

## 设计骨架（按保守默认，待 owner 修正 F2/F3/F5 后定稿）

1. **每日生成管线**（生产/发布机 cron，零网络依赖）：
   - 源 = 本地仓 `git log --since=<昨日> --until=<今日> --format='%s'` 提取 (#N)+标题（squash 一一对应已验证）；
   - 分类规则引擎（SSOT 配置文件）：conventional 前缀 → 功能/修复基线；scope 白名单（用户可见业务域）+ 工程 scope 黑名单（governance/ci/llm/sentinel/etl/ingest/issues/release/sync-vps/docs…）派生体验/剔除工程项；纯 git 拿不到走查标签，以 scope 近似「用户可见」（已知误差: 标题含「前端未接线」类服务端预埋 PR 会误入，缓解=关键词黑名单或切 gh api 拉标签，取舍待定稿）；
   - 产出 `server/data/updates/YYYY-MM-DD.json`（items: type/title/pr/scope），随每日数据批次发布链同步（auto-release 基础设施复用）或独立 launchd。
2. **服务端**：`GET /api/updates?since=&until=` 聚合日文件返回（全员可见无 RLS，内容本就公开面）；缓存友好。
3. **前端**：
   - 铃铛+红点：全局导航挂载（App 组合根），已读键 `updates-read-v1:${username}` 存最后已读日期，红点=存在晚于该日的条目；
   - 首页摘要卡：最近未读日 top 3-5 条 + 「查看全部」深链；
   - /updates 历史页：guide 域 canonical 路由，周分组时间线 + 类型筛选 chips（✨功能/🔧修复/🎨体验）+ 前端关键词过滤；全历史一次拉取按年懒加载。
4. **一次性回填**：同一规则引擎跑全历史 git log → 历史 JSON 批量入仓（W42 发布说明的四类规则即规则引擎初版的单点人肉版）。
5. **不做**：不动 IM 通道；团队版不上面；AI 改写为后续可选升级而非首期范围。

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
