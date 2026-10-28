# 021 · 导航命名与经营体检状态、趋势统一

- 状态: scouted（系数二态、上月末趋势、增速红线 R1-R4 均已裁（Q2-Q7）；方案就绪未实施，上限数值留待后裁登注册表）
- 目标仓: chexian-api（先探已定：repos.md 路由及现有导航、经营体检术语）
- 类型: 优化（导航信息架构与展示口径统一）
- 录入: 2026-10-28
- 关联: [016](016-updates-entry-demotion.md)（同批更新入口诉求并入既有卡）

## 原文

> 导航栏名称变化：营业收入变为“承保规模”，经营成本变成“承保效益”，经营体检中只保留“承保规模”“承保效益”“监管红线”，系数监控作为“监管红线”的一部分。经营体检统一用全仓的”状态“和“趋势”，状态统一都是“优秀”。
>
> 健康预警的危险趋势分为两种：优化和恶化。
> 1. 好指标：上升叫优化，下降叫恶化。
> 2. 成本类指标（如赔付率、费用率）：上升叫恶化，下降叫优化。

## 澄清（AUQ）

- Q1 「状态统一都是优秀」：A（推荐）统一最佳档名称为「优秀」，其余状态按全仓既有阈值判断 / B 所有指标的状态均固定显示「优秀」 → **owner 自定义答（原话）：四种状态优秀健康预警危险**。
- 据此裁定：经营体检状态统一为「优秀、健康、预警、危险」四档，复用全仓既有事实源；不固定所有指标显示优秀，不另设阈值。
- 原文已明确：导航「营业收入」改为「承保规模」，「经营成本」改为「承保效益」；经营体检只保留「承保规模」「承保效益」「监管红线」，其中系数监控纳入监管红线。
- 原文已明确：健康预警趋势使用「优化」「恶化」，按指标好坏方向判断；具体指标归类与零变化、缺数据、无可比基期处理在目标仓核对既有规则，不能凭数值上升一律判断优化。
- 2026-10-28 owner 已授权按澄清结果登记、提交并推送需求文档；本卡尚无目标仓开工指令。
- Q4 监管红线增速数据源 → **owner 裁定（2026-10-28 AUQ）：A 体检同源 PolicyFact（推荐采纳）**。年度=KPI 现成字段（年初至今 vs 上年同期）；月度在 /health-check 路由层补一路当月+上年同月聚合；与体检同权限/同省/同业务筛选。
- Q5 增速上限数值 → **owner 裁定：先只展示数值不亮灯**（沿「注册表无阈值，不亮灯」既有表达）；上限数值/档位/监管依据文号后裁，届时登注册表再亮灯。
- Q6 与承保规模组同屏反向灯 → **owner 裁定：保留双展示（推荐采纳）**。承保规模组「保费增长率」沿现阈值（增速低亮危险灯），红线组增速先只展示数值；注记标明两处同源。
- Q7 月度签单增速窗口 → **owner 裁定（自定义）：月内同日进度折算**——当月 1 日至最新数据日 vs 上年同月 1 日至同日进度窗（两端可比）；非整月口径、非滞后月。
- Q2 系数监控是否四档 → **owner 答（2026-10-28）：保留补足二态事实，四档仅用于已有定义的指标（推荐）**。监管红线继续呈现「已补足／未补足」及未配置/缺数据事实，不新增系数四档阈值。
- Q3 趋势比较基准 → **owner 自定义答（2026-10-28，原话）：趋势，与上月末比**。此答取代探索 #1 的沿各指标明细基准推荐方案；体检趋势统一标注「较上月末」，按指标方向显示优化/恶化。

## 讨论

- 防重：已读 REGISTRY.md 全表；016 负责同批更新入口，014 涉导航手风琴与默认落点，002 涉全站口径。三者均不重复承载本卡的体检三部分结构与四档状态诉求，故新增本卡并保留关联指针。
- 本卡记录 owner 需求与裁定；指标归属、状态阈值和趋势事实源以目标仓为准，实施时复用其注册表，不在 flint 另建业务规则表。
- 范围按原文限定：体检内系数监控并入监管红线；未据此推定删除独立系数监控页面，也不变更默认落地路由。
- 全仓统一要求涉及现有页面术语与事实源的一致性；具体消费点、联动说明与改动面由后续探索核实。
- 关联：[014](014-sidebar-accordion-default-landing-v2.md)（导航及首页落点历史）、[002](002-site-wide-caliber-map-and-principles.md)（全站口径讨论）。

## 后续探索验收要点（尚未执行）

