# 020 · 全站聚焦卡统一规范与 ov2 辅助栏宽度可调

- 状态: discussed（AUQ 四问已裁，2026-10-27 全采推荐）
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
