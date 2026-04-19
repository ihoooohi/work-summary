# 工作成果总结

**统计周期**：2026-04-10 ～ 2026-04-18　｜　**PR 总数**：18　｜　**已合并**：16　｜　**待合并**：1　｜　**已关闭（未合并）**：1

---

## 一、新功能开发（feat:）

### [#2241](https://github.com/Vispie-AI/VisPie_backend/pull/2241) feat(daily-report): 将 Amy Liveness 集成至 George AI 日报卡片
- **日期**：2026-04-18　｜　**状态**：✅ 已合并
- **问题**：日报卡片仅有应用层指标（技能调用、会话健康），无法反映 Amy 容器本身的存活状态，导致低流量误读。
- **根因**：`amy-liveness.sh` 每 5 分钟探测一次，数据已入库，但日报渲染层未消费。
- **方案**：新增独立模块 `infra_health.py`，查询 `monitoring_probe_results` 并在卡片顶部渲染 `🛡️ Infra` 块；健康时单行展示，发生故障时自动展开事件详情；通过环境变量 `REPORT_INFRA_HEALTH=1` 暗启动。
- **成果**：19 个单元测试全通过；日报卡片可一眼区分"业务平静"与"容器宕机"，信息误导率显著降低。

---

### [#2227](https://github.com/Vispie-AI/VisPie_backend/pull/2227) feat(monitoring): 基于 Liveness 的告警器 + 持久化去重
- **日期**：2026-04-17　｜　**状态**：✅ 已合并
- **问题**：旧的 e2e 合成探针在过去 14 天产生 7002 次虚假超时告警，每 15 分钟重复发 5 张 Lark 卡片。
- **根因**：①合成探针设计缺陷（机器人无法在仅自己的群内检测到回复）；②去重 `dict` 存储于模块级变量，Prefect 每次启动新进程导致状态丢失。
- **方案**：引入 4 条基于 `liveness` 探针的告警规则（infra_down / process_missing / high_error_rate / liveness_silent）；新增 `amy-liveness.sh` 宿主侧脚本，无需容器内工具；去重状态写入 PostgreSQL（`monitoring_alert_dedup`）实现跨进程持久化。
- **成果**：27 个测试全通过；告警噪音从每天数百条降至精准触发；30 分钟冷却窗口真正生效。

---

### [#2226](https://github.com/Vispie-AI/VisPie_backend/pull/2226) feat(monitoring): 新增 monitoring_alert_dedup 表（数据库迁移）
- **日期**：2026-04-17　｜　**状态**：🚫 已关闭（未合并，功能由 #2227 直接包含）
- **内容**：为 #2227 提供前置数据库迁移，创建 `monitoring_alert_dedup` 复合主键表。

---

### [#2177](https://github.com/Vispie-AI/VisPie_backend/pull/2177) feat(alerter): 合成健康告警器——每 15 分钟可用性监控
- **日期**：2026-04-16　｜　**状态**：✅ 已合并
- **问题**：Amy/Eva 可用性无自动告警，故障只能依赖人工发现。
- **方案**：新建 `flows/synthetic_health_alert/` Prefect 流，实现 3 条告警规则（consecutive_timeouts / writer_dead / high_timeout_rate），30 分钟内存去重，定时 `*/15 * * * *`，Lark OPS 群推送。
- **成果**：23 个测试全通过；告警平均发现时延从"无限"压缩至 ≤15 分钟。

---

### [#2174](https://github.com/Vispie-AI/VisPie_backend/pull/2174) feat(monitoring): 添加 error_reason 字段并删除冗余索引（PR #1）
- **日期**：2026-04-16　｜　**状态**：✅ 已合并
- **问题**：`monitoring_probe_results` 存在两个零扫描冗余索引，合计占用约 1 GB 数据库空间；失败探针缺少错误原因字段，排查困难。
- **方案**：Alembic 迁移删除 `idx_mon_probe_agent_layer_time`（621 MB）和 `idx_mon_probe_layer_time`（422 MB）；新增 `error_reason / error_code / error_category` 三个 nullable 字段，向后兼容。
- **成果**：5 个单元测试通过；释放约 1 GB 索引空间；为后续精细化错误分类奠定模型基础。

