# 019 · 聚焦趋势图三套实现归一（新版 echarts 私有／KPI SVG 私有／业绩透视 TrendChart）

- 状态: discussed
- 目标仓: chexian-api（依据：三套实现均在该仓前端）
- 类型: 优化（承载归一重构，行为不变优先）
- 录入: 2026-10-26

## 原文

owner 2026-10-26（chexian-api 协调会话，对「工单 B（聚焦视图复用 dashboard 聚焦共享基座）如何重新裁定」的 AUQ 作答）：选择「撤 B，原则并入 #2595（推荐）」。所选选项描述原文：「撤销独立工单 B；把『复用优先、基座缺口 additive 扩展、不 fork』写进 #2595 刀三的验收约束（矩阵组件落共享位或复用既有壳）；趋势图三套归一另立 P3 小 issue 择机。零撞写集。」同日前序指令原话：「……先把注册表按省修改，未明确按原来，已明确按省差异化，再对工单B做重新裁定。」

## 澄清（AUQ）

- Q1 工单 B 如何重裁 选项: A 撤 B、原则并入 #2595（推荐）/ B 收窄为趋势图归一 / C 保持原卡串行其后 / D 彻底撤销 → **owner 答: A**（2026-10-26）。本卡即 A 项「趋势图三套归一另立小项择机」的载体；按 chexian-api 根 AGENTS.md「灵感先进卡、转正才建 issue」纪律先录卡。
- 先探可定：目标仓 chexian-api；类型 优化/重构；优先级 P3（选项原文「择机」）。

## 讨论

- 现状（2026-10-26 只读探索，锚 chexian-api @ 53bcb145e）：三套趋势图承载并存——① overview-v2 聚焦 `src/features/operating-overview-v2/components/FocusAuxiliary.tsx` 内 `buildFocusTrendOption`（echarts 私有装配，含 06 卡附线与逐月数据表）；② KPI 聚焦 `src/features/dashboard/kpiFocus/` 的 `FocusTimelineChart`（SVG 自绘；头注称 echarts 在辅助区有死态故自绘）；③ `src/shared/pivot/TrendChart`（业绩透视族）。轴族组件已由 PR #2576 上移 `src/shared/focus/`，趋势图未随之归一。
- 前置与依赖：串行于 chexian-api issue #2595（VCR 矩阵刀三同写 FocusAuxiliary）合并之后，以其合并后 main 为基线。#2595 刀三已承接「复用优先、不 fork」验收约束——若其矩阵图承载落了共享位，本卡目标随之收窄。
- 关键分叉待探（scout 阶段）：以哪套为基（echarts vs SVG——KPI 侧放弃 echarts 的「辅助区死态」成因是否仍成立须先复核）；归一后 06 卡附线／数据表／部分月标注／达成率线如何承载；#2568 遗留「达成率线/同比虚线未迁」可否顺带收口。
- 与现有卡关系：015（shipped）为 KPI→ov2 能力吸收，本卡为其后续承载归一，不并卡。

## 探索 #1

探查锚：chexian-api @ 53bcb145e（2026-10-26 fetch 后 origin/main；本地检出在 feat/sx-cost-thresholds-by-branch 瘦身分支，取证一律 `git show origin/main:<path>`）。

### 三套实现定位与能力面【事实】

