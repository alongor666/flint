# 020 · 全站聚焦卡统一规范与 ov2 辅助栏宽度可调

- 状态: building（2026-10-27 开工：#2639 宽度小刀先行；#2640 串行 PR #2628 owner 手合后接续）
- 目标仓: chexian-api（原文明示）
- 类型: 优化（前端承载统一＋交互补齐；先探可定）
- 录入: 2026-10-27

## 原文

> chexian-api，overview-v2 聚焦卡复用其他辅助栏的聚焦卡的能力，页面宽度可调节，所有页面的聚焦卡统一规范

## 澄清（AUQ）

- Q1 「页面宽度可调节」落点 选项: A（推荐）ov2 解除 316 固定宽回到全站标准拖调（320–560）/ B 全站宽度体系重设 / C 正文页宽 → **owner 答: A**
- Q2 统一规范基座方向 选项: A（推荐）中立共享规范（shared/focus 既有组件族顺势扩展）/ B ov2 FocusAuxiliary 为基座、他页看齐 / C scout 出全站能力对表矩阵后再裁 → **owner 答: A**
- Q3 宽度小刀是否先行 选项: A（推荐）先行独立小 issue（写集与在途 #2628 零重叠）/ B 并入统一规范一刀走 / C 等 #2628 合并后一起 → **owner 答: A**
- Q4 转正归属 选项: A（推荐）独立 issue（跨页范围）/ B 挂 #1090 统一辅助工作区史诗 / C 挂 #2274 计划 v2 → **owner 答: A**

## 讨论

### 防重（不并卡依据）

三层相邻不重叠：**015**（shipped）是 KPI→ov2 单向能力吸收，已闭环；**019**（building，PR #2628 在途）只归一聚焦卡内的趋势图承载；**chexian-api #1090**（史诗 OPEN）统一的是辅助区**壳**（公共入口/四目录接入），其 v0.3 正文「工具可关闭、切换、收起和调宽」的壳层目标已落地。本卡是内容层——全站聚焦卡规范统一＋ov2 宽度缺口——为第三层，独立成卡。

### 先探事实（2026-10-27，锚 chexian-api @ origin/main `12fb5144c`）

- **壳层已全站统一且宽度可调**：`src/components/layout/WorkbenchAuxiliary.tsx:14-16`（MIN 320/MAX 560/缺省 360）、`:364`（拖调钳位＋localStorage 持久化）、`:396-397`（拖调手柄 role=separator）；窄窗自动降级 bottom/full。50+ 文件消费该壳。
- **ov2 是全站唯一固定宽例外**：`src/components/layout/SidebarLayout.tsx:515` 对 `/overview-v2` 写死 `fixedLayout={width:316, minCoreWidth:948}`，拖调手柄在 fixedLayout 下显式隐藏（WorkbenchAuxiliary.tsx:396 `!fixedLayout` 门）。来源=ov2 初建 PR #2342，非后续 owner 裁定——「ov2 聚焦卡复用其他辅助栏的能力」的字面缺口即此。
- **聚焦卡内容层 9 处独立实现**（对表矩阵的对象全集）：kpiFocus KpiFocusTool（KPI 看板；my-items KpiPanorama 复用）、ov2 FocusAuxiliary、performance PerformancePivot/PivotFocusCard、PerformanceOrgHeatmapV2/HeatmapFocusPanel、industry-benchmark IndustryFocusCard、cross-sell CrossSellHeatmapFocusPanel、quote-conversion pivot/FocusCard、renewal-tracker v2/FocusCard。
- **防撞车**：开放 issue/PR 池无聚焦卡统一规范主题（「聚焦」命中 #2619=趋势图归一、#2068=筛选器聚焦模式、#1090=辅助区壳）；PR #2628（flint 019）在途，同写 FocusAuxiliary/kpiFocus/shared/focus——内容层工作须串行其后。

### 初评

宽度小刀改动面极小（SidebarLayout 一处 fixedLayout 撤除＋ov2 核心区在 720px 基准下的八卡网格回归验证）；统一规范是大面（9 处对表→规范/组件收敛，scout 需出能力对表矩阵：页签结构/指标头/图表承载/口径注记/重开行为/窄窗呈现/筛选继承等维度）。015/019 成果均在 `src/shared/focus/`（L2 横向禁令下唯一合法共享位），中立共享规范路线有现成基座。