---

### [#2056](https://github.com/Vispie-AI/VisPie_backend/pull/2056) feat(monitoring): 在 SLA 探针中追踪 error_reason
- **日期**：2026-04-13　｜　**状态**：🔄 待合并（Open）
- **内容**：在 SLA 探针写入时填充 #2174 新增的 `error_reason` 字段，实现端到端错误原因闭环追踪。

---

## 二、Bug 修复（fix:）

### [#2228](https://github.com/Vispie-AI/VisPie_backend/pull/2228) fix(monitoring): 补充 liveness probe_type + 修正 L2 进程检测
- **日期**：2026-04-17　｜　**状态**：✅ 已合并
- **问题**：`amy-liveness.sh` 首次上线后所有 POST 返回 HTTP 422，且 L2 进程检测持续为 `false`，导致无一条探针数据入库。
- **根因**：①`ProbeResultCreate.probe_type` 的 Literal 类型未包含 `"liveness"`，Pydantic 校验拒绝全部请求；②容器内无 `pgrep` 可执行文件，`docker exec` 静默失败。
- **方案**：在后端模型中添加 `"liveness"` 到 `VALID_PROBE_TYPES`；L2 改为宿主侧 `docker top | grep openclaw-gateway`，无需容器内工具。
- **成果**：探针数据正常入库，liveness 监控体系完整生效。

---

### [#2225](https://github.com/Vispie-AI/VisPie_backend/pull/2225) fix(vizzy-lark): 修复折叠面板内标题和表格的渲染
- **日期**：2026-04-17　｜　**状态**：✅ 已合并
- **问题**：Amy 回复 Done 卡片的 Shadow/Activity/Reasoning 折叠面板内，`####` 标题和 `|col|` 表格以原始文本展示，完全不可读。
- **根因**：Feishu `markdown` 标签不支持 ATX 标题和管道表格；且 `table` 组件官方明确禁止嵌套于面板内。
- **方案**：新增 `sanitizePanelMarkdown()` 转换函数：ATX 标题 → `**bold**`，管道表格 → 带标签的项目符号行；代码围栏内内容保持原样。
- **成果**：13 个单元测试通过；折叠面板内容可读性从无到有。

---

### [#2224](https://github.com/Vispie-AI/VisPie_backend/pull/2224) fix(nanobot/feishu): 修复最终流式卡片中表格和标题渲染
- **日期**：2026-04-17　｜　**状态**：✅ 已合并
- **问题**：nanobot 在 Feishu 中发送含 markdown 表格/标题的最终回复时，显示原始字符 `| col |` 和 `####`。
- **根因**：流式结束时调用 `_stream_update_text_sync` 写入单个 `markdown` 元素，Feishu 该标签不渲染管道表格和 ATX 标题。
- **方案**：`_stream_end` 时删除流式卡片，改用现有 `_build_card_elements` 辅助函数重新发送 `interactive` 卡片，管道表格自动转换为原生 `{tag:"table"}` 元素，标题转为加粗。
- **成果**：nanobot 最终卡片渲染效果与 amy-prod/eva-prod（OpenClaw 路径）完全一致。

---

### [#2176](https://github.com/Vispie-AI/VisPie_backend/pull/2176) fix(migration): 修正 error_reason 迁移的 down_revision
- **日期**：2026-04-16　｜　**状态**：✅ 已合并
- **问题**：#2174 的 Alembic 迁移文件 `down_revision` 写的是文件名而非实际 revision ID，导致迁移链断裂。
- **方案**：将 `down_revision` 修正为当前 DB head 的实际 revision ID `56c43216fc2a`，恢复线性迁移链。
- **成果**：迁移链完整，`alembic upgrade head` 可正常执行。

