# 018 · 生产 JWT 签名密钥弱值治理（换强密钥＋启动闸强度升级）

- 状态: building（2026-10-26 owner 下令开工；对锚 origin/main 198e818a7，较探索#3 锚 53bcb145e 漂移 12 提交全属 hunch 域与 merge，env.ts:43-45 / server/.env.example:6 / config 测试目录 / #1303 未合入四处关键事实零漂移，免重探）
- 目标仓: chexian-api（先探可定：隐患全部事实在该仓生产环境与 `server/src/config/env.ts` 实证）
- 类型: 安全加固
- 录入: 2026-10-26

## 原文

> 「JWT_SECRET 换强密钥这是什么？存在什么问题？」
> 「怎么发生的」
> 「登记到Flint」

（背景：owner 追问 AI 在八卡推送收尾时顺带汇报的生产鉴权隐患。）

## 澄清（AUQ）

先探已定字段（依据见「讨论」实证链）：目标仓=chexian-api、类型=安全加固、状态直入 discussed（事实链完整无真分叉需 AUQ）。

非交互假设清单（owner 纠偏即停）：
- 范围假设：换强密钥（运维动作）与启动闸强度升级（代码改动，约三行）**建议同批处置**——只换密钥则下一个弱值仍可无声绕闸。
- 公开仓红线脱敏：卡内不录生产域名、不录弱密钥原文（弱值等同于公开仓上的凭据泄露）。

## 讨论

**现状（2026-10-26 实证）**：目标仓生产环境 `server/.env` 的 `JWT_SECRET` 是一个自述「本地测试用途」的固定弱开发值（值不入卡）。持有该值即可离线自签任意身份 JWT（含全国超管/任意省管理员）直打生产 API——本会话做推送样例时已实测一次成功（签省级管理员身份拉生产数据，无需密码）。生产站点公网可达，攻击面真实。

**成因（已溯源）**：
1. 该值**不属于仓库**——全部 git 历史（`git log -S` 全分支）与当前主干零命中，非模板/代码自带；
2. 部署环节人工写入：仓库占位符是另一字符串（`.env.example`，注释明令生产必改），`server/src/config/env.ts:43-45` 的启动 fail-fast 闸**只拦那一个占位字符串**，任何其他值（含此弱值）均放行——防线设计缺口是「不等于占位符」而非强度校验；
3. 具体写入时间/操作者已不可考（`.env` 修改时间被后续配置更新覆盖）。

**修法两步**：
1. 换密钥（运维）：生产 `.env` 换 `openssl rand -base64 48` 强随机值，经部署 wrapper restart 生效。副作用=在线登录态全失效需重新登录（JWT 最长数小时自然淘汰；PAT 独立校验不受影响）。
2. 闸加固（代码，约三行）：`env.ts` 生产校验从「不等于占位符」升级为「长度≥48 且不命中已知弱值字典」，堵「换弱值绕闸」通道。

## 探索 #1（2026-10-26）

- 探查锚: chexian-api @ origin/main 62845768f（`server/src/config/env.ts:43-45`、`.env.example`、`deploy/DEPLOY_FULLSTACK.md` 部署文档密钥修改段）
- 防撞车: 目标仓开放 PR/issue 无同主题（`gh pr list`/`gh issue list` 检索 jwt/secret 零命中）；历史无同型加固
- 实施要点: 代码改动仅 `server/src/config/env.ts` 一处（校验函数）；换密钥动作在 VPS `.env`＋wrapper restart（须 owner 授权的生产变更）；建议配套通知在线用户重新登录
- 转正映射建议: 1 张卡 → 1 个 issue（安全类，建议 P1）→ 1 个 PR（闸加固）；换密钥运维动作随 PR 合并部署窗口一起做
- 验收: ①弱值重启被拒（闸生效，日志 fail-fast）；②强值重启正常；③旧 JWT 请求 401、重新登录后 200；④`bun run test --run server/src/config` 相关用例绿

## 探索 #2（2026-10-26 · owner 四轮追问深化，方案收敛）

owner 追问链：密钥为何不从 PAT 派生 → 「部署时自动生成」的风险 → 更有效的方式 → 机器归属与威胁模型校准 → 「每天更新一次很有必要」评估。结论如下：

**威胁模型校准（owner 事实陈述）**：全部机器（生产 VPS/发布机/开发机/BI 采集机）为 owner 本人所有，操作主体=owner+harness——单信任域。真实外部门只有一个：生产站公网可达（字典撞库/定向猜测）；内部风险史（弱值、占位符尖括号）全部来自可信操作者失误。据此：跨机密钥面治理降为运维可选；重型方案（受管密钥存储/自动轮换体系）确认不适用。

