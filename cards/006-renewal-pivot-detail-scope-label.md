# 006 · 续保透视明细带标题未随机构快选

- 状态: clarifying
- 目标仓: chexian-api
- 类型: 功能（小修）
- 录入: 2026-10-17

## 原文

（#2089 走查遗留 P2，代理走查发现）

## 澄清（AUQ）

### 先探可定（依据）

- 走查实证（2026-10-17，报告 PR① 评论 #6231225534）：机构快选=大同时，明细带标题仍显示「业务员明细 · 山西分公司」，数据实际已是 orgNames=大同——标题与数据范围不符。
- 根因：`src/features/renewal-tracker/v2/RenewalPivotView.tsx` 的 `scopeLabel = effScope ?? scope.analysisLabel`——analysisLabel 由 `useRenewalPivotScope` 的全局筛选 orgNames 派生，不感知 orgOverride（机构快选只在 useRenewalPivot/useRenewalPivotDetail 内叠加）。
- KPI 带不受影响（声明权限范围，设计如此）；趋势带标题同用 scopeLabel，同误。

### 待答

- 修法无真分叉（orgOverride 非空时 scopeLabel 取「{orgOverride}（机构快选）」），非交互假设清单：一行改动 + 走查快照更新，随时可开工。

## 讨论

- 与 005 同域收尾件，建议随手并入任一续保后续 PR 或独立小 PR。

## 探索 #1

（未探索——改动面一行，无需 PR 深度方案）

## 开工记录

（未开工）