**转正映射（AUQ 裁定后定稿）**：两 issue 两刀——①宽度小刀（独立 issue，先行，不等 #2628，写集=SidebarLayout＋ov2 布局测试）；②统一规范大刀（独立 issue，串行 #2628 合并后以新 main 为基线，shared/focus 中立基座，9 处聚焦卡对表收敛；scout 先出能力对表矩阵供实施规划）。

## 探索 #1（2026-10-27）

- 探查锚: chexian-api @ origin/main `c611ac4de`（同日 fetch；PR #2628 在途 OPEN，仅 Production Gate 未终）；矩阵取证含只读子代理，载荷断言已主会话抽核（见下）。

### 防撞车（两法交叉验证）

- 【事实】gh issue/pr 搜索「辅助栏 宽度」「fixedLayout」「聚焦 统一」零相关命中（命中项 #2619=趋势图归一、#2068=筛选器聚焦模式、#2274/#1090=ov2/壳层史诗，均非本卡）；文档域 `git grep fixedLayout origin/main -- 开发文档 docs .claude` 零命中。
- 【事实】#1090 史诗执行规划 `docs/plans/2026-10-03-unified-auxiliary-workbench.md` 明文「1280px 不必强制底部；须测实际容器、浏览器缩放与页内表格宽度」——宽度刀与其原则**同向**非撞车；其边界表把「宽度偏好」划给布局宿主、「聚焦内容」划给页面适配器——本卡两刀均在史诗已交付壳层之外，无在途规划认领聚焦内容规范。
- 【事实】PR #2628 文件清单（`gh pr diff 2628 --name-only`，13 文件）不含 SidebarLayout/WorkbenchAuxiliary/layout 测试——**宽度刀写集净空可先行**；但含 FocusAuxiliary.tsx 与 ov2 interaction.test——**规范刀须串行其后**。

### 实施要点 · 宽度小刀

- 唯一病灶: `SidebarLayout.tsx:515` 路由条件 fixedLayout（全仓唯一消费方，PR #2342 ov2 初建带入，无后续裁定锁值）。
- 948 来历【事实】: ＝ov2 八卡网格自身 4 列容器断点 `OperatingOverviewV2Page.tsx:198` `[@container_(min-width:948px)]:grid-cols-4`——fixedLayout 语义是「并排时恒 4 列」。撤除后核心区最低 720（壳 CORE_MIN_WIDTH），560–947px 容器区间网格回落 2 列（容器查询既有设计档 index.css:470-482 族）、<560 单列——与其他页一致的回流行为。
- 壳层行为变化【事实，WorkbenchAuxiliary.tsx:310-317/:364/:396-397，GAP=8】: side 门限 1272→1048；拖调手柄出现（320–560 钳位、localStorage 按 preferenceOwner 持久化、缺省 360）；面板宽 316→360 缺省。
- 内容适配【事实】: FocusAuxiliary 无定宽假设（网格均 `auto_1fr` 响应式型）；VcrMatrixPanel 联动表 `min-w-[600px]`（:970）在 316 下本就横滚，加宽只更友好。
- 死代码处置: 撤条件后 fixedLayout prop 零消费——按减法红线同 PR 删 prop（WorkbenchAuxiliary.tsx:218/:220/:310-311/:317/:396 五处简化）＋ `workbench-auxiliary.test.tsx:50` fixed 分支改写。
- 测试面【事实】: ov2 单测直挂 WorkbenchAuxiliary 不带 fixedLayout（focus-reopen.test.tsx:3/:25、interaction.test.tsx:622 注释已是 1048 语义）——零改动；E2E 无 316/948 断言（tests/e2e grep 零命中）。
- 行为披露（写进 PR 正文）: 1048–1271 帧宽区间从 bottom 停靠变 side 并排；典型笔记本（1440 视口−184 侧栏=1256 帧）从「bottom＋4 列网格＋聚焦全屏模态」变「side＋2 列网格＋并排聚焦」——即裁定方向本身，但需在 PR 明示防走查意外。

### 实施要点 · 统一规范大刀（能力对表矩阵结论）

九处分级（A 宿主/B 内容/C 口径/D 重开/E 筛选/F 共享件六维全查，行号级矩阵在探索记录，主会话抽核三处载荷断言实证）：