1. 两个导航名称统一为「承保规模」「承保效益」，链接仍可用。
2. 经营体检仅保留三个部分；系数监控在监管红线内可见并可访问既有明细。
3. 状态显示「优秀、健康、预警、危险」四档，来源与全仓一致，分档随实际数据与既有阈值变化。
4. 好指标上升显示优化、下降显示恶化；成本类指标上升显示恶化、下降显示优化，覆盖赔付率与费用率。
5. 核实零变化、缺数据和无可比基期的既有表达；不将这些情况硬判为优化或恶化。
6. 检查体检与对应明细对同一数据的状态、趋势结论一致。

## 开工记录

（待「开工 021」）

## 探索 #1（2026-10-28）

> 本节保留初次探索及当时待裁结论；当前裁定见澄清 Q2/Q3，方案与就绪结论以探索 #2 为准。

### 探查边界与结论

- 探查锚：`chexian-api @ b8f2de75100f21688d6053839a388a5090ec5f6c`（2026-10-28）。目标仓只读，以 `git show <SHA>:<path>` / `git grep <SHA>` 取证；未使用本地旧检出代替主干，未写业务代码、建目标 issue/PR、访问生产 API。
- 已读目标根与 `src/AGENTS.md`、`server/AGENTS.md`、`数据管理/AGENTS.md`、知识协议/相关索引及 `.claude/workflow/pr-development-protocol.md` §1.1。实施纪律、预算与合并要求引用这些当前正本；本卡不另造行数或 PR 体量契约，最终改动量未定。
- **NOT READY FOR IMPLEMENTATION**：导航改名、体检三部分重组、已裁阈值指标的状态映射已可方案化；系数四档适用范围与趋势比较基期仍有真实业务分叉（见末段），卡头保留 `discussed`。本结论不阻止已授权探索，也不等于目标仓开工授权。
- 方法：以下均为【事实·静态核实】或【断言·代码推理】，无浏览器/API/业务实测结果；测试、生产验收均 **NOT RUN**。

### 当前 producer → consumer 与可复用事实源

1. 【事实·静态核实】导航名正本在 `src/shared/config/routeRegistry.ts:86-104`：`growth` 显示营业收入、`cost` 显示经营成本；独立系数页仍为 `routeRegistry.ts:351` 的 `/coefficient-monitor`。两处域 label 改名即可带动注册表消费方，不改域 id、路由、权限或默认落点；不做全仓业务语句的同义词替换。
2. 【事实·静态核实】`server/src/routes/query/kpi.ts:215-293` 的 `/health-check` 复用 KPI SQL、筛选/行级权限、计划适用门控，再加战略覆盖率；调用 `server/src/services/dashboard-health-check.ts:161-205`。服务输出 growth/cost/customer/overview/specialty，`src/features/dashboard/hooks/useHealthCheck.ts:33-54` 建指标裁定索引，既供体检也供 KPI 卡消费。前端 `healthCheckPresentation.ts:49-56` 另加经营闭环行，`HealthCheckSection.tsx:164-224` 逐行渲染。
3. 【事实·静态核实】首页 `src/features/pages/LandingHomePage.tsx:48-57` 顺序装配更新摘要、独立系数摘要、体检；系数尚在体检外。`CoefficientMonitorSummaryCard.tsx:49-58` 以现有同源接口取当月默认客群，`:20-46` 汇总全省/同城的最终缺口与当日已补，`:68-82` 显示补足状态并链接独立明细。现有失败是零足迹降级，移入监管红线时必须显式呈现该部分加载/失败/无可判数据，不能整部分消失。
4. 【事实·静态核实】体检 `HealthCheckMetric` 只有当前值、阈值、灯、注记，**无比较值或趋势字段**（服务 `dashboard-health-check.ts:30-50`、客户端 `src/shared/api/types.ts:234-267` 两侧同证）；不能把静态灯名“恶化”当成数值趋势。灯序与空值规则由 `dashboard-health-check.ts:92-121,135-150` 提供，按省取档，阈值相等归较好档。

### 指标归属、状态与趋势核对

