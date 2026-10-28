# 026 · 聚焦卡拖调上限动态化（50% 屏宽）＋开卡自动收导航

- 状态: discussed
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

## 探索 #1（YYYY-MM-DD）

（待 owner 确认后探）

## 开工记录

（待「开工 026」）
