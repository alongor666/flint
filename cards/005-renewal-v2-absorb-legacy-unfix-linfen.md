# 005 · 续保页旧版下线评估 + 临汾续保四指标不固定

- 状态: building
- 目标仓: chexian-api（owner 原文点名）
- 类型: 功能 + 数据口径
- 录入: 2026-10-15

## 原文

chexian-api，评估旧版下线，能力和指标设计是否吸收到新版，临汾续保不应固定。

## 澄清（AUQ）

### 先探可定（依据）

- 探查锚: chexian-api @ 61063382（origin/main，2026-10-15，与 003 同锚无漂移）
- 「旧版/新版」指认（先探收敛到续保域）：owner 原文点名「临汾续保」，临汾四指标面板正挂在 /#/renewal-tracker 页底部；该页页顶即「旧版 / 新版透视（灰度）」版本切换 Tabs（RenewalTrackerPage.tsx:72；灰度六人 RENEWAL_PIVOT_GRAY_USERS，src/shared/config/organizations.ts:811）。业绩页同型题已有卡 003，本卡按续保页立卡。
- 旧版主表 = /renewal-tracker 24 层固定切片（A-E universe + 派生率 + 影响度 + 跟进状态/目标客户 + 20 字段通用筛选 + start/end/cutoff 到期窗口）。
- 临汾面板「固定」的三层硬编码（server/src/routes/query/linfen-renewal-kpi.ts 口径常量，企微智能表 shanxi-linfen-renewal.yaml 逐键镜像）：
  1. 机构锁 ORG_LEVEL_3='临汾'（前端对所有 SX 账号渲染，RenewalTrackerPage.tsx:80）；
  2. cohort 锁死 2025-Q4 起期窗（2025-10-01~12-31）+ 单VIN净保费>200 + 每VIN取止期最晚原单；
  3. 报价窗起点 2026-05-01、续保认定 ≥2026-01-01。
  口径 SSOT = 数据管理/integrations/wecom_smartsheet/sync_org_renewal_wecom.py build_rows（与企微表逐行同口径，2026-09-22 实测 base=1171 行对齐）。
- 吸收面现状（v2 = server/src/sql/renewal-pivot.ts）：
  - 已有：month 模式 due/expDue/quoted/dealt 量 + day1(T-30)/week(T-24)/t14/renew/quote 五率、phase 模式五档队列（[24,30]/[14,23]/[7,13]/[1,6]/[-30,0] × 未报价/已报未续/未续回）、八维行/平铺含 org_level_3/salesman、年度 KPI、detail 下钻、与旧版同款 20 字段筛选 + RLS。
  - 缺口：①节奏锚不同——临汾首日/首周 = T-60/T-53（企微表口径），v2 = T-30/T-24（可续期闸门 30 天，规划 v2 裁定）；②命名指标缺 T-7 未成交件数、在管件数（phase 队列可近似派生但非同名指标）；③分业务员+机构合计的盯盘布局是临汾面板形态。
- 指标注册表：临汾四指标为 L4 占位符（metric-registry/categories/renewal.ts:292-，2026-09-22 登记）；v2 自有 renewal_pivot_* 条目。
- 防撞车（2026-10-15）：开放 PR 无同面；issue #1791（renewal_mode 续导出待上游）、#1709（SC 续保档位复跑）为数据管道面，不相交。
- 下线前置验收沿用 003 已立先例：指标数值对拍 + 能力（控件/交互）验收双通过。

### 已答（AUQ，2026-10-15）

