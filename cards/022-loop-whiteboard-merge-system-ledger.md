# 022 · 问题处理闭环：手工跟踪表与系统派工台账合流（PR #2679）

- 状态: discussed（owner 2026-10-28 点名「探索卡 022」；探索 #1 完成，R1-R3 待裁）
- 目标仓: chexian-api（先探已定：PR #2679 与系统派工链均在该仓，见「澄清」依据）
- 类型: 优化（流程资产指针修正与合流说明，改动面为文档级）
- 录入: 2026-10-28

## 原文

> 问题处理闭环「登记表与系统台账合流」——PR #2679 登记的手工跟踪表应切换为系统在用的派工台账。

## 澄清（AUQ）

- Q1 跟踪表归属方案: 甲（推荐）修订 PR #2679 把派工跟踪表指针切到系统在用台账 / 乙 保留 PR 表为人工兜底并行 → **owner 答: 待答（非交互登记，停 clarifying）**
- 先探可定字段与依据：
  - 目标仓 = chexian-api：PR #2679 为该仓开放 PR（021 卡探索防撞车扫描亦在 chexian-api 开放 PR 列表见到 #2679）；系统派工链代码在该仓 `server/src/operating-loop/services/`（daily-report-task-pack.ts / vcr-action-dispatch.ts / vcr-ledger-schema.ts / vcr-ledger-writeback.ts）。
  - 类型 = 优化：诉求是资产指针修正＋流程文档补说明行，非新功能开发。
- 非交互假设清单（owner 纠偏即停）：
  1. 「体验问题处理闭环·派工任务跟踪表」与「经营总览·派工任务跟踪表」确认为两张并行表、零连接（2026-10-28 实查）。
  2. 甲/乙取舍留 owner 裁定，卡内推荐甲仅是初评建议，不预设结论。
  3. 合议/实施/复盘三个未接环节不在本卡范围（如需另立卡）。

## 讨论

**先探核实（2026-10-28 实查，录入会话完成）**

- PR #2679（OPEN 未合并）登记飞书侧「问题处理闭环体系」四件资产：流程画板（八步：预警→诊断→派工→认领→合议→决策→实施→复盘）、步骤详情表、派工任务跟踪表、机构通讯录；PR 自述「尚未与本仓代码深度集成」。
- 实查发现两张表并存、零连接：
  - PR 登记的「体验问题处理闭环·派工任务跟踪表」10 行记录全部为空（手工模板）。
  - 系统已有自动派工链在生产：日报任务包/变动成本率行动项自动派工（`server/src/operating-loop/services/daily-report-task-pack.ts`、`vcr-action-dispatch.ts`）落到「经营总览·派工任务跟踪表」（18 列台账，schema 见同目录 `vcr-ledger-schema.ts`；飞书侧改状态可回写平台状态机，见 `vcr-ledger-writeback.ts`）。
  - 风险：运营会在两张表各记一套，漂移不可避免。
- 八步实现状态实查：预警/诊断/派工/认领四步系统已贯通（网页触点＋VCR 矩阵＋spark-echo 回执状态机）；合议/实施/复盘三个飞书触点（会议纪要/公司系统/知识库沉淀）系统未接。

**候选方案（供 scout 阶段展开）**

- 甲（推荐，最省力）：修订 PR #2679（或其后续 PR）把 §1.3 派工任务跟踪表的指针改为系统在用的「经营总览·派工任务跟踪表」——表格指针只写「生产 env 配置/见 vcr-ledger-schema.ts 同族配置」级别，凭据不入公开仓；画板、步骤表、通讯录保留；同步在画板或步骤表加一行「派工跟踪以系统台账为准」。改动量＝文档级，预计半小时内；运营侧零迁移（系统台账已在生产循环）。
- 乙（不推荐但记录）：保留 PR 表为人工兜底、表头注明与系统台账并行——需人工纪律防漂移，两表漂移风险未除。

**范围与边界**

- 合议/实施/复盘三个未接环节不在本卡范围，如需另立卡。
- 目标仓实施走 chexian-api PR-first 协议，转正必建 issue，AI 不执行合并。

**防重与并卡**

- 建卡前已读 REGISTRY.md 全表并 grep「闭环/派工/台账/跟踪表/external-ops/2679」：无同源卡。命中均为无关上下文——017 是观赔域导航拆分，021 仅在防撞车扫描把 #2679 列入 chexian-api 开放 PR 名单（未承载其内容），011/002 的「台账」是锚漂移提交名与图表台账页口径。故新增本卡，无并卡关系。

