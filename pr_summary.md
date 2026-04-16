# 工作成果总结

> 统计区间：2026-04-10 ~ 2026-04-16 | 共 **12 个 PR**（已合并 11 个，待审核 1 个）
> 最后更新：2026-04-16

---

## Bug 修复

### [#2176](https://github.com/Vispie-AI/VisPie_backend/pull/2176) fix(migration): 修正 error_reason 迁移文件的 down_revision

- **日期**：2026-04-16 | **状态**：已合并 ✅
- **问题**：`down_revision` 使用了文件名字符串而非实际 revision ID，且 #2174 合并期间另一迁移抢先落地，导致 Alembic 无法解析迁移链，生产部署将被阻塞。
- **根因**：Alembic 只识别 revision ID，不识别文件名；并发 PR 合并造成 DB head 偏移。
- **修复方案**：将 `down_revision` 改为指向当前实际 DB head 的真实 revision ID。
- **成果**：迁移链恢复一致，消除生产部署阻塞风险。

---

### [#2175](https://github.com/Vispie-AI/VisPie_backend/pull/2175) fix(shadow): 错误/取消路径补发 shadow-done 回调 + 延长 cardCache TTL

- **日期**：2026-04-16 | **状态**：已合并 ✅
- **问题**：shadow nanobot 在出错（Bedrock 超时、工具失败）或被取消（Hatchet 超时、用户 /stop）时，`/shadow-done` 回调从未发出，OpenClaw 卡片永久卡在 `shadow=processing` 状态。
- **根因**：`_post_shadow_done()` 仅在成功路径调用，错误和取消路径均缺失。
- **修复方案**：抽取 `_post_shadow_done()` 辅助函数，在成功、取消、错误三条路径上均调用；错误/取消响应携带 ⚠️ 消息供 OpenClaw 展示故障原因。
- **成果**：消除卡片"永久 loading"问题，用户可及时获知 shadow 执行结果。

---

### [#2143](https://github.com/Vispie-AI/VisPie_backend/pull/2143) fix(shadow): 修复 shadow-done 回调跨队列合并时 trace_id 丢失导致对话卡片永久卡死

- **日期**：2026-04-15 | **状态**：已合并 ✅
- **问题**：Amy nanobot 正在处理消息时，后续 shadow 请求被放入 `ConversationState.pending` 队列，入队时未携带原始 `x-trace-id`。队列排空时生成全新 UUID，导致 `/shadow-done/<trace_id>` 回调指向一个 nanobot 从未记录的 trace，主卡片永久停留在 `shadow=processing` 状态。
- **根因**：`QueuedMessage` 数据类未保存 `trace_id` / `is_shadow` 字段，`_drain_pending` 合并批次时丢失了所有原始调用方的 trace 信息。
- **修复方案**：`QueuedMessage` 新增 `trace_id` + `is_shadow` 字段，在入队前从请求头填充；`_drain_pending` 收集所有待处理消息的 trace，写入 `MessageInput.source_trace_ids`；shadow-done POST 向每条原始 trace 逐一回调，所有合并调用方的卡片同步解除卡死。
- **成果**：连续快速发送 3 条消息时，所有对话卡片均能正常完成并显示 Shadow 面板，无遗漏。

---

### [#2140](https://github.com/Vispie-AI/VisPie_backend/pull/2140) fix(shadow): shadow 回复展示上限 1500 → 4000 字符，修复可见截断

- **日期**：2026-04-15 | **状态**：已合并 ✅
- **问题**：`shadow-judge.ts` 中 `shadow.response.slice(0, 1500)` 硬限制导致 Shadow 面板内回复被截断，而 Primary 回复无上限，用户可直观感知不对等。
- **根因**：早期为控制 Lark 卡片 payload 大小设置的保守阈值，未随实际使用场景调整。
- **修复方案**：将截断上限从 1500 提升至 4000 字符，覆盖典型 Claude 输出，同时远低于 Lark 卡片 payload 上限（~30KB）。Judge prompt 截断为独立调用路径，本次不做改动。
- **成果**：Shadow 面板完整展示回复内容，与 Primary 体验对齐，消除用户可见截断。

---

### [#2024](https://github.com/Vispie-AI/VisPie_backend/pull/2024) fix(vizzy-lark): 统一 elapsed_sec 计时起点，消除 shadow A/B 对比偏差

- **日期**：2026-04-11 | **状态**：已合并 ✅
- **问题**：OpenClaw 的 `elapsed_sec` 从 `streamStartTime`（模型首次输出）开始计算，不含 session 加载、prompt 组装、上下文路由等开销；nanobot 从 `start_time`（handler 入口）开始，包含全部开销。计时基准不一致导致 shadow A/B judge 中 nanobot 始终"看起来"更慢。
- **根因**：两套系统计时起点定义不同，非性能差异。
- **修复方案**：将 OpenClaw 计时起点改为 `routeMessage()` 入口的 `routeStartTime`，与 nanobot `start_time` 作用域一致；涉及正常 finalize 路径和孤立卡片路径共 2 处。
- **成果**：elapsed_sec 口径统一，A/B 对比结果更准确，可有效区分真实性能差异。

