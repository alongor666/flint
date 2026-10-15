# 003 · 新版透视吸收旧版四指标并下线旧版 + 计划逐层化（川业务员/团队、晋 2027 全层级）

- 状态: scouted
- 目标仓: chexian-api（owner 原文点名「该仓有计划域」）
- 类型: 功能 + 数据口径/数据扩展
- 录入: 2026-10-15

## 原文

需求，达成率、报价量、成交量、成交率都要在新版实现，在确定吸收旧版的前提下，下线旧版，山西到三级机构层面是有计划的，该仓有计划域。四川到业务员、销售团队都要计划。2027年山西也要明确组织每个层级和业务员的保费计划。

## 澄清（AUQ）

### 先探可定（依据）

- 探查锚: chexian-api @ 61063382（origin/main，2026-10-15）。
- 「新版/旧版」= /#/performance-analysis 页热力图区块的两版切换（`PerformanceAnalysisPanel.tsx:181`「新版透视（灰度）」/「旧版热力图」）；新版 = PerformancePivot，灰度名单仅 xuechenglong + 开发者模式。
- 新版缺的指标（本会话已逐项对过源码）：**达成率**（透视后端只吐可加原子保费/环比基期/同比基期/件数/系数分子分母，无计划列）、**报价量/成交量/成交率**（报价域 QuoteConversion 不在原子内）；另有旧版独有控件未迁移：**系数段过滤**（coefficientSegment，前端新版未传）、**系数监管口径切换**（coefficientCaliber 只读透传无 UI）。
- 计划域现状（`server/src/sql/premiumPlan.ts` + `duckdb-domain-loaders.ts`）：
  - 四川：PlanFact `level='salesman'` 业务员级计划 → achievement_cache 预聚合，团队/机构层派生（团队口径分子分母同一批人，25 补充① + owner 2026-10-04 澄清）——**业务员/团队计划已存在**。
  - 山西：PlanFact `level='organization'` 仅机构层；团队/业务员无计划（热力图 C17 置灰 HINT_DISABLED_SX；premiumPlan.ts 走 buildSxPremiumPlanCore 特例分支，仅 org/company 层取计划）。
- 报价域：只含商业险、车架号去重；成交率 = 成交量 ÷ 报价量，分子分母均可加——适配新版「原子求和后相除」模型；但报价域维度是子集（无 business_nature，战略段有缺口披露），旧版有置灰判据 C16。
- 达成率口径：年初至所选时间末累计签单保费 ÷（年计划 × 时间进度），仅组织三维（机构/团队/业务员）在计划分配链上（旧版判据 C15：业务/战略维无计划，tab 置灰）。
- 防撞车（2026-10-15）：开放 issue/PR 无同面。相邻不撞：#1834（热力图残留选中格 bug 补迁）、#1980（同业报价对标新域）。

### 已答（AUQ，2026-10-15）

- Q1 山西 2027「组织每个层级 + 业务员」计划的落地路径？ 选项: A 上游提供 SX 业务员级计划源，入 PlanFact level='salesman'，团队/机构层聚合派生（推荐——与四川同构，可收敛 buildSxPremiumPlanCore 特例分支）/ B 上游给 SX 逐层计划行（机构/团队/业务员各 level），仓库扩 level 值域逐层直读 / C 本仓建计划录入/管理功能（界面维护计划数据） → **owner 答: A（上游给业务员级计划源，与四川同构）**
- Q2 下线旧版的方式与节奏？ 选项: A 四指标吸收+数值对拍验收后，同批删旧版（组件+切换器；/performance-heatmap 路由按前端零调用后仓内纪律定去留）（推荐）/ B 新版先灰度转全量，旧版保留观察期后再删 / C 仅隐藏旧版入口，代码保留 → **owner 答（原话）: 「指标和能力都要验收」**——下线前置 = 指标数值对拍 且 能力（控件/交互）验收双通过；未提观察期，理解为验收通过即具备下线条件
- Q3 报价三指标在新版指标区的形态？ 选项: A 并入新版指标选择器为独立选项组，沿用「报价口径·商业险」披露与维度置灰判据平移（推荐）/ B 保留旧版「报价 ▾」下拉形态 → **owner 未答（跳过）**——按推荐项 A 作工作假设（UI 形态非业务口径，探索/实施阶段可纠偏）
- Q4 「吸收旧版」的范围除四指标外是否含系数两控件（系数段过滤、监管双口径切换）？ 选项: A 一并吸收，新版补齐两控件（推荐，完整对齐才谈得上吸收）/ B 只补四指标，系数控件不迁（新版维持现状） → **owner 答: A（一并吸收）**

