# 工作成果总结

> 统计周期：2026-04-10 ~ 2026-04-17 | 共 17 个 PR（已合并 15 · 关闭未合并 1 · 待合并 1）
> 最后更新：2026-04-17

---

## 一、Bug 修复（fix:）

### [#2228](https://github.com/Vispie-AI/VisPie_backend/pull/2228) fix(monitoring): liveness probe_type 缺失 + L2 进程检测修复
- **日期**：2026-04-17 | **状态**：✅ 已合并
- **问题**：`ProbeResultCreate.probe_type` 的 `Literal` 未包含 `"liveness"`，导致所有 liveness 行 POST 均被 Pydantic 以 422 拒绝，无数据入库；L2 check 使用 `pgrep` 但 amy-prod 容器内无此工具，静默失败。
- **根因**：模型层 Literal 定义遗漏新枚举值；依赖容器内工具而非宿主 `/proc`。
- **修复**：在 `models.py` 的 `VALID_PROBE_TYPES` 和 Literal 中补充 `"liveness"`；L2 改为宿主侧 `docker top amy-prod | grep openclaw-gateway`，无需容器内任何工具。
- **成果**：liveness 探针数据正常入库，L2 进程检测 100% 可靠。

### [#2225](https://github.com/Vispie-AI/VisPie_backend/pull/2225) fix(vizzy-lark): 折叠面板内标题与表格渲染修复
- **日期**：2026-04-17 | **状态**：✅ 已合并
- **问题**：Shadow / Activity / Reasoning 折叠面板中 `####` 和 `| col |` 显示为原始 Markdown 文本，不可读。
- **根因**：飞书 `markdown` 标签不支持 ATX 标题和管道表格；`table` 组件无法嵌套在折叠面板内（官方文档限制）。
- **修复**：新增 `sanitizePanelMarkdown()`，将标题转为 `**bold**`，表格转为带标签的 bullet rows，代码块内容受保护不被转换。接入 `buildCollapsiblePanel` 和 `buildShadowPanel` 两处调用点。
- **成果**：13 个单元测试全通过；折叠面板内容可读性大幅提升。

### [#2224](https://github.com/Vispie-AI/VisPie_backend/pull/2224) fix(nanobot/feishu): 最终流式卡片中表格与标题渲染修复
- **日期**：2026-04-17 | **状态**：✅ 已合并
- **问题**：nanobot Feishu 频道流式回复结束时，通过 `markdown` 元素展示最终内容，导致管道表格和 `#` 标题以原始字符显示。
- **根因**：`_stream_end` 路径直接写入 CardKit `markdown` 标签，未经表格/标题解析。
- **修复**：`_stream_end` 时删除流式卡片，重发为调用现有 `_build_card_elements` / `_split_elements_by_table_limit` 构建的 `interactive` 卡片，与 OpenClaw Done-card 路径一致。顺带清理了不再使用的 `_close_streaming_mode_sync`。
- **成果**：nanobot 最终回复卡片渲染效果与 amy-prod/eva-prod 一致。

### [#2176](https://github.com/Vispie-AI/VisPie_backend/pull/2176) fix(migration): 修正 error_reason 迁移的 down_revision
- **日期**：2026-04-16 | **状态**：✅ 已合并
- **问题**：迁移文件 `down_revision` 使用了文件名而非真实 revision ID，Alembic 无法解析；另一迁移并行落地导致 head 变更。
- **根因**：迁移链指针错误，无法升级数据库。
- **修复**：将 `down_revision` 改为实际当前 DB head `"56c43216fc2a"`，形成线性链：`56c43216fc2a` → `20260416_1400`。
- **成果**：数据库迁移链恢复正常，无多头分叉。

