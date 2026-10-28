# 016 · 前日更新/历史更新入口降权

- 状态: scouted（2026-10-28 探索 #2 已覆盖补充范围；仅方案就绪，未实施）
- 目标仓: chexian-api（先探已定：008 卡产物——首页更新摘要卡+铃铛红点在 `src/features/` updates 域与 guide 域 /updates 历史页）
- 类型: 优化（信息架构/入口位置）
- 录入: 2026-10-24

## 原文

（owner 2026-10-24，在 014 影响评估讨论中带出）

> 更新我原本也不打算，因为它只是系统的更新，并不是数据分析。因为我们的主要用户还是数据分析者，怎么更新的并不是最核心的功能，所以这一点也没有关系。甚至本来我都想把前日更新及历史更新放到不那么显眼的地方。

## 先探结论

- 动因定位：用户观——主要用户是数据分析者，系统更新日志非核心功能；008 卡「首开=铃铛红点+首页摘要卡」的主路径设计与该定位冲突，摘要卡属过度曝光。
- 与 014 的耦合：014 落点切走首页后更新曝光自然下降，两卡同向；但降权是独立诉求（即便落点不动也可做）。

## 澄清（AUQ）

- Q1 降权形态: A（推荐）撤首页摘要卡、铃铛保留（红点未读提示仍在，点开见前日更新）/ B（推荐外）铃铛也撤，更新历史只在 guide 域 /updates 页可达（入口藏进设置或页脚）/ C 降为首页角落小字链接 → **owner 答: A（2026-10-26 AUQ 补答）**
- Q2 「前日更新」的每日触达是否保留推送/提醒能力: A（推荐）保留铃铛红点（想知道的人有信号）/ B 完全静默，只有主动访问 → **owner 答: A（2026-10-26 AUQ 补答）**

## 讨论

原讨论范围为前端入口降权（008 产物已有 per-username 已读键与历史页骨架）。Q1/Q2 已于 2026-10-26 裁定；2026-10-28 新增统一更新入口与版本展示，以本卡补充录入为当前范围，增量方案见探索 #2。

## 探索 #1（2026-10-26）

- 探查锚: chexian-api @ origin/main 198e818a7（静态核实，`git show origin/main:<path>`，行号可复核）
- 防撞车（两法同证）: ①gh——开放 issue 搜「更新入口」「摘要卡」命中的 #2335/#2544/#2595/#1751/#1752/#1629/#2392 均无关，开放 PR 搜 updates 零命中；②git——近 30 日 `src/features/updates/` 改动全属 #2208 特性线（9a022e157 建、19cbc6327 测试存根、c7caff6eb 返修），无降权/撤卡工作。
- 实施要点（改动面＝净删约 5 文件，零新增）:
  - `src/features/pages/LandingHomePage.tsx:6,49-50`：删 import 与 `<HomeUpdatesCard />`（连同 :49 注释行）；同容器 CoefficientMonitorSummaryCard / HealthCheckSection 不动
  - 删 `src/features/updates/HomeUpdatesCard.tsx` 与 `__tests__/HomeUpdatesCard.test.tsx`
  - `src/features/updates/index.ts:3-4`：删导出行，头注释「首页摘要卡」措辞同步
  - `src/features/INDEX.md:40`：updates/ 行去掉摘要卡描述与文件清单项（目标仓 AGENTS.md §7 多文件改动同步索引）
  - **孤儿导出（先删后加红线）**：`readState.ts:52 latestUnreadDay` 仅摘要卡消费（铃铛用 hasUnreadDays/latestDayDate、页面用 latestDayDate，`git grep` 实证），删卡后成死导出须同删，`readState.test.ts` 对应用例一并清
  - 零语义副作用：卡片不写已读态（纯消费 useUpdatesRead），已读水位推进在铃铛「全部标已读」与 /updates 页浏览——删卡不改红点行为；e2e 仅 `tests/e2e/13-a11y-axe.spec.ts:151-159` 注释提及（铃铛计数不含卡片，不破 a11y 基线）
