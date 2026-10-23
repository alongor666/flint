# 011 · 车型筛选更名「客群」＋增「低风险」（附签单口径 UI 分布核对）

- 状态: clarifying
- 目标仓: chexian-api（先探可定——「车型」筛选即该仓全站筛选器的 vehicle_quick_filter，前端 `src/features/filters/` + 服务端注册表 `server/src/config/vehicle-quick-segments.ts`）
- 类型: 功能（呈现词更名）＋ 功能（快捷段扩容，依赖口径裁定）
- 录入: 2026-10-23

## 原文

UI哪些页面哪些指标固定编码用签单口径、哪些默认签单口径？筛选器，车型更名"客群"，其中增加"低风险"

## 澄清（AUQ）

### 先探可定（依据）

- 「车型」筛选 = vehicle_quick_filter 车型快捷筛选（18 段 id：客车 7 档＋货车吨位 4 档＋10 吨以上 5 子项＋特种/挂车），SSOT = `server/src/config/vehicle-quick-segments.ts`（append-only，顺序即口径），前端镜像 `src/shared/components/quick-filters/config.ts`（双侧对账测试锁一致）。
- 该筛选语义本就是客户类别（customer_category）＋吨位＋厂牌关键词的分组呈现（config.ts:123 注释「客户类别下拉退役，③④行即客类分组呈现」）——更名「客群」属名实相符的**呈现词**改动。
- 主张只动呈现层（label/文案/锁注），不动 URL 参数名 `vehicleQuickFilter` 与 18 段 id（公开 URL 兼容，非交互假设，owner 纠偏即停）。
- 第一问（签单口径 UI 分布）为调研问题，已在探索 #1 核对当前 HEAD 直接答复（002 卡口径全景仍有效，增量＝009 PR-2b-1a #2261 上线后 KPI 墙签单独立锚落地）。

### 待 owner 裁定（AUQ）

- Q1 「低风险」的定义？ → 待答。选项: A（推荐）车险风险等级 A/B 档（insurance_grade，PolicyFact 已有列，与筛选向导既有「风险」维同源）/ B 连续无赔款（NCD 优待口径）/ C 其他定义（owner 描述）
- Q2 「低风险」与车型段的叠加语义？ → 待答。选项: A（推荐）独立横切开关、与车型 AND 叠加（低风险非客类分组，OR 并列会破坏③④行互斥组语义且并集口径反直觉）/ B 作为车型段第 19 个 chip OR 并列（与现有段并集）

## 讨论

- 筛选向导已有独立「风险」维（`guide-questions.ts` insuranceGrade，order 9，A–G/X/无评级单选，全站抽屉 basicOptions 组同款）——「低风险」若取风险等级 A/B 档定义，价值＝一键圈定低风险客群（免逐级点选＋跨维组合）；若与既有风险维功能重叠度让 owner 觉得冗余，可改 B 定义或撤。
- 服务端注册表 whereExpr 支持任意 PolicyFact 列合取式，技术上可直接承载「低风险」段；但段结构 rowGroup 仅 car/truck 两行组，「低风险」是横切维度不属于任何一行——Q2 裁定 A 则走 QuickFilters 新键（独立开关行），裁定 B 则注册表末尾 append＋前端 chips 扩位。

## 探索 #1（2026-10-23）

- 探查锚: chexian-api @ 918e6d75b（origin/main）
- 防撞车: 未查开放 issue（转正前补查）；「客群」字样在该仓 UI 无现成用例。
- 实施要点:
  - 更名改动面（纯呈现词）：`guide-questions.ts:158`（vehicle 维 label「车型」＋aliases）、`quickFilterHelpers.ts` VEHICLE_LABELS、`summaryChips.ts` 车型片文案、`AdvancedFilterPanel.tsx`/`PageFilterPanel.tsx` 行③④标题与隐藏注（如「隐藏抽屉内车型芯片行」）、preset 锁注（`filters.ts` claimsDetail lockNotes「车型筛选由本页快捷芯片承担」）、guide 教学文案（`guideContent.ts`）——全为字面词替换，不动段 id/参数名/口径。
  - 「低风险」走独立开关（Q2-A）：QuickFilters 新键 + `filter-dimension-capability` 能力矩阵 + `filter-caliber.ts` 投影/URL 序列化 + 各域视图列校验（无 insurance_grade 列的域须 locked，防 fail-closed 400）；走注册表 append（Q2-B）：`vehicle-quick-segments.ts` 末尾追加 + 前端 VehicleType 联合/VEHICLE_TYPE_IDS/chips + 前后端对账测试同步。
  - 口径分布核对（第一问答案，2026-10-23 HEAD 快照）：固定签单＝网点经营页（唯一 lockedDateCriteria:'policy_date' preset）、KPI 大盘墙卡六列＋签单保费卡（恒签单 YTD，不随口径选择器）、计划达成（后端恒签单）、金融同业全族＋机构版（恒锚最新签单日）、日报（计划达成＋对标考核口径）、行业对标考核口径；默认签单可切＝home/dashboard/org-custom（full preset）、业绩分析、保费增长、保费报表、图表台账、战略驾驶舱（cost tab 锁起保例外）；另挂 full 但时间实际锁定＝overview-v2/费用发展（恒保单年度批次）、系数监控（后端不消费通用筛选器）。对照：锁起保＝成本分析/赔案明细/客户流向/战略 cost tab；第三口径＝续保追踪（到期日）/报价转化（报价时间）/队伍标保（承保确认）/维修/摩托计算器。
- 转正映射建议: 1 issue 2 PR（更名纯呈现词一 PR；「低风险」段一 PR，依赖 Q1/Q2 裁定后定形态）。
- 验收: `bun run typecheck` ＋ 前后端段对账测试（vehicle-quick-segment-frontend-parity）绿；更名 PR 加全站「车型」字样 grep 清零佐证。

## 开工记录

（未开工）