### [#2175](https://github.com/Vispie-AI/VisPie_backend/pull/2175) fix(shadow): error/cancel 路径通知 OpenClaw + 延长 cardCache TTL
- **日期**：2026-04-16 | **状态**：✅ 已合并
- **问题**：shadow nanobot 出错或被取消时，`/shadow-done` 回调从未发送，OpenClaw 卡片永久卡在 `shadow=processing`；Opus shadow 运行常需 5-10 分钟，旧 5 分钟 TTL 导致 cardCache 过期死锁。
- **根因**：`/shadow-done` 仅在成功路径调用；TTL 设计未考虑 Opus 模型耗时。
- **修复**：提取 `_post_shadow_done()` helper，在成功/取消/错误三条路径均调用；`shadow-judge.ts` cardCache TTL 从 5 分钟延长至 15 分钟。
- **成果**：所有路径下 shadow 面板均能正确更新，消除永久卡死问题。

### [#2143](https://github.com/Vispie-AI/VisPie_backend/pull/2143) fix(shadow): 队列排空时跨消息保留 trace_id 用于 shadow-done fan-out
- **日期**：2026-04-15 | **状态**：✅ 已合并
- **问题**：Amy nanobot 并发处理时，后续 shadow 请求入队时未携带原始 `x-trace-id`，队列排空后生成新 UUID，`/shadow-done/<trace_id>` 回调指向未知 trace，所有合并消息的卡片永远卡在 `shadow=processing`。
- **根因**：`QueuedMessage` 未持久化 trace_id；`_drain_pending` 未对多个原始 trace 进行 fan-out。
- **修复**：`QueuedMessage` 增加 `trace_id` + `is_shadow` 字段；`_drain_pending` 收集所有排队消息的 trace 到 `MessageInput.source_trace_ids`；shadow-done POST fan-out 到每个原始 trace。
- **成果**：多消息并发场景下所有 shadow 面板均能正常解锁。

### [#2140](https://github.com/Vispie-AI/VisPie_backend/pull/2140) fix(shadow): shadow 回复展示上限 1500 → 4000 字符
- **日期**：2026-04-15 | **状态**：✅ 已合并
- **问题**：shadow 面板内容在 1500 字符处被截断，用户明显感知到回复不完整。
- **根因**：`shadow-judge.ts` 中 `slice(0, 1500)` 硬编码，与主回复无上限的不对称截断。
- **修复**：`slice(0, 1500)` → `slice(0, 4000)`，覆盖典型 Claude 输出，仍远低于飞书卡片 ~30KB 限制。
- **成果**：shadow 面板完整展示，用户体验显著改善。

### [#2024](https://github.com/Vispie-AI/VisPie_backend/pull/2024) fix(vizzy-lark): elapsed_sec 计时对齐，确保 shadow A/B 比较公平
- **日期**：2026-04-11 | **状态**：✅ 已合并
- **问题**：OpenClaw 从模型首次输出计时（不含 session 加载/prompt 组装），nanobot 从 handler 入口计时，导致 shadow 在 A/B judge 中始终显得更慢。
- **修复**：OpenClaw 改为从 `routeMessage()` 入口 (`routeStartTime`) 开始计时，与 nanobot 的 `start_time` 作用域一致。
- **成果**：A/B 性能对比数据更客观准确。

### [#2023](https://github.com/Vispie-AI/VisPie_backend/pull/2023) fix(nanobot): 对齐 context window 和 max output tokens 与 OpenClaw
- **日期**：2026-04-11 | **状态**：✅ 已合并
- **问题**：nanobot CONTEXT_WINDOW 618K（OpenClaw 200K）、max_tokens 64K（OpenClaw 16384），导致 shadow 在 A/B 中 TTFT 多 5-10s、生成时间多 1-5s。
- **修复**：CONTEXT_WINDOW 618K → 200K，max_tokens 64K → 16,384。
- **成果**：消除 A/B 比较中最大的两项系统性偏差。

### [#2017](https://github.com/Vispie-AI/VisPie_backend/pull/2017) fix(nanobot): feedback 使用 env var bot_name，修复旧卡片数据残留
- **日期**：2026-04-11 | **状态**：✅ 已合并
- **问题**：旧飞书卡片 action payload 中 `bot_name="nanobot"` 固化，点击 feedback 时代码优先读取 payload 中的旧值，导致仍记录为 `nanobot`。
- **修复**：`handle_card_action` 中移除 `value.get("bot_name")` fallback，完全依赖 env var（`BOT_NAME` → `AGENT_NAME`）。
- **成果**：feedback 记录 bot_name 完全准确，旧卡片不再污染数据。

