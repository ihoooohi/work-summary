# 工作成果总结

> 统计周期：2026-04-10 ~ 2026-04-22 | 共 44 个 PR（已合并 40 · 关闭未合并 3 · 待合并 0）
> 最后更新：2026-04-22

---

## 一、Bug 修复（fix:）

### [#2303](https://github.com/Vispie-AI/VisPie_backend/pull/2303) fix(shadow-judge): handle Lark v2 card schema
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：Shadow Judge 模块无法处理 Lark v2 卡片 schema 格式。
- **修复**：适配 Lark v2 卡片 schema，确保 shadow judge 正常解析。
- **成果**：Shadow Judge 支持最新 Lark 卡片格式，评判功能正常。

### [#2302](https://github.com/Vispie-AI/VisPie_backend/pull/2302) fix(mobile): align Profile niche to L1+L2 taxonomy (prevents data loss)
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：移动端 Profile 页面 niche 字段未对齐 L1+L2 分类体系，存在数据丢失风险。
- **修复**：将 Profile niche 字段与 L1+L2 分类体系对齐，防止数据丢失。
- **成果**：移动端用户档案分类数据完整，与后端分类体系保持一致。

### [#2301](https://github.com/Vispie-AI/VisPie_backend/pull/2301) fix(tiktok-ads): improve pagination, security, and documentation
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：TikTok 广告接口分页逻辑有误，存在安全与文档缺失问题。
- **修复**：改进分页处理逻辑，加强安全校验并补充接口文档。
- **成果**：TikTok 广告数据拉取稳定、安全，接口文档完整。

### [#2300](https://github.com/Vispie-AI/VisPie_backend/pull/2300) Codex/fanka top10 missing media followup
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：Fanka TOP10 广告分析存在媒体数据缺失的后续问题。
- **修复**：补充修复 Fanka TOP10 分析中媒体字段缺失的处理逻辑。
- **成果**：Fanka TOP10 广告分析媒体数据完整，展示正确。

### [#2298](https://github.com/Vispie-AI/VisPie_backend/pull/2298) fix(twilio-conversations): refresh inbox after sending message
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：发送消息后收件箱未自动刷新，显示状态滞后。
- **修复**：发送消息成功后自动触发收件箱刷新操作。
- **成果**：消息发送后收件箱即时更新，用户无需手动刷新。

### [#2297](https://github.com/Vispie-AI/VisPie_backend/pull/2297) fix(twilio-conversations): sort inbox by last_message.at, not date_updated
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：会话收件箱按 date_updated 而非 last_message.at 排序，顺序有误。
- **修复**：将收件箱排序字段改为 last_message.at 实现准确排序。
- **成果**：收件箱消息顺序正确，与实际最新消息时间一致。

### [#2295](https://github.com/Vispie-AI/VisPie_backend/pull/2295) fix(twilio-conversations): return newest 50 messages, not oldest 50
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：Twilio 会话消息接口返回最早的 50 条而非最新的 50 条消息。
- **修复**：调整消息查询逻辑，改为返回最新 50 条消息。
- **成果**：用户查看会话时默认展示最新消息，交互体验更合理。

### [#2294](https://github.com/Vispie-AI/VisPie_backend/pull/2294) Codex/fix ads analytics media fallback
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：广告分析媒体回退优先级逻辑有误，导致数据展示错误。
- **修复**：调整媒体回退优先级顺序，确保正确选择媒体来源。
- **成果**：广告分析页面媒体展示数据准确可靠。

### [#2293](https://github.com/Vispie-AI/VisPie_backend/pull/2293) fix(deploy): prevent FastAPI gunicorn-not-found crash loop
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：部署时 FastAPI 因 gunicorn 未找到导致崩溃循环。
- **修复**：修复 gunicorn 依赖检测逻辑，防止服务崩溃重启循环。
- **成果**：部署稳定性提升，服务启动不再因依赖缺失而崩溃。

### [#2292](https://github.com/Vispie-AI/VisPie_backend/pull/2292) Fix ads analytics media fallback priority
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：广告分析媒体回退优先级逻辑有误，导致数据展示错误。
- **修复**：调整媒体回退优先级顺序，确保正确选择媒体来源。
- **成果**：广告分析页面媒体展示数据准确可靠。

### [#2291](https://github.com/Vispie-AI/VisPie_backend/pull/2291) fix(dashboard): auto-scrape on config save + reliable cache invalidation
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：仪表盘配置保存后未自动触发抓取，缓存失效不可靠。
- **修复**：配置保存时自动触发数据抓取并确保缓存可靠失效。
- **成果**：仪表盘数据实时刷新，配置生效无需手动干预。

