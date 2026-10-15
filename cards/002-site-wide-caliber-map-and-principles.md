# 002 · 全站口径一览与分派原则（成本类起保、业绩签单）

- 状态: clarifying
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
- Q2 「业绩页面应是签单口径」的落地形态？ 选项: A 业绩页锁定签单口径不可切（对标成本页锁起保先例）（推荐）/ B 维持现状默认签单+允许手动切起保 → **待答**

### 澄清补记（2026-10-15 owner 纠正）

- 「业绩页」= 业绩分析页 /performance-analysis（owner 点名）。该页现状：主面板（汇总/下钻/趋势/Top业务员）经 buildFilterParams 跟随全局口径选择器——默认签单、但用户可切起保；机构热力图下钻格已强制签单（performanceHeatmapSelection.ts:143，filter-params-debt #1225）。原则差距点 = 业绩页主面板仍可被切到起保。

## 讨论

- 两条原则作为目标仓口径裁定的上位规则，若 owner 确认，转正时可建议登记进 chexian-api 业务规则字典（与 2026-09-04 成本页锁起保裁定同库）。
- 边缘口径（非签单/起保二分）不属于两原则管辖但入表披露：到期日（续保族）、报价时间（报价转化）、承保确认时间（队伍标保）、满期时间分摊（KPI 满期八列/成本满期赔付率）、保单年度批次（费用发展/赔付率发展）、起期锚（收入价质监控）。

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
