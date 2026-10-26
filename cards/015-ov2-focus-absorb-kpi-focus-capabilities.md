# 015 · 八卡聚焦吸收 KPI 看板聚焦能力清单

- 状态: shipped（2026-10-25 owner 合并 PR #2576 @ 62845768f，deploy 双 success 生产已上线；附产 #2577 存量回归待裁）
- 目标仓: chexian-api
- 类型: 功能
- 录入: 2026-10-24

## 原文

> 好，现在我的需求是 KPI 看板中把除了满期边际贡献额卡以外的 7 张卡片的聚焦卡片，"能力清单"包含的一切信息，都迁移、借鉴到新版经营分析页的 8 张卡片中。先讨论具体的情况，要登记到 Flink 仓库。先不要做任何的动作，只是探索和讨论，待我做决定

## 澄清（AUQ，2026-10-24 全答）

- Q1 「除满期边际贡献额以外的 7 张」指哪个集合？选项: A 战略驾驶舱满期族 / **B KPI 已配聚焦卡** → **owner 答: B**（即八卡 01-07 每张按能力形态对应 KPI 聚焦已配卡；08 边际贡献额无源、明示排除）
- Q2 承载形态？选项: **扩八卡配置驱动（推荐）** / 直接挂 KpiFocusTool → **owner 答: 扩八卡配置驱动**（抽 KPI 聚焦图表组件到 shared，FocusAuxiliary 按配置驱动模式扩展，轴数据源换八卡起保口径——保留八卡已有预警登记/影响度等 KPI 没有的能力）
- Q3 体检灯（健康扫描三色灯）进八卡？→ **owner 答: 不进，用八卡档位**（primaryTier 三档已有，避免 #2439 式双语义）
- Q4 周演进轴（12 周截面）为八卡重做？→ **owner 答: 不做，月粒度够**
- Q5 域映射下钻区八卡形态？→ **owner 答: 同伴卡＋板块页链接**（OVERVIEW_GROUPS 组内同伴卡＋跳各板块页，沿 KPI kpiDrilldownMap 思路；2026-10-25 AUQ）
- Q6 单位轴＋客群轴信息架构？→ **owner 答: 并入趋势页签下方**（页签结构不动，#2513 窄屏模态顺势；2026-10-25 AUQ）
- Q7 03 收入进度单位轴时序？→ **owner 答: 先降级**（本卡一刀内按 FOCUS_NO_AXIS_NOTE 模式诚实声明分母口径未对齐，003 落地后小刀补走 plan；2026-10-25 AUQ）

## 讨论

先探澄清的两处事实歧义（2026-10-23/24 探明）：「聚焦卡片」唯一载体是 KPI 看板 KpiFocusTool（目标仓工单 #1989/#1990/#1991 三刀，`src/features/dashboard/kpiFocus/`）；战略驾驶舱 8 卡（满期族）无聚焦接线；KPI 看板无满期边际贡献额卡。owner 集合 B 的落地映射：八卡 01-07 每张在 KPI 聚焦有对应配置卡（车险保费/满期保费/计划达成率/保费增长率/变动成本率/满期赔付率/费用率/满期出险率族），能力形态迁移+轴数据源换起保口径（KPI 轴全部签单口径，直接照搬会混口径——这是本卡核心工程难点，非组件复制）。

KPI 聚焦能力清单七块（迁移标的物）：指标头（主值+灯+计划/达成/同比摘要+口径提示）/时间轴（月序列）/业务单位轴（计划接口或 pivot）/客群轴（pivot 率类直出）/业务结构族视图/域映射下钻区/筛选继承。其中按裁定：体检灯不迁、周演进族不迁；结构族 11 卡与八卡无对应不迁。

## 探索 #1（2026-10-24）

