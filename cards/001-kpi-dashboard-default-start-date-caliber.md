# 001 · KPI 看板默认起保口径

- 状态: shipped
- 目标仓: chexian-api（owner 原文明确）
- 类型: 优化 / 数据口径（AUQ 回填细化）
- 录入: 2026-10-15

## 原文

chexian-api仓，默认kpi看板默认使用起保口径

## 澄清（AUQ）

### 先探可定（依据）

- 目标仓 chexian-api，本地 `/Users/xuechenglong/workspace/chexian-api-dev`，锚 origin/main @ 61063382（2026-10-15）。
- 改动落点定为**前端**：后端 `/api/kpi` 的 dateField 默认 policy_date 是 MCP/PAT/CLI 公开契约，不动后端默认，只改前端 KPI 看板传参/默认值。
- KPI 看板（dashboard 域）不锁口径（无 lockedDateCriteria），跟随全局筛选 date_criteria；口径选择器（DateCriteriaSelector）已支持签单/起保互斥切换，能力已在，只缺默认值。

### 待 owner 裁定（AUQ）

- Q1 「默认起保」的生效范围？ 选项: A 仅 KPI 看板页默认起保（推荐）/ B 全站全局默认改起保 / C KPI 看板锁死起保不可切 → **owner 答: A（仅 KPI 看板页）**
- Q2 KPI 大盘后端恒签单 YTD 的六列墙卡（保费/增长率/达成率，A10 裁定）是否联动改口径？ 选项: A 维持签单不变（推荐，不动裁定）/ B 六列也改起保（需后端改动+修订 A10）→ **owner 答（2026-10-15 首答）: A** → **owner 推翻（2026-10-15 同日复核）: 六列墙卡变更为默认起保口径**

### 裁定修订记录（2026-10-15）

- owner 复核推翻 Q2 首答：**六列墙卡（保费/件数/增长率/达成率）默认起保**。
- 探索勘正（重要）：A10 的「恒签单」实为「恒 YTD 窗口 + 默认签单」——六列 SQL 的 `kpi_date` 本就消费 dateField（`server/src/sql/kpi.ts:156,308`，路由层 `server/src/utils/route-helpers.ts:228` dateField=dateField 参数 ?? policy_date）。故六列默认起保**无需动 SQL 架构**：001 Q1 页面级默认起保（date_criteria=insurance_start_date）即自动生效；需做的是 A10 裁定文本勘正登记（恒 YTD 窗口语义保留，口径随 dateField）。
- 连带待裁：数据锚 latest_policy_date 随 dateField 变为最新起保日（语义自洽，同比基期同口径平移）；经营体检 /health-check 与 KPI sparklines 同源指标（达成率/增长率恒 YTD）的 dateField 默认是否同步——**owner 已裁（2026-10-15，004 卡 Q2）：同步起保，口径单一事实源**。

## 讨论

- 现状：全站口径基线单源 `buildResetFilters`（`src/shared/types/data.ts:283`，date_criteria: 'policy_date'），URL 同步 clearOnDefault、filter-caliber 计数基线（`src/features/filters/filter-caliber.ts:327`）等多处以此为基线。仅页级改默认可避开全站基线多点联动；若走全站改默认，改动面含 FilterContext 初载态（`src/shared/contexts/FilterContext.tsx:232`）、URL 省略键语义、筛选计数基线，影响所有跟随全局筛选的页面。
- 防撞车（2026-10-15）：开放 issue/PR 无直接撞车；相邻不撞：#1959（KPI 看板指标映射联动）、#1314（时间口径元规则·截止日）、#1321（机构层达成率归属）。

## 探索 #1（2026-10-15）

- 探查锚: chexian-api @ 61063382（origin/main，2026-10-15）
- 防撞车: 开放 issue/PR 查重无撞车；#1959/#1314/#1321 相邻不同面。
- 实施要点（按 Q1=A 仅看板页默认的最小面预估）:
  - dashboard 域初载时 date_criteria 缺省注入 'insurance_start_date'（页级覆盖，具体注入点探索转正时定：dashboard 入口 or FilterContext 页级基线钩子）；
  - 口径选择器与全局联动语义（CX-ADR-005）不动；
  - 后端不改动（Q2=A 前提下）；
  - 风险：页级默认与「恢复默认」按钮基线（buildResetFilters 全局基线签单）可能打架——重置后回到签单而非起保，需一并定义「KPI 页的默认」语义；URL 回放（f_ 参数）与页级默认的优先级需对齐既有规整逻辑。
