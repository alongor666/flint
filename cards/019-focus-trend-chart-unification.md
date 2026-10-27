# 019 · 聚焦趋势图三套实现归一（新版 echarts 私有／KPI SVG 私有／业绩透视 TrendChart）

- 状态: shipped（2026-10-27 owner 合并 PR [#2628](https://github.com/alongor666/chexian-api/pull/2628) @956585fc9，deploy success 生产上线；issue #2619 CLOSED）
- 目标仓: chexian-api（依据：三套实现均在该仓前端）
- 类型: 优化（承载归一重构，行为不变优先）
- 录入: 2026-10-26

## 原文

owner 2026-10-26（chexian-api 协调会话，对「工单 B（聚焦视图复用 dashboard 聚焦共享基座）如何重新裁定」的 AUQ 作答）：选择「撤 B，原则并入 #2595（推荐）」。所选选项描述原文：「撤销独立工单 B；把『复用优先、基座缺口 additive 扩展、不 fork』写进 #2595 刀三的验收约束（矩阵组件落共享位或复用既有壳）；趋势图三套归一另立 P3 小 issue 择机。零撞写集。」同日前序指令原话：「……先把注册表按省修改，未明确按原来，已明确按省差异化，再对工单B做重新裁定。」

## 澄清（AUQ）

- Q1 工单 B 如何重裁 选项: A 撤 B、原则并入 #2595（推荐）/ B 收窄为趋势图归一 / C 保持原卡串行其后 / D 彻底撤销 → **owner 答: A**（2026-10-26）。本卡即 A 项「趋势图三套归一另立小项择机」的载体；按 chexian-api 根 AGENTS.md「灵感先进卡、转正才建 issue」纪律先录卡。
- 先探可定：目标仓 chexian-api；类型 优化/重构；优先级 P3（选项原文「择机」）。
- Q（转正 AUQ，2026-10-26 晚，前置 #2595 合并后发起）四问全采推荐：① pivot TrendChart **不并入**（聚焦域内归一，透视域留原地）；② 悬停精读＝素 `<title>`＋既有逐月数据表；③ 取色层＝chartColors 常量（与 VcrMatrixPanel 基座先例一致）；④ **现在建 issue**——已转正 [chexian-api#2619](https://github.com/alongor666/chexian-api/issues/2619)（type/tech-debt＋area/frontend）。

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

## 探索 #2

探查锚同 #1（chexian-api @ 53bcb145e，2026-10-26）。四项增量：

- **#2595 对本卡目标的影响面【事实】**：读 #2595 issue 全文——刀三组件清单为「象限图/哑铃≤3 自动切/五块一页/吸顶 Tab+scroll-spy/联动表/业务员下钻」，**未列时间趋势折线承载**；且原型目录 `design-handoff/vcr-matrix-prototype-20261026/` 尚未入 origin/main（`git ls-tree origin/main -- design-handoff/` 无 vcr-matrix*，属 owner 本地/待入仓）。暂判：卡内「若其矩阵图承载落了共享位，本卡目标随之收窄」不触发——#2595 动 FocusAuxiliary 接线（VCR 卡聚焦内容替换）但不触碰 buildFocusTrendOption/FocusTrend 代码路径；撞写面=同文件并行政动，串行约束维持。终判以原型 README 裁定回执复核（issue 失效条件原文「以 README 为准」）。
- **chartKey 非死态防御【事实】**：`src/widgets/charts/zoomAwareChart.tsx` 实现+头注——`chartKey` 只随页面缩放档变化（`zf-${factor}`，2026-09-19 评审 R1 的 canvas 位图密度补偿），与切卡/换容器内容无关、不触发 remount。ov2 侧经 EChartContainer 无任何已知死态防御层。
- **ov2 域死态零报告（两法零命中）**：a) issue 三组关键词（overview-v2 图表空白/聚焦 死态/ov2 趋势）搜索无报告；b) FocusAuxiliary.tsx 全史 12 提交（#2290→#2576）无死态/空白修复提交。结论【断言·代码推理】：ov2 同栈（echarts-for-react/lib/core）同场景（`FocusAuxiliary.tsx:125` view 条件渲染换内容）风险敞口与 KPI 先例同构，「未爆」归因使用频率低而非免疫——019 归一动机从「防患」坐实为「还债」。
- **主题机制实为三层混用【事实】**：① `chartColors` 为编译期 hex 常量语义板（`src/shared/styles/index.ts:442` 起；canvasNeutral 注释明言「明暗恒白/恒深」，有 chart-colors/contrast 测试锁）——FocusTimelineChart 全部取色走此层，暗色下不随动；② KPI 域内 TrendSpark 先例另走 `var(--c-primary)` CSS 变量（暗色自动跟随）；③ ov2 echarts 走 `getChartTheme(resolvedTheme)` 运行时切换。【断言·代码推理】归一组件主题最优路线=CSS 变量（TrendSpark 先例、暗色免费、SVG 原生支持）；chartColors 常量色在 SVG 用亦有对比度保证但暗色不随动——归一时须选定一层写进组件契约，这是探索 #1「风险」段两套主题机制条目的细化收窄。