- **① ov2 聚焦（echarts）**：`src/features/operating-overview-v2/components/FocusAuxiliary.tsx`（754 行）`buildFocusTrendOption`@527 / `FocusTrend`@588，经 `EChartContainer`（import@5）挂 `@/widgets/charts/EChartContainer`；消费方 `OperatingOverviewV2Page.tsx:117`。能力：单轴折线、缺数断线不补零（connectNulls:false + 缺数计数脚注）、部分月标注（#2568 S5：末点标签「截至」+点透明 0.45）、06 卡附线两图（`CLAIM_FACTOR_TREND_META` 出险率/案均，各独立容器）、逐月数据表（含缺数原因 title）、主题化（getChartTheme dark）。
- **② KPI 聚焦（SVG 自绘，域内共三件）**：`kpiFocus/FocusTimelineChart.tsx`（柱+达成率自轴[0,achCap≤2]+同比对称双轴±yoyBound、累计同比实线/当月同比虚线、窄栏<430px 砍虚线、部分月柱降透明）；`kpiFocus/FocusExtraTimelines.tsx`（#1991：件数族双序列+优质占比月序列，轻量 SVG）；`kpiFocus/FocusWeeklyTimeline.tsx`（#2031 周演进，头注成文「**本域禁 echarts**」）。消费方两处经 `KpiFocusTool`：`dashboard/PremiumDashboard.tsx:306`、`my-items/components/KpiPanorama.tsx:52`。
- **③ 业绩透视族（SVG 自绘）**：`src/shared/pivot/TrendChart.tsx`（源自 PR #920/CX-ADR-d1ce75，上提 CX-ADR-b85e5a）；单轴柱+可选虚线+可选实线、barColor 逐柱着色、plotHeight 定高、hoverIndex。消费方 4 处：`dashboard/performance/PerformancePivot/PivotFocusCard.tsx:15`、行业对标 `IndustryFocusCard.tsx:4` / `HistoryInsurerBoard.tsx:20` / `InsurerPivot.tsx:21`。

### 关键分叉复核【事实】

- **死态成因仍成立，且 ov2 侧同栈**：`src/widgets/charts/EChartContainer.tsx:3` 即 `import ReactEChartsCore from 'echarts-for-react/lib/core'`——KPI 侧头注记录的四轮未根治死态（同一容器换内容再挂载落 0 宽 → zrender 永不补建 canvas）正是这个封装；ov2 FocusTrend 也是条件渲染换内容（`FocusAuxiliary.tsx:125` `{view === 'trend' && <FocusTrend …/>}`），同风险面（未爆≠不存在，方法学：代码推理）。
- **#2568 遗留出处**：issue #2568 正文（`gh issue view 2568` 第 42 行区）「时间轴标注增强（仅 01/02）：部分月透明＋『截至 x-x』标注；**达成线/同比虚线按八卡数据可得性做，缺则不画**」——ov2 八卡服务端契约无达成率/同比逐月序列，部分月标注已落地、双线未做非承载缺口而是数据缺口。
- **#2595 前置现状**：OPEN 无 PR；`origin/i/2595` == `origin/main` == 53bcb145e（`git rev-parse` 同 SHA，零提交）——刀三撞写面尚未发生，本卡基线仍以「#2595 合并后 main」为准不变。
- **防撞车（两法零命中）**：`gh issue list --state open --search '趋势图'` / `--search 'TrendChart 归一'` + `git grep -ri '趋势图.*归一|归一.*趋势图|TrendChart.*统一' origin/main -- 开发文档 docs .claude` 均无既有项（命中的 #2595 是前置依赖自身）。

### 落位与选型证据【事实+断言】

- 共享位唯一合法：`src/shared/INDEX.md:16` 载明 `shared/focus/` 为「L2 横向全禁 CX-ADR-cebbe5 下的唯一合法共享位」（#2576 上移轴族所在，含 FocusAxesCharts/focusAxesData/focusCalibers/focusScope）。
- 【断言·代码推理】基座选型证据压倒性倾向 SVG：a) KPI 聚焦域有成文「本域禁 echarts」约束，echarts 为基等于要求该域回迁被禁栈；b) EChartContainer 与死态栈同源且竞态四轮未根治；c) 五件聚焦趋势图中四件（②三件+③）已是 SVG。实际可行路线基本只剩「SVG 为基、echarts 侧换承载」。
- 【断言·代码推理】③ 是否并入是真分叉：pivot TrendChart 的 4 个消费方是透视域（单轴、props 注入格式化、hoverIndex 联动），与聚焦域双轴+附线+部分月需求不同层；合理切法是「聚焦域归一落 `shared/focus/`（以②FocusTimelineChart 泛化为基组件），③ 留 pivot 域不动或仅抽底层 path 绘制件」，避免为归一而拉平两域。此分叉转正时 AUQ。

