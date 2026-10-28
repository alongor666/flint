# 022 · 问题处理闭环：手工跟踪表与系统派工台账合流（PR #2679）

- 状态: building（owner 2026-10-28「开工 022」；主会话曾误用主检出旧版卡问出甲方案，owner 知情重申后终裁＝甲′镜像方案，甲作废）
- 目标仓: chexian-api（先探已定：PR #2679 与系统派工链均在该仓，见「澄清」依据）
- 类型: 优化（流程资产指针修正与合流说明，改动面为文档级）
- 录入: 2026-10-28

## 原文

> 问题处理闭环「登记表与系统台账合流」——PR #2679 登记的手工跟踪表应切换为系统在用的派工台账。

## 澄清（AUQ）

- Q1 跟踪表归属方案: 甲（推荐）/ 乙（兜底并行）→ **owner 答（2026-10-28 AUQ 第一轮·自定义）: 二者同步——多维表与系统台账保持一致且自动更新**（升级为甲′，第二轮拍定承载与方向，见探索 #1 裁定落账）
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

**系统台账配置锚【静态核实】**：`server/src/operating-loop/services/vcr-ledger-bootstrap.ts:5`——env 未配 `VCR_LEDGER_BITABLE_APP_TOKEN`/`VCR_LEDGER_BITABLE_TABLE_ID` 时自动建 base；表名锚 `vcr-ledger-schema.ts:18,21`（「经营总览·派工任务跟踪表」/「VCR 行动项」，owner 可在 console 改名、同步按 app_token 寻址不受影响）；回写链 `vcr-ledger-writeback.ts`（event_id 去重可重入，见 :249 附近注释）；派工落任务 `daily-report-task-pack.ts:9,29,41`；行动项第二出口 `vcr-action-dispatch.ts` 存在（ls 实测）。

**防撞车【两法同证·均零命中】**：法一 `git grep` origin/main——「体验问题处理闭环」零命中、「external-ops」仅 #2679 已合四文件自身；法二 `gh search prs/issues --repo alongor666/chexian-api "派工任务跟踪 OR external-ops" --state open` 零命中 → 无并行实现、无在途冲突。

### 甲 · PR 实施方案（R1 终选后执行；目标仓纪律：转正必建 issue、PR Closes #N、推送前 `bun run governance`、AI 不合并）

1. 改动面（单 PR，≤2 个仓内文件，文档级）：
   - `数据管理/knowledge/external-ops-assets.md` §1.3（行 40-50 重写）：资源名称→「经营总览·派工任务跟踪表（系统在用）」；链接/表标识换为系统台账实际值——值从生产 env `VCR_LEDGER_BITABLE_APP_TOKEN`/`VCR_LEDGER_BITABLE_TABLE_ID` 读取后按既有条目风格写入该私有仓文档（值不入本公开卡），并注「与生产 env 对齐，变更走本文件同步规则」；字段结构行→「18 列，schema SSOT＝vcr-ledger-schema.ts」；加一行「状态列由系统回写链维护，人工只读」。
   - 同文件 §2 第 5 条（行75）补注：「系统台账运营数据同样仅在飞书侧、不进 warehouse——数据隔离规则不变」，防误读合流＝数据入仓。
2. 画板加注＝R2 待裁：若采纳「画板/步骤表加一行『派工跟踪以系统台账为准』」，按 `.claude/rules/process-whiteboard-ssot.md` 三件套先改飞书画板→同步 SVG 备份→更新本文件；若裁「仅改 md」，则本 PR 只做上两条。
3. 手工空表处置＝R3 待裁：飞书侧改名「（停用·已被系统台账取代）」或归档；属飞书操作非仓内改动，PR 内只登记处置结果一行。
4. 验收命令（PR 描述贴执行结果）：①`git show origin/main:数据管理/knowledge/external-ops-assets.md | grep -n "经营总览"` 命中 §1.3；②`lark-cli base +base-get` 系统台账 200 且名称一致；③`bun run governance` 绿；④手工表 `lark-cli base +record-list` 确认处置态。
5. 风险：①生产 env 键名/取值以 bootstrap 实现为准，实施时从生产读取，拿不到则降级为「见生产 env」指针（与登记口径一致）；②换表需知会原表读者（车险部，运营动作在 PR 外）；③【断言·代码推理】台账改名不破坏指针（按 app_token 寻址，schema :20 注释）。

