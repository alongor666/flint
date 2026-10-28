# 029 · 任务流派工步接飞书：机构群派发 + 飞书任务台账表

- 状态: scouted（2026-10-28 深探完成，实施要点到 PR 方案深度，随时可转正）
- 目标仓: chexian-api（先探可定：八步任务流代码域 `server/src/operating-loop/` 已随 023 shipped；飞书发送层 `server/src/services/feishu-app-client.ts` 既有）
- 类型: 功能
- 录入: 2026-10-28

- 关联: [028](028-home-loop-taskflow-pill.md)（本卡＝其「一键派工」动作的真实外发后端，028 转正接线时消费本卡交付的 API）；[023](023-loop-process-state-into-repo.md)

## 原文

> 我可能在任务流和相关预警的指标页面加入派工功能，即通过飞书发送消息到对应的机构、业务单位群，文字部分和飞书任务表，是否可行？

同日拍板（对话原话归纳）：

- 派工对象：按任务 org_level_3 自动路由到对应经营群
- 任务表形态：所有派工进一张常驻台账表
- 派工权限：山西车险部经理、山西分管车险副总两人（生产已核实：账号在用＋飞书扫码登录）
- 重发语义：同任务重复派发＝催办（留痕多条）

## 澄清（AUQ）

真分叉四项均已答（见原文段）。页面触点：另一会话迭代的 taskflow-vcr 原型（八步：预警→诊断→派工→认领→合议→决策→实施→复盘；派工按钮＝机构卡单家派工＋全省一键批量），原型转正后接入。

## 讨论

- 与 023 关系：023（流程与状态引入）已 shipped；本卡＝派工步的真实外发通道，同流程族增量，非同源重录。
- 前置链 2026-10-28 已全部实测就绪（见探索 #1）。

## 探索 #1（2026-10-28）

- 探查锚: chexian-api @ origin/main（群映射文档 PR #2715 待合并）
- 防撞车: 目标仓无「派工飞书化」既有 issue/PR；发送层与通知 outbox 均有先例（待办任务包派发样板）
- 实施要点:
  - 群映射：机构→群 chat_id 清单已建（目标仓 `开发文档/飞书群映射.md` 指针；真实数据为运行时资产不进 git）
  - 凭据：飞书应用「应用开发助手」凭据已配目标仓生产 env（FEISHU_DEVASSIST_*，token 获取已验证）
  - 任务台账表：该应用 bot 身份对多维表格 建/读/写/改/删 全链实测通过（2026-10-28，测试表已清理）
  - 发送层：既有发群函数按凭据参数化零改动复用（text/post＋message_id 回执）
  - 权限：两人账号白名单走配置禁硬编码；催办＝同任务多行留痕
- 转正映射建议: 1 issue、2 PR——①服务端派工 API＋台账表首建（不依赖页面可先行）；②页面转正后按钮接线（随 023 后续 UI 线）
- 验收: 派工一条真实任务 → 目标群收到文字＋表格链接 → 台账表落一行 → outbox 留 message_id → 重复派工＝催办多行


## 探索 #2（2026-10-28 深探 · scout 协议）

- 探查锚: chexian-api @ origin/main b131d495（PR #2709 已合并；PR #2715 文档待合并）
- 防撞车（两法同证）:
  - 法一 grep 派工/dispatch：既有派工链共两条——日报任务包（`routes/daily-report-task-pack.ts`）与 VCR 行动项派工（`services/vcr-action-dispatch.ts`，CX-ADR-0b9663）——落点均为**单一派工体验群**（百晓生通道）+ SE 任务台账；
  - 法二 grep feishu_chat_groups / DEVASSIST：origin/main 仅文档命中（PR #2715 两文件），零代码消费点。
  - 结论：机构群分发（13 群×org 路由×应用开发助手）为全新增量，非重复。