### 非交互假设（owner 纠偏即停）

- 四川「业务员、销售团队都要计划」按现状即满足理解（level='salesman' 计划 + 团队层派生口径已是 owner 澄清过的裁定）；若意为需要显式团队级计划行，属 Q1-B 形态，纠偏即可。
- 达成率在新版非组织维布局下平移旧版 C15 置灰语义（置灰 + 原因提示），不另设口径。
- 报价三指标在新版的时间口径沿用报价域 quote_time 锚（与旧版热力图报价列同锚）。

## 讨论

- 归类：一条需求两个面——①功能面：新版透视补齐四指标 → 吸收后下线旧版热力图；②数据面：2027 计划覆盖扩展（SX 从机构层扩到全层级+业务员；SC 确认既有覆盖）。两面可分转正（数据面到位前，功能面按 SX 现状口径实现即可，SX 团队/业务员层达成率在数据到位前自然 NULL/置灰——与 fail-closed 风格一致）。
- 新版原子模型适配性初评：报价量/成交量是可加计数原子、成交率分子分母分别求和，直接进原子；达成率需要**非时间可加**的计划原子（计划沿组织维成员可加、沿时间不累加，且时间进度按格值期末取），聚合层要特判——这是转正探索的重点改动面。
- 下线旧版牵动面：PerformanceOrgHeatmapV2/HeatmapHeader/usePerformanceOrgHeatmap、置灰判据与口径脚注迁移、E2E 07/39 等热力图用例、/performance-heatmap 路由去留（MCP/PAT 程序化消费方需查）。
- 与 001/002 无并卡关系（不同页面不同面）。
- 与 004（计划达成起保，另一会话）**不冲突但需协调**（2026-10-15 会话间判定的依据）：004 动 dashboard 域 + /plan-achievement + achievement_cache，003 动 /performance-analysis + 透视原子，文件面无交集；透视达成率原子按旧热力图同构——分子 YTD 随 dateField、计划取静态值，与 004「计划按起保制定」裁定同向，且 003 不读 achievement_cache。转正时 issue 须 cross-link 004 裁定，防达成率公式口径注释两处漂移（004 勘正 metric-registry / 003 写透视新原子）。
- 裁定落点（AUQ 后）：吸收范围 = 四指标 + 系数两控件（Q4=A）——新版透视需引入 coefficientSegment 参数透传与控件、coefficientCaliber 切换 UI；下线验收门 = 指标对拍 + 能力验收双通过（Q2 owner 原话）；晋 2027 数据面 = 上游业务员级计划源（Q1=A），仓库侧 ETL 走既有 level='salesman' 通道、SX 特例分支待收敛。

## 探索 #1（2026-10-15）

- 探查锚: chexian-api @ 61063382（origin/main，2026-10-15 晨 fetch 成功；午后二次 fetch SSL 抖动，工作树即该锚）
- 防撞车: 开放 issue/PR 无同面；#1834（热力图残留选中格）、#1980（同业报价域）相邻不同面。

### 现状结构（PR 改动面的地基）