### [#1969](https://github.com/Vispie-AI/VisPie_backend/pull/1969) fix(nanobot): feedback 记录使用 AGENT_NAME 作为 bot_name fallback
- **日期**：2026-04-10 | **状态**：✅ 已合并
- **问题**：所有 fleet bot 的 `agent_feedback` 记录 `bot_name` 均为 `"nanobot"`，无法区分具体 agent（george-ai、zane-ai 等）。
- **根因**：docker-compose 只设置 `AGENT_NAME` 未设 `BOT_NAME`，代码回退到硬编码字符串 `"nanobot"`。
- **修复**：`os.environ.get("BOT_NAME") or os.environ.get("AGENT_NAME", "nanobot")`，无需改动 docker-compose。
- **成果**：feedback 数据中 agent 归因准确，支持后续分析。

---

## 二、新功能开发（feat:）

### [#2227](https://github.com/Vispie-AI/VisPie_backend/pull/2227) feat(monitoring): 基于 liveness 的告警器 + 持久化去重
- **日期**：2026-04-17 | **状态**：✅ 已合并
- **问题**：旧 e2e 合成探针设计缺陷导致 99.97% 误报（7002 次超时 / 2 次健康）；模块级 `_dedup` dict 在 Prefect 每次 tick 进程重启后清空，30 分钟冷却从未生效，每 15 分钟发 5 张告警卡片造成告警风暴。
- **根因**：探针信号不可靠；告警去重依赖内存状态，无法跨进程持久化。
- **修复**：重写为基于 liveness 探针的 4 条规则（R1 infra_down、R2 process_missing、R3 high_error_rate、R4 liveness_silent）；去重改用数据库 UPSERT（依赖 #2226 的 `monitoring_alert_dedup` 表）。27/27 单元测试通过。
- **成果**：告警误报率大幅降低，冷却窗口跨进程生效，监控可靠性质的提升。

### [#2226](https://github.com/Vispie-AI/VisPie_backend/pull/2226) feat(monitoring): 新增 monitoring_alert_dedup 表（迁移）
- **日期**：2026-04-17 | **状态**：⚠️ 已关闭（未合并）
- **内容**：为持久化告警去重状态创建 `monitoring_alert_dedup` 表，作为 #2227 的前置依赖。复合主键 `(agent_name, platform, rule)` 使 UPSERT 语义简洁。
- **备注**：该 PR 已关闭，相关功能通过 #2227/2228 等后续 PR 覆盖。

### [#2177](https://github.com/Vispie-AI/VisPie_backend/pull/2177) feat(alerter): 每 15 分钟合成健康检查告警流
- **日期**：2026-04-16 | **状态**：✅ 已合并
- **内容**：新增 `flows/synthetic_health_alert/` Prefect 流，基于 `monitoring_probe_results` 表监控 Amy/Eva 端到端可用性，通过 Lark webhook 向 ops 群发告警卡片。3 条告警规则（连续超时、writer_dead、高超时率）+ 30 分钟内存去重（后由 #2227 升级为持久化）。23/23 单元测试通过。
- **成果**：建立系统健康自动监控基础设施。

### [#2174](https://github.com/Vispie-AI/VisPie_backend/pull/2174) feat(monitoring): 新增 error_reason 字段 + 删除无用索引
- **日期**：2026-04-16 | **状态**：✅ 已合并
- **内容**：删除 `monitoring_probe_results` 上两个零扫描无用索引（共节省约 1 GB 存储）；新增 `error_reason TEXT`、`error_code INT`、`error_category VARCHAR(50)` 三个可选字段，全链路透传。5 个单元测试通过。
- **成果**：监控数据具备错误溯源能力，数据库存储优化约 1 GB。

### [#2056](https://github.com/Vispie-AI/VisPie_backend/pull/2056) feat(monitoring): SLA 探针新增结构化 error_reason 追踪
- **日期**：2026-04-13 | **状态**：🔄 待合并（Open）
- **内容**：为 SLA 报告页添加错误原因上下文，让 "Amy 24h: 87%" 不再只显示数字，而是能区分 47× HTTP 503 vs 6× timeout 等具体原因。涵盖 DB 层、API 层、前端展示层完整改动。
- **备注**：PR 开放中，待审核合并。