---

### [#2023](https://github.com/Vispie-AI/VisPie_backend/pull/2023) fix(nanobot): 上下文窗口和最大输出 token 与 OpenClaw 对齐，消除 shadow 性能虚高

- **日期**：2026-04-11 | **状态**：已合并 ✅
- **问题**：shadow nanobot 在 A/B 对比中持续比 OpenClaw primary 慢 6~15s，误导判断认为 nanobot 性能差。
- **根因**：`CONTEXT_WINDOW` 618K vs OpenClaw 200K（3 倍差距 → TTFT 多 5~10s）；`max_tokens` 64K vs OpenClaw 16384（4 倍差距 → 生成多 1~5s）。
- **修复方案**：`nanobot_server.py`、`bedrock_provider.py`、两份 docker-compose 及 provisioner.py 中的默认值统一改为 200K / 16384，与 `openclaw.json` 配置对齐。
- **成果**：nanobot shadow 响应时间降低约 6~15s，A/B judge 结果回归真实性能对比。

---

### [#2017](https://github.com/Vispie-AI/VisPie_backend/pull/2017) fix(nanobot): feedback 记录改用 env var 取 bot_name，消除旧卡片数据污染

- **日期**：2026-04-11 | **状态**：已合并 ✅
- **问题**：旧版 Lark 卡片的 action payload 中 `bot_name="nanobot"` 被硬编码；点击旧卡片反馈时，代码优先读取 payload 中的 `value.get("bot_name")`，导致 `agent_feedback` 表中仍然写入 `"nanobot"` 而非真实 agent 名。
- **根因**：#1969 修复了新卡片路径，但未处理存量旧卡片的 payload 值优先级问题。
- **修复方案**：在 `handle_card_action` 中移除 `value.get("bot_name")` 兜底链，统一只读取 env var（`BOT_NAME` → `AGENT_NAME`）。Lark 回调始终路由到原始 bot 容器，env var 天然权威。
- **成果**：无论新旧卡片，点击反馈均记录正确的 bot 名称，历史数据污染问题彻底修复。

---

### [#1969](https://github.com/Vispie-AI/VisPie_backend/pull/1969) fix(nanobot): agent_feedback 中 bot_name 改用 AGENT_NAME 兜底，修复错误归因

- **日期**：2026-04-10（合并 2026-04-11） | **状态**：已合并 ✅
- **问题**：绝大多数 `agent_feedback` 记录的 `bot_name` 均为 `"nanobot"` 而非实际 agent 名（george-ai、zane-ai 等），无法区分各 bot 的用户反馈。
- **根因**：所有 fleet bot docker-compose 均设置 `AGENT_NAME` 但未设置 `BOT_NAME`，代码直接 fallback 到硬编码字符串 `"nanobot"`。
- **修复方案**：改为 `os.environ.get("BOT_NAME") or os.environ.get("AGENT_NAME", "nanobot")`，优先 `BOT_NAME`，其次 `AGENT_NAME`，无需修改任何 docker-compose 文件。`nanobot_server.py` 和 `server.py` 共 6 处同步修改。
- **成果**：fleet bot 反馈记录中 bot_name 准确还原为各 agent 真实名称，数据可用性显著提升。

---

## 新功能开发

### [#2177](https://github.com/Vispie-AI/VisPie_backend/pull/2177) feat(alerter): Synthetic Health Alerter — 每 15 分钟 bot 可用性监控

- **日期**：2026-04-16 | **状态**：已合并 ✅
- **问题**：生产环境缺乏对 Amy/Eva 端到端可用性的主动监控，故障往往滞后发现，运维依赖用户反馈。
- **方案**：新增 `flows/synthetic_health_alert/` 流程，读取 `monitoring_probe_results` 数据，按三条告警规则（连续超时 Critical、writer_dead Critical、高超时率 Warning）生成分级 Lark 告警卡片推送运维 webhook；告警静默窗口 30 分钟防抖。
- **成果**：实现每 15 分钟一次合成健康探测，运维响应窗口从「用户反馈」缩短至分钟级。

---

### [#2174](https://github.com/Vispie-AI/VisPie_backend/pull/2174) feat(monitoring): 新增 error_reason 字段并删除废弃索引（PR #1）