---

### [#2175](https://github.com/Vispie-AI/VisPie_backend/pull/2175) fix(shadow): 错误/取消路径补发 shadow-done 回调 + 延长 cardCache TTL
- **日期**：2026-04-16　｜　**状态**：✅ 已合并
- **问题**：Shadow nanobot 报错或被取消时，OpenClaw 卡片永久卡在 `shadow=processing` 状态；Opus shadow 运行超过 5 分钟时 cardCache 过期导致死锁。
- **根因**：`/shadow-done` 回调仅在成功路径调用；`shadow-judge.ts` cardCache TTL 仅 5 分钟，Opus 正常运行时长为 5-10 分钟。
- **方案**：提取 `_post_shadow_done()` 辅助函数，在成功、取消、错误三条路径均调用；cardCache TTL 从 5 分钟延长至 15 分钟。
- **成果**：shadow 卡片不再出现永久"处理中"僵死状态。

---

### [#2143](https://github.com/Vispie-AI/VisPie_backend/pull/2143) fix(shadow): 队列排空时跨 shadow-done fan-out 保留 trace_id
- **日期**：2026-04-15　｜　**状态**：✅ 已合并
- **问题**：队列合并路径下 shadow-done 分发时 `trace_id` 丢失，导致 OpenClaw 卡片无法正确关联 shadow 结果。
- **成果**：修复后 shadow-done fan-out 在所有路径下均正确传递 `trace_id`。

---

### [#2140](https://github.com/Vispie-AI/VisPie_backend/pull/2140) fix(shadow): 将 shadow 响应展示上限从 1500 提升至 4000 字符
- **日期**：2026-04-15　｜　**状态**：✅ 已合并
- **问题**：Opus shadow 响应常超过 1500 字符，卡片内容被截断，用户看不到完整推理过程。
- **成果**：上限提升至 4000 字符，Opus 详细推理内容完整可见。

---

### [#2024](https://github.com/Vispie-AI/VisPie_backend/pull/2024) fix(vizzy-lark): 对齐 elapsed_sec 计时以确保 shadow A/B 测试公平
- **日期**：2026-04-11　｜　**状态**：✅ 已合并
- **问题**：OpenClaw 与 nanobot 的 `elapsed_sec` 起点不一致，导致 shadow A/B 对比数据失真。
- **成果**：计时基准统一，shadow 响应时间对比数据准确可信。

---

### [#2023](https://github.com/Vispie-AI/VisPie_backend/pull/2023) fix(nanobot): 对齐 nanobot 与 OpenClaw 的上下文窗口和最大输出 token
- **日期**：2026-04-11　｜　**状态**：✅ 已合并
- **问题**：nanobot 上下文窗口和最大输出 token 配置与 OpenClaw 不一致，shadow A/B 对比条件不等价。
- **成果**：参数对齐后，shadow 实验在相同约束条件下运行，比较结果更具参考价值。

---

### [#2017](https://github.com/Vispie-AI/VisPie_backend/pull/2017) fix(nanobot): 用环境变量管理 bot_name，修复反馈卡片数据陈旧问题
- **日期**：2026-04-11　｜　**状态**：✅ 已合并
- **问题**：反馈卡片中 `bot_name` 硬编码，部署到不同 agent 时记录错误的机器人名称。
- **成果**：改为读取环境变量后，反馈记录准确关联到实际响应的 agent。

---

### [#1969](https://github.com/Vispie-AI/VisPie_backend/pull/1969) fix(nanobot): 以 AGENT_NAME 作为 bot_name 的回退值
- **日期**：2026-04-10 创建 / 2026-04-11 合并　｜　**状态**：✅ 已合并
- **问题**：反馈记录中 `bot_name` 在未设置专用变量时为空，无法溯源至具体 agent。
- **成果**：兜底使用 `AGENT_NAME` 环境变量，所有反馈记录均有有效 bot 标识。