- **旧版达成率实现**（`server/src/sql/performance-heatmap.ts:587-674`）：`plan_by_dim`（SX 分支读 PlanFact level='organization'；SC 分支读 SalesmanTeamMapping `car_insurance_plan_2026`＝最新计划年业务员计划，按 org/team/salesman 维聚合）× 时间进度（`EXTRACT('doy')/全年天数`，闰年感知，按每期 `current_cutoff`）= `plan_period`；分子 `ytd_by_dim_period`（年初→各期 cutoff 的累计保费，随 exclude 口径谓词）。达成率仅组织三维（`heatmapSupportsAnnualPlan` :271），SX 再叠加 `isSxOrganizationPlanScope` + 禁团队/业务员（`heatmapHasPersonPlanScope` :339）。
- **报价域**（`server/src/sql/performance-analysis/quote-metrics.ts:703-760`）：`buildQuoteHeatmapCtes` 产 `quote_dim_period`（维度×期，`COUNT(DISTINCT frame_key)` 报价量/成交量）+ `quote_period_total`（周期整体重算）+ `quote_scope`（覆盖闸）。锚点跟随 dateField（quote_time / insurance_start_date 双锚）。
- **新版透视**（`server/src/sql/performance-pivot.ts` + `routes/query/performance.ts:1008-1053`）：服务端**已接受** `coefficientSegment`（缺省 all）/ `coefficientCaliber`（缺省 exclude）/ `yoyAlign`——系数两控件是**纯前端接线**缺口。透视无 planScope、无报价 CTE——达成率与报价是**服务端原子扩展**缺口。
- **共享聚合引擎**（`src/shared/pivot/types.ts:79-97`）：reducer 纯 addTo 求和；**已有 `PivotTotals='server'` 钩子**（行业对标先例：服务端吐各维 TOTAL 合计原子，前端绝不对成员求和得合计）。业绩透视当前 sum 模式。
- **SX 计划源通道**（`server/src/services/data-bootstrapper.ts:613-665`）：`resolveBranchDimExtras('plan')` 已支持 `warehouse/validation/SX/dim/plan/latest.parquet` 多省副本探测→UNION 进 PlanFact 携 branch_code——**晋 2027 业务员级计划源的基础设施已在**，缺的是上游数据 + SX 特判收敛。
- **消费方**：`PerformanceAnalysisPanel` 两处消费——通用页 `PerformanceAnalysisPage` + **战略驾驶舱 `StrategyCockpitPage`**（传 allowedHeatmapDimensions/defaultHeatmapDimension 收窄）。下线旧版＝驾驶舱热力图区块同切 pivot，需一并适配验收。
- **测试面**：E2E 07（heatmap drilldown）/39（url-state）/42（gray-assembly）；`performance-cube.test.ts` F5 golden sha256 锁热力图 SQL 文本；pivot 族 vitest（pivotModel/pivotConfig/性能 pivot 路由契约）。

### 两大设计难点（转正方案核心）

1. **计划/YTD 非可加**：计划沿维成员可加、沿期不累加（每期同值会重复计入）；YTD 是期末快照，跨期求和无意义（时间轴合计格应取窗口末期快照）。且透视任意布局下，达成率可表达条件 = **非时间轴全部为计划维（org/team/salesman）**——org×category 交叉格计划无法归属（计划拆不到业务维），C15 置灰需从「维度级」升为「布局级」判据。
2. **报价 VIN 去重跨格不可加**：同车架号可跨维成员/跨期重复，`quote-metrics.ts` 明写「车架号跨维度可重复，禁止前端加总」（旧版整体行由服务端 `quote_period_total` 单独重算）。sum 引擎直接加总会虚增。
   - 两个难点的共同解法方向：**合计原子走 server 模式**（复用既有 `PivotTotals='server'` 钩子）——透视 SQL 以 GROUPING SETS 产出各维 TOTAL 组合的计划/YTD/报价合计原子；或引擎把 totals 来源细到指标级（保费等仍 sum、报价/计划字段取 server 原子）。实施期二选一，倾向后者（原子行数增长最小）。

### 实施要点（改动面清单）

