# 工作成果总结

> 统计周期：2026-04-10 ~ 2026-04-22 | 共 69 个 PR（已合并 64 · 关闭未合并 4 · 待合并 0）
> 最后更新：2026-04-22

---

## 一、Bug 修复（fix:）

### [#2331](https://github.com/Vispie-AI/VisPie_backend/pull/2331) fix(ads-analytics): allow custom date ranges up to 365 days
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：广告分析日期选择器硬编码回溯上限为30天，与预设选项重叠导致自定义日期不可用。
- **修复**：为 TimeRangeSelector 新增 maxDaysBack 属性，ads-analytics 页面设为365天，其余页面默认值不变。
- **成果**：用户可在广告分析页面自由选取最多365天内的自定义日期范围。

### [#2328](https://github.com/Vispie-AI/VisPie_backend/pull/2328) fix(twilio): CRITICAL — ::jsonb cast broke all webhook PG writes
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：SQLAlchemy text() 中的 ::jsonb 类型转换与参数绑定冲突，导致所有 Webhook PG 写入静默失败。
- **修复**：将 ::jsonb 改为标准 SQL 的 CAST(:payload AS jsonb)，消除参数绑定冲突。
- **成果**：Webhook PG 写入恢复正常，UI 可实时读取最新对话数据。

### [#2326](https://github.com/Vispie-AI/VisPie_backend/pull/2326) fix(twilio): backfill updates conversation last_activity_at
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：回填脚本写入消息后未更新 last_activity_at，导致170条对话的收件箱排序和状态错误。
- **修复**：回填后找到最新消息并单调更新对话记录，并一次性修复已受影响的170条历史数据。
- **成果**：对话列表排序恢复正确，收件箱展示最新活动时间。

### [#2325](https://github.com/Vispie-AI/VisPie_backend/pull/2325) fix(twilio): error handling — no more silent PG failures
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：Twilio PG 写入失败仅记录 warning，Lark DLQ 失败完全静默，数据漂移难以发现。
- **修复**：PG 写入失败升级为 error 级别日志，Lark DLQ 失败改为 warning/error 输出。
- **成果**：PG 数据漂移和消息投递失败均可及时被日志告警捕获。

### [#2324](https://github.com/Vispie-AI/VisPie_backend/pull/2324) fix(twilio): webhook PG write silently failing — form body + FK constraint
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：请求体被 FastAPI Form() 消耗后再次读取返回空值，新对话缺父行触发 FK 约束回滚，PG 写入整体静默失败。
- **修复**：从已解析 Form 参数重建 raw_payload，写入前先调用 upsert_conversation() 保证父行存在。
- **成果**：Twilio Webhook PG 写入恢复正常，twilio_inbound_events 数据正确落库。

### [#2323](https://github.com/Vispie-AI/VisPie_backend/pull/2323) fix(review-ui): clickable links + dynamic AI progress card + remove Gemini label
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：AI 评审评论中链接无法点击，进度卡片未读取动态评审内容，且仍显示已废弃的 Gemini Vision 标签。
- **修复**：链接添加样式可点击，进度卡片优先读取 Auto-Amy 动态评审，去除 Gemini Vision 页脚标签。
- **成果**：评审界面链接可直接跳转，动态评审进度正确展示，品牌标识已更新。

### [#2321](https://github.com/Vispie-AI/VisPie_backend/pull/2321) fix(rule-parser): Knowledge API as SSOT for video review rules
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：规则解析器从 git 仓库或文件路径读取规则，无法保证读到 Ops 在 /autopilot/memory UI 编辑的最新版本。
- **修复**：改为从本地 Knowledge API 加载规则，与 /autopilot/memory UI 使用同一数据源。
- **成果**：视频审核规则始终使用 Ops 最新编辑版本，陈旧文件和权限问题彻底解决。

### [#2320](https://github.com/Vispie-AI/VisPie_backend/pull/2320) fix(auto-amy): read Ops workspace + human-readable AI review format
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：路径优先级错误导致读取 git 仓库版本（13条规则）而非 Ops 实际编辑版本（11条规则），评审以规则 ID 而非可读描述展示。
- **修复**：调整路径优先级使 Ops workspace 排在首位，评审改用规则描述文字代替规则 ID。
- **成果**：Auto-Amy 准确加载 Ops 维护的规则集，评审内容对用户更易读。