- 转正映射建议: 与 004 并一个 issue（「业绩墙卡与计划达成默认起保口径」），本卡为轻支 PR。
- 验收: KPI 看板初载（无 f_ 参数）各面板口径标注为起保；切换签单/起保正常；URL 回放/重置行为符合裁定；相关 filters 测试族（filter-caliber / query-param-equivalence 等）通过。

## 探索 #2（2026-10-15 · 确认后 PR 方案）

- 探查锚: chexian-api @ 034d9378（origin/main，2026-10-15）。⚠ 本地工作区停在旧分支 fix/audit-48h-m6-l1-l3（落后 main），本段已全部按 origin/main 核对；61063382→034d9378 仅 1 个不相关提交（hunch P2-E），SQL 层零漂移。
- 防撞车: KPI 卡片走势线 /kpi-sparklines 已在 main 实装（2026-10-12 重设计）且**已消费 dateField**（`server/src/routes/query/kpi-sparklines.ts:102` parseFiltersAndBuildBothWhere → `:167` weekCtx.dateField）——体检（kpi.ts 路由 `/health-check` 段 dateField 直传 generateKpiQuery）与走势线「同步起保」**零服务端改动**，前端默认值自动带动。
- 实施要点（PR 方案）:
  1. **前端覆写机制**，对齐仓内先例（综合分析页 useComprehensiveBundle「口径 SSOT 在 preset、调用点 pre-build 覆写」）：KPI 看板页数据入口 hooks 构造参数时覆写 dateField——入口清单（origin/main 行号）：useKpiData.ts:102、useDashboardBundle.ts:46、useHealthCheck.ts:35、useKpiSparklines.ts:40、GeoSection.tsx:46、useTrendData、usePolicyGeo（后两者同页模块一并接）。
  2. **「默认起保 vs 用户显式签单」判定**：URL f_ 参数显式携带 date_criteria 时尊重用户值，未携带时 KPI 域默认 insurance_start_date；判定信号由 FilterContext 地址回放层提供（回放时可知键是否携带）。口径 SSOT 建议落 full preset 扩展（如 defaultDateCriteria），与 cost preset 的 lockedDateCriteria 同文件同形态。
  3. **全局不动**：buildResetFilters（main 上 `src/shared/types/data.ts`）与 FilterContext 初载基线（main `src/shared/contexts/FilterContext.tsx:261`）保持签单；其他页默认不变；CX-ADR-005 跨页联动语义保持（显式值带出仍有效）。「KPI 页恢复默认」按钮基线需同步定义为页级起保（探索 #1 遗留风险在此收口）。
  4. **后端零改动**：六列 SQL kpi_date 已消费 dateField（`server/src/sql/kpi.ts:156,308`）；SC 分支达成率已核实分子 bp.vehicle_ytd_premium 随 dateField、分母为计划静态值（achievement_cache plan_vehicle，`server/src/sql/kpi.ts:301-305`）——SC/SX 全分支达成率随 dateField 自动切换。
  5. **A10/#20 勘正登记**：`数据管理/knowledge/rules/车险数据业务规则字典.md`（裁定 #20 登记行 + 新版本行）、`server/src/config/query-routes-metadata.ts:358-379`（KPI/HEALTH_CHECK/DASHBOARD_BUNDLE timeWindowNote 的 A10 文本）、`server/src/config/metric-registry/categories/growth.ts:103`（A10 补登注释）——「恒 YTD 窗口」语义保留，勘正为「口径字段随 dateField，KPI 前端默认起保（2026-10-15 owner 裁定）」。
  6. 风险：KPI 页默认起保后六列达成率自动变起保分子，004 落地前与计划达成页现状（签单）短暂不一致——两 PR 同 issue 相邻合并（建议 001 先 004 后，或同批）。
- 验收命令: 前端 filters/dashboard 契约测试族 + `bun run governance`（目标仓 AGENTS.md 要求）；手工对拍——KPI 看板初载（无 f_）六列/体检/走势线/趋势/地理全模块起保口径且口径标注一致；URL 显式 date_criteria=policy_date 回签单；他页初载仍签单默认。

## 开工记录

- 未独立开工——随 002 #2509 PR1 收口（#2514 merged e3d6e132，KPI/首页默认起保可切已落地）。