### [#2290](https://github.com/Vispie-AI/VisPie_backend/pull/2290) fix(lark): isolate SMS-notification creds from shared singleton (safer, backward-compatible)
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：Lark SMS 通知凭证与共享单例混用，存在配置污染风险。
- **修复**：将 SMS 通知凭证从共享单例中隔离，独立管理。
- **成果**：Lark 短信通知更安全，向后兼容性保持完整。

### [#2288](https://github.com/Vispie-AI/VisPie_backend/pull/2288) fix(cc-army): callback URL + create timeout
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：cc-army 回调 URL 配置错误，任务创建超时异常。
- **修复**：修正回调 URL 地址并优化任务创建超时处理逻辑。
- **成果**：cc-army 任务调度稳定，回调机制正常运作。

### [#2287](https://github.com/Vispie-AI/VisPie_backend/pull/2287) fix(ads): TOP 10% per-ad by spend, not per-account
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：广告消耗 TOP 10% 计算范围错误，按账户而非按广告计算。
- **修复**：将 TOP 10% 计算逻辑改为按单个广告的消耗额排名。
- **成果**：广告消耗排名数据精确，分析结果更具参考价值。

### [#2285](https://github.com/Vispie-AI/VisPie_backend/pull/2285) fix(twilio-conversations): sort conversation list by real last-message time
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：会话列表排序基于更新时间而非最后消息时间，顺序不正确。
- **修复**：改用真实最后消息时间对会话列表进行排序。
- **成果**：会话收件箱按最新消息时间正确排序，用户体验改善。

### [#2283](https://github.com/Vispie-AI/VisPie_backend/pull/2283) Fix ads analytics media fallback priority
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：广告分析媒体回退优先级逻辑有误，导致数据展示错误。
- **修复**：调整媒体回退优先级顺序，确保正确选择媒体来源。
- **成果**：广告分析页面媒体展示数据准确可靠。
### [#2282](https://github.com/Vispie-AI/VisPie_backend/pull/2282) fix(daily-report): Lark send_lark_card 在 HTTP 4xx 时重试一次
- **日期**：2026-04-20 | **状态**：🚫 已关闭（未合并）
- **问题**：2026-04-19 和 2026-04-20 连续两天定时日报在 01:00 UTC 发送失败，Lark POST 返回 HTTP 400，卡片未送达。
- **根因**：`send_lark_card` 无重试机制；Lark 偶发瞬时 4xx（内容正确，10 分钟后重发即成功）直接导致当天日报丢失。
- **修复**：仅对 HTTP 4xx 添加一次 30 秒后重试（4xx = Lark 未处理消息，重试安全）；5xx / 超时 / 连接错误维持不重试（避免重复发卡风险）。
- **成果**：最小化改动，消除瞬时 4xx 导致日报丢失的场景；PR 关闭未合并，待后续决策。

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

### [#2307](https://github.com/Vispie-AI/VisPie_backend/pull/2307) feat(cc-army): redirect all coding-task hints to Coder Army
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：编码任务提示未统一引导到 Coder Army 处理。
- **修复**：将所有编码任务相关提示重定向至 Coder Army 统一处理。
- **成果**：编码任务分发流程规范化，Coder Army 承接效率提升。

### [#2306](https://github.com/Vispie-AI/VisPie_backend/pull/2306) feat(cc-army): two-column drawer layout + fix headless PTY resize
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：cc-army 抽屉布局单列展示内容拥挤，headless PTY resize 存在 bug。
- **修复**：实现两列抽屉布局并修复 headless PTY resize 问题。
- **成果**：cc-army 界面布局更清晰，PTY 终端尺寸自适应正常。

### [#2305](https://github.com/Vispie-AI/VisPie_backend/pull/2305) perf(cc-army): skip reset if at HEAD + Kimi env + layout chmod
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：cc-army 在已是最新版本时仍执行不必要的 reset 操作，存在环境配置问题。
- **修复**：在已是 HEAD 时跳过 reset，补充 Kimi 环境变量及目录权限设置。
- **成果**：cc-army 启动流程更高效，环境配置完整可靠。

### [#2304](https://github.com/Vispie-AI/VisPie_backend/pull/2304) perf(cc-army): reuse EFS worktree — 90s → 13s startup
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：cc-army EFS worktree 每次重新创建，启动耗时约 90 秒。
- **修复**：复用已有 EFS worktree，避免重复初始化。
- **成果**：启动时间从 90 秒缩短至 13 秒，效率大幅提升。