## 探索 #1（2026-10-28 · owner 点名「探索卡 022」触发）

探查锚：chexian-api @ ccec01651（2026-10-28 origin/main）；flint 本卡。证据纪律：事实/断言二分，方法学标注；零命中结论两法同证。

**关键事实更新【实测】**：PR #2679 已于 2026-10-28T03:03Z 合并（merge commit 4edbbe413，`gh pr view 2679 --json state,mergedAt` 复核）——甲的实施形态由「修订未合 PR」改为「新 PR 修 main」。

**main 现状锚【静态核实】**：`数据管理/knowledge/external-ops-assets.md` §1.3 派工任务跟踪表＝行 40-50（资源名称 行44、base_token 行47、字段结构 行49）；§2 防漂移规则第 5 条「数据隔离」＝行75（复核 `git show origin/main:数据管理/knowledge/external-ops-assets.md | grep -n "派工任务跟踪表"`）。

**系统台账配置锚【静态核实】**：`server/src/operating-loop/services/vcr-ledger-bootstrap.ts:5`——env 未配 `VCR_LEDGER_BITABLE_APP_TOKEN`/`VCR_LEDGER_TABLE_ID` 时自动建 base；表名锚 `vcr-ledger-schema.ts:18,21`（「经营总览·派工任务跟踪表」/「VCR 行动项」，owner 可在 console 改名、同步按 app_token 寻址不受影响）；回写链 `vcr-ledger-writeback.ts`（event_id 去重可重入，见 :249 附近注释）；派工落任务 `daily-report-task-pack.ts:9,29,41`；行动项第二出口 `vcr-action-dispatch.ts` 存在（ls 实测）。

**防撞车【两法同证·均零命中】**：法一 `git grep` origin/main——「体验问题处理闭环」零命中、「external-ops」仅 #2679 已合四文件自身；法二 `gh search prs/issues --repo alongor666/chexian-api "派工任务跟踪 OR external-ops" --state open` 零命中 → 无并行实现、无在途冲突。

### 甲 · PR 实施方案（R1 终选后执行；目标仓纪律：转正必建 issue、PR Closes #N、推送前 `bun run governance`、AI 不合并）

1. 改动面（单 PR，≤2 个仓内文件，文档级）：
   - `数据管理/knowledge/external-ops-assets.md` §1.3（行 40-50 重写）：资源名称→「经营总览·派工任务跟踪表（系统在用）」；链接/表标识换为系统台账实际值——值从生产 env `VCR_LEDGER_BITABLE_APP_TOKEN`/`VCR_LEDGER_TABLE_ID` 读取后按既有条目风格写入该私有仓文档（值不入本公开卡），并注「与生产 env 对齐，变更走本文件同步规则」；字段结构行→「18 列，schema SSOT＝vcr-ledger-schema.ts」；加一行「状态列由系统回写链维护，人工只读」。
   - 同文件 §2 第 5 条（行75）补注：「系统台账运营数据同样仅在飞书侧、不进 warehouse——数据隔离规则不变」，防误读合流＝数据入仓。
2. 画板加注＝R2 待裁：若采纳「画板/步骤表加一行『派工跟踪以系统台账为准』」，按 `.claude/rules/process-whiteboard-ssot.md` 三件套先改飞书画板→同步 SVG 备份→更新本文件；若裁「仅改 md」，则本 PR 只做上两条。
3. 手工空表处置＝R3 待裁：飞书侧改名「（停用·已被系统台账取代）」或归档；属飞书操作非仓内改动，PR 内只登记处置结果一行。
4. 验收命令（PR 描述贴执行结果）：①`git show origin/main:数据管理/knowledge/external-ops-assets.md | grep -n "经营总览"` 命中 §1.3；②`lark-cli base +base-get` 系统台账 200 且名称一致；③`bun run governance` 绿；④手工表 `lark-cli base +record-list` 确认处置态。
5. 风险：①生产 env 键名/取值以 bootstrap 实现为准，实施时从生产读取，拿不到则降级为「见生产 env」指针（与登记口径一致）；②换表需知会原表读者（车险部，运营动作在 PR 外）；③【断言·代码推理】台账改名不破坏指针（按 app_token 寻址，schema :20 注释）。

### 待裁（转正前）

- R1 甲/乙终选（推荐甲）。
- R2 画板是否加注（走三件套）或仅改 md 指针。
- R3 手工空表处置：改名停用 vs 归档。

### 粒度映射（转正时定）

单卡单 issue 单 PR（≤2 文件、无代码逻辑）；不与他卡并。

## 开工记录

（未开工）
