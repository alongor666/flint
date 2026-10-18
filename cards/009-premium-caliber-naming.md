# 009 · 保费三口径命名正本清源（保费收入/签单保费/实收保费）

- 状态: building（2026-10-18 开工；对锚 ba25c6cc，漂移 2 commit 均为 deploy 修复与卡面零交集，未重探）
- 目标仓: chexian-api（主；「几个仓库」是否含 auto_loadbi 待 AUQ，依据：repos.md 三仓中仅 chexian-api 有保费 UI 展示面，auto_loadbi 为 BI 自动化未探）
- 类型: 数据口径 / 规范
- 录入: 2026-10-18

## 原文

与保险有关的仓库，保费有三个口径，起保、签单和实收，起保未必实收、签单未必起保、签单未必实收，在相关仓库中在 UI 端显示的页面名称应该都要不同，分别对应着：
1. 起保口径：保费收入
2. 签单口径：签单保费
3. 实收口径：实收保费，需要做好提前准备工作，同时在几个仓库（包括 UI 和报告）都要正本清源：
1. 显示的保费，应该都不同；
2. 不显示的保费，对应的名字要跟口径相关。满期保费一定是起保口径的，因为它是根据从起保日期算到数字日期（或者叫观察日期）。

## 澄清（AUQ）

### 先探可定（依据，chexian-api @ 8fb4f069 origin/main 2026-10-18）

- **三口径目标命名与仓内先例完全一致，方向无冲突**：
  - 起保口径 =「保费收入」：已落地——`server/src/sql/shared/metric-date-anchor.ts:6`（inforce_premium 保费收入=保险起期落窗口内）+ `server/src/config/metric-registry/categories/foundation.ts:53`（注册名「保费收入」，附与 iap 经营决策平台对拍 8/8 命中证据）；口径选择器双标签即「签单业绩/保费收入」（`src/shared/config/date-criteria-labels.ts:34-44`，注释明示「起保日期口径下看的指标名是保费收入而非起保业绩」）。
  - 签单口径 =「签单保费」：已是通用词（仓内出现文件数以 `grep -rln '签单保费' src server | wc -l` 为准，探针时点 111）。
  - 实收口径 =「实收保费」：仅存在于标保页族（`server/src/config/metric-registry/categories/sales-team.ts`、`src/features/sales-team-performance/`，admin-only）。
- **满期保费现状**：出现面广（同法 grep「满期保费」探针时点 98 文件）；算法 = 起保日→观察日的暴露天数分摊（KPI 满期八列恒数据日估值、不消费口径选择器，002 卡探索已锚）——与 owner 原文「从起保日期算到观察日期」陈述一致。
- **与 002 卡关系**：正交不并卡——002 管「哪页用哪个日期口径」（已裁定无实施项），本卡管「各口径保费显示名叫什么」。本卡探索可直接复用 002 的页面×口径全景表作底。

### 待 owner 裁定（AUQ）

- Q1 覆盖范围（「几个仓库」具体指哪些）？ 选项: A（推荐）chexian-api 全量（UI 显示位+仓内报告/IM 推送文案），auto_loadbi 探索后再定是否入列 / B chexian-api + auto_loadbi 一起 / C 仅 chexian-api UI 显示位（不含报告文案） → **owner 答: A**
- Q2 裸词「保费」的处置？ 选项: A（推荐）指标位消灭裸词（凡显示一个保费数值处必带口径名：保费收入/签单保费/实收保费/满期保费等专名），页面名/tab 名/分组标题等非指标通称位允许「保费」 / B 全站连通称位也消灭「保费」二字 / C 维持现状，只改明显错配处 → **owner 答: A**
- Q3 派生口径（满期保费、财务已赚保费）的命名挂靠？ 选项: A（推荐）维持专名不改——满期保费/已赚保费是行业通用术语且各只有一个算法，在口径地图与注册表 description 锚定「满期=起保暴露派生」即可 / B 显示名挂口径字样（如「满期保费（起保）」） / C 由探索阶段盘点后按歧义风险逐个定 → **owner 答: A**
- Q4 实收口径的处置深度？ 选项: A（推荐）仅命名正本清源——「实收保费」名字已对，不新增实收口径展示面（实收数据面现仅标保页 admin-only） / B 同时把实收保费提升为常规 KPI 展示 / C 探索阶段先盘点实收数据可用性再定 → **owner 答: A**

## 讨论