- 探查锚: chexian-api @ origin/main `8341078bc`
- 防撞车: 开放 PR 池无聚焦迁移主题；owner 此前已另持两张基建工单（聚焦卡复用辅助栏能力面 / 聚焦视图复用 dashboard 聚焦基座）——本卡是其上位需求，转正时建议并轨：基建刀先行、本卡配置刀随后
- 实施要点: 组件源 `src/features/dashboard/kpiFocus/`（KpiFocusTool/kpiFocusMetricConfig 配置驱动三轴/FocusAxesCharts/FocusTimelineChart/useKpiFocusData 等；23 卡全有聚焦=core 12 三轴族 #1991+业务结构 11 族 #2049——owner 2026-10-24 纠正：此前误读 #1991 头注「结构族维持占位」旧态为现状）；目标面 `src/features/operating-overview-v2/components/FocusAuxiliary.tsx`（已有概览/趋势/轻诊断三视图+影响度工作区+预警登记）；轴数据源承接：八卡 cost 序列与 claimFactorsTrend 已有月起保口径序列，客群轴起保口径 pivot 原子需新增
- 转正映射建议: 1 issue（挂目标仓 #2274）分两刀——基建刀（聚焦组件抽 shared+八卡配置驱动骨架）/配置刀（7 卡逐张能力对齐+轴数据源换口径）
- 验收: 两页聚焦能力对照清单逐项核对；起保口径轴数值与八卡主值同源对拍（同窗同数）

## 探索 #2（2026-10-24，行号级深化）

- 探查锚: chexian-api @ origin/main `0847fb9e9`（2026-10-24；本地主检出在旧分支 behind 57，全程读 origin/main）

### 防撞车（勘正探索 #1「开放 PR 池无聚焦迁移主题」——该结论已过时）

- 【事实】**PR #2513 开放在池**（feat(overview-v2): 聚焦设施复用辅助栏能力面，Refs #2274）：ready、MERGEABLE、双轮独立评审 APPROVE，改 `FocusAuxiliary.tsx` +37/-5（narrowPresentation="modal"＋suspended 挂起＋layout 初始自动展开门）——即探索 #1 所称 owner「基建工单」之一的落成物，待 owner 合并（owner 2026-10-24 裁定禁 AI 合并）。其正文互斥条款：与「聚焦卡复用 dashboard 基础设施」工单互斥串行，后者以 #2513 合并后 main 为基线。
- 【事实·零命中三法交叉验证】「聚焦卡复用 dashboard 聚索基座」工单在目标仓 issue 池不存在：标题搜「聚焦卡复用 dashboard」「聚焦基座」「能力面」＋ #2274 评论全文 grep「dashboard 基础设施」均零命中——它只被 #2513 互斥条款引用，尚未立 issue。
- 转正排序据此定死：**基建刀一=#2513（在池）→ 基建刀二=KpiFocus 组件抽 shared（需立 issue）→ 本卡配置刀（7 卡逐张）**。

### 配置驱动正本与组件群（迁移标的物，均为 origin/main 行号）

- 配置形态: `src/features/dashboard/kpiFocus/kpiFocusMetricConfig.ts`——`FocusMetricConfig` 12 字段（headerField/headerUnit/headerIsRatio/headerIsPercent/derive/pivotScale/headerSummary/timeline/orgAxis/categoryAxis/pivotMetric/structure），`FOCUS_METRIC_CONFIG` 23 卡全覆盖（core 12 三轴族＋focus 11 结构族 #2049）
- 消费链: `KpiFocusTool.tsx:52` 起 `MetricFocusView`——useKpiFocusData（指标头+时间轴）→ useFocusStructureData（结构族）→ useKpiFocusOrgData/useKpiFocusPivotOrgData（单位轴）/useKpiFocusCategoryData（客群轴）＋ useFocusScopeParams（#2040 筛选继承，读 dashboard 全局筛选上下文）
- 取数链（**全部签单口径**——换口径的根源）: useKpiFocusData.ts 头注四趟（/kpi 无日期参数恒 YTD 签单、/trend 两趟、/health-check 体检灯）；`kpiFocusAxesData.ts:51`（plan-achievement）、`:128-129,:188`（getPivot 两趟，**未传 dateField→缺省签单**）
- 目标面: `src/features/operating-overview-v2/components/FocusAuxiliary.tsx`（587 行）——现状三视图 summary（FocusSummary＋InputEvidence/CardFaceEvidence 证据区）/trend（FocusTrend＋claimFactorsTrend）/diagnosis（FocusDiagnosis）＋AttentionList 预警登记；无三轴、无域映射区