- 与 014 的关系：落点段挂起中、LandingHomePage 仍是全员落地页（页头注释 CX-ADR-14e573），删卡与落点切换正交、先后皆可
- 转正映射建议: 1 卡 → 1 issue（type/feature）→ 1 PR；与 014 无文件冲突可并行
- 验收: ①`bun run typecheck` 绿（无悬空 import）；②`bun run test --run src/features/updates src/features/pages` 绿；③未读态下首页不再渲染「系统更新」卡（`data-testid="home-updates-card"` 消失——注意无未读时本就零足迹，验收前须清 `updates-read-v1:*` localStorage 造未读）；④铃铛红点/下拉/全部标已读与 /updates 页行为不变；⑤`tests/e2e/13-a11y-axe.spec.ts` 铃铛计数断言不动仍绿

## 开工记录

（待「开工 016」）

## 补充录入（2026-10-28）

### 原文

> 把系统更新从首页移到数据更新下方，数据更新变更为更新，上半部是数据更新下半部是系统更新，系统更新要有最新的版本号，不展示前端体验不到的更新。

### 澄清与范围

- owner 明确：现有「数据更新」入口改名「更新」，其中上半部为数据更新，下半部为系统更新；系统更新从首页迁入该处。
- 系统更新展示最新版本号，只展示前端用户能体验到的变化；可见性要求沿用 [008](008-prod-web-release-notes.md) 已裁定的用户可用门槛。
- owner 再次提出同源诉求，优先级备注提升为「owner 再次明确要求，优先补充探索」；不另建更新卡。
- 本次未变更原先铃铛与未读红点裁定，统一入口与它们的组合待目标仓核对；版本来源与显示格式复用目标仓既有发布机制，不在本卡另造版本规则。
- 2026-10-28 owner 已授权按澄清结果登记、提交并推送需求文档；授权范围为 flint 文档登记。

### 对原探索的影响

- 探索 #1 仅覆盖首页摘要卡撤除；新增入口布局、系统更新承载与版本展示尚未探索，旧方案不能直接覆盖本次需求。
- 状态回到 discussed，保留旧探索历史，补充探索后再判断转正就绪。
- 同批导航、经营体检与状态趋势诉求见 [021](021-health-check-navigation-status-trend.md)。

### 后续验收要点（尚未执行）

1. 首页不再展示系统更新卡；「更新」入口可达，数据更新在上、系统更新在下。
2. 系统更新可见最新版本号；核对其与实际部署版本的对应关系，不能把尚未上线的版本当作当前版本。
3. 前端无感知的条目不展示；数据更新信息与已读提示行为按既有裁定保持可用。

## 探索 #2（2026-10-28）

### 探查锚与范围

- 目标仓：chexian-api-dev，固定默认分支锚 `b8f2de75100f21688d6053839a388a5090ec5f6c`（2026-10-28）；`git cat-file -t <SHA>` 返回 commit，收尾 `git rev-parse origin/main` 仍为该锚。下列行号均属于该完整 SHA，以 `git show <SHA>:<路径> | nl -ba` 复核。
- 方法：只读源码、协议、GitHub；静态核实与代码推理，**未运行目标仓测试、浏览器或生产接口**。已读根及 src/server 分区 AGENTS、根知识入口、相关索引、架构边界及 PR 开发协议 §1.1；实际实施规则仍以目标仓这些入口为准。
- 目标：分析者在现有「更新」辅助区先看数据新鲜度，再主动查看系统变化及当前运行版本；首页正文撤掉系统更新摘要，铃铛提醒和历史页继续可用。008 已裁用户可用门槛及本卡 Q1/Q2 未被本轮推翻。

### 当前事实与复用判断