### 改动面与风险【断言·静态核实】

- 改动面（SVG 为基路线）：新增 `src/shared/focus/` 趋势图组件（双轴柱+多线+部分月标注，从 FocusTimelineChart 泛化）；②三件改薄壳或直换 import；① ov2 `FocusTrend` 拆数据映射与承载（06 附线/逐月数据表/主题 dark 保留，SVG 侧需补 tooltip/hover 与主题化——KPI SVG 现无 tooltip，ov2 echarts 有，承载迁移是能力降级面，须逐项对表）；③ 视分叉结论。测试面：三套各自 `__tests__/`（FocusTimelineChart.test / pivotComponents.test 等）迁移或保壳。
- 风险：#2595 刀三同写 FocusAuxiliary（串行约束不变）；ov2 换 SVG 后失 echarts 原生 tooltip/legend/主题细节，交互回归面大——「行为不变优先」要求逐能力对表（tooltip 格式、缺数文案、附线图例、表格不动）；KPI SVG 主题化走 chartColors 常量，与 ov2 getChartTheme(resolvedTheme) 的暗色切换机制不同，合并时统一。
- 验收命令（转正 PR 用）：`bun run typecheck`；`bun run test --run src/features/dashboard/kpiFocus/__tests__/ src/shared/pivot/__tests__/ src/features/operating-overview-v2/__tests__/`（定向三域）；`bun run test:e2e`（聚焦页走查）；切卡/切 tab 死态回归人工验证一轮（KPI 侧先例复现路径）。

### 结论

方向确认可行、无撞车；选型证据收敛于 SVG 为基（KPI 域禁令+死态同栈+四件已 SVG）；真分叉收窄为「③ pivot TrendChart 是否并入聚焦归一」与「ov2 侧 tooltip/主题能力以何种形态补齐」两处，转正 AUQ 呈 owner。前置 #2595 未合并（分支零提交），本卡维持择机 P3、待其合并后以新 main 复核基线再转正。

### 追加讨论（2026-10-26）：`renderer:'svg'` 候选的信任评估【静态核实】

owner 追问「echarts svg renderer 是否更佳选择、值得信任吗」。核实 `node_modules/echarts-for-react/lib/core.js`：

- 【事实】`initEchartsInstance` 为 async（`__awaiter` 包装），`componentDidUpdate` 存在 `dispose()→renderNewEcharts()` 重建路径——封装层有时序竞态面；容器 resize 监听走 `size-sensor` 库（哨兵元素 hack，非 ResizeObserver）。
- 【事实】死态两条竞争病理：H1 渲染器层 canvas 补建缺陷（svg renderer 可救）；H2 封装层 async init × dispose 交错 × size-sensor 0 容器失灵（svg renderer 同死——0×0 画布无人触发重画，换渲染器只换受害者）。头注「zrender 永不补建 canvas」是症状观察非根因隔离；结合四轮修复失败与封装层竞态面，H2 先验更高。
- 【断言·代码推理】`renderer:'svg'` 定位为「30 分钟三臂实验可裁决的假设」而非可信方案：臂1 canvas 复现 / 臂2 svg（H1 检验）/ 臂3 svg+外层 ResizeObserver 手动 resize（H2 检验）。臂2 活→H1 成立候选转正；臂2 死臂3 活→根因在监听层，正解是 EChartContainer 包 ResizeObserver（修好后 KPI 域禁令原始理由消失）；臂3 也死→SVG 自绘彻底封案。验证通过也仅在 019 重写承载窗口有采用时机，KPI 域已稳定三件 SVG 无回迁理由（撤销域禁令是治理成本，收益仅富 tooltip）。

## 开工记录

（无）