- 终局结论（2026-10-18 owner 全按推荐裁定）：四题皆 A——范围 chexian-api 全量（UI+报告/IM 文案，auto_loadbi 探后再定）；指标位消灭裸「保费」、通称位允许；满期/已赚维持专名+口径地图锚定；实收仅正名不加展示面。
- 本卡与 001/002/004 同族但正交：前者们裁「口径分派」（哪页用哪个日期字段），本卡裁「口径命名」（显示名与口径一一绑定、互斥可区分）。002 产出的页面×口径全景表是本卡盘点底图。
- 已有命名先例（inforce_premium=保费收入 + 双标签）说明方向早已对齐，本卡实际工作面是**系统性覆盖**：清理无口径限定的裸「保费」显示位、报告/推送文案对齐。
- 【断言】改动面主要是前端 label/表头文案与报告模板文案，数据与口径逻辑零改动，属低风险高面广的文案纪律活；具体面须探索阶段盘点（含 IM 推送、周报 shell、guide 文案）。

## 探索 #1（2026-10-18）

- 探查锚: chexian-api @ 5e468e4f（origin/main；本地 worktree 8fb4f069 落后 310 commit，盘点全部 `git grep origin/main` 树取数）
- 防撞车: 两法同证零命中——法一 `gh pr list --state open`（8 条全为 spark 日报/a11y/deploy，无命名相关）；法二 `gh issue list --state all --search '正本清源 OR 显示名 OR 命名规范'` + `gh pr list --state all --search '命名 OR 文案 OR label'`（命中均为 AI 解读/治理/更名迁移旧案，无保费显示名正名项）。
- auto_loadbi（Q1 遗留探查）: 不入列。无 UI/报告展示面（BI 下载导出仓），「保费」仅在 docs/ 字段字典——属 BI 源表字段名引用，改名反而与源系统脱钩。入卡披露即止。

### 总量与方法（复核命令）

【事实】`git grep -n '保费' origin/main -- 'src/**/*.ts' 'src/**/*.tsx'` 排除 `*.test.*` = 238 文件/1146 行；剔除已限定（签单/实收/满期/已赚保费、保费收入）与注释后裸词/模糊候选 400 行（server 同法 303 行，SQL 注释占大头）。全仓四名覆盖（含 docs/测试）：签单保费 223 文件、满期保费 204、实收保费 32、保费收入 43。

### 发现 1【事实】裸词指标位 38 处（1a 主清单，按杠杆分组）

- **A 全局共享层（杠杆最大，前后端孪生）**：`src/shared/types/view-perspective.ts:55,58` + `server/src/types/view-perspective.ts:55,58`（视角切换 label「保费」+ Y 轴「保费（万元）」，performance/growth/truck 三页复用，SUM(premium) 签单口径未标——改一处须两侧同步）；`src/widgets/charts/{LineChart:474,QualityBusinessChart:211,DualYAxisComparisonChart:148,160,161,183,211,220}`（Y 轴/系列名/tooltip，当期保费/基期保费等）；`src/shared/config/metric-labels.ts:443`（deal_quote_premium label 裸「保费」，注册名已是「成交保费（报价口径）」quote-pivot.ts:150 可直接对齐）。
- **B dashboard 族 14 处**：热力图 tooltip/焦点卡（`HeatmapTooltip.tsx:117`、`HeatmapFocusPanel.tsx:125`）、GeoSection:132,154、TableSection:38「总保费」、TruckAnalysisPanel:66、kpiFocus 四文件 6 处（FocusExtraTimelines:96,107 / KpiFocusTool:178,213 / FocusTimelineChart:96 / TrendSection:180）、kpiCardProps.ts:322,346,356,367,442。
- **C growth**：GrowthAnalysisPanel:137、GrowthComparisonSection:160,161（当期/基期保费列头）。
- **D 系数监控**：CoefficientMonitorTable:580（列头「保费」，口径只在 tip「签单口径池保费」）、coefficientTrendChartOption:233。
- **E cost 族 8 处**：CostAnalysisPanel:312,324,340 + EarnedPremiumTable:203,221 + EarnedPremiumCharts:126 + VariableCostKpiBoard:59（「保费合计(万)」族）；NewEarnedPremiumTable:396,412（滚动 12 月保费，实为起保日期口径财务已赚族——错配高危）。
- **F 金融同业族**：OrgReportTable:62,63（昨日/当月保费，policy_date 签单口径，口径仅存 query-routes-metadata.ts:920）；BranchStackedBars:131、financialChannel.ts:480-482。
- **G 网点**：NetworkOutletPage:271,306,383（列头「保费(万元)」，同页 KPI 名反而已用「签单保费」metric-labels.ts:229-236——页内自不一致）。
- **H 行业对标 9 处**：historyModel:388、industryDisplay:5（指标选择器裸「保费」，历史域=起保口径 :509 / 快报=签单口径 InsurerPivot:589——**同名跨口径现成实证**）、InsurerPivot:115、HistoryInsurerBoard:103,224,407,413,427,429、IndustryFocusCard:86,87、IndustryMonthlySources:50。
- **I 零散**：RepairDiversionList:84、EchartsPanels:139,186。
- 模式总结：重灾区=表格列头（保费(万)/当期保费/保费合计）、图表 Y 轴与系列名、热力图 tooltip；口径信息往往存在于 tip/脚注/variantNote，主显示名不带。

