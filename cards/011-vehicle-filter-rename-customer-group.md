# 011 · 车型筛选更名「客群」＋增「低风险」（附签单口径 UI 分布核对）

- 状态: building
- 目标仓: chexian-api（先探可定——「车型」筛选即该仓全站筛选器的 vehicle_quick_filter，前端 `src/features/filters/` + 服务端注册表 `server/src/config/vehicle-quick-segments.ts`）
- 类型: 功能（呈现词更名）＋ 功能（快捷段扩容，依赖口径裁定）
- 录入: 2026-10-23

## 原文

UI哪些页面哪些指标固定编码用签单口径、哪些默认签单口径？筛选器，车型更名"客群"，其中增加"低风险"

### 补充录入（2026-10-23）

车型更名客群，客群"里面增加"低风险"，低风险选择时要跟与此无关的互斥。例如，货车就不能再被选择了。另外，油和气应该可以同时选，因为油和气其实是传统的非新能源、区别于电动车，但凡有超过2个选项的都应该允许多选。。。。

## 澄清（AUQ）

### 先探可定（依据）

- 「车型」筛选 = vehicle_quick_filter 车型快捷筛选（18 段 id：客车 7 档＋货车吨位 4 档＋10 吨以上 5 子项＋特种/挂车），SSOT = `server/src/config/vehicle-quick-segments.ts`（append-only，顺序即口径），前端镜像 `src/shared/components/quick-filters/config.ts`（双侧对账测试锁一致）。
- 该筛选语义本就是客户类别（customer_category）＋吨位＋厂牌关键词的分组呈现（config.ts:123 注释「客户类别下拉退役，③④行即客类分组呈现」）——更名「客群」属名实相符的**呈现词**改动。
- 主张只动呈现层（label/文案/锁注），不动 URL 参数名 `vehicleQuickFilter` 与 18 段 id（公开 URL 兼容，非交互假设，owner 纠偏即停）。
- 第一问（签单口径 UI 分布）为调研问题，已在探索 #1 核对当前 HEAD 直接答复（002 卡口径全景仍有效，增量＝009 PR-2b-1a #2261 上线后 KPI 墙签单独立锚落地）。

### 待 owner 裁定（AUQ）

- Q1 「低风险」的定义？ → **owner 已裁（10-23 补充录入隐含确认）＝战略 1+2 段并集**（互斥举例「货车不能再被选择」即战略 5 客类；与 10-22 行业对标确认同源，SSOT=`config/strategy-segments.ts`）。
- Q2 「低风险」与车型段的叠加语义？ → **owner 已裁（10-23）＝与「与此无关」的客群段互斥**：选低风险后，非 1+2 覆盖客类（公交/公路/网约/出租/摩托＋货车系全部＋特种/挂车）禁选；1+2 覆盖客类（家用/机关/企业）可继续叠加（AND 收窄）。
- Q3 低风险段的完整语义？ → **owner 已裁（10-23「按照你的建议办」）＝A 完整战略段并集**（客类×能源×险类逐段 OR，与行业对标 lowRiskPredicate 同构；用户再手选能源/险类时自然 AND 收窄）。

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

## 探索 #2（2026-10-23 · 行业对标「低风险」机制，owner 问句触发）

- 探查锚: chexian-api @ 918e6d75b（同日）
- 背景: owner 拼音问句「行业对标的低风险，应编码签单正确吗」——确认行业对标页既有「低风险（1+2）」范围的口径编码。
- 结论: **正确，且是数据粒度硬约束下的唯一可行口径**：
  - 低风险 = `lowRiskPredicate`（`server/src/sql/industry-benchmark-pivot.ts:722`）：战略 1+2 段（STRATEGY_SEGMENTS group∈{1,2}）→ BENCHMARK_GROUPS 对标分组 → 行业车种，逐段 `(vehicle IN … AND energy = … AND insurance = …)` 取 OR——需要 vehicle/energy/insurance 三维列。
  - 三列只有表12（BI 行业快照）有：t12Ctes 恒取 `caliber='投保'`（:461，即签单考核口径）；起保口径走表3（IndustryMonthlyHistoryFact），粒度省×险种×主体，SQL 里 vehicle/energy 恒 `CAST(NULL …)`（:583-584）——低风险谓词在表3 行恒不命中、全被滤除。
  - 故「低风险」事实上只能编码在签单（投保）口径上；前端口径切换提示同证（签单=「行业快报投保口径，不借起保月源补历史」；起保=「全部取月报表3，只到省×险种，非考核口径」，IndustryPivot.tsx:543）。
- 边界注记: 「起保口径＋低风险」组合现无显式拦截（路由不 400、前端 Tabs 无 disabled、hook 无过滤）——后端结果恒空集（fail-visible 空而非报错）。若 owner 要求该组合显式拒/禁，可作小改项登记。
- 对 Q1 的修正: owner 问句方向指向行业对标既定的「低风险＝战略1+2」定义——AUQ Q1 推荐项已改为「战略 1+2 段并集」（与行业对标同口径，SSOT 复用 strategy-segments.ts）。

