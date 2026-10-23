# 002 · 全站口径一览与分派原则（成本类起保、业绩签单）

- 状态: building
- 目标仓: chexian-api（原则落地与口径修订均在目标仓；本卡梳理产出存 flint）
- 类型: 数据口径 / 研究
- 录入: 2026-10-15

## 原文

给我一个一览表，每个页面每个指标，哪些是起保口径、哪些是签单口径，凡是成本类应起保口径，业绩页面应是签单口径。

## 澄清（AUQ）

### 先探可定（依据）

- 「每个页面每个指标」以**页面/面板级**为表粒度，混合页（KPI 看板、战略驾驶舱）拆到指标块级；后端恒定口径（不随前端口径选择器变的指标）单列。
- 现状梳理见「探索 #1」；两条原则（成本类→起保、业绩→签单）对照现状几乎全符合，唯一结构性冲突在 001 卡（KPI 看板默认起保 vs 业绩页应签单）。

### 待 owner 裁定（AUQ）

- Q1 原则与 001 卡冲突如何处理？ → **owner 答: 反问纠正指称——「业绩页」= /performance-analysis 业绩分析页，非泛指业绩类页面；KPI 看板不在「业绩页」指称内，001 无冲突、维持原裁定**
- Q2 「业绩页面应是签单口径」的落地形态？ 选项: A 业绩页锁定签单口径不可切（对标成本页锁起保先例）（推荐）/ B 维持现状默认签单+允许手动切起保 → **owner 答: B（默认签单可切，维持现状，业绩页无实施项）**

### 澄清补记（2026-10-15 owner 纠正）

- 「业绩页」= 业绩分析页 /performance-analysis（owner 点名）。该页现状：主面板（汇总/下钻/趋势/Top业务员）经 buildFilterParams 跟随全局口径选择器——默认签单、但用户可切起保；机构热力图下钻格已强制签单（performanceHeatmapSelection.ts:143，filter-params-debt #1225）。原则差距点 = 业绩页主面板仍可被切到起保。

## 讨论

- 终局结论（2026-10-15 owner 双裁定）：两条原则对照现状**全部符合，本卡无实施项**。001 卡独立维持（KPI 看板默认起保，非「业绩页」指称）；业绩页维持默认签单可切。
- ~~终局结论~~ **被 2026-10-23 原则修订取代（见下「原则修订 v2」）**——原 Q2「业绩页默认签单」不再成立。
- 可选转正项（owner 未要求）：将两条原则作为上位规则登记进 chexian-api 业务规则字典（与 2026-09-04 成本页锁起保裁定同库），供后续新页面口径设计引用——需要时说一声即可开卡转正。
- 边缘口径（非签单/起保二分）不属于两原则管辖但入表披露：到期日（续保族）、报价时间（报价转化）、承保确认时间（队伍标保）、满期时间分摊（KPI 满期八列/成本满期赔付率）、保单年度批次（费用发展/赔付率发展）、起期锚（收入价质监控）。

## 原则修订 v2（2026-10-23 owner 裁定，取代上方 2026-10-15 结论）

**owner 原话**：

> 硬编码签单口径应该只能是一个，那就是签单保费。在其他情况下，都应该允许筛选口径，而且首先应该默认成为起保口径。业绩热力图这个页面是默认签单口径，但也可以改成起保口径。在签单口径时，保费就等于签单保费；当起保口径时，保费就等于保费收入。

**三条新原则**：

1. 硬编码签单口径的唯一例外＝「签单保费」指标；其余一切场景必须允许口径筛选。
2. 可切页面的默认口径＝起保（推翻 2026-10-15 Q2「业绩页默认签单」）。
3. 命名映射：签单口径下「保费」＝签单保费、起保口径下「保费」＝保费收入（已由 009 卡 #2213 落地：`DATE_CRITERIA_PREMIUM_METRIC_LABELS`）。

**差距清单（探索 #2 核对，2026-10-23 @ 918e6d75b）**：

已符合：KPI 墙「签单保费」卡（唯一例外位，恒 policy_date 独立锚）；KPI 六列与业绩页全族（主面板/热力图/透视/汇总含达成率）锚列随 dateField（`performance-heatmap.ts:767` `CAST(p.${dateField} AS DATE) AS pd`；kpi.ts dateField 已流入）；指标命名 SSOT 已上线。

需改造（按影响排序）：