### 八卡 01-07 × KPI 聚焦形态映射（含口径处置）

八卡 id 正本：`server/src/routes/query/operating-overview.ts:320-321`（7 基础卡序）＋ `:781`（efficiency 组＝第八卡 earned_margin_amount）。

| 八卡 | id | KPI 形态源卡 | 轴口径处置 |
|---|---|---|---|
| 01 签单保费 | signed_premium | vehicle_premium（premium 三轴+plan 摘要） | **原样复用签单口径**——口径原则 v2 唯一硬编码签单例外＝签单保费本身，轴不换口径 |
| 02 保费收入 | premium_income | 同 vehicle_premium 形态 | 起保窗（起保口径＝保费收入，命名裁定见 flint 009）：pivot 透传 dateField |
| 03 收入进度 | income_progress | vehicle_achievement_rate（orgAxis=plan） | 起保分子（计划按起保制定，owner 口径裁定 2026-10-15）；plan-achievement 分子现状以 flint 003（待 PR-B）落地态为准 |
| 04 收入同比 | income_growth | vehicle_growth_rate（双 pivot 轴） | 起保窗 pivot 两趟（当年 vs 上年同窗） |
| 05 变动成本率 | variable_cost_ratio | 同名卡 | Q4 裁定周演进不迁→时间轴用八卡已有 card.trend 月序列；成本族不做客群轴（工单原口径沿用） |
| 06 保单年赔付率 | policy_year_loss_ratio | earned_claim_ratio | 同 05 |
| 07 获取成本率 | acquisition_cost_ratio | expense_ratio | 同 05 |
| 08 满期边际贡献额 | earned_margin_amount | 无源 | 排除（Q1 裁定） |

### 轴数据源承接（核心工程难点，比探索 #1 收窄）

- 【事实】**起保窗 pivot 服务端已支持**: `server/src/routes/query/pivot.ts:153`（消费 req.query.dateField）、`:195`（anchor 优先于显式 dateField）——客群/单位轴换口径＝前端透传 dateField=insurance_start_date＋锚窗，**非新接口**
- 时间轴: 八卡 card.trend 已是服务端月值（cardFaceModel.ts:41「金额必须已是服务端月值」）；KPI 的 /trend 签单序列不搬。⚠ pivot.ts:195 anchor 优先——total_premium 等指标若注册表有锚字段，显式 dateField 可能被覆盖，须逐 pivotMetric 核对锚语义（实施第一步，本次未逐指标验证）
- 指标头/摘要: 用八卡响应（card＋cardFace readings）现成数据，不搬 /kpi 四趟；体检灯不迁（Q3），八卡档位 primaryTier 已有
- 筛选继承: ov2 已具备同构输入——OperatingOverviewV2Page.tsx:8-11（PageFilterPanel/useGlobalFilters）、useOperatingOverview.ts:20-22（buildFilterParams 剔日期三件套＝与 #2040「时间三键剥除」同构）、server :762 消费 commonFilterSchema

### 风险

1. **scope 通道不同源**: KPI 聚焦 useFocusScopeParams 读 dashboard 筛选上下文，ov2 走 buildFilterParams——组件抽 shared 时 scope 注入必须参数化，否则八卡聚焦轴误读 dashboard 筛选或丢 ov2 筛选
2. pivot 指标锚覆盖（见上，实施第一步核对）
3. plan-achievement 分子口径依赖 flint 003 落地
4. #2513 未合并→配置刀基线漂移（开工前 fetch 对齐）
5. 05-07 月序列覆盖度: 八卡 card.trend 对成本三卡是否有月点需实施时核对（缺则时间轴诚实降级，沿用 FOCUS_NO_AXIS_NOTE 模式）

