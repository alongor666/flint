# 026 · 聚焦卡拖调上限动态化（50% 屏宽）＋开卡自动收导航

- 状态: scouted
- 目标仓: chexian-api（原文明示「chexian-api前端」）
- 类型: 优化（前端交互；先探可定）
- 录入: 2026-10-28

## 原文

> chexian-api前端，聚焦卡片支持拉宽，动态拉宽，可占占50%的屏幕。打开时导航栏自动关闭

## 澄清（AUQ）

- Q1 「可占 50% 屏幕」拉宽机制 选项: A（推荐）拖调上限动态化——上限=min(50% 视口宽, 帧宽−720 主区最小保护)，仍用既有拖调手柄，不加按钮 / B 一键拉宽按钮直达 50%、拖调上限维持 560 / C 两者都要 → **owner 答: A**
- Q2 开卡自动收导航后、关卡导航栏行为 选项: A 关卡自动恢复（临时收起不写偏好存储）/ B 不自动恢复——收起后保持，用户点顶栏展开钮自行恢复 / C 仅面板宽超出可用空间时才收 → **owner 答: B**

先探可定字段（依据声明）：落点=壳层单点——020 已把九处聚焦卡统一进 WorkbenchAuxiliary 壳，壳一处改全站生效，范围非分叉；50% 基准取视口宽（「屏幕」字面义，且 Q2 收导航后帧宽与视口宽差即 184px 侧栏，取视口更稳定）。

## 讨论

### 防重（不并卡依据）

020（shipped）交付「全站聚焦卡统一规范＋ov2 回到 320–560 标准拖调」，其 AUQ Q1A 把上限明裁为固定 320–560 档；本卡改的是该已交付体系的上限语义（固定 560 → 动态 50% 视口）并新增导航联动，是 020 交付面之上的新增量，非同一诉求重复录入。019（聚焦趋势图承载）无宽度面。不并卡。

### 先探事实（2026-10-28，锚 origin/main `b131d495b`）

- **壳层钳位现状**：`src/components/layout/WorkbenchAuxiliary.tsx:14-17`（MIN 320 / MAX 560 / 缺省 360 / CORE_MIN_WIDTH 720 / GAP 8）；`:361` 拖调钳位 `Math.max(MIN, Math.min(MAX, size.width − 720 − 8, next))`——「帧宽−主区最小」这层动态上限已存在，本刀核心=把 MAX 常量换成视口 50%（`Math.min(window.innerWidth*0.5, …)` 一处，min 链其余不动）；`:12` 存量偏好读取 `min(MAX, stored)`——上限放宽后存量 560 偏好无需迁移，用户可继续拖大；`:394` aria-valuemax 同步动态化。
- **导航收起机制现成**：`src/components/layout/SidebarLayout.tsx:283-295`（collapsed 态=184↔0、`SIDEBAR_COLLAPSED_KEY` localStorage 持久化、toggle 经 useSidebar context 暴露）；`src/components/layout/ContentTopbar.tsx:121-149` 收起态顶栏展开钮已存在。联动接线=壳层 open 时置收起（owner 裁定「不自动恢复」=直接写偏好存储，与既有 toggle 语义一致，无临时态设计——比推荐的临时恢复更简）。
- **边界**：抽屉视口（<1024）无内联侧栏（SidebarLayout.tsx:283 collapsed 恒 false），联动天然只作用桌面 ≥1024；ov2 侧 ov2 用同一壳（020 已撤 fixedLayout）。
- **防撞车【实证】**：开放池 `gh issue/pr list` 检索「宽度|拉宽|导航|收起|聚焦」——issue 仅 #2068（筛选器聚焦模式，非聚焦卡宽度）与 #1656（导航名补迁，非收起联动），PR 零命中；净空。

### 初评

改动面小：WorkbenchAuxiliary（MAX 常量→动态值＋aria 同步）＋壳层 open 处导航联动接线（经 useSidebar context，壳与 SidebarLayout 已同树）＋定向测试（钳位边界 50%/720 保护/偏好存量、收导航联动）。建议单 issue 单 PR；50% 视口在超宽屏（如 2560→1280px 面板）下内容层九卡是否有破版属实施时回归项，无预设结论。

## 探索 #1（2026-10-28）

- 探查锚: chexian-api @ origin/main `b131d495b`（同日 fetch；只读取证，未动目标仓）

### 防撞车（两法交叉验证）

- 【事实·法1】`git grep 'collapsed|useSidebar|toggle' origin/main -- src/components/layout/WorkbenchAuxiliary.tsx` 零命中——壳内无任何导航联动既有实现。
- 【事实·法2】`useSidebar` 生产消费面全集＝ContentTopbar.tsx:82 与 SidebarNavigation.tsx:152 两处（定义 SidebarLayout.tsx:203），均为按钮/渲染消费，无开卡触发逻辑。
- 【事实】视口宽生产消费 `window.innerWidth` 共 6 处，全为 tooltip 定位/断点检测/遮罩计算，无宽度钳位用途——「无既有视口比例上限逻辑」两法同证。
- 【事实】020 族五 PR（#2642/#2646/#2659/#2660/#2665）已全合闭环，写集净空；开放池复核同录入时结论。

### 实施要点 · 刀面 1：拖调上限动态化（WorkbenchAuxiliary.tsx）