1. **全局默认口径翻转为起保**：`src/shared/types/data.ts:293` buildResetFilters `date_criteria: 'policy_date'` → 'insurance_start_date'。影响全部可切 preset（full/performance/growth/report/chartLedger/strategyCockpit），同时落地 001（KPI 默认起保）与 004（计划达成默认起保）方向。连带：`dateCriteriaPremiumMetricLabel` 回退值注释（现回退签单保费对齐旧基线）。
2. **业绩热力图下钻格强制签单**：`performanceHeatmapSelection.ts:139` `dateField: 'policy_date'` 硬编码（#1225 技术债，整格周期替换期间时一并强制口径）——改为跟随全局口径，保留期间替换。
3. **网点经营页全页锁签单**：filters.ts networkOutlet `lockedDateCriteria:'policy_date'` + SQL 硬编码签单分桶（network-outlet.ts:94）——NetworkOutletFact 是 PolicyFact 视图、insurance_start_date 列在位（:186-187），技术可切，改造分桶列随口径。
4. **金融同业全族+机构版恒签单窗口**：financial-channel.ts:603 abcWhere `policy_date BETWEEN`；机构版三窗恒签单（传 startDate 即 400）——底表 PolicyFact 有起保列，窗口过滤列可随口径（latest=MAX(policy_date) 数据新鲜度锚与口径过滤列是两个维度，可分离）。
5. **计划达成/报告中心恒签单**：/plan-achievement+achievement_cache 单口径——004 卡已主张双口径改造，本原则强化其转正。
6. **系数监控、日报 benchmark/planAchievement 段恒签单锚**——随各自域改造评估。

过时文案（顺手修）：PerformanceAnalysisPanel.tsx:1120/1292「达成率口径：累计签单保费÷年计划」——实际分子已随口径（起保口径下=保费收入 YTD），文案误导。

**开放点（实施时定或再裁）**：行业对标是否保持例外（表12 签单考核口径默认；表3 起保仅省×险种粒度——10-22 owner 已确认低风险恒签单正确，倾向保持）；计划达成时间进度锚（最新签单日）在起保口径下的锚列；金融同业起保口径下窗口上界锚。

## 原则修订 v2 补充（2026-10-23 第二轮，owner 细化）

**owner 原话**：

> 还有哪些其实是讲签单业绩的？应该是默认签单口径，而不是锁死签单口径。继续排查，然后列清单和你的建议。然后，如果是涉及到原则性的问题，你要来问我。至于事实上，它的数据是签单，那当然就是签单嘛

**细化语义**（与 v2 合成完整原则）：

- 锁死（硬编码）签单的唯一合法位＝「签单保费」指标本身。
- **讲签单业绩的页面＝默认签单＋可切**（不是锁死）。
- 数据事实就是签单的（行业表12 投保快照、签单行为属性类指标）＝保持签单，不强行加切换。
- 其余页面＝默认起保＋可切。

**排查清单（2026-10-23 @ 918e6d75b 全量复核）**：

- ✅ 已符合「默认签单可切」：业绩分析（标题词即「签单业绩」，DATE_CRITERIA_PERFORMANCE_TITLES）、保费增长、保费报表、图表台账、战略驾驶舱（cost tab 锁起保例外）——全局默认 policy_date（data.ts:293）现服务于这些页。
- 🔧 需从「锁死签单」改造为「默认签单可切」：①业绩热力图下钻格（performanceHeatmapSelection.ts:139 硬编码→跟随口径）；②网点经营页（preset 锁+SQL 签单分桶；NetworkOutletFact 有起保列，分桶列可随口径）；③金融同业全族+机构版（financial-channel.ts:603 policy_date BETWEEN；底表 PolicyFact 有起保列；窗口上界=最新签单日属数据新鲜度锚，可与口径过滤列分离）。
- 🔧 需「默认起保可切」（001/004 同族）：KPI 看板+首页大盘（001）；计划达成/报告中心（004，achievement_cache 双口径）。**架构前提：preset 现只有「全局默认签单可切」「锁死 lockedDateCriteria」两态，无 per-preset 默认口径——需新增「页面级默认口径」第三态**，全局单值默认无法同时表达「业绩页签单+KPI 页起保」。
- ✅ 数据事实签单，保持：行业对标表12（投保快照；表3 起保已页内可切）；系数监控（自主定价系数=签单行为属性，起保口径无业务意义）；体检推送「签单保费同比近 4 周」（推送物签名位）；KPI 墙「签单保费」卡（唯一合法硬编码例外位）。
- 边缘披露：/pivot 工具 week_number/month_number 维度硬编码 policy_date 提取（pivot.ts:87-88，Agent 工具域非 UI 页面，随工具口径化改造评估）；日报 bundle 各段跟随所属域。