---

## 三、文档建设（docs:）

### [#2027](https://github.com/Vispie-AI/VisPie_backend/pull/2027) docs: 添加 auto-amy 架构双语系统图
- **日期**：2026-04-12　｜　**状态**：✅ 已合并
- **成果**：中英双语架构图落地，新成员可快速理解 auto-amy 整体系统结构。

---

## 总览

| PR | 标题（简） | 类型 | 状态 | 合并日期 |
|---|---|---|---|---|
| [#2241](https://github.com/Vispie-AI/VisPie_backend/pull/2241) | Amy Liveness 集成日报卡片 | feat | ✅ 已合并 | 2026-04-18 |
| [#2228](https://github.com/Vispie-AI/VisPie_backend/pull/2228) | 补充 liveness probe_type + 修正 L2 检测 | fix | ✅ 已合并 | 2026-04-17 |
| [#2227](https://github.com/Vispie-AI/VisPie_backend/pull/2227) | 基于 Liveness 的告警器 + 持久化去重 | feat | ✅ 已合并 | 2026-04-17 |
| [#2226](https://github.com/Vispie-AI/VisPie_backend/pull/2226) | 新增 monitoring_alert_dedup 迁移 | feat | 🚫 已关闭 | — |
| [#2225](https://github.com/Vispie-AI/VisPie_backend/pull/2225) | 修复折叠面板标题和表格渲染 | fix | ✅ 已合并 | 2026-04-17 |
| [#2224](https://github.com/Vispie-AI/VisPie_backend/pull/2224) | 修复流式卡片最终渲染 | fix | ✅ 已合并 | 2026-04-17 |
| [#2177](https://github.com/Vispie-AI/VisPie_backend/pull/2177) | 合成健康告警器 | feat | ✅ 已合并 | 2026-04-16 |
| [#2176](https://github.com/Vispie-AI/VisPie_backend/pull/2176) | 修正迁移 down_revision | fix | ✅ 已合并 | 2026-04-16 |
| [#2175](https://github.com/Vispie-AI/VisPie_backend/pull/2175) | shadow 错误/取消路径补发回调 + TTL | fix | ✅ 已合并 | 2026-04-16 |
| [#2174](https://github.com/Vispie-AI/VisPie_backend/pull/2174) | 添加 error_reason 字段 + 删除冗余索引 | feat | ✅ 已合并 | 2026-04-16 |
| [#2143](https://github.com/Vispie-AI/VisPie_backend/pull/2143) | 队列排空时保留 trace_id | fix | ✅ 已合并 | 2026-04-15 |
| [#2140](https://github.com/Vispie-AI/VisPie_backend/pull/2140) | shadow 响应上限 1500→4000 字符 | fix | ✅ 已合并 | 2026-04-15 |
| [#2056](https://github.com/Vispie-AI/VisPie_backend/pull/2056) | SLA 探针追踪 error_reason | feat | 🔄 待合并 | — |
| [#2027](https://github.com/Vispie-AI/VisPie_backend/pull/2027) | auto-amy 架构双语系统图 | docs | ✅ 已合并 | 2026-04-12 |
| [#2024](https://github.com/Vispie-AI/VisPie_backend/pull/2024) | 对齐 elapsed_sec 计时 | fix | ✅ 已合并 | 2026-04-11 |
| [#2023](https://github.com/Vispie-AI/VisPie_backend/pull/2023) | 对齐上下文窗口和最大输出 token | fix | ✅ 已合并 | 2026-04-11 |
| [#2017](https://github.com/Vispie-AI/VisPie_backend/pull/2017) | bot_name 改用环境变量 | fix | ✅ 已合并 | 2026-04-11 |
| [#1969](https://github.com/Vispie-AI/VisPie_backend/pull/1969) | bot_name 兜底使用 AGENT_NAME | fix | ✅ 已合并 | 2026-04-11 |

---

*最后更新：2026-04-19*
