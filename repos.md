# 目标仓路由表

owner 有权读写的仓登记在此，新仓加入说一声即可登记。
目标仓协议以其根 AGENTS.md 为准，本表只是路由提示；两者冲突以目标仓为准。

| 仓 | 本地路径 | 远端 | 默认分支 | 转正通道与注意 |
|----|---------|------|---------|---------------|
| chexian-api | /Users/xuechenglong/workspace/chexian-api-dev | github.com/alongor666/chexian-api | main | PR-first：从 origin/main 切分支，合并前独立对抗评审并修完 P0/P1，推送前 `bun run governance`；开工前必读其根 AGENTS.md。转正必建 issue（该仓纪律：工作项住 issue 树，此原则不豁免；多卡可并一个 issue，PR 以 Closes #N 关联完成；issue 打 type/*+area/* 标签族）。**禁 auto merge / AI 合并（owner 2026-10-24）：AI 只做到可合并就绪，merge 由 owner 执行** |
| alongor666-skills | /Users/xuechenglong/workspace/alongor666-skills | github.com/alongor666/alongor666-skills | main | 技能仓；新增/修改技能走 PR，遵循仓内自身约定 |
| auto_loadbi | /Users/xuechenglong/workspace/auto_loadbi | github.com/alongor666/auto_loadbi | main | BI 自动化仓；涉及生产实例/班次操作的变更须 owner 点头后实施 |