| 证据（均为【事实】·静态核实） | 实施含义 |
|---|---|
| `src/app/App.tsx:19,326-327,349,354` 分别在 feedback 槽装配 DataFreshnessHost、notificationCenter 槽装配 UpdatesBell；`src/features/data-freshness/DataFreshnessHost.tsx:13-19` 持有圆环和 `data-freshness` 工具。`ARCHITECTURE.md:34-49`、`scripts/governance/arch-layer-boundaries.mjs:14-19` 禁特性域横向互引，pages/App 可装配 | 在现有工具内加系统段，由 App 注入 updates 域组件；不让 data-freshness 直接 import updates，不新建更新中心或第二个工具 |
| `DataFreshnessRing.tsx:37-52` 只在壳层首次具体落点为 `/home`、同会话尚未弹出时自动打开；中途从业务页返回首页不弹。`:63-78` 控制可访问名与工具开关。`:54-65,85-90` 圆环/警示红点只表示数据状态 | 入口可见名改「更新」，工具 id 保留；数据圆环不混入系统未读含义，不增加自动弹出、跳转或滚动到系统段 |
| `DataFreshnessPanel.tsx:179-207` 数据加载/错误为局部返回，正常态 `h-full` + 内层滚动；`WorkbenchAuxiliary.tsx:168,170-198` 已有工具正文滚动且仅激活时挂载 | 两段共同依托现有正文滚动；移除数据段撑满整面板的限制，避免下段被挤出可达区域。错误态只影响对应段 |
| `src/features/pages/LandingHomePage.tsx:6,49-50` 仍挂 HomeUpdatesCard；`HomeUpdatesCard.tsx:22-33,57-64` 只显示近窗最新未读日、上限 5 条、跳历史，不推进已读 | 原探索 #1 的「只删卡」不足。迁移并改造现有摘要实现，删除旧首页名称/挂点；面板摘要应在已读后仍可主动查看 |
| `src/features/updates/useUpdates.ts:24-39,46-51` 已有空日剔除和任意时间窗查询，铃铛用近两周窗；`readState.ts:45-67` 已有最大日期选择与未读判断；`UpdatesPage.tsx:62-71,198-210` 有历史/按年加载和浏览推进已读 | 面板复用 `useUpdatesRange({})` 取现有完整历史后选最新非空日，避免沿用近窗导致长期无新发布时「最新摘要」消失；不新增 API。铃铛近窗提醒及其既知边界不改 |
| `src/features/guide/GuidePage.tsx:99-104,179-182` 已经用 `queryKeys.dataVersion()` + `apiClient.data.version()` 显示 releaseSha 前 7 位；`src/shared/api/data-api.ts:37-46,76-84` 为现有契约 | 同源显示「当前版本：短指纹」，完整值可供核对；不取 package.json 的 `0.0.0`、最新 git main 或更新日志日期当版本 |
| `scripts/generate-release-metadata.mjs:10-26` 同次生成前后端 release.json；`.github/workflows/deploy.yml:39-54,67-71` 绑定本次部署 SHA；`server/src/config/release.ts:20-38` 读取运行制品；`server/src/routes/data.ts:596-600` 回传它 | 显示的是当前服务制品版本。面板打开时重取该共享查询，重新确认中/失败不把旧缓存标成最新；缺失或 dev 明示未确认/开发环境 |
| `scripts/updates/generate-daily.mjs:196-209,241-258` 用 origin/main 合并标题分类、改写后按日落盘；`server/src/routes/updates.ts:91-108` 聚合日文件；`server/src/config/update-notes/rules.ts:90-110` 是已有筛选源 | 系统段、铃铛、历史页继续消费同一条生产链，不在前端复制黑名单或新增过滤系统 |

**【事实】·008 门槛的实际边界**：`server/src/config/update-notes/rules.ts:6-15,28-55` 按前缀、工程 scope、预埋标题剔除，且明确披露「预埋未带关键词时可能误入」；`:44-46` 与 `__tests__/rules.test.ts:80-86` 明确保留用户可感知的服务端修复。按本卡补充范围沿用 [008 设计定稿及裁定](008-prod-web-release-notes.md)，本轮不把「有后端改动」等同用户无感、不改为仅允许前端文件的白名单，也不新增逐条审批。生成侧筛选可复用，**NO CHANGE NEEDED 仅适用于该筛选机制，不代表整卡无需修改**。本次未查证具体新增漏网项，不据推测扩黑名单。