### 甲′ · AUQ 两轮裁定落账（2026-10-28，R1 从甲/乙升级）

**裁定（结构化提问两轮收拍）**：
- R1＝**甲′：两张表并存且自动同步**（owner 自定义答案，非甲非乙）：闭环体系跟踪表改造为系统台账的镜像，保持一致且自动更新。
- 承载方（第二轮拍定）＝**chexian-api 数据管理/integrations 集成区**新增镜像脚本（先例＝lark_network_outlet/sync_ledger.py 挂每日发布链），chexian-api 内走 PR 评审。
- 方向（第二轮拍定）＝**单向只读镜像**：系统台账→闭环表；闭环表只展示不可编，编辑与回写仍走系统台账（SE 状态机唯一事实源不破，现有回写链不动）。

**甲′ PR 实施方案（更新探索 #1 的甲方案）**：改动面＝1 个新脚本 + env 配置 + external-ops-assets.md §1.3 注记 + 测试，非纯文档级（约半天—一天）：
1. `数据管理/integrations/vcr_ledger_mirror/sync_mirror.py`（照网点台账先例）：源＝系统台账（生产 env `VCR_LEDGER_BITABLE_APP_TOKEN`/`VCR_LEDGER_BITABLE_TABLE_ID`），目标＝闭环表（KdQPb8…，值入私有仓配置不入本卡），经飞书 API 读源 upsert 目标，幂等键＝派工编号；挂每日发布链（或 mini cron）。
2. 目标表 schema 改造为与台账同构（18 列）；旧 10 列中的口径定义行（影响度公式/状态/趋势/用时口径）迁 external-ops-assets.md §1.3 注记保留，不丢口径。
3. 验收：跑后两表行数一致＋抽行字段一致；失败走既有告警通道；`bun run governance` 绿。
4. 旧「甲（纯指针切换）」降为甲′的子集步骤（§1.3 注记仍要做）；乙作废。

**假设清单（owner 纠偏即停）**：
1. R2（画板加注）默认**不加注**：甲′下触点类别与实体均未变（还是那张多维表且始终新鲜），不属画板 SSOT 规则所列结构变更。
2. R3（空表处置）**消解**：空表不作停用/归档，改造为镜像目标表（见上 2）。
3. 同步频率默认＝随每日发布链一次；如需小时级另催。
4. 闭环表对读者只读的约束靠列说明＋运行规约（飞书侧无硬锁），表头注明「系统镜像·勿手工编辑」。

### 待裁（转正前）→ 已收（2026-10-28 AUQ）

- ~~R1 甲/乙终选~~ → 甲′（见上裁定落账）；~~R2 画板加注~~ → 假设不加注（假设 1）；~~R3 空表处置~~ → 消解为镜像目标表（假设 2）。无剩余待裁。

- R1 甲/乙终选（推荐甲）。
- R2 画板是否加注（走三件套）或仅改 md 指针。
- R3 手工空表处置：改名停用 vs 归档。

### 粒度映射（转正时定）

单卡单 issue 单 PR（≤2 文件、无代码逻辑）；不与他卡并。

## 开工记录