- Q1 本卡「评估旧版下线」范围？ 选项: A 续保页整页——旧版主表+临汾面板，吸收验收后同批下线（推荐，与 003 同模式）/ B 仅临汾面板并版泛化，旧版主表暂不评估下线 / C 全站旧版（业绩+报价+续保）统一盘点（报价域需另立探索） → **owner 答: A（续保页整页）**
- Q2 临汾四指标「不固定」的目标形态？ 选项: A 并入新版透视：机构参数化 + 窗口滚动化（不锁 2025-Q4 起期窗），临汾仅为默认入口（推荐）/ B 保留独立面板，仅加机构切换参数，口径窗不动 / C 并入新版透视但口径原样平移（保留固定季窗语义，仅机构可切） → **owner 答: A（并入新版透视+滚动窗）**
- Q3 节奏锚冲突（临汾 T-60/-53 vs v2 T-30/-24）吸收时以谁为准？ 选项: A 双锚并存——v2 现锚保留，临汾节奏锚作独立列组进 v2（推荐，两侧口径 SSOT 都不破）/ B 统一到 v2 现锚，临汾旧锚废弃（企微表对拍换锚）/ C 统一到临汾锚，v2 换锚 → **owner 答: B（统一到 v2 锚 T-30/T-24，临汾 T-60/-53 锚退役；未选推荐项，连带影响见下）**
- Q4 企微智能表联动（现口径 SSOT 住在同步脚本）怎么处理？ 选项: A 主站侧泛化后以仓内 SQL 为新 SSOT，企微临汾实例不动（推荐，文档声明解耦）/ B 企微实例一并模板化（新机构实例 yaml + 同步任务），双端同口径 / C 主站改为读企微同步产物，不自建 SQL → **owner 答: A（主仓 SQL 为新 SSOT，企微实例不动）**

### 裁定连带（Q3-B + Q4-A 组合效应）

- 企微智能表若继续按 T-60/-53 口径出数，将与主站（T-30/-24）系统性不同数——「逐行同口径 1171 行对拍」不变量按裁定**有意打破**，临汾侧换锚或双口径并存的善后属企微实例侧自理（Q4-A：实例不动，口径解耦须在主站文档与指标注册表 changelog 显式声明，防后来者拿旧对拍当回归基线）。
- T-7 未成交件数/在管件数两枚计数不受锚统一影响，仍需以 v2 指标形态补齐（month 或 phase 模式列组）。
- 窗口滚动化后临汾面板「2025-Q4 起期窗 cohort」语义退役，统一落 v2 到期月滚动窗（当前月前 11 后 3）。

### 非交互假设（owner 纠偏即停）

- 旧版主表 24 层切片与 v2 的逐能力对拍清单在「确认即探索」阶段产出（PR 实施方案深度），本卡只锁范围与形态分叉。
- 旧版主表的下线牵动面（/renewal-tracker 路由、A-E universe、MCP/PAT 程序化消费方、E2E 用例）与临汾面板路由 /api/query/linfen-renewal-kpi 的去留，同在探索阶段定——按 003 先例，路由去留按前端零调用后仓内纪律定。

## 讨论

- 与 003 同型不同页（业绩页/续保页各自的「吸收→下线」），无并卡关系；验收先例（数值对拍+能力验收双通过）互相引用。
- 临汾面板特殊性：它是企微智能表的主站镜像（业务侧催办工具），泛化后若与企微表解耦，「逐行同口径」的对拍锚换成什么——这是 Q4 的实质。
- v2 phase 模式已覆盖临汾面板约半数能力（未报价/已报未续/未续回队列），真正的指标设计缺口集中在节奏锚与 T-7 未成交件数/在管件数两枚计数。

## 探索 #1（2026-10-15）

- 探查锚: chexian-api @ 034d9378（origin/main，2026-10-15）。建卡锚 61063382 → 034d9378 漂移仅理赔域 P2-E（#2088），续保/临汾相关文件 diff 为空，零增量重探。
- 防撞车: 开放 PR 无同面；issue #1791/#1709 数据管道面不相交（已录）。交叉项一处：`docs/plans/2026-09-26-续保追踪页AI植入-准备方案.md` 以**旧版视图为宿主**（洞察卡联动复用 handleSelectOrg、aiDepsKey 签名含 timeView/timeRange、LinfenKpiPanel 仅布局参照）——见风险 5。

### 对拍清单（旧版能力 → v2 覆盖判定）

