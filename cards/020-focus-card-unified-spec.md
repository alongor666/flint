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

## 开工记录（2026-10-27，进行中）

- **#2639 宽度小刀**：issue #2639 → worktree `chexian-api-wt-2639` 分支 `i/2639`（基线 8c5c8d614，锚复核写集零漂移，issues:claim 认领）。**闸① 两轮过**：R1 REQUEST_CHANGES（1×P1+1×P2+4×P3——P1=§4 漏 side 区间两条行为披露：ov2 加载自动展开异常列表（FocusAuxiliary.tsx:91-94 sideLayout 门）＋转主区不收起（:95-98）；P2=E2E spec 26 1280×1024 帧宽 1096 落新区间初始态变化预告；P3=:219 JSDoc 漏删/「三套」实为六套 23 处/宽度偏好全站共享键/矮窗 full 新态＋问句表）→ v2 全处置 → R2 **APPROVE**（R2-1 非阻塞 P3＝S3 窄窗示例须高≥650，已修入）。规划正本 `docs/plans/2026-10-27-ov2-aux-width-plan.md` v2+R2-1 已 commit 推送 @ i/2639 `31a36bded`。
- **暂停点**（2026-10-27 owner 重启电脑）：闸①过、S1-S3 实施未开始（编辑区域已勘定：SidebarLayout:515-516／WorkbenchAuxiliary :218-220/:310-311/:317/:396／test :44-70）。续接：直接按规划 S1-S3 实施→§5 验证链→PR（Closes #2639，正文含 §4 行为变化全表＋ui-user-perspective 问句表）→独立评审→闸②。
- **#2640 规范统一**：串行 #2628 owner 手合后（重启后 owner 侧动作）。

- **#2639 续接完成（2026-10-27 重启后）**：S1-S3 实施（dc415d674，rebase 至含 #2628 的 c21171398，rebase 后定向 270 用例复跑全绿）→ **PR [#2642](https://github.com/alongor666/chexian-api/pull/2642)**（走查-可查标签）。独立评审 **APPROVE**（P0-P2 清零；2×P3 正文文字级已修：行为表补 316→360 行＋自动展开门行号 :90-93）；闸②主会话亲评全量 diff 通过（+78/-21 纯减法，四处逻辑等价逐行核）。**CI 九项全绿＋MERGEABLE——可合并就绪，停手待 owner 手合**。PR 正文含行为变化全表七行＋问句表三答＋人工走查四判断点。
- **#2640 解封启动（同日）**：#2628 已合（03:25Z）→ 基线复核过（九处分级 6/1/1 不变，趋势承载已收敛 shared/focus/FocusTrendChart 217 行双侧消费，续保 :122 手写 init/车驾意自造 overlay 收编目标完好）；worktree `chexian-api-wt-2640` 分支 `i/2640` 认领；初刀（零代码：开发文档/聚焦卡规范与资产清单＋DESIGN.md §7 行＋shared INDEX 指针）规划落盘，闸①评审在途。

- **#2640 初刀完成（同日）**：基线复核过（九处分级 6/1/1 不变；补记勘误：行业对标/报价/续保三处均有页内嵌重开断言，无锁面实为 3/9——独立评审 P1 揪出我矩阵漏检，已如实改）→ 规划两轮闸①（R1 1×P1「横条仅限单值分布」无锚新造降待裁＋3×P2＋4×P3 → R2 APPROVE）→ 实施三产物（`开发文档/聚焦卡规范与资产清单.md` 57 行：六维规范全锚既有裁定＋九处矩阵＋收编路线刀 B/C/D＋能力边界声明；DESIGN.md §7 归宿③行；shared/INDEX 指针＋doc-entries 登记）→ PR **[#2646](https://github.com/alongor666/chexian-api/pull/2646)**（Refs，多刀制）→ 独立评审 R1 REQUEST_CHANGES（P1 重开锁覆盖面失实）→ 返修 12fcd60d8 → R2 APPROVE → 复审 P3 两处修（d292ac00e）→ 闸②亲评过 → **CI 全绿（docs-only 路径）＋MERGEABLE，待 owner 手合**。
- **双 PR 停手点（2026-10-27）**：#2642（#2639 宽度刀）与 #2646（#2640 初刀）均闸①②过＋CI 绿＋可合并就绪待 owner。#2640 余量＝收编刀 B（续保图表收编）/C（车驾意，AUQ 待裁 fallback 处置）/D（六处口径补缺），以两 PR 合并后 main 为基线择机。
- **#2639 shipped（2026-10-27 owner 05:42Z 合并 PR #2642，issue 自动关闭；worktree 已清）**。
- **#2640 初刀 shipped（同日 owner 06:16Z 合并 PR #2646；规范文档三产物入 main bf4f3ae08）**。
- **刀 C 裁定（2026-10-27 owner AUQ）**：车驾意 fallback **收编 HeatmapFocusPanelShell**（消 0 共享件状态，同刀补口径注记与重开锁）——规范清单 §五待裁项之三落定，实施时同步勾销。
- **刀 B 启动（同日）**：续保 v2 图表收编管线开工，基线 main bf4f3ae08。

