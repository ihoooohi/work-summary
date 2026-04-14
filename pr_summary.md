# 工作成果总结

> 统计区间：2026-04-10 ~ 2026-04-13 | 共 **6** 个 PR（已合并 5，待合并 1）
> 最后更新：2026-04-14

---

## Bug 修复

### [#1969](https://github.com/Vispie-AI/VisPie_backend/pull/1969) — 修复反馈记录中 bot_name 始终为 "nanobot" 的问题

- **合并日期**：2026-04-11
- **问题**：所有 `agent_feedback` 记录的 `bot_name` 均写入硬编码字符串 `"nanobot"`，无法区分 george-ai、zane-ai 等各舰队 bot。
- **根因**：各 bot 的 docker-compose 文件均设置了 `AGENT_NAME`，但未设置 `BOT_NAME`；代码直接回落到硬编码默认值。
- **修复方案**：将取值逻辑改为 `os.environ.get("BOT_NAME") or os.environ.get("AGENT_NAME", "nanobot")`，优先使用 `BOT_NAME`，其次回落 `AGENT_NAME`，无需修改任何 docker-compose 文件。
- **成果**：反馈记录准确携带各 bot 真实名称，数据可按 bot 维度正常聚合分析。

---

### [#2017](https://github.com/Vispie-AI/VisPie_backend/pull/2017) — 修复旧 Lark 卡片回调仍写入 "nanobot" 的问题（#1969 后续）

- **合并日期**：2026-04-11
- **问题**：#1969 修复了新卡片，但用户点击旧卡片的反馈按钮时，回调 payload 中已烙印 `bot_name="nanobot"`；`handle_card_action` 优先读取 `value.get("bot_name")`，导致旧数据污染持续。
- **根因**：`handle_card_action` 信任 payload 中的 `bot_name` 字段，而旧卡片 payload 在生成时已写入错误值。
- **修复方案**：从回退链中移除 `value.get("bot_name")`，统一改为只读环境变量（`BOT_NAME` → `AGENT_NAME`）；安全性有保障，因 Lark 反馈回调始终路由至原始 bot 实例。
- **成果**：彻底堵住历史旧卡片产生的 bot_name 污染，数据一致性完全修复。

---

### [#2023](https://github.com/Vispie-AI/VisPie_backend/pull/2023) — 修复 nanobot 上下文窗口与最大输出 token 配置偏差

- **合并日期**：2026-04-11
- **问题**：影子 nanobot 在 A/B 对比中持续慢于 OpenClaw 主线，初步排查 TTFT 偏高 5–10 秒、生成速度偏慢 1–5 秒。
- **根因**：nanobot 配置与 openclaw.json 存在两处关键不对齐——`CONTEXT_WINDOW` 为 618K（OpenClaw 200K），`max_tokens` 为 64K（OpenClaw 16,384）；3× 上下文窗口导致 LLM 加载过多历史，4× 输出长度导致生成时间虚高。
- **修复方案**：将 `CONTEXT_WINDOW` 从 618K 调整为 200K，`max_tokens` 从 64K 调整为 16,384，与 OpenClaw 配置对齐。
- **成果**：消除 A/B 对比中最主要的两处系统性偏差，影子模型延迟指标回归合理基线。

---

### [#2024](https://github.com/Vispie-AI/VisPie_backend/pull/2024) — 修复 vizzy-lark 耗时统计口径不一致导致 A/B 判定失真

- **合并日期**：2026-04-11
- **问题**：A/B 评判中影子 nanobot 始终显示更慢，即便 LLM 推理速度完全一致。
- **根因**：OpenClaw 的 `elapsed_sec` 从 `streamStartTime`（模型首字符输出）开始计时，**不含**会话加载、prompt 组装、上下文路由；而 nanobot 从 `start_time`（handler 入口）开始，**含全流程**。两者统计口径不同，使 OpenClaw 天然显得更快。
- **修复方案**：将 OpenClaw 计时起点改为 `routeMessage()` 入口（`routeStartTime`），与 nanobot `start_time` 覆盖范围一致。
- **成果**：A/B 对比计时口径统一，评判结果真实反映两侧 LLM 推理性能，排除系统性测量偏差。

---

## 文档建设

### [#2027](https://github.com/Vispie-AI/VisPie_backend/pull/2027) — 新增 auto-amy 架构双语系统地图

- **合并日期**：2026-04-12
- **内容**：为 auto-amy 系统补充完整架构文档，中英双语各一套，共 8 个文件，涵盖 4 个维度：
  | 编号 | 文档 | 说明 |
  |------|------|------|
  | 01 | 模块地图 | 系统组成（7 个模块、优先级索引、文件树） |
  | 02 | 数据流地图 | 数据流向（3 条管道：消息/记忆/响应，存储位置，数据生命周期） |
  | 03 | 调用链地图 | 请求执行路径 |
  | 04 | 状态机地图 | 系统状态与转换 |
- **成果**：新成员可快速建立系统全局认知，降低入职与协作沟通成本。

---

## 新功能开发

### [#2056](https://github.com/Vispie-AI/VisPie_backend/pull/2056) — 为 SLA 探针新增 error_reason 结构化错误追踪 ⏳ 待合并

- **创建日期**：2026-04-13 | **状态**：Open，审核中
- **背景**：生产 SLA 页面仅显示可用率数值（如"Amy 24h: 87%"），无法定位 13% 失败的具体原因（HTTP 503 × 47 vs 超时 × 6 完全不同的处置方向）。
- **方案**：在探针记录中写入结构化错误上下文，SLA 报告页面可展示失败原因分布，共 7 次提交，新增 322 行 / 删除 4 行，涵盖数据层、采集层、展示层。
- **预期成果**：SLA 报告从"可用率数字"升级为"可诊断的可观测性面板"，快速定位可用性下降根因。

---

## 总览

| PR | 标题 | 分类 | 状态 | 日期 |
|----|------|------|------|------|
| [#1969](https://github.com/Vispie-AI/VisPie_backend/pull/1969) | 修复 bot_name 硬编码为 "nanobot" | Bug 修复 | ✅ 已合并 | 2026-04-11 |
| [#2017](https://github.com/Vispie-AI/VisPie_backend/pull/2017) | 修复旧 Lark 卡片回调 bot_name 污染 | Bug 修复 | ✅ 已合并 | 2026-04-11 |
| [#2023](https://github.com/Vispie-AI/VisPie_backend/pull/2023) | 对齐 nanobot 上下文窗口与输出 token | 性能/质量优化 | ✅ 已合并 | 2026-04-11 |
| [#2024](https://github.com/Vispie-AI/VisPie_backend/pull/2024) | 修复 A/B 耗时统计口径不一致 | 性能/质量优化 | ✅ 已合并 | 2026-04-11 |
| [#2027](https://github.com/Vispie-AI/VisPie_backend/pull/2027) | 新增 auto-amy 双语架构系统地图 | 文档建设 | ✅ 已合并 | 2026-04-12 |
| [#2056](https://github.com/Vispie-AI/VisPie_backend/pull/2056) | SLA 探针新增 error_reason 追踪 | 新功能开发 | ⏳ 待合并 | 2026-04-13 |