**owner 已裁（2026-10-23 AUQ）**：①成本类三页（成本分析/赔案明细/客户流向）**维持锁死起保**——新原则「允许筛选」不适用成本类；②渠道出单业绩页（网点经营/金融同业/机构版）**改默认签单可切**；③默认口径分布 owner 要求**逐指标裁定**——两批 AUQ 已全部裁完：

| 页面/指标位 | 默认口径 | 可切性 |
|---|---|---|
| KPI 看板墙卡四列＋达成率两列（/dashboard） | 起保（001 既有＋达成率正配计划） | 可切 |
| 首页经营体检（/home，与 KPI 同源） | 起保 | 可切 |
| 图表台账（/chart-ledger） | **起保（owner 选非推荐项）** | 可切 |
| 计划达成（报告中心，004 合流） | 起保 | 可切（achievement_cache 双口径改造） |
| 成本类三页（成本分析/赔案明细/客户流向） | 起保 | **锁死（owner 维持）** |
| 业绩分析（/performance-analysis） | 签单 | 可切（现状已符合） |
| 保费增长（/growth） | 签单 | 可切（现状已符合） |
| 保费报表（/reports） | 签单 | 可切（现状已符合） |
| 战略驾驶舱（/strategy-cockpit，cost tab 锁起保不变） | 签单 | 可切（现状已符合） |
| 网点经营 | 签单 | 可切（**从锁死改造**：SQL 分桶随口径＋preset 解锁） |
| 金融同业全族＋机构版 | 签单 | 可切（**从恒签单改造**：窗口过滤列随口径） |
| KPI「签单保费」卡 | 签单 | **锁死（唯一例外位）** |
| 行业对标表12／系数监控／体检推送同比 | 签单 | 数据事实保持，不加强制切换 |

**架构前提（实施第一刀）**：preset 现只有「全局默认签单（buildResetFilters data.ts:293 单值）＋锁死 lockedDateCriteria」两态——默认起保的页（dashboard/home/chart-ledger/计划达成）与默认签单的页（performance/growth/reports/strategyCockpit）并存，需新增 **per-preset 默认口径（defaultDateCriteria 第三态：默认不锁）**，全局单值默认退役为回退值。

**转正映射建议（owner 2026-10-23 采纳）**：①per-preset 默认口径机制＋全站默认分布落地（含 001 合流；过时文案两处顺手修）②热力图下钻格跟随口径（performanceHeatmapSelection.ts:139 小改）③网点经营页可切④金融同业可切⑤计划达成双口径（004 合流）；另口径原则 v2 正本登记进业务规则字典。待「开工 002」令即按 build 协议执行。

## 探索 #1（2026-10-15）

- 探查锚: chexian-api @ 61063382（origin/main，2026-10-15）
- 防撞车: 无直接撞车；口径裁定记录已有成本页锁起保裁定（2026-09-04-user-0e28d3，业务规则字典 B 章节）。
- 现状梳理（页面×口径全景，完整表见 flint 会话交付，要点）:
  - 前端 preset 锁定登记于 `src/shared/types/filters.ts`（178-606）：cost/claimsDetail/customerFlow 锁起保，networkOutlet 锁签单，renewalAnalysis/renewalTracker/quoteConversion/salesTeamPerformance/repairPage/motoCost 锁第三口径或无口径；full/performance/growth/chartLedger/strategyCockpit 可切（默认签单=全局基线 policy_date，`buildResetFilters` data.ts:283）。
  - 战略驾驶舱 cost tab 锁起保+保单年度成熟度（`src/features/pages/strategyCockpitTabs.ts:63`）。
  - 后端恒定口径：/kpi 六列墙卡恒签单 YTD（A10）；满期八列恒数据日估值（不消费 dateField）；/financial-channel 全族、/financial-report-org、/coefficient-monitor、/plan-achievement 恒签单；/monitoring-revenue-quality 恒起期锚；/expense-development、/claims/loss-ratio-development、/loss-ratio-attribution 恒保单年度批次。
  - 原则对照结论：成本类现状已全部起保或生效日归属（✓）；业绩页现状已全部签单或默认签单（✓）；**唯一冲突 = 001 卡裁定 KPI 看板默认起保**。另注：KPI 页成本类指标（满期八列）根本不消费口径选择器，改默认起保对成本指标无增益——001 的原始动机在该页无法通过默认值实现。
- 转正映射建议: 视 Q1 裁定——若 001 维持签单默认则 002 无实施项（纯原则登记，可并 001 的 issue 或单独 doc issue）；若有口径修订项再定。
- 验收: 一览表与 preset 登记/后端路由口径逐条对得上；owner 确认原则表述。

## 开工记录

（未开工）