- **PR-A 服务端原子扩展**（`performance-pivot.ts` + 路由）：
  - 加 planScope 解析（复用 heatmap 路由的 `resolveRequiredPlanFactBranchCode`/`extractTeamPlanScope`，performance.ts:780/:905/:1093 同款）；
  - 加计划/YTD CTE（从 performance-heatmap.ts 的 planCtes 平移到多维 GROUP BY 版本，期窗同源 `buildHeatmapPeriodSpec`）；
  - 加报价 CTE（`buildQuoteHeatmapCtes` 多维版 + 路由 buildQuoteFilterContext + 三态/覆盖闸元信息随响应下发）；
  - GROUPING SETS 合计原子（或指标级 totals 方案）。
- **PR-B 前端**（`PerformancePivot/`）：PivotMetric 扩 achievement/quote_count/deal_count/deal_rate（阈值复用 V2 config 的 ACHIEVEMENT/DEAL_RATE_THRESHOLDS，配色 legacyColorInput 已有映射范式）；布局级置灰判据（达成率×非计划维布局、报价×含 business_nature 布局，平移 C15/C16/C17）；报价三态呈现（数字/-/不适用）与口径脚注（QuoteMetricsCaption 机制）；系数两控件接线（服务端参数已在，纯 UI + usePerformancePivot 透传）。
- **PR-C 下线旧版**（验收门 = Q2：指标对拍 + 能力验收双通过后）：删 Panel legacy 分支/PerformanceOrgHeatmapV2 树/HeatmapHeader/usePerformanceOrgHeatmap；灰度转全量（PERFORMANCE_PIVOT_GRAY_USERS 退役）；驾驶舱 StrategyCockpitPage 适配；E2E 07/39/42 改造；F5 golden 随热力图退役。`/performance-heatmap` 路由是 MCP 公开契约（cx_query_performance_org_heatmap）——建议按 premium-report 前例保留为「界面已下线、程序化访问保留」，去留单独裁定，不随 PR-C 强删。
- **晋 2027 数据面**（独立 issue，上游数据到位后）：SX plan parquet 落 `validation/SX/dim/plan/latest.parquet`（level='salesman'，携 branch_code='SX'）→ 既有 loader 通道自动 UNION；随后收敛特判：`buildSxPremiumPlanCore` 分支退役、`isSxOrganizationPlanScope`/`heatmapHasPersonPlanScope` 门控退役、前端 HINT_DISABLED_SX 退役、SX 团队/业务员达成率自然出数。PlanFact 计划年动态取 MAX(plan_year)，2027 行入库即生效。

### 风险

- 透视 SQL 复杂度显著上升（plan/ytd/quote 三组 CTE + GROUPING SETS），60k 行上限（PERFORMANCE_PIVOT_MAX_ROWS）可能提前触顶——需在 PR-A 里压测业务员维 × 12 期的原子行数。
- 旧版 F5 golden 与热力图 SQL 字节锁：PR-A/B 不得动 performance-heatmap.ts（下线前双轨并存期）。
- 驾驶舱收窄维度（战略两维）× 新指标（达成率不可表达、报价战略段有缺口）——置灰/脚注须覆盖驾驶舱形态，能力验收要含驾驶舱。
- 报价三态（数字/-/不适用）超出 pivot CellMetrics 数值模型，聚焦卡/走势图对 null 与 NA 的呈现需区分。

### 转正映射建议

- issue 1（type/feature + area/api+frontend）：新版透视吸收四指标+系数两控件（PR-A/B），随附对拍验收；PR-C 下线旧版挂在同 issue 或其子任务（验收门见 Q2 裁定）。
- issue 2（type/data 或 feature）：晋 2027 业务员级计划源入库 + SX 特判收敛（上游数据依赖，独立推进）。

### 验收

- 服务端：`bun run governance`；pivot/heatmap 路由契约测试（对拍：同布局同筛选下透视格值 = 旧热力图逐格相等——达成率/报价三列 + 既有指标回归；报价三态与覆盖闸用例）。
- 前端：pivot 族 vitest（pivotModel/pivotConfig/置灰判据）；E2E 07/39/42 改造版通过。
- 能力验收（Q2）：系数段过滤、口径切换、达成率置灰提示、驾驶舱形态、AI 解读（pivot 自带）逐项清单核对。

## 开工记录

（未开工）