- 【事实】**6 处已壳内规范化**（kpiFocus 266 行、ov2 FocusAuxiliary 773 行、业绩透视 PivotFocusCard 227 行、业绩热力 HeatmapFocusPanel 226 行、行业对标 IndustryFocusCard 132 行、报价透视 FocusCard 487+323 行——均住 WorkbenchTool 且消费至少一个共享基座）；**1 处半规范化**（续保 v2 FocusCard 305 行：几何已入壳但 0 共享件，且 `:121` 手写 `echarts.init(svg)`——与报价侧 FocusCharts.tsx:15 头注纪律「EChartContainer 为 L1 唯一实现、禁手写 echarts.init」直接冲突）；**1 处域内私有**（车驾意 CrossSellHeatmapFocusPanel 211 行：fallback 自造 overlay `:95-99`，0 共享件消费）。
- 四维分歧度排序【断言·代码推理】（统一收益高→低）: ①**图表承载四栈并存**（SVG 自绘/EChartContainer/HTML 横条/手写 init）——#2628 合并后聚焦趋势图已收敛 SVG 家族，剩余处置面=报价 echarts 三图（复杂图合法栈）与续保手写 donut（违纪）；②**口径注记**——infoText ⓘ 气泡＋FOCUS_NO_AXIS_NOTE 诚实降级模式仅 KPI 族与 ov2 落地，业绩透视/热力/车驾意/续保四处裸奔；③**指标头五型并存**（主值大字+档位章/chip 网格/dl 事实表/指标卡网格/主值+灯+摘要）；④**重开行为三模式**（selectionKey 自动/显式 open/宿主持 tab）且回归锁测试仅 ov2 与业绩透视有专项（interaction.test/pivotFocusReopen.test）。
- 宿主接线维度已基本收敛（7/9 入 WorkbenchTool，唯二特例均有实证理由：ov2 `narrowPresentation="modal"` 有 E2E 顶栏拦截实证、续保工具注册上移宿主）——**大刀主战场在内容层四维，非壳层**。
- 壳关系定论【事实】: PivotFocusShell（3 消费方：业绩透视/行业对标/报价）与 HeatmapFocusPanelShell（2 消费方：业绩热力/金融同业）是「无宿主 fallback 保底壳」，与 WorkbenchAuxiliary 平行互补、互斥渲染（`if (auxiliary) WorkbenchTool else Shell` 统一写法）——统一规范落位＝shared/focus 内容件族＋规范文档，不动两 fallback 壳机制。
- 基线串行: 规范刀以 #2628 合并后 main 为基线（FocusAuxiliary 趋势承载 echarts→SVG 换装在途）。

### 转正映射建议（细化 AUQ 裁定）

2 issues：①**宽度小刀**（type/feature＋area/frontend，即刻可建可开工；写集 3 文件：SidebarLayout/WorkbenchAuxiliary/其测试，零在途冲突）；②**聚焦卡统一规范**（type/feature＋area/frontend，串行 #2628 合并后转正；初刀＝规范文档落位（DESIGN.md §7 新增聚焦卡行或 shared/focus 规范文档）＋九处差距清单，收编刀按体量拆 PR——续保 v2（图表承载收编+口径注记补齐）与车驾意（fallback 壳收编 HeatmapFocusPanelShell 或声明例外）两块最大）。

### 验收

- 宽度刀: `bun run typecheck`；`bun run test --run src/components/layout/__tests__/workbench-auxiliary.test.tsx src/features/operating-overview-v2/__tests__/`；`bun run governance`；E2E CI（26 路由矩阵走查）；人工验证＝1440 视口开聚焦面板拖调 320–560、网格 4↔2 列回流无破版。
- 规范刀: 按 issue 细化（九处六维对照清单逐项核对＋定向三域测试），实施规划时定。

## 转正记录（2026-10-27）

- 探索 #1 后 owner 下令「转正 020」，按 AUQ 既有裁定（Q3/Q4：宽度小刀先行独立 issue＋两刀均独立 issue）即刻建两 issue，无新增分叉：
  - **[#2639](https://github.com/alongor666/chexian-api/issues/2639)** ov2 辅助栏解除固定宽——回到全站标准可拖调（type/feature＋area/frontend；方案/行为披露/验收/失效条件随 issue 入库，锚 c611ac4de）——可即刻开工，与在途 #2628 零写集重叠。
  - **[#2640](https://github.com/alongor666/chexian-api/issues/2640)** 聚焦卡内容规范统一：九处对表收敛至 shared/focus 规范（type/feature＋area/frontend；串行 #2619 合并后，矩阵行号级正本以本卡探索 #1 为引用源）。
- 卡维持 scouted（building 待 owner「开工」令）；时点注记：转正时 PR #2628 已 CI 11 项全绿待 owner 手合（flint 7099911）。
