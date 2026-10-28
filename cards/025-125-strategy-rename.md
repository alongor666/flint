# 025 · 「1+2+5 战略」更名治理（实为细分目标客群简称，防误导）

- 状态: scouted
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

## 探索 #1（2026-10-28）

- 探查锚: chexian-api @ `b131d495b`（origin/main，全程只读 `git grep/show origin/main`，未动目标仓任何东西）
- 防撞车:【事实·两法同证】开放 PR = 0（`gh pr list --state open --json number --jq 'length'` → 0，与列表输出空一致）；开放 issue grep「战略|客群|更名|改名|rename|1+2+5」唯一命中 #2460（计划域 CSV-IN 边界，主题无关）——无撞车。

### 全量量级【事实，`git grep -c` 汇总】

- 「战略」242 文件 / 1048 处；「1+2+5」126 文件 / 286 处。
- 变体分布：战略组 187 处/79 文件｜战略驾驶舱 135/71｜战略段 122/60｜「1+2+5 战略」90/54｜战略1 65/30｜战略5 49/17｜专项战略 37/25｜战略2 24/12（变体间有重叠）。

### 面归类

**改（人读面，≈208 文件）**【事实，目录级 grep 归类】：

1. 服务端非测试 51 文件：注释、参数 description（`server/src/config/query-routes-metadata.ts`——MCP/AI 直读元数据）、指标注册表公式文本（`metric-registry/categories/structure.ts`）、agent prompt（`server/src/agent/prompts/heatmap-consult/prompt.md`）、SQL 注释。
2. 前端+共享层非测试 ≈71 文件：UI 芯片与标题（`strategyCockpitTabs.ts:109-111` label '战略1/2/5'）、日报标题（`StrategySection.tsx:47`「战略板块（1+2+5）」）、`metric-labels.ts`、`routeRegistry.ts`、`chart-atlas`、guide 文案、heatmap 维度标签。
3. 测试联动 ≈67 文件（server `__tests__` 30 + src/tests 37）：断言中文文案随源改，否则 CI 红。
4. 机检脚本 4：`kpi-cross-route-consistency.mjs:151` scope 字符串属审计输出文案须改，`api-wire-conservation.mjs`/`rls-route-coverage.mjs`/`dimension-list-ratchet.test.mjs` 为注释。
5. 活文档 ≈15：README.md、DESIGN.md、server/AGENTS.md、src/features/INDEX.md、src/widgets 两 INDEX、dashboard README、`开发文档/00_index/CODE_INDEX.md`、`开发文档/指标字典.md`、`开发文档/经营指标诊断体系.md`、`开发文档/筛选控件资产清单.md`、`开发文档/架构设计/盈利能力分析架构设计.md`、`数据管理/knowledge/rules/车险数据业务规则字典.md`、`segment-dictionary.json`、`PARQUET_SCHEMA_KNOWLEDGE.md`、`数据管理/config/metric-labels.json`。

**不改**【AUQ Q2/Q3 裁定落地】：

- API 契约标识符：`strategyGroup`/`strategySegment` 参数名与 enum、路由 `/strategy`、指标 id `strategy_1_2_5_coverage_pct`、strategy-* 文件名、`StrategyCockpitPage` 等组件名。
- 历史区 ≈38 文件：docs/plans 22、docs/decisions 6、docs/audits 1、design-handoff 3、开发文档 reviews 3 + plans 2 + 架构设计 2026-08-08/08-09 两篇设计文档、数据管理 validation 1、analysis-brief inbox 1。
- 历史文档路径引用（指针）：如 `strategy-segments.ts:4` 引「`开发文档/架构设计/2026-08-08-专项战略1+2+5标签页.md`」——历史文件不改名，路径引用保持原样否则指针断裂。

### 措辞映射原则【断言，实施时逐变体表先行入 issue】

- 「1+2+5 战略」→「1+2+5 客群」；「专项战略」→「1+2+5 客群」（按语境）；「战略组/战略段」→「客群组/客群段」；「战略1/2/5」→「客群1/2/5」。
- 注释内历史裁定引语（如 `strategy-segments.ts:20`「2026-08-08 用户裁定：不连续档是战略本意」）保留原词＋括注更名，不硬改语义（Q3 审计留存精神延伸到引语）。

### 新分叉（转正时定，未再打扰 owner）

1. 「战略驾驶舱」页面/导航名（135 处，routeRegistry 导航名）是否随改（推导应改「1+2+5 客群驾驶舱」，但页面名影响 owner 日常使用习惯）——建议 issue 标 `needs:owner` 单项拍板。
2. 日报标题「战略板块（1+2+5）」同理随分叉 1 联动。

### 转正映射建议

1 个 issue（附逐变体映射表作 SSOT ＋ 分叉① needs:owner 标注）→ 3 个 PR：PR-1 服务端+机检+MCP 元数据+server 测试；PR-2 前端 UI+共享 config+前端测试；PR-3 活文档。三 PR 只共享映射表、无代码依赖，可并行。

### 验收

- 合并后活面路径集 `git grep -n '战略' origin/main -- <活面路径>` 零命中（固定串 + 变体正则两法复核，历史区与路径引用白名单除外）。
- `bun run typecheck`；`bun run test --run`（点名受影响测试文件）；`bun run governance`；机检脚本自跑（kpi-cross-route-consistency 输出文案已同步）。
