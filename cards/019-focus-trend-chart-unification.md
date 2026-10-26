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

（未开始；owner 确认后按 docs/scout.md 执行）

## 开工记录

（无）
