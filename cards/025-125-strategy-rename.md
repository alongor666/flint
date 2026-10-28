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

- 「1+2+5 战略」→「1+2+5 客群」；「专项战略」→「1+2+5 客群」（按语境）；「战略组/战略段」→「客群组/客群段」；「战略1/2/5」→「客群1/2/5」；「战略驾驶舱」→「1+2+5 客群」（无驾驶舱后缀，owner 裁定）；「战略板块」→「1+2+5 客群」（日报标题，owner 裁定）。
- 注释内历史裁定引语（如 `strategy-segments.ts:20`「2026-08-08 用户裁定：不连续档是战略本意」）保留原词＋括注更名，不硬改语义（Q3 审计留存精神延伸到引语）。

### 新分叉（owner 2026-10-28 已裁定，悬置消除）

1. 「战略驾驶舱」页面/导航名（135 处，含 routeRegistry 导航名）→ **「1+2+5 客群」**——不带「驾驶舱」后缀（owner 原话：「1+2+5 客群驾驶舱」不要驾驶舱）。
2. 日报标题（`StrategySection.tsx:47`「战略板块（1+2+5）」及 aria-label）→ **「1+2+5 客群」**——名称本身含 1+2+5，原括号后缀去掉。

### 转正映射建议

1 个 issue（附逐变体映射表作 SSOT，分叉已全部裁定、无需 needs:owner）→ 3 个 PR：PR-1 服务端+机检+MCP 元数据+server 测试；PR-2 前端 UI+共享 config+前端测试；PR-3 活文档。三 PR 只共享映射表、无代码依赖，可并行。

### 验收

- 合并后活面路径集 `git grep -n '战略' origin/main -- <活面路径>` 零命中（固定串 + 变体正则两法复核，历史区与路径引用白名单除外）。
- `bun run typecheck`；`bun run test --run`（点名受影响测试文件）；`bun run governance`；机检脚本自跑（kpi-cross-route-consistency 输出文案已同步）。

## 评审 #1（2026-10-28 · DSH strong 档 glm-5.3 冒烟自报 · 规划评审卡骨架 · 只读探针对象库）

**verdict: REQUEST_CHANGES → 7 findings（2×P0/2×P1/2×P2/1×P3）已全部处置，回报名录 /tmp/review-025.out。调度者抽验 5 处关键证据全部坐实（heatmap 标签表同表并列、chart-ledger 客群=customer_category、非战略类别、战略外业务贡献四处双端锁、.gitignore:405 规则行、server 非测试 48）。以下口径**覆盖探索 #1 的同项数字**，转正以本段为准。**

### owner 二次裁定（评审后 AUQ，2026-10-28）

- 派生词带前缀：战略组/战略段/战略细分/战略维度等 → **1+2+5 客群组/段/细分/维度**。
- 否定/外延语境不用「非 1+2+5 客群」也不用机械替换：非战略 → **非目标客群**；战略外贡献（占比句）→ **非目标客群贡献／非目标客群占比**；无战略 → 无目标客群（呼应主定性「细分目标客群的简称」）。
- 主名「1+2+5 客群」与段名芯片「客群1/2/5」维持原裁定。

### 映射表 v2（F1/F2/F5 处置；完整 21 变体表随 issue 产出作 SSOT，评审 F1 表为底）

| 类 | 例 | 产物 |
|---|---|---|
| 主名 | 1+2+5 战略／专项战略／战略驾驶舱／战略板块 | 1+2+5 客群 |
| 段芯片 | 战略1/2/5 | 客群1/2/5 |
| 派生词（带前缀） | 战略组/段/细分/维度/范围/两维/口径/面板/组芯/步骤 | 1+2+5 客群× |
| 否定/外延 | 非战略(22)/战略外(16)/无战略(3) | 非目标客群／非目标客群贡献(占比)／无目标客群 |
| 技术语境 | 战略过滤/战略页窗 | 1+2+5 客群过滤／1+2+5 客群页窗 |
| 引语（不动+括注） | 战略本意 | strategy-segments.ts:20、**车险数据业务规则字典.md:2168**、**metric-registry/categories/growth.ts:63/157**（「已裁定保留为公式协议」句）均比照处理 |
| 补条目 | 战略概览 | 1+2+5 客群概览（strategySections.ts:37 与 chart-atlas.ts:125 两处平行字面量**必须同 commit**，仓内自认无测试拦得住） |

### 面归类口径修正（F3/F7 勘误，覆盖探索 #1）

- 四桶：server/src 非测试 **48**（原 51 系排除模式漏一层 `__tests__`）；src 非测试 **68**；server `__tests__` **30**；src `**/__tests__` **30**（原「src/tests 37」路径不存在）；根 `tests/` **10**（原口径完全遗漏）——测试联动合计 **70**。
- 根 tests/ 逐文件归属：`heatmap-strategy-dimension-labels.test.ts`（镜像锁，:40/:59 断言段中文镜像↔注册表、组芯片↔label）与 `strategy-cockpit-segment-chips.test.ts` **必须与被锁字面量同 PR**；e2e 03/21 为历史/语境注释**不随改**；pipelines/test_coverage_combination 归 PR-1。
- 双端锁句「战略外业务贡献 18.7%」四处（`daily-report-digest.ts:273`+其 test:142 ／ 前端 `derive.ts:392`+其 test:289）分属 PR-1/PR-2——**同句定名、两 PR 同批交付或并一 PR**。
- docs/plans 含「战略」实测 17 非 22（历史区合计 36≈38 结论不变）。

### 白名单补全（F4/F6）

- 不改白名单增补：`server/data/updates/` 2 文件（git-log 生成的 PR title 快照，改则篡改历史且会被再生成覆盖）；`.gitignore:405` ignore 规则行（:399 注释可加更名注）。
- 漏网补改：`数据管理/pipelines/coverage_combination.py:20`（活管道注释）归 PR-1。
- 活面路径集 = 全仓 − 历史区 − server/data/updates − .gitignore 规则行，**显式清单随 issue 产出**（本卡不给枚举，防漂）。
- F6：`segment-dictionary.json`、`数据管理/config/metric-labels.json` 为数据资产，实施前确认不触发该仓数据校验闸。

### 验收增补（F2/F4）

- 原验收保持，另加两条：①「客群」增量复核——改后 `git grep -c '客群'` 相对基线 406 处的**新增命中须全部出自映射表 v2 产物**（防语义污染既有 customer_category 语境）；②受影响测试文件点名清单随 issue 产出。

### 风险排除与前提备注（评审证伪/确认）

- 假设 d 证伪：chart-atlas/metric-atlas 测试无「战略」断言，无传导。
- dimension-list-ratchet baseline 条目为 `路径::声明名[英文键]` 形态、不含中文标签——改标签不触发「改名须登记」，**前提是不动 strategy-* 文件名**（owner 已裁定 ✓，issue 内带一句防实施者误动）。