- 实施要点（文件级）:
  1. `server/src/config/env.ts`：FEISHU_DEVASSIST_* 读取器（生产 env 已配并验证）+ 派工权限账号清单（两人，env 配置禁硬编码）
  2. `server/src/services/feishu-app-client.ts`：零改动（sendFeishuAppPostMessage/sendFeishuAppChatMessage 已参数化凭据，token 缓存按 appId 分桶）
  3. 群路由模块（新）：读 `server/data/feishu_chat_groups.json`（org_level_3→chatId，运行时资产；缺机构 fail-visible）
  4. `schemas/notification-outbox.schema.ts`：OUTBOX_SCENE_EXTRA_CHANNELS 加新通道枚举（照抄百晓生「只加宽行级、不并全集」先例，不污染既有 scene）
  5. `services/notification-dispatch.ts`：新增 devassist 机构群派发函数（message_id 回执）
  6. 派工编排 service（新，对齐 vcr-action-dispatch 形态）：派工条件（预警/危险档且环比恶化，028 裁定）→ org→群 → 台账 → outbox
  7. 路由：operating-loop.ts 新端点（权限=两人白名单）
- 台账口径（转正时须 owner 确认一项）: 【断言】既有 VCR 飞书台账（`services/vcr-ledger-sync.ts`，SE 折叠态唯一事实源+client_token 幂等+每日 sweep）已是事实上的任务台账；owner 拍板的「一张常驻台账表」三种落法——A 扩展 VCR 台账加分发列（合「无第二本账」铁律，倾向）／B 独立分发表（每派发一行，催办多行天然成立）／C outbox 即分发台账（message_id 已留痕，无飞书侧表格）。028 原型展示了「表格链接」交互 → 若需要飞书侧可点表格，B 或 A。
- 催办语义调和（028×029）: 028 裁定「已派工不重复发」与 029「重复派发=催办」不冲突——正常态幂等（同任务同日不重发，抄 vcr-action-dispatch 日粒度 dedupe 先例），显式点「催办」=人工动作落新行（新 dedupe_key）。
- 风险: 群发仅需 bot 在群内（13 群已全在，实测），无可用范围风险；@机构负责人需本域 open_id 反查（跨域存量不可用，已实证），首版可不做 @。
- 转正映射建议: 1 issue「任务流派工飞书机构分发（029 后端通道）」＋1 PR；028 转 PR②（页面接线）Closes 同域相邻 issue，两线在 operating-loop 域协作不互堵。
- 验收: 白名单账号 curl 派工端点 → 目标机构群收到文字＋表格链接 → outbox 行含 message_id → 同任务当日重发幂等、显式催办落新行 → 非白名单 403 → 缺机构映射 fail-visible。


- **双目标分发（owner 2026-10-28 追加裁定）**：一次派工动作同时发两类目标——①派工体验群（既有百晓生通道，定位=项目组观察渠道，**后续看情况停止**，停法=配置下线不动代码）②13 个机构经营群（本卡新通道，org 路由）。outbox 按通道粒度各自留痕（既有「渠道粒度幂等，只补缺失通道」先例直接适用）；既有自动派工链（vcr-action-dispatch 生产 cron）与页面手动派工均按双目标执行，细则实施规划定。

## 勘证补充（2026-10-28 · owner 质询「体验群是否非真实生产」）

- 【事实·实测】运行态=真实生产：VCR 派工挂每日发布链自动跑，生产 outbox 近两月 dispatch_task_pack 12 行、当日仍有 2 条 sent（SE 任务编号真实）。
- 【事实·实测】受众面=试点覆盖：目标群「派工体验群」实测 10 人+1 bot（群详情接口），派工单从未到达机构经营群；机构侧认领环节无感，靠体验群内 SE 回执线上闭环。
- 【断言】029 定位校准：非新建派工链，而是给已生产化的派工发动机换上真实受众（机构群分发）——转正价值更清晰。

## 开工记录

（未开工）