- 现状锚【事实】：`:233` size state＋`:292-300` ResizeObserver 观察 **frame**（size=帧宽，非视口）；`:314` actualWidth `= min(width, max(320, 帧宽−728))`——**只有帧帽、无 MAX 帽**（今日 stored≤560 故无感，动态帽后必补）；`:361` 拖调钳位含 MAX；`:394` aria-valuemax 同源。
- 改法：`effectiveMax = max(320, min(视口宽×50%, 帧宽−728))`，统一落 `:12/:361/:394` 三处并**补入 `:314`**（不补则 >1456 视口收导航后帧帽>50% 帽，渲染态可超 50%）；MAX_WIDTH 常量退役。视口宽经 resize 监听 state 取（模板＝SidebarLayout.tsx:273-279 既有先例，非 RO 可得——frame⊂视口，视口变化帧必变但值本身读不到）。
- 数值感【断言·代码推理】：1440 视口开卡收导航后帧=1440 → 帽 712（≈49.4%）；1920 → 960（50% 帽生效）；1280 → 552。MIN 320／缺省 360 不动；存量偏好超新帽由 `:314` 渲染钳位兜底，无迁移面。

### 实施要点 · 刀面 2：开卡自动收导航（SidebarLayout.tsx＋壳一处接线）

- 挂载点【事实】：壳单点挂载于 `SidebarLayout.tsx:514`，在 `:497` SidebarContext.Provider **之内**——接线走 **prop 回调零 context 改动**：仿既有 onModalChange 先例（`:517`）新增 onOpenChange，visible 上升沿（false→true）触发；面板已开时切工具不重触（owner「打开时」字面义）。
- 收起动作＝复刻 toggle 副作用序（`:288-296`）：`safeStorage.setItem(SIDEBAR_COLLAPSED_KEY,'true')` → `setCollapsedPref(true)`，guard＝`!isDrawerViewport && !collapsed`（抽屉视口无内联侧栏不写偏好；已折叠跳过，兼消 toggle 翻转风险）。owner 裁定「不自动恢复」＝持久写偏好，无临时态——比 #2063 纯态机制简。
- 先例与共存【事实】：#2063 纯态收导航栏（`:298-323`）为临时恢复型（进纯态折叠/退出还原+touched 标记），与本刀持久型正交；guard !collapsed 下纯态已折叠场景零交互。落地复位（`:335-340` 强制展开侧栏）正交不冲突。侧栏折叠=整体不渲染（`:506-507`），展开钮在 ContentTopbar 左簇现成。
- 行为披露（PR 正文必列）：①ov2 加载自动展开异常列表（FocusAuxiliary.tsx:91-94 sideLayout 门）＝进页即收导航；②关卡后导航保持收起（裁定本义），下次落地复位仍会强制展开；③1024–1231 视口区间（帧帽未达 1048 side 门限）开卡收导航+184px 可使 bottom 态翻 side 态——属有利副作用，披露即可。

### 测试面

- unit【事实】：`workbench-auxiliary.test.tsx:332`（aria-valuenow '560'）与 `:386-397`（storage '560' 往返）在 jsdom 默认视口 1024（新帽=512）必红——须 stub `window.innerWidth`（如 2560）按新帽改写；新增三组：50% 帽钳位／开卡收导航联动（含抽屉视口不写偏好）／存量超帽偏好渲染钳位。
- E2E【事实】：spec 34（financial）`:109/:132` 两处 '560' 硬断言必改——效仿 27:54／29:81／35:117／38:102 既有 cap 相对模式 `min(376, valuemax)`；spec 23:133 '360'（缺省宽）与拖调取消复原断言不受帽影响；**全量 e2e 回归必须**（导航流 14/22＋七个 workbench spec）——开卡收导航改变用例流程中导航态，存在依赖侧栏可见的隐性面，红了逐例核。
- 文档【事实】：《聚焦卡规范与资产清单.md》「宽度/调宽/拖」三词 grep 零命中——清单管内容层六维、宽度属壳层，无文档行要改；宽度语义随代码注释改，行为表进 PR 正文。

### 风险

- e2e 隐性依赖面（上述）——CI 全量兜底，属可枚举小改。
- 超宽屏 50% 帽（如 2560→1280px 面板）九卡内容层破版属回归验证项，无预设结论【断言·代码推理】；VcrMatrixPanel 联动表 `min-w-[600px]` 加宽只更友好（020 已探）。

### 转正映射建议

单 issue 单 PR（type/feature＋area/frontend）：WorkbenchAuxiliary.tsx＋SidebarLayout.tsx＋workbench-auxiliary.test.tsx＋spec 34 两行 ≈4 文件小刀；写集净空无在途冲突。

### 验收

- `bun run typecheck`；`bun run test --run src/components/layout/__tests__/workbench-auxiliary.test.tsx`（＋sidebar 侧定向用例）；
- `bun run governance`；E2E 全量（仓内既有 e2e 命令，重点 14/22/23/27/29/34/35/38）；
- 人工：1440 视口开聚焦卡→导航自动收、拖至 ≈712（≈50%）、主区 ≥720 无破版、刷新后宽度与收起偏好均保持；1280×640 以下抽屉视口开卡不写 `sidebar-collapsed`。

## 开工记录

（待「开工 026」）