### [#2319](https://github.com/Vispie-AI/VisPie_backend/pull/2319) fix(review-ui): deduplicate AI comments — always show latest
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：创作者重新提交后，动态评审与旧版 AI 评审同时显示，造成重复评论。
- **修复**：当存在多条 AI 评论时，只展示最新一条。
- **成果**：评审界面评论不再重复，始终展示最新 AI 评审结果。

### [#2318](https://github.com/Vispie-AI/VisPie_backend/pull/2318) fix(review-ui): show dynamic AI review notes instead of overwriting with 4-rule format
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：buildTimelineEvents() 无条件用4条规则格式覆盖 action.notes，导致 Auto-Amy 动态13条规则评审内容丢失。
- **修复**：仅在 action.notes 为空时才回退到 submission_ai_reviews，有内容时直接使用 notes。
- **成果**：Manus 活动展示完整13条规则评审，其他活动保持4条规则格式，无需额外配置。

### [#2317](https://github.com/Vispie-AI/VisPie_backend/pull/2317) fix(lark): resolve @_user_N mention placeholders to real names
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：Lark 将 @提及 替换为 @_user_N 占位符，LLM 无法识别真实被提及用户，误判消息对象。
- **修复**：利用消息 mentions 数组将占位符解析为真实姓名，机器人自身的提及直接去除。
- **成果**：LLM 收到正确的用户真实姓名，群组消息中的 @提及 解析准确。

### [#2315](https://github.com/Vispie-AI/VisPie_backend/pull/2315) fix(rule-parser): robust workspace path discovery for Amy EC2
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：Amy EC2 未设置 WORKSPACE_PATH 环境变量，HOME 指向错误用户目录，规则文件路径解析失败。
- **修复**：_find_workspace_file() 按优先级检查5个路径，优先匹配 EC2 主机 repo 挂载路径。
- **成果**：Amy EC2 能正确找到 Ops 编辑的 video-preferences.md，动态规则加载生效。

### [#2313](https://github.com/Vispie-AI/VisPie_backend/pull/2313) fix(shadow-judge): rebuild card instead of fetch-and-append for v2 compat
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：飞书卡片更新拼接 collapsible_panel 时类型不匹配触发 code=230099 错误，字段名不一致使所有 shadow 结果显示为"失败：未知"。
- **修复**：改为通过 _shadow_card_state 缓存调用 build_final_card() 重建完整卡片，修正 status/elapsed 字段名并增加裁决渲染。
- **成果**：Shadow judge 卡片更新成功，裁决结果（评分与理由）正常显示。

### [#2310](https://github.com/Vispie-AI/VisPie_backend/pull/2310) fix(cc-army): chown guard prevents CI permission denied
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：SSM 写入产生的 root 属主文件在 CI 部署时引发权限拒绝错误，导致舰队部署失败。
- **修复**：在技能目录操作前添加 chown 守卫检查，防止 root 属主文件残留。
- **成果**：CI 舰队部署中 George 技能目录权限问题不再复现。

### [#2309](https://github.com/Vispie-AI/VisPie_backend/pull/2309) fix(cc-army): setup-george-skills.sh → cc-army-delegate
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：claude-code 目录已删除但仍在 ACTIVE_SKILLS 中导致复制失败，SSM 写入产生 root 属主文件引发权限拒绝。
- **修复**：将 setup-george-skills.sh 中的引用更新为 cc-army-delegate，修复路径与权限问题。
- **成果**：CI 舰队部署恢复正常，技能安装步骤不再因路径缺失或权限问题失败。
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

### [#2322](https://github.com/Vispie-AI/VisPie_backend/pull/2322) feat(supabase): per-campaign toggle to disable legacy 4-rule AI review
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：Manus 等活动同时运行 Auto-Amy 动态评审和旧版4条规则触发器，产生冗余评审。
- **修复**：在活动 metadata 中新增 disable_legacy_ai_review 开关，Manus 首批启用，其他活动通过 SQL 按需开启。
- **成果**：支持按活动级别灵活禁用旧版 AI 评审，动态评审独立运行无干扰。

### [#2316](https://github.com/Vispie-AI/VisPie_backend/pull/2316) feat(twilio): enable PG source-of-truth flags on GCR
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：Twilio PG 重构已完成全量验证，但 GCR 生产环境的两个特性开关尚未启用，仍走旧路径。
- **修复**：在 .env.gcr 和 .env.dev 中将 TWILIO_USE_PG_WORKER 和 TWILIO_READ_FROM_PG 均设为 true。
- **成果**：Twilio 收件箱和消息全面切换为 PG 读写，冷启动延迟从20秒降至7.5毫秒（降幅2667倍）。