- **日期**：2026-04-16 | **状态**：已合并 ✅
- **问题**：SLA 页面只能显示可用率数字，无法呈现故障根因；`monitoring_probe_results` 表存在两个从未被查询的冗余索引占用约 1 GB 磁盘空间。
- **方案**：删除 `idx_mon_probe_agent_layer_time`（621 MB，0 scans）和 `idx_mon_probe_layer_time`（422 MB，0 scans）；新增可空字段 `error_reason TEXT`、`error_code INT`、`error_category VARCHAR(50)`，在 Schema 层及两个摄入端点同步透传。
- **成果**：释放约 1 GB 索引空间，为后续 SLA 根因分析提供结构化数据基础，零停机迁移。

---

### [#2056](https://github.com/Vispie-AI/VisPie_backend/pull/2056) feat(monitoring): SLA 探针新增 error_reason 结构化错误上下文

- **日期**：2026-04-13 | **状态**：审核中 🔄
- **问题**：SLA 报告页仅显示可用率百分比（如"Amy 24h: 87%"），无法知晓宕机原因，运维排障效率低。
- **方案**：全链路新增结构化错误字段（error_reason / error_code / error_category），覆盖 DB ORM、Alembic 迁移、Pydantic 模型、探针写入、SLA 聚合查询、前端类型与 UI 共 7 层；错误分为 6 类（http_error / timeout / network / auth / internal / probe_error）；所有新字段 nullable，向后完全兼容。
- **预期成果**：SLA 页面新增 Top Error 列，可展开查看错误分类占比，运维可直接定位失败原因（如"47× HTTP 503 vs 6× timeout"）。
- **待完成**：生产 RDS 执行 Alembic 迁移后方可合并部署。

---

## 文档建设

### [#2027](https://github.com/Vispie-AI/VisPie_backend/pull/2027) docs: 新增 auto-amy 架构双语系统地图

- **日期**：2026-04-12 | **状态**：已合并 ✅
- **成果**：新增中英双语架构文档各 4 篇（共 8 个文件），覆盖模块地图、数据流地图、调用链地图、变更约束地图，为团队提供可参考的系统全景文档，降低新成员上手和跨模块修改的认知成本。

---

## 总览

| PR | 标题 | 类别 | 日期 | 状态 |
|----|------|------|------|------|
| [#2177](https://github.com/Vispie-AI/VisPie_backend/pull/2177) | feat(alerter): Synthetic Health Alerter 可用性监控 | 新功能 | 2026-04-16 | ✅ 已合并 |
| [#2176](https://github.com/Vispie-AI/VisPie_backend/pull/2176) | fix(migration): 修正迁移文件 down_revision | Bug修复 | 2026-04-16 | ✅ 已合并 |
| [#2175](https://github.com/Vispie-AI/VisPie_backend/pull/2175) | fix(shadow): 错误/取消路径补发 shadow-done 回调 | Bug修复 | 2026-04-16 | ✅ 已合并 |
| [#2174](https://github.com/Vispie-AI/VisPie_backend/pull/2174) | feat(monitoring): 新增 error_reason 字段 + 删废弃索引 | 新功能 | 2026-04-16 | ✅ 已合并 |
| [#2143](https://github.com/Vispie-AI/VisPie_backend/pull/2143) | fix(shadow): 保留 trace_id，修复 shadow-done fan-out 卡死 | Bug修复 | 2026-04-15 | ✅ 已合并 |
| [#2140](https://github.com/Vispie-AI/VisPie_backend/pull/2140) | fix(shadow): shadow 展示上限 1500→4000 | Bug修复 | 2026-04-15 | ✅ 已合并 |
| [#2056](https://github.com/Vispie-AI/VisPie_backend/pull/2056) | feat(monitoring): SLA 探针结构化 error_reason | 新功能 | 2026-04-13 | 🔄 审核中 |
| [#2027](https://github.com/Vispie-AI/VisPie_backend/pull/2027) | docs: auto-amy 双语架构系统地图 | 文档建设 | 2026-04-12 | ✅ 已合并 |
| [#2024](https://github.com/Vispie-AI/VisPie_backend/pull/2024) | fix(vizzy-lark): 统一 elapsed_sec 计时基准 | Bug修复 | 2026-04-11 | ✅ 已合并 |
| [#2023](https://github.com/Vispie-AI/VisPie_backend/pull/2023) | fix(nanobot): 对齐上下文窗口和 max_tokens | 性能/质量优化 | 2026-04-11 | ✅ 已合并 |
| [#2017](https://github.com/Vispie-AI/VisPie_backend/pull/2017) | fix(nanobot): feedback bot_name 改用 env var | Bug修复 | 2026-04-11 | ✅ 已合并 |
| [#1969](https://github.com/Vispie-AI/VisPie_backend/pull/1969) | fix(nanobot): AGENT_NAME 兜底修复 bot_name 归因 | Bug修复 | 2026-04-10 | ✅ 已合并 |