### 探索 #4（2026-10-26 晚，前置满足确认）

- 【事实】PR #2611（#2595 刀3 终刀）已 MERGED（11 项 CI 全绿）；issue #2595 CLOSED(COMPLETED)。main 推进至 **c54004cc6**（同日 #2612/#2613 一并合入）。
- 【事实】基线复核通过：`src/shared/focus/` 六文件含 VcrMatrixPanel 入位；FocusAuxiliary 矩阵页签接线定稿（matrix 页签 @138/145，与 k3 分支预览一致）；**三套趋势图零漂移**（53bcb145e..c54004cc6 对 kpiFocus/、pivot/TrendChart.tsx、FocusAxesCharts.tsx、EChartContainer.tsx diff 为空）。
- 前置时机成熟，探索阶段收口；转正 AUQ 三分叉发起（③ 并入与否／tooltip 形态／主题层——注意 #2595 基座新先例 VcrMatrixPanel 用 chartColors 常量，与 TrendSpark 的 CSS 变量先例构成主题层分叉的两个对立锚）。

### 探索 #3（2026-10-26 晚，#2595 落地进展触发的基线复核）

探查锚更新：origin/main 53bcb145e → **fda7d79d7**（同日 5 个合并）；#2595 刀2 已合（PR #2601，阈值 byPage 78/83/88 注册表登记 CX-ADR-8a818f）、**刀3 终刀在途**（PR #2611 开放，`origin/i/2595-k3`）。

- 【事实】刀3 形态＝**加页签非替换**：FocusAuxiliary 仅 +25 行接线——新增 `matrix` 页签（仅 `variable_cost_ratio`/`policy_year_loss_ratio` 两卡显示，`VCR_MATRIX_FOCUS` 映射），`view==='trend'` 的 FocusTrend/FocusAxes 原样保留。探索 #2 的「目标不收窄」暂判就此落定（README 复核降为形式项）；趋势图归一对象范围不变。
- 【事实】`VcrMatrixPanel`（1,069 行＋366 行测试）落位 **`src/shared/focus/`**——019 卡原文验收约束「矩阵组件落共享位」兑现；且头注明文「**零依赖手绘 SVG（组图纯 SVG 先例，禁引 echarts）**」，用色走 chartColors（+36 行色阶扩展随刀入 styles）。
- 【断言·代码推理】聚焦域共享基座的事实技术栈已被两点钉死为 SVG：轴族（FocusAxesCharts，#2576）＋矩阵（VcrMatrixPanel，#2595 刀3，组件级「禁引 echarts」约束）。本卡选型从探索 #1 的「证据倾向 SVG」**升格为「基座既定 SVG」**——echarts 路线若坚持须同时翻 KPI 域禁令与共享基座组件级约束两案，AUQ 不再把「以哪套为基」列分叉；`renderer:'svg'` 三臂实验仅剩翻案检验价值。
- 剩余动作（按序）：① 等 PR #2611 合并（前置满足）；② 合并后基线复核（fetch 重扫 FocusAuxiliary 接线定稿形态＋三套趋势图无漂移＋防撞车重扫）；③ 转正 AUQ——分叉已收窄为三小项：③ pivot TrendChart 是否并入（域界）、ov2 换 SVG 后 tooltip 形态（素 title vs 自绘富提示）、主题层选 CSS 变量还是 chartColors 常量（探索 #2 倾向前者）；④ owner 拍板后建 issue（工作项住 issue 树纪律）→ P3 择机开工。

### 探索 #5（2026-10-27，开工前基线复核）

- 【事实】main 推进至 **5e3d98324**（11 提交：#2614/#2616/#2617/#2618——vcr-matrix 阈值链收尾刀「byPage 退役改 byBranch＋右闭边界」、ETL 三件），**开放 PR 队列清空**。
- 【事实】019 写集五块（FocusAuxiliary/kpiFocus/pivot TrendChart/shared/focus 趋势面/EChartContainer＋styles）对 c54004cc6 **零漂移**；唯一 shared/focus 变化＝VcrMatrixPanel ⓘ 说明层 6 行文案勘误（阈值区间边界表述），非趋势承载、不在写集。
- 结论：issue #2619 失效条件未触发，基线复核通过，随时可「开工 019」；锚点刷新 5e3d98324（2026-10-27）。

