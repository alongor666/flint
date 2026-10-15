# 目标仓路由表

owner 有权读写的仓登记在此，新仓加入说一声即可登记。
目标仓协议以其根 AGENTS.md 为准，本表只是路由提示；两者冲突以目标仓为准。

| 仓 | 本地路径 | 远端 | 默认分支 | 转正通道与注意 |
|----|---------|------|---------|---------------|
| chexian-api | /Users/xuechenglong/workspace/chexian-api-dev | github.com/alongor666/chexian-api | main | PR-first：从 origin/main 切分支，合并前独立对抗评审并修完 P0/P1，推送前 `bun run governance`；开工前必读其根 AGENTS.md。小项直接 PR（正文引用本仓卡链接，可不建 issue）；跨多 PR 大项建 issue（type/*+area/* 标签族） |
| alongor666-skills | /Users/xuechenglong/workspace/alongor666-skills | github.com/alongor666/alongor666-skills | main | 技能仓；新增/修改技能走 PR，遵循仓内自身约定 |
| auto_loadbi | /Users/xuechenglong/workspace/auto_loadbi | github.com/alongor666/auto_loadbi | main | BI 自动化仓；涉及生产实例/班次操作的变更须 owner 点头后实施 |