### 发现 2【事实】通称位 ~12 处合规（Q2 裁定允许）

页面名「保费增长」routeRegistry:266、tab「保费达成明细」、段落标题「保费与件数走势」「保费节奏」等（清单存探索工作稿；边界一处：metric-labels.ts:243 devPickerLabel「保费」是 tab 短名但直切保费数值格）。

### 发现 3【事实+断言】模糊位五家族（不带三口径限定、语义特定——是否纳入需 owner 分档裁定）

1. 比值派生族：车均/件均/人均/单均保费（metric-labels 多处；⚠ 已有同名跨口径实证：车均保费同比 my-items 起保锚 query-routes-metadata.ts:1551 vs KPI 墙签单口径，自注「同名不同数」）。
2. 险种/客群限定族：车险保费、驾意保费、新能源保费占比、非车险保费、优质保费。
3. 税后保费族：金融同业整页术语（FinancialChannelPage:1144 等十余处，签单口径税后展示，页内自注）。
4. 缺口/计划/派生族：保费缺口、阈值保费、承保保费、净保费、新签保费、保费计划。
5. 「总保费」(total_premium)：主力指标、SUM(premium) 签单口径、无口径词——见发现 4。

### 发现 4【事实】注册表命名分叉（本卡最大结构性发现）

- 同一 SUM(premium)：metric-registry `total_premium` name=「总保费」（foundation.ts:16）vs agent 注册表 `signed_premium` name=「签单保费」且 **aliases 收裸词「保费」/「总保费」/「签单规模」**（agent-metric-registry.ts:79）——AI 问答会直接吐裸词。该文件 :187-209 满期/财务已赚条目口径严谨（#32① 改名先例）。
- 【断言】两套注册名并存（总保费/签单保费）本身就是「同物异名」，正名方向二选一：agent 侧对齐 metric-registry，或 metric-registry 主名补口径——波及面差异极大（前者只动 agent 文案，后者动全部前端消费位）。

### 发现 5【事实】正面清单（已达标，无需动）

- inforce_premium=「保费收入」链路一致：注册 foundation.ts:49-95（dateAnchor=insurance_start_date、iap 对拍）↔ 前端 metric-addressing.ts:137 同名 ↔ 口径切换词 date-criteria-labels.ts:44。✅
- 满期家族 description 口径锚定已到位（抽查 3/3 ✅：earned_premium cost.ts:230 含公式+#13+#32① 互替禁令；baseline_earned_premium :353；premium_per_policy_earned :1317）——Q3 裁定的「注册表锚定」满期侧已完成，剩余已赚侧仅 agent 注册表。✅
- received_premium「实收保费」sales-team.ts:39、deal_quote_premium「成交保费（报价口径）」quote-pivot.ts:150（括号口径=好样板）。✅

### 发现 6【事实】server 报告/推送文案族

IM 推送通道本体（notification-dispatch/outbox/decision-digest 等）0 处裸词（正文由 case 标签拼装）。裸词在：today-top3 文案 4 处（pipeline.ts:241,245、circles.ts:393、sort-keys.ts:71；对照正面例 today-top3.ts:273「保费按签单日」）；告警 2 处（shared.ts:720、dashboard-health-check.ts:68）；AI 诊断文案 6 处（agent-growth-diagnosis:17、diagnosis 四注册表）；路由元数据 query-routes-metadata.ts 51 处（喂 copilot 的描述）；SQL 列别名直出报表（premium-report.ts:105,116-118,125,164、marketing-report.ts:87,89——险种限定无口径）。

### 新真分叉（探索发现，2026-10-18 owner 已裁）

- Q5 模糊位分档：A 维持现名 / B 五族全量补口径限定 / C 逐处点名 → **owner 答: A（模糊位不变）**
- Q6 total_premium 主名：owner 自定第四案（非 A/B/C）——**「总保费」显示名跟随前端选择的口径动态变化：签单口径显示「签单保费」、起保口径显示「保费收入」**。即「总保费」作为静态显示名退役；实现理解为共享 caliber→label 映射（dateField→签单保费/保费收入），全部 total_premium 显示位引用。配套取向（按正本清源精神保留，owner 纠偏即停）：metric-registry 的 id/name 为内部契约不动，只动显示层；agent 注册表裸词别名「保费」删除（agent 无口径上下文，不得吐裸词）；实现时须核对 total_premium 在 insurance_start_date 窗口下的聚合语义与 inforce_premium 归集规则（metric-date-anchor 批改行分支式归集）是否一致，不一致则实施时呈差异请 owner 裁。
- 命名规则体系终局（Q1-Q6 合成）：①用户可切口径页面——保费主指标名动态随 dateField；②恒定口径位——固定补口径名（金融同业族=签单保费、cost 族=满期/已赚专名、行业对标按域补名：历史域起保/快报签单）；③模糊位五族——维持；④派生口径——专名+注册表锚定（已完成大半）。