### [#2314](https://github.com/Vispie-AI/VisPie_backend/pull/2314) feat(auto-amy): dynamic video review rules from video-preferences.md
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：Auto-Amy 视频审核规则硬编码在 Python 中（11条），与 video-preferences.md 中的13条规则不同步，Ops 编辑 md 文件不即时生效。
- **修复**：新增 rule_parser.py 从 video-preferences.md 动态解析规则，每次调用实时读取，Python 常量仅作兜底。
- **成果**：规则以 md 文件为单一数据源，Ops 编辑即时生效，恢复缺失的 C8 和 C9 规则。

### [#2311](https://github.com/Vispie-AI/VisPie_backend/pull/2311) feat: add wearwow to default brand filter
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：性能监控看板默认品牌筛选中缺少 wearwow_v1.8_ai_digital_wardrobe_launch 品牌。
- **修复**：将 wearwow_v1.8_ai_digital_wardrobe_launch 添加至性能监控页面的 DEFAULT_BRANDS 列表。
- **成果**：wearwow 品牌默认显示在性能监控看板中，满足 Didi 的配置需求。
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

### [#2332](https://github.com/Vispie-AI/VisPie_backend/pull/2332) refactor(cc-army): token-based status detection — replaces agent-specific markers
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：状态检测依赖特定 Agent 标记，OpenCode 无标记导致状态永卡"运行中"，窗口大小变化引发翻转误判，dump-screen -f 标志在 fork zellij 中静默失效。
- **修复**：改用 dump-screen token 提取进行通用状态检测，10秒 token 不变即判为完成，修复 zellij fork 兼容性和 PTY 尺寸问题。
- **成果**：所有 Agent 类型状态检测统一准确，抗干扰能力显著提升，翻转问题消除。

### [#2330](https://github.com/Vispie-AI/VisPie_backend/pull/2330) Allow links in dashboard messages
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：Dashboard 消息输入框屏蔽了网址链接，导致运营无法在 /dashboard/messages 中发送 Google Docs 等链接。
- **修复**：移除消息输入的链接拦截逻辑，保留邮箱和电话号码检测不变。
- **成果**：运营人员可在 Dashboard 消息中正常发送任意网址链接。

### [#2329](https://github.com/Vispie-AI/VisPie_backend/pull/2329) Fix stripe transfer
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：Stripe 转账功能存在缺陷，导致付款流程异常。
- **修复**：修复 Stripe 转账相关代码逻辑，涉及8个文件共7次提交。
- **成果**：Stripe 转账功能恢复正常，支付流程可靠运行。

### [#2327](https://github.com/Vispie-AI/VisPie_backend/pull/2327) Fix stripe transfer
- **日期**：2026-04-21 | **状态**：🚫 已关闭
- **问题**：Stripe 转账功能异常，需要修复相关代码逻辑。
- **修复**：提交了初步修复方案但存在冲突未被合并，由后续 PR #2329 替代完成修复。
- **成果**：此 PR 已关闭，相关修复通过 #2329 正式合并。

### [#2312](https://github.com/Vispie-AI/VisPie_backend/pull/2312) perf-monitor: add wearwow_v1.8 to default brand filter
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：性能监控看板默认品牌筛选中缺少 wearwow_v1.8 品牌（与 #2311 并行提交）。
- **修复**：将 wearwow_v1.8_ai_digital_wardrobe_launch 加入 DEFAULT_BRANDS 默认筛选列表。
- **成果**：wearwow 品牌在性能监控看板中默认展示，满足 Didi 的配置需求。

### [#2308](https://github.com/Vispie-AI/VisPie_backend/pull/2308) refactor(cc-army): generic screen-change status detection
- **日期**：2026-04-20 | **状态**：✅ 已合并
- **问题**：Agent 状态检测依赖特定标记，OpenCode 无标记永卡"运行中"，Claude 快速任务因 had_working 未设置而漏检。
- **修复**：替换为基于 JSONL 数据变化的通用屏幕变化检测，5秒无活动→空闲，30秒→完成。
- **成果**：适配所有 Agent 类型，净减少70行代码，状态检测准确可靠。
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

**合计：19 个 PR | 已合并 64 | 关闭未合并 4 | 待合并 0**