- **刀 B 完成（2026-10-27）**：闸①一轮 REQUEST_CHANGES（1×P1 null 态满环语义回归＋3×P2＋7×P3 全处置后授权直接实施）→ 实施四件（RateDonut 纯 SVG/口径 ⓘ/零白名单机检闸红转绿自证/清单行状态）→ PR **[#2659](https://github.com/alongor666/chexian-api/pull/2659)** → 独立评审 APPROVE_WITH_CHANGES（P2 清单头注自相矛盾＋4×P3 全随 PR 修）→ 闸②亲评过。**插曲（如实）**：首跑 CI E2E Shard1 红——spec 25 三段断言盯 `_echarts_instance_`（规划漏探 E2E 面），断言面迁移 `renewal-rate-donut` testid 三段原意保全后复跑**全绿＋MERGEABLE，待 owner 手合**。
- **刀 C 已裁定待刀 B 合并后开**（收编 HeatmapFocusPanelShell）；刀 D 随后（含 InfoHint/donut 同构件上提盘点——评审 F5 登记）。

- **刀 B shipped（2026-10-27 owner 07:41Z 合并 PR #2659）**；spec 25 E2E 断言面迁移随刀入库。
- **刀 C 完成（同日）**：闸①一轮 REQUEST_CHANGES（1×P1 InfoHint 落位守 shared/focus 硬约束＋5×P2＋4×P3 全处置）→ 实施四件（fallback 换 HeatmapFocusPanelShell 镜像业绩先例/InfoHint 上提 shared/focus 消三处复刻＋车驾意新增消费/重开锁页内嵌/契约测试迁移）→ PR **[#2660](https://github.com/alongor666/chexian-api/pull/2660)**（10 文件 +195/−144）→ 独立评审 APPROVE_WITH_CHANGES（P2＝规划漏入库已补 commit＋P3×5 处置）→ 闸②亲评过 → **CI 全绿＋MERGEABLE，待 owner 手合**。#958 史实辨析入 PR 正文（当年拒的是 shared/ui/Drawer 模态壳，族壳即 #958 正解）；fallback 生产不可达披露（specialty 页恒在宿主内）。
- **余量＝刀 D**（待 C 合并后）：#3/#4 口径注记补齐、重开锁测试名对账＋shared/focus 消费关系对账两机检、SVG donut 两处上提盘点。

- **刀 D 收官（2026-10-27）**：闸①两轮（R1 15 findings——P1×3 全是我 ⓘ 草稿的口径事实错：系数均值实为商业险加权/车均应为件均÷保单号件数/同比基期；R2 4 findings 收窄）→ 实施五件（#3/#4 口径 ⓘ 逐句对 SSOT 核字/业绩热力页内嵌重开锁/两对账机检 spec-inventory-contract/清单收官含 donut 不上提结论/kpiFocus 豁免换锚显式 open）→ PR **[#2665](https://github.com/alongor666/chexian-api/pull/2665)**（Closes #2640，6 文件 +197/−7）→ 独立评审 **APPROVE**（零 P0-P2；ⓘ 文案逐句对源终验全成立；「Closes」终判＝验收三条全着落、残余四项全带论证）→ F1 一行修（消费方计数六）→ **CI 全绿＋MERGEABLE，待 owner 手合即 #2640 自动关闭、020 全程闭环**。
- **变红自证插曲（如实）**：前两次变异实验不红——多现文件单点变异不改变 includes 判定，换单现锚文件（QuoteFocusCard「重开」唯一出现）才红→恢复绿，两轮教训记 PR 正文。
- **020 终态**：#2639 shipped（#2642）；#2640 四刀＝初刀 A（#2646）/B（#2659）/C（#2660）已合＋D（#2665）就绪。九处聚焦卡全部壳内规范化（含机制壳 2），口径 ⓘ 全域覆盖，重开锁专项 2＋页内嵌 5＋豁免 1（kpiFocus 显式 open 兜底），机检 3 件（echarts.init 闸/重开锁锚对账/无孤儿消费），SSOT 清单入仓。owner 走查判断点余量＝各刀 PR 正文所列（本机无数据未跑实页）。
