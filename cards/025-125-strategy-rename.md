# 025 · 「1+2+5 战略」更名治理（实为细分目标客群简称，防误导）

- 状态: discussed
- 目标仓: chexian-api（依据：1+2+5 段口径 SSOT `server/src/config/strategy-segments.ts`、`/strategy` 路由族与战略驾驶舱 UI 均在该仓，MCP 路由描述多处引用）
- 类型: 优化（命名治理/防误导）
- 录入: 2026-10-28

## 原文

> 1+2+5 战略统一改名为1+2+5，其实是细分目标客群的简称=细分目标客户类别的简称，文档、代码和注释都要改掉，防止误导。

## 澄清（AUQ）

- Q1 更名后的名称: A 「1+2+5 客群」（名词点明客群属性）/ B 只删「战略」二字→裸「1+2+5」，简称含义在文档与注释标注 / C 其他写法 → **owner 答: 「1+2+5 客群」**
- Q2 「代码改掉」的边界: A 人读面全改（UI 文案、代码注释、文档、日报/推送文案、参数 description），API 契约标识符（参数 `strategyGroup`/`strategySegment`、路由 `/strategy`、指标 id `strategy_1_2_5_coverage_pct`、文件名 strategy-*）不动 / B 标识符同步全改（接受前后端/外部消费方契约破坏与迁移成本）/ C 只改文档与 UI 文案，代码注释不动 → **owner 答: A 人读面全改，标识符不动**
- Q3 历史裁定/ADR/旧规划文档中的旧称: A 原文不改写（审计留存），仅活文档与代码全改，历史处加更名注 / B 历史记录也统一改写 → **owner 答: A 历史不改写，加更名注**

### 先探已定字段与依据（2026-10-28）

- 目标仓 chexian-api、类型=优化（依据见卡头）。
- 改动面量级：gh code search `1+2+5` 全仓命中 123 文件——前端（StrategyCockpitPage / strategyCockpitTabs / SpecialtyPage / 日报 StrategySection / guide 文案 / chart-atlas / metric-labels）、服务端（`routes/query/strategy.ts`、`sql/strategy/`、`strategy-group-inject`、`kpi.ts`、`dashboard-health-check`、`query-routes-metadata`、agent prompts）、测试 10+、文档（README / AGENTS.md / 指标字典 / 开发文档 / docs/plans+decisions / design-handoff 原型 / 数据管理 config）。
- 若 Q2 选 B 才动的标识符清单：`/strategy` 路由族、`strategyGroup`/`strategySegment` 参数（多路由透传）、strategy-* 文件名、指标 id `strategy_1_2_5_coverage_pct`、测试文件名。
- 防重：与 011（车型筛选改客群，已 shipped）主题同族（命名防误导）但对象不同，不并卡；REGISTRY 无其他语义同源卡。

## 讨论

- 正式名裁定为「**1+2+5 客群**」，全称标注＝细分目标客群（＝细分目标客户类别）的简称；目的＝去「战略」误导。
- 范围三线：人读面全改（UI 文案/代码注释/活文档/日报推送文案/API 参数 description）；API 契约标识符零改动；历史裁定/ADR/旧规划不改写、仅在活文档对照处加更名注。
- 初评估级：`1+2+5` 字面已命中 123 文件，但其中测试/标识符层大量不动，实际人读面预计显著收敛；真正的盘点对象是「战略」系措辞（战略驾驶舱/专项战略/战略段/战略1·2·5段）在人读面的分布，需 scout 阶段在 strategy 相关文件内 grep 精确圈定。
- 转正映射建议（待 scout 细化）：SSOT `strategy-segments.ts` 头注释 → 前端 UI 文案与图集/标签配置 → 活文档/指标字典 → 日报/推送文案，可拆 2~4 个 PR。
- 与 011（车型筛选改客群）同族先例：命名治理类改动走全仓 sweep + 机检（测试内断言文案同步改），可复用其三 PR 拆法。