### [#2286](https://github.com/Vispie-AI/VisPie_backend/pull/2286) feat(shadow-arena): PR-3 — vizzy-lark shadow mode + 50% auto-shadow
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：缺乏自动化的 vizzy-lark shadow 模式及流量分配机制。
- **修复**：新增 vizzy-lark shadow 模式，实现 50% 流量自动 shadow 分配。
- **成果**：Shadow Arena PR-3 上线，支持 vizzy-lark 影子测试与对比评估。

### [#2284](https://github.com/Vispie-AI/VisPie_backend/pull/2284) feat(cc-army): session-aware callback — wake nanobot in correct chat
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：cc-army 回调无法在正确的对话中唤醒 nanobot。
- **修复**：实现 session-aware 回调机制，确保在正确聊天中唤醒 nanobot。
- **成果**：cc-army 多会话场景下消息路由准确，用户体验提升。
### [#2241](https://github.com/Vispie-AI/VisPie_backend/pull/2241) feat(daily-report): 将 Amy Liveness 集成至 George AI 日报卡片
- **日期**：2026-04-18 | **状态**：✅ 已合并
- **问题**：日报卡片仅有应用层指标（技能调用、会话健康），无法反映 Amy 容器本身的存活状态，导致低流量误读。
- **根因**：`amy-liveness.sh` 每 5 分钟探测一次，数据已入库，但日报渲染层未消费。
- **修复**：新增独立模块 `infra_health.py`，查询 `monitoring_probe_results` 并在卡片顶部渲染 `🛡️ Infra` 块；健康时单行展示，发生故障时自动展开事件详情；通过环境变量 `REPORT_INFRA_HEALTH=1` 暗启动。加权可用率 `(healthy + 0.5×degraded) / total`，≥2 连续异常才判定事件。
- **成果**：19 个单元测试全通过；日报卡片可一眼区分"业务平静"与"容器宕机"，信息误导率显著降低。

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

### [#2299](https://github.com/Vispie-AI/VisPie_backend/pull/2299) refactor(twilio-conversations): PG source of truth — Day 1-2 foundation
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：Twilio 会话模块缺乏以 PG 为数据源的架构基础。
- **修复**：完成 PG 数据源架构重构 Day 1-2 基础工作，建立可靠数据层。
- **成果**：Twilio 会话以 PostgreSQL 为单一数据源，架构更清晰稳定。

### [#2296](https://github.com/Vispie-AI/VisPie_backend/pull/2296) docs(twilio-conversations-refactor): core refactor plan — DO NOT MERGE YET
- **日期**：2026-04-21 | **状态**：🚫 已关闭
- **问题**：Twilio 会话模块需要核心重构但尚未完成，不可合并。
- **修复**：起草核心重构计划文档，明确重构范围与实施步骤。
- **成果**：重构计划清晰，为后续分步实施提供指导（仅文档，未合并）。

### [#2289](https://github.com/Vispie-AI/VisPie_backend/pull/2289) docs(cc-army): delegate skill v2 — requester_context + auto-callback
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：cc-army delegate skill 文档缺少 requester_context 与自动回调说明。
- **修复**：更新 delegate skill v2 文档，补充 requester_context 与自动回调配置说明。
- **成果**：文档完整准确，开发者可快速接入 cc-army delegate 功能。
### [#2027](https://github.com/Vispie-AI/VisPie_backend/pull/2027) docs: 新增 auto-amy 架构双语系统地图
- **日期**：2026-04-12 | **状态**：✅ 已合并
- **内容**：新增 4 部分架构文档（英文 `docs/system-map/` + 中文 `docs/system-map-cn/`），共 8 个文件，涵盖模块地图、数据流图、调用链、部署拓扑。
- **成果**：系统架构知识文档化，降低团队新成员理解成本。

---

## 四、总览

| PR | 标题摘要 | 类型 | 状态 | 日期 |
|----|---------|------|------|------|
| [#2282](https://github.com/Vispie-AI/VisPie_backend/pull/2282) | 日报 Lark 4xx 重试一次 | fix | 🚫 已关闭 | 2026-04-20 |
| [#2241](https://github.com/Vispie-AI/VisPie_backend/pull/2241) | Amy Liveness 集成日报卡片 | feat | ✅ 已合并 | 2026-04-18 |
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

**合计：19 个 PR | 已合并 40 | 关闭未合并 3 | 待合并 0**