---

## 三、文档建设（docs:）

### [#2027](https://github.com/Vispie-AI/VisPie_backend/pull/2027) docs: 新增 auto-amy 架构双语系统地图
- **日期**：2026-04-12 | **状态**：✅ 已合并
- **内容**：新增 4 部分架构文档（英文 `docs/system-map/` + 中文 `docs/system-map-cn/`），共 8 个文件，涵盖模块地图、数据流图、调用链、部署拓扑。
- **成果**：系统架构知识文档化，降低团队新成员理解成本。

---

## 四、总览

| PR | 标题摘要 | 类型 | 状态 | 日期 |
|----|---------|------|------|------|
| [#2228](https://github.com/Vispie-AI/VisPie_backend/pull/2228) | liveness probe_type + L2 进程检测修复 | fix | ✅ 已合并 | 2026-04-17 |
| [#2227](https://github.com/Vispie-AI/VisPie_backend/pull/2227) | liveness 告警器 + 持久化去重 | feat | ✅ 已合并 | 2026-04-17 |
| [#2226](https://github.com/Vispie-AI/VisPie_backend/pull/2226) | monitoring_alert_dedup 表迁移 | feat | ⚠️ 已关闭 | 2026-04-17 |
| [#2225](https://github.com/Vispie-AI/VisPie_backend/pull/2225) | 折叠面板标题/表格渲染修复 | fix | ✅ 已合并 | 2026-04-17 |
| [#2224](https://github.com/Vispie-AI/VisPie_backend/pull/2224) | nanobot 最终流式卡片渲染修复 | fix | ✅ 已合并 | 2026-04-17 |
| [#2177](https://github.com/Vispie-AI/VisPie_backend/pull/2177) | 合成健康检查告警流 | feat | ✅ 已合并 | 2026-04-16 |
| [#2176](https://github.com/Vispie-AI/VisPie_backend/pull/2176) | 迁移 down_revision 修正 | fix | ✅ 已合并 | 2026-04-16 |
| [#2175](https://github.com/Vispie-AI/VisPie_backend/pull/2175) | shadow error/cancel 回调 + TTL 延长 | fix | ✅ 已合并 | 2026-04-16 |
| [#2174](https://github.com/Vispie-AI/VisPie_backend/pull/2174) | error_reason 字段 + 无用索引清理 | feat | ✅ 已合并 | 2026-04-16 |
| [#2143](https://github.com/Vispie-AI/VisPie_backend/pull/2143) | shadow 队列 trace_id fan-out 修复 | fix | ✅ 已合并 | 2026-04-15 |
| [#2140](https://github.com/Vispie-AI/VisPie_backend/pull/2140) | shadow 展示上限 1500→4000 字符 | fix | ✅ 已合并 | 2026-04-15 |
| [#2056](https://github.com/Vispie-AI/VisPie_backend/pull/2056) | SLA 探针 error_reason 追踪 | feat | 🔄 待合并 | 2026-04-13 |
| [#2027](https://github.com/Vispie-AI/VisPie_backend/pull/2027) | auto-amy 架构双语文档 | docs | ✅ 已合并 | 2026-04-12 |
| [#2024](https://github.com/Vispie-AI/VisPie_backend/pull/2024) | elapsed_sec 计时对齐 | fix | ✅ 已合并 | 2026-04-11 |
| [#2023](https://github.com/Vispie-AI/VisPie_backend/pull/2023) | nanobot context/max_tokens 对齐 | fix | ✅ 已合并 | 2026-04-11 |
| [#2017](https://github.com/Vispie-AI/VisPie_backend/pull/2017) | feedback bot_name env var 修复 | fix | ✅ 已合并 | 2026-04-11 |
| [#1969](https://github.com/Vispie-AI/VisPie_backend/pull/1969) | AGENT_NAME bot_name fallback 修复 | fix | ✅ 已合并 | 2026-04-10 |

**合计：17 个 PR | 已合并 15 | 关闭未合并 1 | 待合并 1**