## 开工记录

- 2026-10-27 开工（本日完成探索#4/#5 基线复核后 owner 下令）。issue [#2619](https://github.com/alongor666/chexian-api/issues/2619) → **PR [#2628](https://github.com/alongor666/chexian-api/pull/2628)**（worktree `chexian-api-wt-2619` 分支 `i/2619`，基线 5e3d98324）。
- 闸① DSH·GLM5.3（strong 冒烟自报 glm-5.3）三轮：R1 REQUEST_CHANGES（1P0/5P1/4P2/3P3——P0=ov2 既有趋势单测 interaction.test 被规划漏列）→ v2；R2 REQUEST_CHANGES（1P1/3P2/6P3——P1=断言迁移示例性列举致覆盖净损失）→ v3；R3 终审 APPROVE（余 5×P3 并入 v3.1）。规划 `docs/plans/2026-10-27-focus-trend-chart-unification-plan.md` v3.2 随 PR 入库。
- 实施 S1-S7（13 文件 +690/−251）：新增 `shared/focus/FocusTrendChart`（L1 绘制件）+`shared/hooks/useElementWidth` 上提（kpiFocus re-export）；KPI FocusTimelineChart 收敛口径映射壳（**3 用例零改动仍绿**）；ov2 FocusTrend+06 附线换承载（buildFocusTrendOption 删、echarts imports 清零）；interaction.test 趋势组断言意图逐条迁 DOM（无净损失）；focus-axes 悬空 mock 删；INDEX 两处登记。
- 独立评审（DSH strong，非作者）：P0×0/**P1×1**/P2×2/P3×4——P1=ov2 趋势域 `Math.min(...vals,0)` 强制含 0 与换前 scale:true 非零基语义相反（金额序列贴顶压缩 ~12 倍）。返修 `bddf8d8e4` 全处置（域改真非零基+cy 分散性锁防复发+isFinite 同构+行为差清单补录 14 项）。评审锚 PR#2628 issuecomment-6431204579。
- 闸②主会话亲自复评返修增量：**通过**（修法与评审建议一致、padv 兜底链覆盖退化已复核、断言验算 146.6>105.6 实跑绿、清单诚实补录）。
- 验收：typecheck 三工程绿／四目录 406 用例绿／governance exit 0／E2E CI 强制（本地缺 E2E_PASSWORD 前置）。
- **第 9 步可合并就绪（2026-10-27 10:15）**：rebase 后 CI 11 项全 pass（双 E2E shard 绿——首轮 claims spec 红在新基座 0ad655808 消失，证实基座竞争非本 PR）；MERGEABLE 无冲突、走查-可查标签、双路评审锚齐备。**停手待 owner 手合 PR #2628**（禁 auto-merge）。
- **终态（2026-10-27 11:25）**：owner 合并 PR #2628（MERGED @956585fc9，03:25:37Z）→ deploy.yml 自动部署 success（03:37:44Z）生产上线；issue #2619 随 Closes 关闭（COMPLETED）。worktree `chexian-api-wt-2619` 与分支 `i/2619` 已清。周发布说明待当周（W44）收录。
- **并行会话撞车实录（2026-10-27 上午，chexian-api 分支级）**：本会话等 CI 期间，owner 另开的 ZCode 会话对 PR #2628 做了第二路独立评审（issuecomment-6431347447，七条 findings）＋返修（commit 69d86913d）＋复审，直接 push 到 `i/2619`。本会话 rebase 到新 main（0ad655808）时完整保留其提交（cb2d3ff17），闸②增量复评通过：修本会话 S7 INDEX.md Focus 行 python replace 静默未命中、真发现第 15 项 KPI x 标签变化（「1月」vs 裸数字）、勘正第 4 项（ov2 单线无 compactHide 不砍线）、更强 y 域定向锁（低值点必落绘图区下半，变异红绿验证）、组件三小修（JSDoc/bareLineIndex 提循环外/zeroLine 越界 dev 警告）。零工作丢失，双会话返修链全保序。
- 首轮 CI：E2E Shard 2 唯一红＝`32-workbench-claims.spec.ts:92`（赔案当年发展列头匹配，与本 PR 写集零交集）——main 基座同期同 shard 红（c611ac4d 01:18Z failure），后 #2624/#2627（含 claims 测试静稳修复 07b6c6c86）合入 main 恢复绿（0ad655808 run success）。处置＝rebase 到 0ad655808 重跑 CI（force-with-lease 推送，四目录 406 用例+governance 复验绿）。