## 探索 #3（2026-10-23 · 补充录入探查：互斥规则／燃料多选／多选原则波及面）

- 探查锚: chexian-api @ 918e6d75b（同日）
- **低风险互斥精确规则**（战略段探查，strategy-segments.ts:129-174）：战略 1=s1a/b/c（非营业个人客车×油/电×交强/商业），战略 2=s2a-f（非营业机关客车＋非营业企业客车×油/电×交强/商业）——1+2 客类覆盖=**家用/机关/企业**三 chip（行③）；互斥禁选=行③其余 5 chip＋行④全部 13 段。互斥机制先例=渠道板块 applyChannelToggle（组内互斥单选替换＋跨组叠加），但方向相反——此处是「低风险 vs 非成员组互斥、成员组内可叠加」，需新互斥组语义（双向禁选而非点选替换）。
- **燃料维油气并选**：现三态循环 toggle（config.ts:250 FUEL_CATEGORY_TOGGLE，全部→电→气→油单值循环；分域能力三形态 full/electric-only/no-gas）；后端 `filter-params.ts:101` fuelCategory 单值 enum。改多选=前端 toggle→多选 chips＋QuickFilters 数组化（channelSection/vehicle_quick_filter 数组化先例，含口径胶囊旧快照归一）＋后端 csv 化＋SQL 三分支互斥改 IN。油+气=非新能源（NOT electric）语义自然。续保域 fuelCategories csv 已有后端先例（renewal pivot）。
- **「>2 选项一律多选」原则波及面**：燃料维 3 值（单选→多选，owner 点名）；筛选向导风险维 9 值（guide-questions.ts insuranceGrade type:'single'→multi；主抽屉已多选✓ data.ts:129 insurance_grade: string[]）；报价转化域 renewalType 3 值（报价 pivot 端点单选 enum，评估随域改造）。已多选无需动：险别（10-16 已裁）/渠道/车型/机构/团队/业务员/客类/风险主抽屉/厂牌。险类（交/商 2 值）与新续转（续/转 2 值）不超 2，保持单选。
- 转正映射建议（owner 2026-10-23「按照你的建议办」采纳）: 1 issue 拆 3 PR——①更名＋低风险互斥段（互斥组机制＋注册表复用战略段 SSOT＋完整段语义 Q3=A）②燃料维多选（前后端数组化＋快照归一）③向导单选维多选化（风险维等小项）；>2 选项原则作为筛选器交互上位规则随口径原则 v2 一起登记业务规则字典。待「开工 011」令即按 build 协议执行。
- 验收: `bun run typecheck`＋前后端段对账测试＋口径胶囊旧快照归一测试（fuelCategory 数组化迁移）绿。

## 探索 #4（2026-10-23 · 开工增量重探）

- 锚漂移: 918e6d75b → b0706e1cf（3 提交：#2478 verify os-p2 / #2465 VCR 台账 / #2504 ADR 追记），全在 operating-loop/VCR/verify 域。
- 零波及两法同证: ①`git diff --stat` 27 文件全列无 011 文件面；②`git diff --name-only | grep -iE 'filter|vehicle|quick|fuel|guide-question|strategy-segment'` 零命中（exit=1）。探索 #1–#3 结论全部有效，锚平移 b0706e1cf。

## 开工记录

（未开工）

### 完成度核实（2026-10-23，owner 问「怎么没结束、已经完成了吧」触发）

**结论：目标仓零实施，scouted 状态准确。** 三层证据：①origin/main 及全分支 git 历史 grep「客群/低风险/燃料多选」无相关提交（搜到的「客群」均为 KPI 聚焦卡 #2003/机构页模板 #2191 等早于本卡的无关特性，「低风险」为行业对标既有功能）；②开放 PR 30 条无相关；③代码现状：guide-questions.ts:158 仍 `label: '车型'`、FuelCategory 仍单值联合（config.ts:72）——更名/互斥段/燃料多选三项均未动。owner 或系将本卡这几轮的探索+裁定+采纳（flint 侧工作，确已完成）记为目标仓实施；转正 1 issue 3 PR 仍待「开工 011」。

### 讨论史核查（2026-10-23，owner 问「之前讨论过吗」触发）

011 四项改动均为 10-23 新提、无先前同题讨论；但存在三层**概念级先例**（owner「已完成」印象的可能来源）：

- 「客群」术语：KPI 聚焦卡「客群轴」已上线（#1990/#2003，2026-10-13，客群=客户类别轴呈现名）；#2460 OPEN（10-22）＝KPI 聚焦卡**计划域**多选/客群承接需后端 CSV-IN（#1959 遗留薄指针卡，未实施）——与筛选器更名不同事，但后端 csv 改造同族，实施时可并看。
- 「低风险」定义：行业对标页「低风险（1+2）」已上线（战略1+2 范围切换）；客群维加低风险互斥段是本卡新提。
- 燃料筛选：#1086（筛选器重建首批，10-02 完成）讨论的是油气**精确回放**（URL 同步不丢值），非并选；并选是本卡新提。
- 「>2 选项一律多选」原则：无先前讨论，本卡新提。