| 旧版能力 | v2 判定 |
|---|---|
| A-E 五量（A 应续/B 报价/C 已续/D 未报价/E 流失）+ 派生率 | 覆盖：due/quoted/dealt + quote_rate/renew_rate；D/E 为差值派生，v2 未单列（phase 模式有 unquoted/not_dealt 同义列） |
| renewal_impact_rate 影响度（renewal.ts:184，L4，E÷同层ΣA 窗口分母） | **缺**——真缺口，转正时裁定平移（v2 全窗行 bucket=null 可承载分母）或显式弃用 |
| 24 层（4 基础层 × 5 维度 GROUPING SETS） | 覆盖（形态不同）：八维行/平铺 + salesman/customer_category 明细带（useRenewalPivotDetail，同 API month 模式） |
| 时间窗自由 start/end/cutoff（TimeFilter） | **收缩**：v2 零时间参数，固定滚动窗（当前月前 11 后 3）；任意窗口需求由 MCP /cube 续保族兜底——取舍在 issue 声明 |
| 20 字段通用筛选 + RLS 四段 + 报价留痕披露（quoteCoverageStart） | 覆盖：同款消费 + meta 已披露 |
| 空态守卫（A=0 → EmptyState，多省装载保护） | 需确认 v2 等价守恒（v2 空窗行为转正时核） |
| 临汾四指标（首日/首周/T-14 报价率 + T-7 未成交件数 + 在管件数，分业务员+机构合计） | 本卡吸收面：锚统一 v2（T-30/-24，T-14 同锚无需换）；T-7 未成交件数/在管件数补为 v2 命名指标 |

### 实施要点（文件:行号，全部对 origin/main 只读取证）

**A. v2 吸收（功能 PR）**
- `server/src/sql/renewal-pivot.ts`：month 模式补 t7_unrenewed_count（到期日 ≤ today+7 ∧ ¬is_renewed，COUNT DISTINCT VIN）与 managed_count；落点二选一在 issue 定——month 列组 vs KPI 带年度累计（`v2/KpiBand.tsx`，与六指标同窗 当年1/1~today）。
- ⚠ 时间锚口径点：v2 全套用服务器 today；临汾指标按口径裁定 #29 一律**数据日**（报价/保单最新日较早者，linfen-renewal-kpi.ts 头注）。吸收时 T-7 分母/窗口锚对齐 #29 还是沿 v2 today，转正 issue 须先裁（影响与临汾旧路由对拍）。
- `server/src/config/metric-registry/categories/renewal.ts`：新增 renewal_pivot_t7_unrenewed_count / renewal_pivot_managed_count（值域绑定）；linfen_* 四枚 L4 占位（:306-394）退役——changelog 记锚统一 T-30/-24 + SSOT 移仓声明（Q3-B/Q4-A 连带，防后来者拿 1171 行旧对拍当回归基线）。
- 机构参数化：v2 已吃 orgNames 筛选 + org_level_3 行维，「不固定」主要落前端——v2 视图机构快选（临汾默认 chip，仅 SX）；分业务员盯盘形态由 salesman 行维承载。

**B. 旧版下线（下线 PR）**
- 前端：`RenewalTrackerPage.tsx` 版本切换条+旧版分支（:125-160）与 TimeFilter/OverviewBand/OrgTable/CategoryTable/HeroFunnel/Crumb 全套、`LinfenKpiPanel.tsx`、`hooks/useLinfenRenewalKpi.ts`、`useRenewalTracker.ts`、types/grading/index 出口、apiClient.getLinfenRenewalKpi/getRenewalTracker。
- 灰度闸：`src/shared/config/organizations.ts:811` RENEWAL_PIVOT_GRAY_USERS 与后端 `special-feature.ts` 镜像双侧拆除（fs 哨兵测试同步）。
- 后端路由去留（按 003 先例：前端零调用后仓内纪律定）：`/renewal-tracker`（24 层）与 `/linfen-renewal-kpi`；程序化消费面——`api-wire-conservation.mjs:106`（getLinfenRenewalKpi 登记）、`tests/api/__golden__/client-wire-golden.json`、`query-routes-metadata.ts:954`、`route-param-contracts.ts:381`、`middleware/permission.ts:109`、MCP catalog 描述同步（cx_query_linfen_renewal_kpi / cx_query_renewal_tracker）。
- ⚠ 共享 SQL：`server/src/agent/routes/agent-diagnosis.ts:424` 的 `/api/agent/diagnosis/renewal-tracker`（A-E + 影响度 + 分省四级判级）与 PAT 工具链消费 `sql/renewal-tracker.ts` 生成器——**页面下线 ≠ 该 SQL 可删**，须先拆分保留 diagnosis 链路再用面。
- 测试：renewal-tracker.route-contract.test.ts、renewal-pivot.route-contract.test.ts、renewalPivotView.test.tsx、E2E `tests/e2e/25-workbench-renewal.spec.ts`（改写为 v2 独占）+ 04/12/19 联动用例、preset-users/domain-cross-metric 注册表测试。
- ETL/企微不动：`scripts/lib/wecom-sync-tasks.mjs:103-155` 临汾实例同步任务保留（Q4-A）。