**【断言】·代码推理**：上述组合可满足已裁信息架构；系统段位于数据段之后，保留既有数据面板首落打开不等于新增系统通知。实现中不得因自动挂载系统段就推进已读；查看历史或显式「全部标已读」继续是已读动作。UI 走查需检验数据优先是否在窄屏/短窗口成立，不能只凭布局代码声称通过。

### 最小实施写集

不改仍会在首页显示系统卡，入口仍名「数据更新」，且面板没有版本，不能满足本卡。最小修改采用现有宿主、摘要、查询和版本源：

1. `src/features/data-freshness/DataFreshnessHost.tsx:13-19`：工具 title 改「更新」，接受一个系统段 ReactNode 槽；同一工具内按「数据更新→系统更新」组合。`DataFreshnessPanel.tsx:192-205` 去整面板 `h-full`/独占剩余高度滚动，适配宿主已有滚动。保留数据状态判据、按省查询与轮询。
2. `src/features/data-freshness/DataFreshnessRing.tsx:63-78`：入口 aria-label/title 的主名改「更新」，提示明示圆环仍是数据更新进度；`data-freshness` 工具 id、首页首次自动打开条件与每会话标记不改。
3. `src/features/updates/HomeUpdatesCard.tsx` 迁名为域内 `SystemUpdatesPanel.tsx` 并改造，原文件/旧导出不并存：常驻系统段标题、运行版本、最近非空日最多 5 条及历史链接；已读后仍显示摘要；加载/失败/空态独立呈现。复用现有日期选择、类型标签与 API 查询，版本取现有共享 key；版本重新确认中/失败时明确标态。摘要数据解析跳日沿用 `skippedFiles` 披露，不能把缺文件当无更新。
4. `src/app/App.tsx:326-354`：组合根把系统段注入 DataFreshnessHost，沿用 Suspense 局部降级；铃铛仍独立装配。`LandingHomePage.tsx:6,49-50` 删除旧 import/摘要挂点，不改经营体检及系数监测。
5. `src/features/updates/index.ts:1-4` 同步新出口；`readState.ts:51-58` 的 `latestUnreadDay` 迁移后无消费即删，并清 `__tests__/readState.test.ts:112-123` 对应已失效用例。保留有消费的最大日、已读键和事件逻辑；`useUpdates.ts` 注释从首页摘要改为新消费点。
6. 原 `__tests__/HomeUpdatesCard.test.tsx` 随组件迁名改为新摘要用例；`data-freshness/__tests__/data-freshness.test.tsx` 更新入口名及组合/布局断言；铃铛、历史页、已读测试仍作回归。新增组合断言可放 data-freshness 既有测试，通过注入槽代替跨域 import。`src/features/INDEX.md` 同步原摘要与新宿主描述；`tests/e2e/13-a11y-axe.spec.ts:151-159` 的旧卡注释随迁移订正，通知数量合约不随删卡任意改动。

本次不动后端版本/API 契约、发布链、日更调度或历史 JSON，不回填数据、不改铃铛与 `/updates` 路由的可达性/已读规则。目标仓实施体量与检查按其当时协议，不在 flint 自设预算。

### 防撞车、风险与失效条件