### 验收命令

`bun run typecheck`；`bun run test --run src/features/operating-overview-v2`（新增聚焦轴用例）；`bun run test --run src/features/dashboard/kpiFocus`（抽 shared 回归）；`bun run governance`。对照验收：01 签单轴与 KPI 聚焦同卡同数；02-07 起保轴与八卡 card.trend/主值同锚同窗对拍。

### 转正映射（更新）

1 issue 挂 #2274、三刀序：基建刀一 #2513（在池待合并）→ 基建刀二（立 issue：组件抽 shared＋scope 参数化）→ 配置刀（本卡）。基建刀二改动面若小可与配置刀并一 PR，按目标仓体量契约裁量。

## 探索 #3（2026-10-25，拉新基线＋空缺收口）

- 探查锚: chexian-api @ origin/main `e86f4e9d6`（2026-10-25）

### 基线变化（自 `0847fb9e9`，与本卡相关）

- 【事实】**#2513 已合并**（084b9a30d）→ 基建刀一落地，探索 #2 风险 4 消除。FocusAuxiliary.tsx 现 619 行：组件 :48 起、suspended 推导 :51-53、useWorkbenchAuxiliary :61、selectionKey 门控 :63-66
- 【事实】#2459 已合并，只改 analysisBriefConfig.ts＋测试，不触聚焦面；#2514（flint 002 口径原则 v2 全站默认分布）已合并，不触 kpiFocus/ov2（0 命中），对本卡中性——02-07 起保窗与之同向
- #2490 ResizeObserver 中央桩可注入回报宽度——配置刀窄/宽两态测试可直接用；#2434 行号漂移闸只扫契约文档＋脚本头注，不管 issue/PR 正文——配置刀代码头注勿写行号引用
- kpiFocus 源码与 pivot.ts 两锚间零变化，探索 #2 行号稳定；operating-overview.ts/card-face.ts 小改，下列行号已刷新

### 核查事实（修正探索 #2）

- 【事实】**05-07 月序列存在**：主路径「各月末以当前数据重算」（operating-overview.ts:985/:1029/:1092；card-face.ts:303 消费 card.trend——05/06/03 现役＋07/04/08 #2397 补全），仅成本立方缺失时退 buildCurrentOverviewCard 末点-only（:1080-1081/:1117）。方法学与 KPI 周演进同构（截面重算），只是月边界——**Q4「月粒度够」前提成立**；探索 #2 风险 5 降为「聚焦轴跟随服务端既有退化，不另造」
- 【断言·未核】v2 计划正本在 owner 另机路径，本机不可读——其是否已有聚焦面规划段，owner 自查

### 七块能力逐块 delta（探索 #2 该做未做，现补）

| 能力块 | ov2 现状 | 处置 |
|---|---|---|
| 指标头 | FocusSummary＋20 枚 cardFace readings 已有 | 已存在，不迁 |
| 时间轴 | FocusTrend 月序列已有（card.trend） | 已存在；增强＝达成率线/同比虚线/部分月透明三标注（仅 01/02） |
| 业务单位轴 | 无 | **迁**（01/03 走 plan；02/04/05/06/07 走 pivot） |
| 客群轴 | 无 | **迁**（01-04；05-07 成本族不做，工单原口径） |
| 结构族视图 | — | 不迁（裁定） |
| 域映射下钻区 | 无；ov2 有 OVERVIEW_GROUPS（:781）与影响度/维度工作区 | **待裁 Q5** |
| 筛选继承 | 已同构（PageFilterPanel／buildFilterParams 剔时间三键） | 已存在；配置刀确保轴 hook 消费同一 scope |

→ 真正迁移面＝两块轴＋一块标注增强＋一块待裁。探索 #2「七块迁移」框架夸大。

### 抽 shared 清单（基建刀二实际范围）