### 风险

1. 节奏无先例：续保将是仓内首个「灰度→全量→旧版下线」三连（业绩/报价透视仍灰度中）；003 先例为「验收通过即具备下线条件、无观察期」，转全量与下线是否同批由 issue 定。
2. 企微表解耦后临汾业务侧两处数字系统性不同（T-60/-53 vs T-30/-24）——owner 已裁，issue 描述向业务侧交代。
3. agent diagnosis 与 sql/renewal-tracker.ts 耦合，误删共享 SQL 会断 PAT 链。
4. today vs 数据日锚（裁定 #29）未决前，T-7/在管指标无法定稿，也无法设计对拍。
5. AI 植入方案（2026-09-26 准备方案）以旧版视图为宿主——下线先落地则该方案需 v2 宿主适配（联动机制/aiDepsKey 签名重设计），两工作项需排顺序。
6. 对拍锚收窄：换锚后首日/首周不可与临汾旧路由对拍；可对拍项 = T-14 报价率（同锚）+ T-7 未成交件数（同定义，若锚裁定 #29 则完全同口径）+ v2 内部新旧版 A-E 对拍。

### 转正映射建议

- 卡 005 → **1 个 issue**（吸收+下线一体，内含锚裁定 #29 与影响度平移/弃用两个前置小裁）→ **2 个 PR**：① v2 补 T-7/在管两枚计数+机构入口+注册表退役 changelog（先合，灰度名单内可验）；② 旧版下线（旧视图+灰度闸+路由+测试+MCP 文档），依赖①验收。
- 验收：`bun run typecheck && bun run test`（renewal 双 route-contract、renewalPivotView、api-wire、注册表测试）+ `bun run test:e2e -- tests/e2e/25-workbench-renewal.spec.ts` + `bun run governance`；走查 harness `src/features/renewal-tracker/v2/__tests__/renewalPivotWalkthrough.harness.js` 基线更新。

## 开工记录

- 2026-10-15：owner 下令「开工 005 + AUQ 裁决」。转正四裁定（AUQ 第二轮）：
  1. **v2 整体换数据日锚**（未选推荐项「仅新指标数据日」，也未选「沿 today」——牵动全部现有列）；
  2. 影响度平移进 v2；
  3. T-7 未成交/在管件数落 KPI 带快照；
  4. 节奏 ①验收后即②（无观察期）。
- 目标仓：issue **#2089**（1 issue 2 PR）；worktree `chexian-api-wt-2089` 分支 `i/2089`（base 034d9378，已认领）。
- **PR①（吸收）= #2093**：数据日锚整体替换（分支最早报价视界探针 + fail-visible 空响应）+ renewal_pivot_impact_rate/managed_count/t7_unrenewed_count 三新指标 + 8 枚锚修订 bump + linfen 四枚退役 + KPI 快照行/影响度列/机构快选（SX 默认临汾）+ 业务规则字典裁定 #40 登记 + 行为锁测试（duckdb-data-date-anchors 22/22、phase-mapping fail-visible 用例）。typecheck / governance 119 项全绿；本机无续保派生域数据，真仓 API 对拍留发布机走查（PR 正文已声明对拍锚：T-14/T-7 可对拍，首日/首周换锚后不可）。
- 实施中发现并修复：影响度分母首版把月格行计入 ΣA 且总合计落空分区（真 DuckDB 夹具穿测抓获，双处修）；路由缓存键与测试用例同日撞键（假钟隔离）。
- **待办**：① #2093 对抗评审 + 灰度名单内验收（T-14/T-7 对拍）→ ② PR② 旧版下线（旧视图+灰度闸+路由+agent diagnosis 共享 SQL 拆分+E2E 25 改写）→ Closes #2089。