**影响面澄清（owner 问「影响部署还是同事登录」）**：不影响部署（部署链走部署私钥与 CI 凭据）；作用于全体网页登录态——登录后签发令牌、此后逐请求验签均用此密钥；换密钥=全员登出重登一次（密码不动）；PAT 与机器摄入令牌独立查库校验不受影响（生产推送链不断）。弱值被利用的表现不是故障而是无声冒充（合法签名的外来令牌）。

**方案评估否决项**：
- 「从 PAT 派生」：PAT 哈希落库服务端无明文（物理不可行）；PAT 是 JWT 体系下游产物（创建 PAT 需先登录）——循环依赖；全局秘密挂个人凭据权限错位。
- 「每天轮换密钥」：密钥与验签方同机，本机沦陷当天新值同样被取（防不了唯一真实泄露场景）；代价=每天全员登出+自动改配置重启断在途查询+轮换脚本成新单点；「快照外泄」场景概率低，账算不平。
- 「部署时弱值自动替换」：幂等边界易写错（每次部署踢全员）；弱值黑名单永不全覆盖=虚假安全感；静默轮换无人知情。若做自动生成，严格限定「值缺失时生成、绝不覆盖既有值」。

**收敛方案（三件套，分工：密钥管伪造、寿命管节奏、闸管失误）**：
1. 换一次强随机密钥（48 字节）——一次性消除公网字典撞库风险；例行低频轮换（半年/年）或疑似泄露时再动。
2. **令牌寿命按需调**（`ACCESS_TOKEN_EXPIRE`，现默认 4h）——owner「每天更新很有必要」的直觉对应的是会话节奏旋钮：若要「每天一登/会话不过夜」，调过期时间即可（如 12h），零代码、粒度细、各自到期，远优于密钥轮换的全体立即失效。
3. 启动闸强度校验（长度≥48+弱值字典 fail-fast）——防线针对的正是弱值的真实来源：可信操作者手滑。

原探索 #1 的「指纹巡检」项降级为运维可选（防遗忘防漂移），移出必做。

## 探索 #3（2026-10-26 · 对目标仓最新主干静态核实，PR 实施方案落地；勘正 #2 四处）

- 探查锚: chexian-api @ origin/main 53bcb145e（2026-10-26）；`server/src/config/env.ts:43-45` 闸逻辑与探索 #1 一致未漂移。全部结论为**静态核实**（`git show origin/main:<path>`，行号可直接复核），未实测。
- 防撞车（两法同证）: ①gh——开放 PR 共 3 个，搜 jwt/secret 零命中；开放 issue 搜 JWT_SECRET 零命中，搜「密钥」命中 #1655（GPG 备份私钥托管，与本卡无关）；②git——`git log --all -S"requireInProduction" -- server/src/config/env.ts` 仅初始快照提交，远端分支名无 jwt/secret 相关。另 #1303 自锁守卫计划（`docs/plans/2026-10-21-1303-self-lock-guard-plan.md:54`）提到 `change-me-in-production` 仅作测试缺省值使用，非治理，无重叠。

**【勘正探索 #2 四处】**（#2 为纯讨论未查码，以下按主干实证修正）：

1. **变量名**：`ACCESS_TOKEN_EXPIRE` 不存在。真实变量 `JWT_EXPIRES_IN`（`env.ts:82`，默认 4h）与 `JWT_REFRESH_EXPIRES_IN`（`env.ts:84`，默认 7d）两枚。
2. **会话寿命旋钮判错**：前端 401 自动 refresh（`src/shared/api/client-core.ts:458,544-554`）；服务端 refresh 为**滑动续期**——`server/src/services/auth.ts:695` 删旧 sid 后重签全新 7d（代码注释原话「每刷新一次重置 7 天 TTL」）。故会话总寿命由 refresh 7d 决定，调 access 到 12h 只是提高 refresh 频率，**不能**实现「会话不过夜」。正确旋钮 = `JWT_REFRESH_EXPIRES_IN=12h`（夜间不活跃间隔 >12h 即登出）；语义为「连续不活跃超时」而非严格每天重登（活跃用户自动续期）。access 4h 维持不动。
3. **换密钥副作用高估**：`refreshTokenStore` 为纯内存 Map（`services/auth.ts:191`）无持久化——**任何部署重启都已使全部 refresh 会话失效**。换密钥＋restart 的额外代价只是存量 access token 提前 401（最长 4h 窗口），用户感知 ≈ 一次常规部署，不是新增的全员登出事件。
4. **门槛数字对齐**：#2 写「长度 ≥48」；仓内两处部署文档既有口径为 **≥64 字符**（`deploy/DEPLOY_FULLSTACK.md:162`「至少 64 字符」；`开发文档/DEPLOYMENT_GUIDE.md:133` 生成命令 `openssl rand -base64 64 | tr -d '\n'`、`:452` 验收清单「≥64 字符」）。闸门槛取 ≥64 = 零新口径；base64 下 64 字符 ≈ 384 bit，高于 HS256 的 256 bit 安全下限。

