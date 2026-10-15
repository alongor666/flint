# 001 · KPI 看板默认起保口径

- 状态: discussed
- 目标仓: chexian-api（owner 原文明确）
- 类型: 优化 / 数据口径（AUQ 回填细化）
- 录入: 2026-10-15

## 原文

chexian-api仓，默认kpi看板默认使用起保口径

## 澄清（AUQ）

### 先探可定（依据）

- 目标仓 chexian-api，本地 `/Users/xuechenglong/workspace/chexian-api-dev`，锚 origin/main @ 61063382（2026-10-15）。
- 改动落点定为**前端**：后端 `/api/kpi` 的 dateField 默认 policy_date 是 MCP/PAT/CLI 公开契约，不动后端默认，只改前端 KPI 看板传参/默认值。
- KPI 看板（dashboard 域）不锁口径（无 lockedDateCriteria），跟随全局筛选 date_criteria；口径选择器（DateCriteriaSelector）已支持签单/起保互斥切换，能力已在，只缺默认值。

### 待 owner 裁定（AUQ）

- Q1 「默认起保」的生效范围？ 选项: A 仅 KPI 看板页默认起保（推荐）/ B 全站全局默认改起保 / C KPI 看板锁死起保不可切 → **owner 答: A（仅 KPI 看板页）**
- Q2 KPI 大盘后端恒签单 YTD 的六列墙卡（保费/增长率/达成率，A10 裁定）是否联动改口径？ 选项: A 维持签单不变（推荐，不动裁定）/ B 六列也改起保（需后端改动+修订 A10）→ **owner 答: A（六列维持签单）**

## 讨论

- 现状：全站口径基线单源 `buildResetFilters`（`src/shared/types/data.ts:283`，date_criteria: 'policy_date'），URL 同步 clearOnDefault、filter-caliber 计数基线（`src/features/filters/filter-caliber.ts:327`）等多处以此为基线。仅页级改默认可避开全站基线多点联动；若走全站改默认，改动面含 FilterContext 初载态（`src/shared/contexts/FilterContext.tsx:232`）、URL 省略键语义、筛选计数基线，影响所有跟随全局筛选的页面。
- 防撞车（2026-10-15）：开放 issue/PR 无直接撞车；相邻不撞：#1959（KPI 看板指标映射联动）、#1314（时间口径元规则·截止日）、#1321（机构层达成率归属）。

## 探索 #1（2026-10-15）

- 探查锚: chexian-api @ 61063382（origin/main，2026-10-15）
- 防撞车: 开放 issue/PR 查重无撞车；#1959/#1314/#1321 相邻不同面。
- 实施要点（按 Q1=A 仅看板页默认的最小面预估）:
  - dashboard 域初载时 date_criteria 缺省注入 'insurance_start_date'（页级覆盖，具体注入点探索转正时定：dashboard 入口 or FilterContext 页级基线钩子）；
  - 口径选择器与全局联动语义（CX-ADR-005）不动；
  - 后端不改动（Q2=A 前提下）；
  - 风险：页级默认与「恢复默认」按钮基线（buildResetFilters 全局基线签单）可能打架——重置后回到签单而非起保，需一并定义「KPI 页的默认」语义；URL 回放（f_ 参数）与页级默认的优先级需对齐既有规整逻辑。
- 转正映射建议: 单卡单 issue（type/feature + area/frontend），单 PR。
- 验收: KPI 看板初载（无 f_ 参数）各面板口径标注为起保；切换签单/起保正常；URL 回放/重置行为符合裁定；相关 filters 测试族（filter-caliber / query-param-equivalence 等）通过。

## 开工记录

（未开工）