- 2026-10-28 owner「开工 022」。开工时清算一次裁定冲突：主会话误用主检出旧版卡（落后 origin/main 三提交，未含 7a22772 甲′落账）问出 R1=甲/R2=画板加注/R3=改名停用；owner 知情（甲′已两轮 AUQ 落账）后终裁＝**甲′镜像方案**，刚才的甲作废。
- 开工实查补证（2026-10-28 lark-cli/gh）：①手工表仍在（base-get 200，名称「体验问题处理闭环·派工任务跟踪表」，记录仍空模板行，零变化）；②PR #2679 纯文档登记（4 文件，零飞书写操作）；③chexian-api origin/main=ccec01651 与探索锚一致无漂移，开放 PR 无撞车。
- 按 build.md 标准流程执行：建 issue → 切分支 → 规划 → 闸① DSH 对抗评审 → 实施+PR+governance → 独立评审 → 闸② → 可合并就绪交 owner。
- 闸①第一轮（DSH·glm-5.3 strong 档，2026-10-28）：REQUEST_CHANGES，13 findings（4×P1）——其中 F-05 勘误本卡：源表 env 键名实为 `VCR_LEDGER_BITABLE_TABLE_ID`（本卡原文漏 BITABLE，server 真实键名见 vcr-ledger-config.ts:31），卡内两处已改。其余 findings 全量吸收进目标仓规划文档修订版。
- 闸①第二轮 REQUEST_CHANGES（1×P2 判据双锚 + 5×P3 顺修，N-06 跨仓提示经 `git show origin/main:` 实证不成立——评审者 web_fetch 命中 CDN 缓存）；第三轮确认轮 **APPROVE**。规划定稿 `docs/plans/2026-10-28-vcr-ledger-mirror.md`。
- **实施**：issue [#2703](https://github.com/alongor666/chexian-api/issues/2703) → 分支 `i/2703` → PR [#2705](https://github.com/alongor666/chexian-api/pull/2705)。改动面＝新集成 `数据管理/integrations/vcr_ledger_mirror/`（bootstrap/sync_mirror/README）+ client.py 两薄封装（delete_field/list_views）+ sync-and-reload.mjs Stage 5.6 扇出 + external-ops-assets.md §1.3 镜像化注记 + 10 用例测试（含 schema.ts 机器对拍锁）+ 治理随行（lineref 基线、钉子登记）。
- **双路独立评审**：DSH 第一路 APPROVE（6×P3）、编排端子代理第二路 REQUEST_CHANGES（1×P2 视图清单导出缺席 + 5×P3）→ 11 项修复一并吸收；**闸②主会话亲评通过**（对拍锁经真实 schema.ts 实测 18 列/七态/三态全提取；并集/防御/双向差集闭环核验）。
- **密钥闸事件与根治**：bootstrap.py 曾硬编码目标表 bitable 标识（代码赋值形态）触发 gitleaks generic-api-key + GitGuardian（#2679 同值在文档表格形态放行）→ 根治＝目标标识参数化 `--target-*`（标识不入代码，同先例纪律）；分支重建净历史。**第一次重建踩坑**：第三轮 DSH 评审在 worktree 内 `git fetch` 前移了 origin/main，随后 `reset --soft origin/main` 把新 main 设为父而树仍旧基线——产出「回退 main 6 个 merge」假象（CI typecheck 红）；从 ce4f（树与父链均正确）重做修正。
- **追 main 两次 rebase**：①#2700 主页重构等 6 merge（无冲突）；②+20 提交含 #2707 同批抬钉子 1315 → 冲突解为叠加 1316/1029（登记行保留双方）。
- **验收**：判据 4（`test:py` 10 passed + governance 107/107）✅、判据 5（双锚 1/1）✅、A.0 检视留痕（lark-cli 实测 11 字段+5 组 select 枚举录 §1.3）✅；判据 1/2/3（dry-run/execute 对拍/field-list 18 列）**留 mini 现场**——前置 R6（owner 飞书 console 给本机探索应用加源 base 只读协作者）+ 生产 env `VCR_LEDGER_BITABLE_APP_TOKEN`/`VCR_LEDGER_BITABLE_TABLE_ID` 登记，步骤见 README「启用步骤」。
- **状态**：PR #2705 CI 全绿（第二次 rebase 后重跑中）、可合并就绪，**合并权在 owner**。合并后：① mini 现场 bootstrap+首跑；② 卡转 shipped。
- 教训（写入记忆）：①`reset --soft origin/main` 前核实本地 origin/main 未被评审者 fetch 前移（worktree 共享 .git，评审者 fetch 即全局生效）；②重建历史禁 `git add -u`，一律点名文件；③bitable 标识不进代码常量（文档表格放行、代码赋值判 secret）；④全仓计数钉子是热点冲突区，追 main 后在对方新值上叠加而非覆盖。