### 转正映射建议

单 issue（口径命名正本清源）分批 PR：
- PR-1 动态命名机制+全局层：共享 caliber→label 映射函数；view-perspective 前后端孪生 label/yAxisLabel 动态化；widgets 图表轴/系列名、dashboard/growth 视角切换位接入（发现 1A/B/C 组）；
- PR-2 恒定口径位补名：金融同业列头（1F）、cost 族挂满期/已赚名（1E，含 NewEarnedPremiumTable 起保口径错配修正）、行业对标按域补名（1H）、网点/系数监控/零散（1D/G/I）、metric-labels:443 对齐注册名「成交保费（报价口径）」；
- PR-3 server 文案族：today-top3 4 处+告警 2 处+SQL 别名 9 处+agent 裸词别名删除；
- PR-4 路由元数据 51 处与 AI 诊断文案（可降级 P2 批次或并 PR-3）。

- 验收: ①改造后按探索命令复跑，发现 1 清单 file:line 处裸词指标位零残留（通称位/裁定豁免族除外）；②前后端孪生 view-perspective 两侧一致；③`bun run governance` 过；④抽查 AI 问答（问「本月保费」）确认 agent 输出带口径名。

## 开工记录

**2026-10-18 开工（PR-1 已 ready 待 owner 走查合并；PR-2/3/4 后续批次）**

- issue: chexian-api [#2213](https://github.com/alongor666/chexian-api/issues/2213)；PR-1: [#2219](https://github.com/alongor666/chexian-api/pull/2219)（分支 i/2213，worktree `~/workspace/chexian-api-wt-2213`）
- 对锚: 开工锚 ba25c6cc（探索锚 5e468e4f 漂移 2 commit 均为 deploy 修复零交集）；实施后 rebase 至最新 main
- 规划: `docs/plans/2026-10-18-口径命名正本清源-实施规划.md` v1→v4 四轮演进
- 闸①（DSH·GLM5.3 strong 档四轮对抗评审）: 首轮 REQUEST_CHANGES 1×P0+3×P1+3×P2（规划外 6 渲染点+「兜底=签单」安全论证被实测推翻——三消费页全部可切口径）→ v2 二审 4×P1+5×P2（truck 切换器 :80/玫瑰图按钮/Dashboard 时间视图「签单日/周/月」/LineChart 双构建器事实——主折线 Y 轴「万元」=单位不经 prop）→ v3 三审 2×P1+2×P2（TrendSection:180 双重归属且 trend 链实测随口径切换非恒签单、验收 grep 判据与件数条件式字面冲突）→ v4 终审 **APPROVE**
- 实施: 共享层（第三表+孪生兜底+切换器 premiumLabel prop 双分支）+五图表+三页面接线（Dashboard/growth/truck 全覆盖，件数视角条件式防错配）；17 文件 166+/37-
- 验证: typecheck 三工程过；全量 1379 文件/21040 用例绿；governance 120 项过；验收 3 两法 grep PASS（独立评审复跑证实）
- 独立评审（步骤 7，DSH 第一路）: **APPROVE 零 P0/P1**+6×P2（兜底名不一致/deps 缺项/词干 undefined 边界/图注无条件式/测试缺口/空态泛词）
- 闸②（步骤 8，主会话亲自复评修复）: **通过**——P2 修 4+1（兜底统一`总+口径名`/deps 补项/词干 `?? 'policy_date'`+IIFE 单次计算/YoyCombo 图注件数视角条件式/两表同值同步守护断言）；DualYAxis 分组头回归与空态泛词归 PR-2 批次
- 终态: PR #2219 ready（rebase 最新 main+P2 收尾 0c21129f；pre-push 全量绿）。**待 owner 三页走查（验收 2/4：growth 切换器+对比列头、Dashboard 趋势标题+时间视图词干、truck 切换器+玫瑰图按钮；件数视角回归）后合并**——部署链 PR 不自动合
- 事故记录: push 卡 40 分钟根因=pre-push hook 全量测试被机器级互斥锁挡（早前被杀 push 的孤儿 vitest 持锁 13 分钟）——kill 孤儿链后正常；TaskStop 杀外层 shell 不杀 hook 子进程树，续接时先 `pgrep -fl pre-push` 清孤儿
- PR-2 前置项（未做）: kpi.ts YTD 六列 dateField 绑定代码级证据核对（A10 复核）；total_premium 起保窗口聚合 vs inforce_premium 归集差异核对