- 【事实·静态核实】服务 `dashboard-health-check.ts:171-197` 的现有归组可直接保留：承保规模消费原 growth 的计划达成率、保费增长率、家自车占比；承保效益消费原 cost 的变动成本率、满期赔付率、费用率。只保留这两组并新增监管红线呈现位，客户经营/经营总览/专项/闭环摘要不再在体检列行；不据此删除它们的独立页面或服务字段。尤其 `/health-check` 的其他字段仍有裁定索引消费方，不能只为首页裁剪就破坏共用响应。
- 【断言·代码推理】监管红线首版只纳现有系数监控摘要，属于本卡原文可支持的最小组成；没有已确认的其他监管指标清单，不把成本危险档、战略覆盖率等自行命名为监管标准。正文精确短语搜索（src/server）与导航域/体检归组全量枚举交叉核对，未见现成同名体检域；研究文档出现的“监管红线”不是该 UI 组的定义。
- 【事实·静态核实】体检灯是达标/接近/偏离/恶化（`dashboard-health-check.ts:17,92-103`），展示映射在 `healthCheckPresentation.ts:12-35,77-115`。全仓已有同档数值对应优秀/健康/预警/危险的共享实现：`src/shared/config/strategyCockpitThresholds.ts:90-155`；分档阈值仍读后端指标注册表，不新增业务注册表。保费增长/家自车占比注册表 tooltip 第三档还写“异常”（`categories/growth.ts:105-125`、`categories/structure.ts:38-66`），只是词汇存量，不能据此多造一个状态或全仓改掉经营闭环 severity。
- 【事实·静态核实】续保占比故意不套续保率阈值、战略覆盖率有角色范围门控、车驾意达成率无注册表阈值（`dashboard-health-check.ts:180-197`）；空值/范围不适用/无阈值保持未裁定、仅给数与原因，不能强分四档。它们本轮不在保留的两组中，也不扩展其业务规则。
- 【事实·静态核实】系数运行态阈值是省×范围×能源×客群的单一下限，权威在 store 配置视图（`server/src/config/coefficient-thresholds.ts:2-19,28-41`）。不是“系数全仓没有阈值”：`categories/cost.ts:1313-1325` 已有自主系数 P50 **同比差值**档位，但输入不是监控表的加权均值/最终缺口；`:1393-1423,1461-1472` 的山西燃油营业货车均值有登记锚，但缓冲档明确写待 owner 定档，且不能外推默认家自车/两能源/两省。已在线核对 [#2325](https://github.com/alongor666/chexian-api/issues/2325) 为 CLOSED，正文与空评论集只支持既定下限/分类变更，不支持把缓冲档视为已裁定；不将该工单整体误报为仍待裁。
- 【事实·静态核实】可复用趋势方向与语气：`src/shared/styles/index.ts:1046-1077,1091-1101` 的 `getTrendDirection`、`getTrendTone` 已区分 positive/negative、good/bad/flat。方向由既有指标阈值 `direction` 转换，不另造指标极性表；对应承保规模三项的 lower_worse 与承保效益三项的 higher_worse，复用注册表即可。调用前校验 current/previous/delta 为有限数；零差复用 flat→持平，不能落入优化/恶化。
- 【事实·静态核实】比较基期没有单一现成体检定义：`kpiCardProps.ts:115-145,148-186` 采用两端非空周值的较上期，成本为截至周末的年初至今；`server/src/routes/query/kpi-sparklines.ts:82-105,144-177` 不吃首页日期窗作同窗环比，不能无说明接到随筛选窗口变化的体检主值。`FocusStructureView.tsx:31-36` 已有上年同窗/无基数/按展示精度持平；`useKpiFocusData.ts:76-102` 成本双窗也按数据锚派生。两种来源只能在基期标签、日期锚、筛选/角色/省份一致时对照，不可把上一条非空记录默称相邻周期。
- 【事实·静态核实】家自车占比不在现有 `kpi-sparklines` 的窗口/周演进清单（`server/src/sql/kpi-sparklines.ts:27-63`），与两侧响应类型检查同证；系数趋势载荷是当月逐日加权系数（`src/shared/api/types.ts:1007-1030`），不含最终缺口历史。不能用当日已补的跨日清零量、系数 P50 差值或系数水平斜率替代最终缺口趋势。
- 【断言·代码推理】状态和趋势须同时可见且独立：危险可以正在优化，优秀也可以正在恶化；缺当前值、缺比较值、无可比基期只显示缺口原因。`CoefficientMonitorSummaryCard.tsx:32-45` 当前把所有无正缺口的分支归已补足，含 null，重组时需消费真实 `threshold/coefficient/final_gap` 空值语义，避免未配置/无数据被显示达标；该问题通过代码推理发现，未实测。

### 最小实施方案与真实写集（未实施）

【断言·代码推理】不改无法满足域名、三部分及四档用词；最小修改可沿现有组件与事实源完成，不新建业务注册表、不重算率值、不改系数口径与独立页面。建议一个目标 issue 承载本卡，一个 PR 完成同一体检结果；若 owner 选新比较基期或新系数档位导致独立数据契约变更，再按实际写集拆子 issue/依赖 PR，不为卡数拆 PR。

| 写集/边界 | 最小改动与复用 | 验收重点 |
|---|---|---|
| `src/shared/config/routeRegistry.ts`；导航注册表及 `sidebar-navigation-collapsed.test.tsx` 相关测试 | 仅改 growth/cost label；保持 id/path/权限、手风琴与默认落点 | 新名称可见，原链接可达，展开记忆与别名路由不回归 |
| `healthCheckPresentation.ts`；`HealthCheckSection.tsx`；对应现有组件测试 | 体检只渲染两业务组及监管红线；复用现有最差档排序与阈值说明，将已裁四档用词统一；空值不亮灯 | 状态 badge、结论句、阈值说明、辅助文字全部一致，不能只改标题 |
| `CoefficientMonitorSummaryCard.tsx`；`LandingHomePage.tsx`；`metricAtlasShell.test.tsx` 等装配测试 | 复用同一查询/汇总，将摘要移入监管红线；独立明细链接保持；去掉首页体检外重复挂载 | 系数仅出现一次；未配置/无数据/失败不伪绿；局部失败不拖垮另外两组 |
| `healthCheckPresentation.ts` / 已有共享状态实现；`kpiCardProps.ts` 及其测试 | 复用共享四档文案/色调，保留 API 灯枚举作兼容输入；同一裁定的 KPI 卡同步显示四档 | 不把共享 `HealthCheckLight` 直接替换为新枚举，避免破坏经营闭环统计与 API 消费方 |
| 趋势：`server/src/services/dashboard-health-check.ts`、`routes/query/kpi.ts`、`src/shared/api/types.ts`、`useHealthCheck.ts` 及对应测试（待基期裁定定写集） | 需要比较信息时给现有体检响应增加可选比较元数据/不可比原因；复用既有 SQL/过滤/数据锚，不另建口径。沿用明细方案须逐指标证明来源同义；家自车占比补现有序列或同窗 producer，不能前端凭当前值造趋势 | 单位/极性、当前与基期、空值传播、请求成本/并发、行级权限/省份/角色与明细一致 |
| `src/features/INDEX.md`、相关现行首页/体检说明、现有索引 | 随实际行为更新说明；不改历史 ADR 原文 | 指针可达，当前说明不再宣称旧六域 UI |

减法与依赖：不新增第二套阈值表；移除首页系数摘要重复装配、体检旧四行及其专用渲染分支。`LandingHomePage` 不再需要经营闭环摘要时解除调用/prop；`useOperatingLoopSummary` 是否删实体须先查其他引用，不能连同其他业务一起删。首跑指引 `src/features/first-run-guide/script.ts:19-22` 的 `home-domain-link:growth` 必须保留；`metric-addressing.ts:14-15` 的家自车占比仍落 `/home`，继续保留指标锚。不增加新导航一级域只为承载“监管红线”页面小节。

关联裁定/依赖只放指针：业务规则字典「口径裁定记录」#44（页面日期默认）、#46（成本分省档）、#50（机构达成率差异）；注册表 `categories/cost.ts` / `plan.ts` / `growth.ts` / `structure.ts`；系数配置正本 `coefficient-thresholds.ts` 与 store；[014](014-sidebar-accordion-default-landing-v2.md)、[002](002-site-wide-caliber-map-and-principles.md)。本卡不复制目标仓规则或阈值数字。

### 防撞车与失效条件

- 【事实·静态核实】2026-10-28 GitHub 连接器分别查开放体检/系数/导航 issue、开放 PR；另一方法以 `gh pr list --repo alongor666/chexian-api --state open --limit 100 --json number,title,files,headRefOid` 与 `gh issue list --state open --limit 500 --json number,title,body` 后按词过滤交叉核对。开放 PR 为 #2680/#2679/#2677/#2676/#2670，当前核心写集无交集（不是永久无冲突承诺）；#2677 涉业务字典的其他领域，转正时检查共用文件变化。
- 相关开放项：[2392](https://github.com/alongor666/chexian-api/issues/2392) 为八卡接入推送提议，[1588](https://github.com/alongor666/chexian-api/issues/1588) 为体检扫描省份参数化，[1525](https://github.com/alongor666/chexian-api/issues/1525) 为续保标准口径迁移，[1656](https://github.com/alongor666/chexian-api/issues/1656) 为导航标题历史残项；未见承载本卡完整三部分/状态/趋势诉求的开放工单。精确“监管红线”零命中已用开放全集标题/正文过滤及源码域枚举交叉核对，不能扩写成全仓不存在监管规则。
- 规划文档另查 `docs/plans` / `开发文档` 的体检/文件符号：旧 KPI 聚焦方案与 CODE_INDEX 仍描述旧多域输出，是本卡兼容面，不能直接复用为新组装合同。
- 与 016 共同写点为 `LandingHomePage.tsx` 和首页装配测试；021不修改 `HomeUpdatesCard` 的未读/降级合同。推荐 016 先完成更新摘要移位，021从其后主干重读装配点再做体检重组；016无需等待本卡业务裁定。若并行开工，保持独立 worktree、逐项移植唯一写点，不互相覆盖。
- 开工步骤 0：重新 fetch、读目标根/分区及协议 §1.1，以完整 SHA 核 main、开放 issue/PR 写集、上述裁定与 producer/consumer；报告实质差异后继续已授权范围。当前 head/main、相关阈值/日期口径、基期定义或共享首页装配变化时，本快照相应部分失效，重新核实；GitHub 查询失败须保留 UNKNOWN，不作无冲突结论。

### 可执行验收计划（全部 NOT RUN）

在目标仓未来独立任务 worktree 执行；本次只编制命令，未创建测试文件或运行服务。

1. `bun run typecheck`：类型通过；随后 `bun run test --run src/shared/config/__tests__/routeRegistry.test.ts src/components/layout/__tests__/sidebar-navigation-collapsed.test.tsx src/features/dashboard/components/__tests__/HealthCheckSection.test.tsx src/features/dashboard/components/__tests__/kpiCardProps.test.ts src/features/pages/__tests__/metricAtlasShell.test.tsx`。在现有测试中补新名称/三部分/系数唯一挂载/受权链接/四档文字断言；负例是旧域仍显示、旧系数摘要重复、四档全部固定优秀。
2. `bun run test --run server/src/services/__tests__/dashboard-health-check.test.ts src/shared/config/__tests__/strategy-cockpit-thresholds.test.ts src/shared/styles/__tests__/metric-polarity-color.test.ts`：复用注册表及既有 oracle；为每个阈值精确边界与两侧加断言，覆盖 SX/SC/无省回退、null/NaN/无阈值/角色不适用；同一输入映射后的体检与 KPI 卡状态一致，API 老灯值仍兼容。测试不抄另一份阈值。
3. 基期裁定后补趋势测试并沿用同一 runner：好指标正差→优化/负差→恶化，成本类反向；零差→持平，缺当前/缺基期/无定义→不评价；危险且优化、优秀且恶化分别成立。负例：一律涨绿、以阈值灯“恶化”当趋势、空周拼接冒充相邻周、不同日期窗或不同省份数据比较、用缺失系数当已补足。家自车占比必须有真实 producer 数据合同，不能只加手造前端 fixture。
4. 真实本地已授权会话下请求 `/api/query/health-check`、同参数 `/api/query/kpi` 及 `/api/query/coefficient-monitor`，检查 200、非空且语义非占位；将返回值/省份/日期锚/筛选/阈值对账。命令形态：`curl --fail --silent --show-error --cookie "$LOCAL_SESSION_COOKIE_FILE" "$LOCAL_BASE_URL/api/query/health-check"`，变量由实际本地环境解析，禁止把生产地址或 cookie 入卡。新增比较字段须与相应明细实际接口核对；401/403为权限负例，不绕鉴权。环境/样本未具备则保持 NOT RUN。
5. `bun run dev:full` 后真实浏览器验首页：宽/窄屏、明暗主题、加载/局部失败/空数据；三个部分顺序清晰、状态与趋势文字可读、系数明细可达、导航展开/登录落点/首跑指引/指标搜索不回归；与016交叉核更新入口最终位置。浏览器模拟响应只证明 UI，另留真实 API→服务→数据与角色限制证据，不冒充生产验收。
6. 按目标仓当前提交/PR纪律执行必要检查；注册表若未改不为标签变化触发全量 codegen。若 owner 选新档位，先在目标正本记录裁定并按实际注册表改动运行其校验/codegen，同批锁定所有消费方。本卡未实施、未部署，也未验证生产。

### owner 真分叉（探索 #1 时点待答；后续已答见澄清 Q2/Q3）

1. **系数状态**：A（推荐）监管红线中的系数保留“已补足/未补足”等真实业务二态及未配置/缺数据说明，优秀/健康/预警/危险仅用于已有已裁四档指标；B 系数也统一四档，须先裁评价对象（系数水平/缺口/同比变化）、适用范围与档位，再改正本。推荐 A 可复用现有监控事实；不能把尚待定档的货车缓冲档或 P50 差值套上默认摘要。
2. **趋势基期**：A（推荐）沿各指标现有明细比较基准并逐项标注基期/窗口，不把不同基期压成一个无说明总趋势；B 统一指定环比或同比，需明确窗口/日期锚、零基数与不可比处理，并补齐对应 producer。推荐 A 的极性判断可直接复用，但仍须验证逐项同窗同范围、明确缺基期；选 B 则不能无条件拿现有周序列代替。

答复前保留 discussed/NOT READY；答复后只更新受影响方案与验收，可靠闭合后再转 scouted。转正映射为本卡→目标仓新 issue→实际实施 PR（编号待开工，不占位、不提前建 issue/PR）。

## 探索 #2（2026-10-28 · owner 裁定后增量）

### 裁定与范围

- 【owner 已定】系数继续显示已补足/未补足及缺数据事实；已有定义的指标使用优秀/健康/预警/危险四档。无新增系数四档阈值任务。
- 【owner 已定】趋势统一「与上月末比」，界面明确标注「较上月末」；不再沿用探索 #1 推荐的逐指标不同基期。优化/恶化仍按已定指标方向判断，状态与趋势独立。
- 本轮只更新本卡及索引，目标仓源码、数据、接口均未改动；不创建目标仓 issue/PR。月末方案不要求修改各独立明细页原有比较基准。

### 增量证据（静态核实，未实测）

- 探查锚仍为 `chexian-api @ b8f2de75100f21688d6053839a388a5090ec5f6c`；本轮 `git fetch origin` 后复核完整 SHA 未变。复核方法为 `git show <SHA>:<路径> | nl -ba`，防撞车快照沿用探索 #1，正式开工仍须刷新。
- 【事实】`src/features/dashboard/hooks/useStrategyCockpitKpi.ts:103-107,119-136` 已有上月末请求，但以系统今天定月、强制当年年初累计窗口，基期传估值日；整体复用会覆盖首页日期筛选，不适合作为首页统一趋势入口。
- 【事实】`server/src/config/operating-overview.ts:225-250` 的 `signedPremiumWindows(asOf)` 生成真实月末累计序列；其 `momBase` 为上月同日进度，不是上月末，不可混用。`server/src/routes/query/operating-overview.ts:798-815` 已按权限范围数据日/显式截止日确定观察锚，显式日不得晚于数据日。
- 【事实】`server/src/sql/kpi.ts:451-465,613` 已提供家自车占比，可按两窗重算；`src/features/dashboard/hooks/useKpiData.ts:16-58,121-153` 的类型/白名单投影未带该字段。既有体检服务可直接消费服务端结果，无需绕前端 hook 取比较值。
- 【事实】成本链 `server/src/routes/query/kpi.ts:106-111,148-170`、`routes/query/shared.ts:601-635`、`sql/kpi.ts:180-220` 支持按估值日截满期暴露和出险窗口；金额使用当前数据重算，不是上月末当时保留的估值快照，方案与界面必须披露这一边界。
- 【事实】`server/src/sql/kpi.ts:247-271,570-576` 的四川纯机构计划达成率读取当前缓存实际值/时间进度，不随 `endDate` 历史截算；重复当前请求不能充当上月末值。其他范围的计划进度使用实际数据锚（`:415-445`），不一定恰为自然月末。

### 最小方案与验收增量（代码推理，未实施）

1. 在既有 `server/src/routes/query/kpi.ts` 体检链增加上月末比较查询，复用 `generateKpiQuery`、相同权限/省份/机构/业务筛选与既有数据锚；按观察截止日确定真正上月末，显式传入基期估值日。抽取/复用既有月末计算，不新造另一套日期或指标规则；不得使用系统今天、上月同日或上一非空周替代。
2. 在 `server/src/services/dashboard-health-check.ts` 装配比较值、实际基期日期/窗口、估值说明、差值及不可比原因；`src/shared/api/types.ts` 与展示侧同步可选元数据，保持原 API 灯值兼容。保费增长比较的是两个时点各自增长率之差，不能将上月保费直接作增长率基准；率值以相同单位显示差值。
3. 保留首页当前值的原筛选合同；当前窗口与月末累计口径兼容时比较，跨年度、无有效基期、分母无效、缓存不支持历史或窗口不兼容时明确显示原因。四川纯机构计划达成率没有历史取值时只阻断该指标趋势，不能显示「持平」；不将某一指标的缺口扩大为整个体检失败，也不静默把首页切成年初累计。
4. 四档仅用于已裁指标；系数继续保留补足二态，不用其逐日系数斜率、当日已补数或缺失值伪造最终缺口趋势。若今后要求所有切片无例外都有趋势，须先补四川机构历史 producer，以及自定义窗口如何回退的合同；本卡不把缺失数据冒充已实现。
5. 写集沿探索 #1 的导航/体检装配方案，趋势部分收敛为既有 `routes/query/kpi.ts`、`services/dashboard-health-check.ts`、客户端类型/体检 hook/展示及对应测试；不用整段驾驶舱三窗 hook，也不新增接口或第二套业务注册表。
6. 沿用探索 #1 验收命令，在原有服务/组件测试中补：数据日与系统今天跨月时仍用数据锚；真正月末与上月同日/周末不同的用例；闰月及一月边界；家自车字段投影；成本估值日传递；增长率差值单位；四川缓存重复值不得判持平；不兼容日期窗明确不可比；好指标与成本指标正负差四种组合；危险且优化、优秀且恶化。真实接口对拍同时核本期/基期日期、范围及估值披露。

### 当前结论

- **READY FOR IMPLEMENTATION（方案就绪）**：已定两项裁定回填，月末取数路径与真实缺口均已标明；卡转 scouted。就绪指可按上述方案转正，不代表所有切片已有月末历史数据。
- 无新的系数四档或趋势基期裁定待答。沿用 1 卡→目标 issue→实际 PR 的映射及 016→021 首页装配顺序；开工时在目标仓业务正本登记本次裁定，仅放指针，不在 flint 复制规则表。
- 本轮测试、浏览器/接口实测、部署与生产验收全部 **NOT RUN**，仍待「开工 021」授权。

## 探索 #3（2026-10-28 · 增速上限红线增量）

> owner 追加指令（2026-10-28）：增长率不超过上限亦属监管红线；市场对标页已有增速数据；体检监管红线纳入展示 2 项——**年度签单增速、月度签单增速**；上限数值届时裁决。本段只记增量；导航/三部分/系数/状态/趋势见探索 #1、裁定回填与上月末趋势方案见探索 #2（与另一会话并行产出，节号顺延）。R1-R4 已于同日 AUQ 裁毕（澄清 Q4-Q7），本段与探索 #2 的方案合并有效——卡回 scouted。

### 探查锚与防撞车

- `chexian-api @ b8f2de751`（2026-10-28，与探索 #1 同锚复核无漂移）；只读静态核实（`git show/grep origin/main`），无实测。
- 增速上限为零命中结论，两法交叉：① `git grep -iE "增速.*上限|上限.*增速|增长率.*上限" origin/main -- server/src 数据管理/knowledge` → 仅命中口径方法论「红线」与系数下限登记，无保费增速上限条款；② `gh issue list` + `gh pr list --state open` 关键词过滤（体检|红线|增速|对标）→ issue 仅 #2250/#2194 无关项、PR 零命中。**仓内无任何保费增速上限登记（自律方案登记只有系数下限）——上限数值、档位与监管依据文号均为待裁新登记项**（owner 已言「到时候可裁决」）。

### 【事实·静态核实】增速数据两条链路

1. **链路 A（体检同源）**：`premium_growth_pct`（`server/src/config/metric-registry/categories/growth.ts:82-126`）= 年初至今签单保费同比，消费字段 vehicle_growth_rate，KPI 行现成——「年度签单增速」在体检链路零新增查询；「月度签单增速」（当月 vs 上年同月）KPI 行无现成列，需在 `/health-check` 路由层补一路当月+上年同月两窗聚合（热力图域 yoy_growth_rate 有月度同比先例，跨域不直接复用其 SQL）。
2. **链路 B（市场对标页同源）**：`server/src/sql/industry-benchmark-insurers.ts:142-176` 逐期×逐主体（含华安行与市场合计）吐本期/同期保费原子，增速 = 本期÷同期−1 前端派生（先例 `src/features/industry-benchmark/growthDecomposition.ts:188` selfGrowth、`IndustryPivot.tsx:243-255` selfGrowth 排序）；view=month = 月度同比（最近 12 期）/ view=year = 年度（进行年 = 年初至数据月 vs 上年同窗）；签单口径（dateCriteria=policy_date 缺省）、不含税、整月对齐。owner 所指「市场对标页有数据」即此页华安行增速。
3. 两链路同省同签单口径但数值会有差：链路 A 为 PolicyFact 保费、年初至今至最新签单日；链路 B 不含税、整月对齐、不经体检业务筛选。展示值取哪条待裁（分叉 R1）。

### 【事实·静态核实】判定与阈值现状——方向冲突是核心裁决点

1. `judgeLight`（`server/src/services/dashboard-health-check.ts:92-103`）对 higher_worse = 超过逐档上探、等值归好档——「不超过上限」判定机制现成，无需新逻辑。
2. **`premium_growth_pct` 现挂 thresholds 为 lower_worse**（增速低才危险，growth.ts:120-126）——KPI 墙与体检承保规模组现灯向「增速太低」报警；监管上限语义相反（增速太高违规）：同指标同数值两组反方向亮灯。注册表一 id 只挂一套 thresholds，不能直接改它（会翻转 KPI 墙现有灯）。实现两条既有机制：
   - 服务层显式阈值覆盖：`buildMetric` 的 `MetricSpec.thresholds` 可显式传值（renewal_rate 显式 null 先例，dashboard-health-check.ts:135-150）——红线组增速 spec 显式传 higher_worse 上限档，不动注册表；
   - 注册表新 metricId：同公式不同阈值方向，登监管依据 notes（先例 cost.ts:1418 太原自律系数）。
3. 建议方案沿探索 #1 分叉 1 推荐口径：优秀/健康/预警/危险四档展示由已裁阈值指标消费，红线组增速若上限阈值已裁则同样四档化；上限未裁前只给数不亮灯（沿 NO_THRESHOLD_NOTE 既有表达）。

### 【断言·代码推理】增量写集（叠加探索 #1 最小方案）

| 写集 | 增量改动 | 验收重点 |
|---|---|---|
| `dashboard-health-check.ts` | 监管红线组指标 spec +2（年度/月度签单增速）；上限未裁（Q5）→ thresholds 显式 null 只展示不亮灯，上限裁后登注册表转 higher_worse 亮灯（Q6 双展示=规模组灯位不动）；月度值空（当月无数据/无同比基期）沿既有 null | 上限裁后灯方向=超上探；空值不伪绿 |
| `routes/query/kpi.ts` `/health-check` 段 | 输入补增速：年度 = KPI 行现成字段；月度 = 补当月+上年同月两窗聚合（链路 A 已裁 Q4；窗口按 Q7 月内同日进度折算：当月 1 日至数据日 vs 上年同月 1 日至同日，SQL 各带日截断） | 行级权限/省份与体检一致 |
| `metric-registry/categories/growth.ts` | 若走新 metricId：登记上限阈值 + 依据 source；数值占位待裁 | 不抄第二份阈值进测试 |
| `HealthCheckSection.tsx` 红线行 | 指标胶囊 +2 | 与探索 #1 红线行渲染合并验收 |

### 分叉 R1-R4（2026-10-28 AUQ 已全部裁定，结果见澄清 Q4-Q7）

- **R1 数据源**【已裁：A，Q4】原选项——A（推荐）链路 A 体检同源（同窗同权限同省、年度值零新增、月度补一路查询）vs B 链路 B 市场对标 BI（与对标页同值同屏，但不经体检筛选、不含税、整月对齐、跨灰度闸）。「市场对标页有数据」按数据可得性证明理解而非指定数据源；若 owner 意在「体检红线增速须与对标页数字一致」则选 B。
- **R2 上限数值与档位**【已裁：先不亮灯，Q5；数值后裁】原选项——年度/月度各一档或同值；监管依据文号（登注册表 source）。
- **R3 同屏关系**【已裁：保留双展示，Q6；与 Q5 叠加=规模组亮灯+红线组只展示】原选项——现指标 lower_worse（增速低危险）与红线组 higher_worse（增速高违规）并存 = 同数值两灯反向，是否保留双展示或收敛为一处。
- **R4 月度窗口语义**【已裁：月内同日进度折算，Q7——owner 自定义，未采纳推荐的整月口径】原选项——当月 vs 上年同月（对标页口径）；当月未满月时按整月展示（月初数值天然偏低）或按月内同日进度折算，或滞后整月展示。

### 验收命令（增量，均 NOT RUN）

- `bun run test --run server/src/services/__tests__/dashboard-health-check.test.ts`：增速 spec 方向/空值/上限边界断言。
- 已授权本地会话请求 `/api/query/health-check`：监管红线组增速两项 200 非空、数值与所选链路明细对账（环境变量同探索 #1 第 4 条，不录生产地址）。
