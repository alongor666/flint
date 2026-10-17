# 007 · alongor666-skills 周更发布说明工作流

- 状态: scouted
- 目标仓: alongor666-skills（repos.md 已登记：本地 ~/workspace/alongor666-skills）
- 类型: 工程流程（变更整理 / 发布说明）
- 录入: 2026-10-18

## 原文

目标仓是alongor666-skills，整理已合并的 PR 和 commit，按功能、修复、体验及工程改进分类，同时生成团队版和面向用户的精简发布说明。只读分析，只使用可验证的仓库事实；证据不足时明确说明，不推测，也不修改代码或外部状态。我想每10个skill派1个agent，共派10个agent去做，但更好的方式是什么？纯探索与研究，以后每周我都想发布

## 澄清（AUQ）

非交互假设清单（owner 纠偏即停，不以未反对为开工前提）：

- 首期窗口 = 开仓全历史。依据：gh release 空 + 仓内无 repo 级 CHANGELOG/RELEASES（两法同证，2026-10-18），唯一 tag 为 archive/xcl-vi-v1.5.0-tool-constitution 归档锚，无发布基线。周更窗口 = 上次发布锚 → 数据日。
- 发布物落点未定（目标仓 docs/RELEASES.md 走 PR vs GitHub Release vs 仅会话产出），待下令转正时拍板。
- 「用户版」受众按 README 事实双读：外部技能安装者（npx skills add）+ 公司同事（knowledge-base 有 30 分钟上手指南）；本期两读兼顾。

## 讨论

- 与既有卡（001–006 全 chexian-api）无语义同源，不并卡。
- 派发策略裁定（本轮实跑验证）：否决「每 10 skill × 10 agent」静态网格——发布说明的单元是变更（PR/commit）不是技能存量，静态网格重复读未变内容、且跨技能 PR 无桶可归。改三层管线：L0 脚本清单（0 agent）→ L1 仅对证据薄弱带 fan-out → L2 单点综合。本期实跑 L0 覆盖 ~95% 证据，仅派 1 个 Explore agent 补 docs/ 资产带（无 frontmatter 可榨）。

## 探索 #1（2026-10-18）

- 探查锚: alongor666-skills @ 98134fd（origin/main，2026-10-18 fetch）
- 防撞车: 发布面空白（gh release 空列表 + find 全仓仅技能级 CHANGELOG×2 与 CI workflow，两法同证）。
- 数据面（命令可复核）: 已合并 PR 426 个（`gh pr list --state merged --base main` 计数；#217 合入非 main 分支不计）；#1 2026-05-30 → #453 2026-10-17；659 commit 含 119 个无 PR 直推（集中于 PR 制确立 #76 之前、xcl-vi 历史库数据入库、IMPROVEMENTS 台账豁免追加 #299/#318 三类）；技能 7 → 98 个；PR 月度分布 3/42/55/126/134/67（5–10 月）；类型分布 feat 134 / fix 128 / docs 98 / chore 15 / refactor 9 / ci 7 / test 6。
- 实施要点: 周更命令序列（详见卡尾附录）；分类轴 = 功能/修复/体验/工程改进，团队版带 PR# 锚、用户版只留可感知项；docs(kb/派工档案/evolution) 为内部维护资产，用户版只取参考价值（Explore agent 盘点锚 docs/knowledge-base/00-总览/README.md、docs/dispatch-prompts/README.md）。
- 转正映射建议: 单 issue 单 PR——目标仓建 docs/RELEASES.md（或 GH Release）模板化双版结构，周更按锚增量追加。
- 验收: 文档条目数与 `gh pr list --base main` 锚点窗计数对账；抽 3 条 PR# 反查 git show 标题一致。

## 开工记录

2026-10-18 探索轮实跑（即本卡探索 #1）：首期全量发布说明两版于会话交付，未动目标仓任何状态（本地 clone 既有未提交改动原样保留）。

## 附录：周更命令序列（锚 = 上次发布 SHA 或 tag）

```bash
cd ~/workspace/alongor666-skills && git fetch origin
gh pr list -R alongor666/alongor666-skills --state merged --base main \
  --limit 200 --json number,title,mergedAt --jq '.[] | "\(.number)|\(.mergedAt[0:10])|\(.title)"'
git log <上次锚>..origin/main --first-parent --oneline          # 直推与 squash 混合面
git log <上次锚>..origin/main --format= --name-only | grep '^skills/' | cut -d/ -f2 | sort -u   # 本周变更技能集
```

派发阈值：变更技能 ≤15 且标题规范 → 0 agent（主会话直写）；>15 → 按变更集分批 fan-out（每批 15–25 技能，非固定网格）；标题含糊 PR → 单发 1 读档 agent。