- 要动: FocusAxesCharts（FocusOrgChart/FocusCategoryChart/FocusPivotAxis）＋ useKpiFocusCategoryData/useKpiFocusPivotOrgData（scope 与 dateField 参数化）＋ useKpiFocusOrgData（plan）
- 不动: KpiFocusDomainMap／FocusExtraTimelines／FocusWeeklyTimeline／FocusStructureView／useKpiFocusData；FocusTimelineChart 也不动（KPI 行形 FocusMonthPoint 与八卡 FacePoint 异构，标注增强在 ov2 侧 buildFocusTrendOption 内做）
- → 基建刀二远小于名字，**与配置刀并一 PR**（探索 #2「按体量裁量」收口）

### 硬约束勘正

**03 收入进度单位轴**：flint 003 起保分子未落地前，/plan-achievement 给的是签单口径机构达成率，与 03 主值（起保）同屏双口径。要么排 003 PR-B 后，要么 03 单位轴先诚实降级（FOCUS_NO_AXIS_NOTE 模式）。探索 #2「以 003 落地态为准」太软。

### 待裁（owner）→ 已裁 2026-10-25，见澄清段 Q5-Q7（均取推荐项）

- **Q5 域映射下钻区八卡形态**: A 组内同伴卡（OVERVIEW_GROUPS）＋板块页链接（最小，推荐）／ B 不要（ov2 影响度/维度工作区已是下钻）／ C 另议
- **Q6 单位轴＋客群轴信息架构**: A 并入趋势页签下方（#2513 已为高趋势页签选窄屏模态，顺势；推荐）／ B 开第四页签「维度」／ C 草图再定
- **Q7 03 单位轴时序**（探索 #3 后补问）: A 先降级（推荐）／ B 改走 pivot 收入机构分布／ C 等 003 落地

### 登记项（配置刀）

drillPath 钻入某机构后单位轴只剩一柱——自动切下一维或隐藏需定规则；infoText 每卡自写（起保口径名「保费收入」按 009，趋势口径消费服务端 trendNote）；pivot.ts:195 锚覆盖逐指标核；计划单位轴「仅单选生效」例外随迁；08 卡聚焦面保持现状、与 01-07 形态不一致（owner 知悉）；每卡聚焦新增约 4 趟请求。

### 转正映射（终版）

1 issue 挂 #2274，**一刀**（基建刀二＋配置刀并 PR）。裁定已齐，随时可转正；03 单位轴以降级态上，003 落地后小刀补。

## 开工记录（2026-10-25，building）

- 目标仓 issue **#2568**（挂 #2274）；PR **#2576**（i/2568 @ 4b3cd1022，毛行 ~850>500 说明档三行已附）
- 流程：闸① 规划对抗评审三轮（DSH strong=glm-5.3；R1 REQUEST_CHANGES 12 findings→v2 数据源重设计「同端点 dimension 变体」消解 pivot 公式不同源；R2 REQUEST_CHANGES N1/N2（P1）→v3；R3 **APPROVE**）；实施 S1-S7（S1 抽 shared/focus 合规必需——L2 横向全禁 CX-ADR-cebbe5；S6 组件级 11 用例）；独立评审 REQUEST_CHANGES 2×P1+2×P2+4×P3 全修复（**P1-1 深挖出存量回归：#1991 漏配 pivotScale 使 KPI 三卡客群轴元值错标万 1 万倍→另开 #2577**）；闸② 主会话复评通过
- 插曲：main 红诊断 #2569（静默降级钉子 1296 落地即过时=合并竞态）→ #2566 自愈 → NO CHANGE NEEDED 关闭
- 交付态：可合并就绪待 owner 合并（禁 auto merge/禁 AI 合并）；规划正本 docs/plans/2026-10-25-ov2-focus-axes-plan.md（v3+偏离登记）随 PR 入库
- 附产收口（2026-10-26）：**#2577 由 PR #2591 修复并已上生产**（owner 03:22Z 合并→03:35Z deploy success）——三卡客群轴+earned_premium 单位轴存量错标+growth_rate 新引入风险一刀痊愈（评审 F1 揪出单位轴交叉消费 pivotScale 的换算缺口，修法=hook 层行值 ÷10000 与标签同源）
