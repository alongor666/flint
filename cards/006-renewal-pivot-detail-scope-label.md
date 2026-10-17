# 006 · 续保透视明细带标题未随机构快选

- 状态: shipped
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

- 2026-10-17：owner 下令「开工修复，举一反三，一次性解决」。issue #2161 → PR #2166（i/2161）。
  - 修复面（2 文件 +37/−5）：scopeLabel 改 orgSel 感知（快选>行点选>全局筛选）；面包屑左段恒为外层范围（**现场走查活捉 #2093 起旧疾**：点行后曾「太原一部 ✕ › 太原一部」左右同名）；快选变更清行点选；回归测试（SX 默认临汾三标题 + orgNames 取数断言 + 清空回退）。
  - 评审：轻量对抗评审 P0/P1 无、P2×3 全当场吸收（报告落 PR 评论）；本机真数据浏览器自验五组 DOM 断言全过（②档走查-可查：AI自验+断言行+走查行）。
  - CI 全绿后 owner 于 2026-10-18 00:59（本地）squash 合并 → main @ 4209236f；issue #2161 随关；生产部署 success（18:05Z）。
  - 花絮：合并后分支名 i/2161 被并行会话复用于他链，本地恢复 fix commit 时一度误判丢失——reflog 找回后核实 patch 已在 main（rebase 静默跳过=已应用），虚惊。worktree 已清。