**【新缺口：模板占位符不一致导致模板本身绕闸】**：根 `.env.example:24` = `change-me-in-production`（闸拦）；`server/.env.example:6` = `your-secret-key-change-in-production`（**闸不拦**）。生产文件路径是 `server/.env`（`deploy/DEPLOY_FULLSTACK.md:245`）→ 运维照 server 模板拷贝占位符即可无声通过启动闸——这是探索 #1「防线只拦一个占位符」缺口的具体命中路径。弱值字典须并收两个占位符。顺带：两模板 `JWT_EXPIRES_IN` 亦不一致（4h / 24h，代码默认 4h）。

**实施要点（PR 改动面，三文件）**：

- `server/src/config/env.ts:43-45` 之后加 JWT_SECRET 专用强度闸：`isProd && (命中弱值字典 || 长度 < 64) → throw`。字典最小集 = 两个模板占位符 + jsonwebtoken 官方文档示例值 `your-256-bit-secret`（字典永不全覆盖，长度闸才是主防线）。`requireInProduction` 全仓仅 JWT_SECRET 一个调用方（`:45`；`:131` 为注释引用），不动通用函数、加专用闸零波及。
- 仓内范式对齐：强度常量先例 `server/src/config/agent-service-principal.ts:35-36`（`AGENT_SERVICE_SECRET_MIN_LENGTH = 32`，owner 召回卡 F1 裁定）＋ `:98-99` 实现；硬拦截先例 `env.ts:748-750`（E2E_TEST_MODE 生产 throw 同款）。
- 新增 `server/src/config/__tests__/env-jwt-secret-guard.test.ts`：范式照 `__tests__/materialize-switch-defaults.test.ts`（`vi.resetModules()` + `await import('../env.js')`），断言 `NODE_ENV=production` × {两个占位符 / `your-256-bit-secret` / 47 字符随机值} 均 throw、88 字符强值通过、非生产弱值放行。注：env.ts 全部生产 fail-fast 闸目前**零测试覆盖**（grep 测试目录无「生产环境必需变量」用例），本 PR 只为新闸自带测试，不扩范围。
- 顺手一行：`server/.env.example:6` 占位符统一为 `change-me-in-production`（消除模板绕闸源；`JWT_EXPIRES_IN=24h` 同行对齐 4h 可选）。
- **约束**：闸必须保持 `isProd` 守卫、开发缺省值 `change-me-in-production` 不动——#1303 自锁守卫 HTTP 层测试依赖该缺省真签 HS256 JWT，动则连带红。
- 运维备忘（不走 PR）：换密钥后，依赖 JWT_SECRET 离线自签的运维脚本（`scripts/multi-branch-stress-test.mjs`，用法见 `开发文档/multi-branch/山西cutover接力_v2.md:72`）须同步新值；单信任域 owner 自持，记一笔即可。

- 转正映射建议: 维持 1 卡 → 1 issue（type/security，P1）→ 1 PR（闸＋测试＋模板一行）。运维三动作随 PR 合并部署窗口一次做：①`JWT_SECRET=$(openssl rand -base64 64 | tr -d '\n')` 写入生产 `server/.env`；②同文件加 `JWT_REFRESH_EXPIRES_IN=12h`；③wrapper restart。生产 `.env` 值变更属运维配置、不入 git，不走 PR-first（flint 根「PR-first 正当域」裁定）。
- 验收: ①本地 `NODE_ENV=production JWT_SECRET=<占位符|your-256-bit-secret|47 字符>` 启动均 fail-fast 抛错；②`NODE_ENV=production JWT_SECRET=$(openssl rand -base64 64 | tr -d '\n')` 正常启动；③`bun run test --run server/src/config/__tests__/env-jwt-secret-guard.test.ts` 绿，且 #1303 相关 HTTP 层测试不受影响；④生产换值 restart 后：持旧 access token 请求 401、重新登录 200、PAT 请求 200（独立查库校验不受影响，推送链不断）；⑤次日复核：隔夜未活跃用户须重登（refresh 12h 生效）。

## 开工记录

- 2026-10-26：owner 下令开工（同批裁定：016 探索另线做）。九步双闸走起——步骤 1 完成（本行＋状态 building）；步骤 2 建 issue、步骤 3 切 worktree 分支、步骤 4 行动规划、闸① DSH 对抗评审、实施、独立评审、闸②、可合并就绪后停手待 owner 合并。锚=origin/main 198e818a7。