- **【事实】·GitHub 只读查询**：连接器 `search_prs(query="repo:alongor666/chexian-api is:open")`，再用 `gh pr list --repo alongor666/chexian-api --state open --limit 100 --json number,title,files` 交叉核对当前开放 PR 写集；未见 updates/data-freshness/首页/版本源重叠。`search_issues` 分别搜 `updates`、`freshness`、`系统更新`、`in:title 更新`，再以 `gh issue list --state open --limit 500 --json number,title` 筛关键词复查；不能把同主题当零：[#2217](https://github.com/alongor666/chexian-api/issues/2217) 仍 open，是 008 生产首跑/降级告警遗留；[#2626](https://github.com/alongor666/chexian-api/issues/2626) 为代码版本准入边界，不属于本卡入口迁移。源码规划命中 `docs/plans/2026-10-18-生产网页更新发布-实施规划.md`，是原 008 设计，未发现另一入口迁移方案；另以 `git log <SHA> -- src/features/updates src/features/data-freshness` 交叉追溯既有线，不把新查询失败当作阴性。
- **已有误差，保留披露**：纯标题筛选不等于逐项可用性证明，沿用 008 边界。更新日文件按合并事实生成，条目无部署指纹（`update-notes/types.ts:13-40`）；本卡版本行只声明运行版本，不能把每条历史记录描述为该版本已包含。若未来需要严格部署批次绑定，再扩既有 release 元数据与现有生成链，本轮不造平行版本系统。
- **交互风险**：数据段原有撑满高度会遮挡系统段；摘要照搬未读门槛会导致已读后系统段空白；近窗照搬会导致旧最新记录消失；自动挂载若写已读会无声消红点；共享版本缓存若不重新确认会显示旧版本。上述均须定向验收。
- **与 021**：直接交叉点仅 `LandingHomePage.tsx`（本卡删更新 import/挂点，021 调整体检及标题）；独立 issue/PR 可按 016→021 顺序降低冲突。若并行，点名合并这两处，禁止整文件覆盖。公共辅助区自身宽度等其他在途事项不纳本卡。
- 开工前刷新默认分支、重读适用协议并复核上述写集/开放工作项；相关代码、008/016 裁定、版本源或共享辅助区契约有实质变化时重做增量加工。回滚为撤回本次 UI PR，不改持久已读键、不动日文件。

### 验收任务包（全部 NOT RUN）

1. `bun run typecheck`；`bun run test --run src/features/updates src/features/data-freshness src/features/pages`；`bun run governance`。期望：旧首页导出无悬空引用，跨域依赖未新增，既有入口/历史/已读语义回归通过。
2. 复用 `bun run test --run server/src/config/update-notes/__tests__/rules.test.ts server/src/routes/__tests__/updates-route.test.ts scripts/__tests__/generate-daily-boundary.test.mjs`：预埋 #2171/#1518、工程前缀/scope 不展示；真实页面 #2187/#1931 与用户可感知服务端修复 #2175/#1532 仍展示；标题改写不能把已剔条目加回。此处验证沿用 008 的实际规则，禁宣称全量语义无误。
3. 新摘要组件定向用例：已读/未读都能查最新非空日；最近两周无更新而更早有内容仍可见；空日不占最新位；摘要挂载不写已读；版本与更新列表一个失败不拖垮另一个，数据段失败也不遮系统段；缺失/dev/请求失败不冒充生产最新版本；混入坏日文件有缺失提示。
4. 本地 `bun run dev:full`，按目标仓走查就绪协议准备登录与真实数据，再做浏览器验收：首页正文没有系统卡；点「更新」上下次序正确、短窗口能滚到系统段；数据首落自动打开条件不变，切业务页再回不新增弹出；版本行与同次 `/api/data/version` 完整指纹相符；铃铛红点/全部标已读、换账号隔离、历史页关键词/类型/跨年加载正常。补有未读与全部已读两种状态，避免因旧卡本就隐藏误判删除成功。
5. `bun run test:e2e -- tests/e2e/13-a11y-axe.spec.ts`，并按走查发现补实际交互回归；数据圆环警示点与系统未读点的可访问名、键盘开关/焦点/关闭行为不混淆。部署后再核实际服务版本和生产页面，**本探索没有执行这些动作**。

### 转正结论

- 建议 1 卡 → 1 个前端优化 issue → 1 个 PR；关联 008/#2208 作为来源、#2217 作为独立遗留，`Closes` 只指本次实施 issue，不顺手关闭旧生产首跑事项。与 021 保持独立结果，协调首页接点即可。
- **READY FOR IMPLEMENTATION（方案就绪）**：现有源与组合路径可复用，未发现必须新增 owner 裁定的产品分叉；008 的启发式边界如实保留。尚未获得「开工 016」授权，不建目标仓 issue/PR、不写业务代码；实现、测试及生产验收全部 **NOT RUN**。
