# 工作成果总结

> 统计周期：2026-04-10 ~ 2026-05-03 | 共 344 个 PR（已合并 290 · 关闭未合并 28 · 待合并 25）
> 最后更新：2026-05-03

---

## 一、Bug 修复（fix:）

### [#2734](https://github.com/Vispie-AI/VisPie_backend/pull/2734) fix(format-analysis): tolerate music search failures
- **日期**：2026-05-01 | **状态**：✅ 已合并
- **问题**：TikTok音乐搜索全部密钥失败时会中断整个格式分析流程。
- **修复**：将音乐搜索改为尽力而为，RapidAPI全部失败时返回空候选列表。
- **成果**：格式分析流程对音乐搜索失败具备容错能力，不再因此中断。

### [#2732](https://github.com/Vispie-AI/VisPie_backend/pull/2732) fix(web): defer video loading until user clicks play
- **日期**：2026-05-01 | **状态**：🔀 待合并
- **问题**：网格视图每张卡片在页面加载时立即创建video元素，产生大量Supabase存储出口流量。
- **修复**：Player组件默认显示缩略图，仅在用户点击播放后才创建video元素。
- **成果**：每次页面加载消除24+次视频预加载请求，显著降低存储出口成本。

### [#2731](https://github.com/Vispie-AI/VisPie_backend/pull/2731) fix: deploy format analysis prefect flow
- **日期**：2026-05-01 | **状态**：✅ 已合并
- **问题**：格式分析Prefect流程未被部署工作流重新部署，Doubao环境变量未传递。
- **修复**：在部署工作流中重新加入format_analysis_flow_v2并注入ARK_API_KEY等变量。
- **成果**：格式分析流程成功部署，支持Doubao Seed 2视频验证。

### [#2730](https://github.com/Vispie-AI/VisPie_backend/pull/2730) fix: improve coder army callback summaries
- **日期**：2026-05-01 | **状态**：✅ 已合并
- **问题**：Coder Army回调摘要使用Nova Micro模型质量低，且缺乏可追溯的trace ID。
- **修复**：切换到DeepSeek v4 pro生成摘要，并在回调载荷中加入完整trace ID。
- **成果**：Coder Army回调摘要质量提升，支持通过trace ID快速定位任务记录。

### [#2729](https://github.com/Vispie-AI/VisPie_backend/pull/2729) fix: compact Agent Doctor debug layout
- **日期**：2026-05-01 | **状态**：✅ 已合并
- **问题**：Agent Doctor页面trace选择器和原始引用默认展开，遮挡主调试内容。
- **修复**：将trace选择器和原始引用改为默认折叠，侧边栏可收起，主区域改为全宽布局。
- **成果**：Agent Doctor调试页面更简洁，诊断内容优先展示，排障效率提升。

### [#2725](https://github.com/Vispie-AI/VisPie_backend/pull/2725) fix: scope coder army watchdog PR metrics
- **日期**：2026-05-01 | **状态**：✅ 已合并
- **问题**：Coder Army看门狗将所有分支都计为缺少PR的任务，指标统计偏高。
- **修复**：仅对明确要求提交/推送/PR的任务检查PR交付，跳过空分支的自动推送。
- **成果**：看门狗PR指标统计准确，非PR类任务不再被误报为缺失PR。

### [#2724](https://github.com/Vispie-AI/VisPie_backend/pull/2724) fix(format-analysis): rebuild failed blacklist query
- **日期**：2026-05-01 | **状态**：✅ 已合并
- **问题**：SQLAlchemy在已附加过滤条件后调用select_from仍拒绝失败黑名单查询。
- **修复**：从干净的select_from重新构建失败格式黑名单查询，避免条件与select_from冲突。
- **成果**：format hunt失败黑名单查询执行成功，格式分析流程得以正常推进。

### [#2722](https://github.com/Vispie-AI/VisPie_backend/pull/2722) fix(format-analysis): disambiguate failed-format blacklist query
- **日期**：2026-05-01 | **状态**：✅ 已合并
- **问题**：#2720优化后的失败格式黑名单查询因SQLAlchemy join歧义在步骤5.5重试。
- **修复**：为join显式指定左侧表，消除SQLAlchemy join歧义问题。
- **成果**：已发现格式去重速度已确认提升，失败黑名单查询亦可正常执行。

### [#2720](https://github.com/Vispie-AI/VisPie_backend/pull/2720) fix(format-analysis): speed up hunt dedup query
- **日期**：2026-05-01 | **状态**：✅ 已合并
- **问题**：格式hunt在步骤0.5因90天窗口N+1查询扫描约183k条引用记录而卡住。
- **修复**：去重窗口缩至14天，N+1查询替换为单次join，候选为空时提前退出。
- **成果**：格式hunt去重阶段大幅提速，active Prefect运行得以恢复正常。
### [#2705](https://github.com/Vispie-AI/VisPie_backend/pull/2705) fix(alembic): recover orphaned legacy_object_map migration + CI guard
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：同事手动建表后未提交迁移文件，git 与生产数据库 schema 不一致。
- **修复**：补提反向工程迁移文件并重链迁移链，新增 CI 检查强制迁移随 PR 提交。
- **成果**：Alembic 迁移树恢复线性，未来遗漏迁移将被 CI 自动拦截。

### [#2697](https://github.com/Vispie-AI/VisPie_backend/pull/2697) fix(ci): repair jq filter quoting in Deploy Vio Nanobot workflow
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：jq 引号嵌套错误导致 Vio Nanobot 部署 workflow 自上线起每次均失败。
- **修复**：改用 heredoc 传入 jq 过滤器，规避 bash 词语分割引起的引号提前终止。
- **成果**：部署 workflow 恢复正常，合并后自动补齐了落下的镜像更新。

### [#2692](https://github.com/Vispie-AI/VisPie_backend/pull/2692) fix(amy): bump primary timeout 600s→1500s, Hatchet 15m→30m
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：Amy 执行多源任务时在 600s 被强制终止，复杂任务无法完成。
- **修复**：asyncio 超时提升至 1500s，Hatchet 执行超时提升至 30m，内外差保持 5 分钟。
- **成果**：Amy 可完整执行复杂多源任务，根因技能发现优化跟进中。

### [#2690](https://github.com/Vispie-AI/VisPie_backend/pull/2690) fix(alert-card): action items use Vio host paths for Vio agents
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：Vio 租户告警操作指引硬编码了 Amy 的主机路径，误导值班人员 SSH 至错误主机。
- **修复**：根据 agent 前缀动态推导容器名、主机标签和日志路径，消除硬编码。
- **成果**：Vio 告警操作指引指向正确主机，新增 4 个回归测试。

### [#2689](https://github.com/Vispie-AI/VisPie_backend/pull/2689) fix(twilio-conversations): null-safe friendly_name
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：friendly_name 为 null 时前端渲染头像抛出 TypeError，详情面板静默失败。
- **修复**：新增 getDisplayName 回退链（friendly_name→recipient_name→phone），替换全部渲染处的直接访问。
- **成果**：无名会话点击正常，通过 30 次快速点击压力测试。

### [#2686](https://github.com/Vispie-AI/VisPie_backend/pull/2686) fix(vio-video-review): align comment format + Gemini prompt with auto-amy canonical
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Vio 视频审核格式与 auto-amy 生产格式不一致，Gemini 提示缺失导致约 30% 误判。
- **修复**：统一评论分组格式，补充规则级 Gemini 提示词及错别字白名单，添加可配置"编辑规则"链接。
- **成果**：审核格式对齐 auto-amy，误判率降低，5 个新增单元测试通过。

### [#2685](https://github.com/Vispie-AI/VisPie_backend/pull/2685) fix: preserve coder army feature branches on slot reuse
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Coder Army 复用工作区槽位时未先切换分支，功能分支引用被重置至 master 导致 PR 被误关闭。
- **修复**：在 reset --hard 前强制切回槽位草稿分支，确保功能分支引用不被覆盖。
- **成果**：功能分支在槽位复用后得以保留，GitHub PR 不再被意外关闭。

### [#2682](https://github.com/Vispie-AI/VisPie_backend/pull/2682) fix(twilio-conversations): cancel in-flight detail fetches + dedupe pollers
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：快速切换会话时多个并发请求竞争写入状态，导致消息头显示错误联系人名称并出现页面冻结。
- **修复**：引入 AbortController 取消过时请求，并为详情轮询和列表轮询分别添加防重入守卫。
- **成果**：消息头始终显示正确联系人，快速切换或点击"加载更多"时页面不再冻结。

### [#2676](https://github.com/Vispie-AI/VisPie_backend/pull/2676) fix(vio-video-review): elevate env-cache forensic to INFO/ERROR
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：PR #2675 的环境缓存诊断日志被 DEBUG 级别屏蔽，生产环境无法确认缓存何时为空。
- **修复**：移除 DEBUG 门控，将缓存为空情形升级为 ERROR 日志并打印完整状态快照。
- **成果**：生产环境缓存异常时触发可见 ERROR 日志，为定位根本原因提供完整现场信息。

### [#2675](https://github.com/Vispie-AI/VisPie_backend/pull/2675) fix(vio-video-review): cache env at module import — defensive workaround for runtime env-clearing
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Hatchet worker 运行期间 os.environ 中的 Supabase 凭据被意外清空，导致写回任务持续报 "Supabase not configured"。
- **修复**：在模块导入时将 SUPABASE_URL 和 KEY 缓存为模块属性，绕过运行时环境变量异常清除。
- **成果**：写回任务不再因环境变量丢失而失败，同时新增诊断日志用于追踪缓存漂移来源。

### [#2674](https://github.com/Vispie-AI/VisPie_backend/pull/2674) fix(vio-video-review): remove non-existent content_id from writeback INSERT — P0 unblocks AI comments
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：写回 submission_actions 时携带不存在的 content_id 字段，PostgREST 返回 400 导致所有 AI 审核评论静默失败。
- **修复**：从 INSERT body 中删除 content_id 字段，并新增回归测试防止其再次引入。
- **成果**：Manus 视频审核完成后 AI 评论成功写入数据库，品牌管理后台可正常展示审核结果。

### [#2673](https://github.com/Vispie-AI/VisPie_backend/pull/2673) fix(nanobot): add ffmpeg to Dockerfile.nanobot — vio_video_review OCR
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Dockerfile.nanobot 缺少 ffmpeg，导致 vio_video_review OCR 帧提取失败，C7 文字叠加规则静默降级为纯 Gemini 视觉判断。
- **修复**：在 apt-get install 列表中补充 ffmpeg，与 Mitchell Dockerfile（PR #2373）保持一致。
- **成果**：nanobot 容器恢复 ffmpeg 能力，OCR 文字叠加检测重新正常工作。

### [#2671](https://github.com/Vispie-AI/VisPie_backend/pull/2671) fix(monitoring): nanobot L2 pattern + de-Amyfy alert card title
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：L2 进程检查未匹配 vio-*-nanobot 容器导致健康容器误报 Critical，且告警卡片标题硬编码 "Amy Alert" 无视实际 agent。
- **修复**：扩展 L2 grep 模式以识别 python.*nanobot_server，并将告警卡片中的 "Amy" 替换为实际 agent 名称。
- **成果**：nanobot 容器健康时不再触发误报，告警卡片准确显示受影响的 agent 名称。

### [#2668](https://github.com/Vispie-AI/VisPie_backend/pull/2668) fix(amy): synthesize reasoning_content for seeded assistant turns (3rd attempt)
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：DeepSeek thinking 模式要求每条历史 assistant 消息携带非空 reasoning_content，种子中 Bedrock 的空值导致 iter 1 立即返回 4xx。
- **修复**：在种子边界为 Bedrock 助手消息合成占位 reasoning_content，同时剥离 thinking_blocks。
- **成果**：DeepSeek shadow 有种子时不再触发 4xx，回复类场景（"撤回/不对"）恢复正常响应。

### [#2667](https://github.com/Vispie-AI/VisPie_backend/pull/2667) fix(amy): synthesize reasoning_content for seeded assistant turns (3rd attempt)
- **日期**：2026-04-29 | **状态**：🚫 已关闭
- **问题**：PR #2658 和 #2662 修复方向相反，生产日志证明任意种子均触发 iter 1 即时 4xx，根本原因仍未解决。
- **修复**：在种子边界合成 reasoning_content 占位符（本 PR 与 master 有冲突，由 rebase 版本 #2668 取代）。
- **成果**：本 PR 已关闭，核心修复思路通过无冲突的 #2668 落地。

### [#2665](https://github.com/Vispie-AI/VisPie_backend/pull/2665) fix(matrix-mining): use Part.from_bytes for Vertex AI (no files.upload)
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Vertex AI 客户端不支持 client.files.upload()，切换 Vertex 后视频上传抛出 "only supported in Developer client" 错误。
- **修复**：改用 Part.from_bytes 将视频数据内联传送，无需调用文件上传 API。
- **成果**：Gemini Vertex AI 可成功分析 IG Reels 视频，matrix-mining 管道恢复正常运行。

### [#2664](https://github.com/Vispie-AI/VisPie_backend/pull/2664) fix(matrix-mining): Vertex AI Gemini — use inline bytes instead of file upload
- **日期**：2026-04-29 | **状态**：🔀 待合并
- **问题**：Vertex AI 不支持 files.upload() API，切换后视频分析功能中断。
- **修复**：改用 Part.from_bytes 内联字节传递媒体（被来自 master 的干净分支 #2665 取代仍处于开放状态）。
- **成果**：实际修复已通过更干净的 #2665 合并，本 PR 仍处于开放状态待关闭。

### [#2662](https://github.com/Vispie-AI/VisPie_backend/pull/2662) fix(amy): strip Bedrock thinking artifacts at shadow seed boundary
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：PR #2658 错误地在 provider 层剥除 reasoning_content，导致 DeepSeek 自身 thinking 链路断裂，相同错误在生产复现。
- **修复**：还原 provider 层修改，改在种子边界精准剥除 Bedrock 注入的 reasoning_content 和 thinking_blocks。
- **成果**：种子边界处理更精准，不影响 DeepSeek 正常 thinking 轮次，测试覆盖 22 个用例。
### [#2640](https://github.com/Vispie-AI/VisPie_backend/pull/2640) fix(vio-video-review): Hatchet 1.29.3 API + Vault for HMAC secret
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：PR #2624 Staging 金丝雀冒烟测试发现 Hatchet API 调用失败及 HMAC 密钥硬编码两处阻塞性 Bug。
- **修复**：升级至 Hatchet 1.29.3 API，并将 HMAC 密钥迁移至 Vault 管理，消除硬编码风险。
- **成果**：视频审核 Phase 1 部署阻塞解除，Staging 冒烟测试通过。

### [#2638](https://github.com/Vispie-AI/VisPie_backend/pull/2638) Fix creator overview submission sync
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Creator Overview 提交数据与后端存在同步不一致问题，影响数据展示准确性。
- **修复**：修正提交同步逻辑，确保 Creator Overview 与后端数据一致性。
- **成果**：Creator Overview 数据同步恢复正常，展示数据可信度提升。

### [#2636](https://github.com/Vispie-AI/VisPie_backend/pull/2636) fix(amy): seed DeepSeek shadow with primary's session history
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：DeepSeek shadow 以全新会话运行，导致回复式对话缺少上下文，答复"没有之前的上下文"。
- **修复**：为 DeepSeek shadow 注入主模型的历史会话记录，使其具备上下文感知能力。
- **成果**：shadow 回复质量与主模型对齐，多轮对话连贯性显著提升。

### [#2631](https://github.com/Vispie-AI/VisPie_backend/pull/2631) Fix Format Studio Seedance generation timeouts
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Format Studio 调用 Seedance 生成视频时，提供商挂起或拒绝请求导致任务超时崩溃。
- **修复**：为 Seedance 任务创建添加有界超时，提供商异常时自动回退至无参考视频模式。
- **成果**：Format Studio 视频生成稳定性提升，超时场景不再导致任务失败。

### [#2630](https://github.com/Vispie-AI/VisPie_backend/pull/2630) fix: prevent live Coder Army slot overwrite
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：Coder Army 活跃槽位被新创建请求覆盖，导致正在运行的 Agent 任务数据丢失。
- **修复**：将槽位识别为可复用执行资源，活跃槽位收到创建请求时返回 HTTP 409，终止槽位可安全释放。
- **成果**：Coder Army 槽位管理机制完善，防止任务被意外覆盖。

### [#2629](https://github.com/Vispie-AI/VisPie_backend/pull/2629) fix(eva): pass GitHub app env to nanobot shell
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：Eva 的 GitHub App 元数据未传入 nanobot shell 子进程，PR/CI 工作流无法访问 GitHub 凭据。
- **修复**：将 GitHub App 环境变量透传至 nanobot shell，镜像中安装 gh/rg 工具并挂载 TOOLS.md 文档。
- **成果**：Eva nanobot 可正常执行 GitHub 相关操作，CI 工作流集成就绪。

### [#2627](https://github.com/Vispie-AI/VisPie_backend/pull/2627) fix(studio): add pro template thumbnails
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Pro Templates 视频格网缺少缩略图，用户无法直观预览模板样式。
- **修复**：生成并上传 33 张格式模板缩略图至 GCS，从视频 URL 自动推导 poster/modal/reference 图。
- **成果**：Pro Templates 展示完整，用户可直观预览各模板样式，体验显著改善。

### [#2625](https://github.com/Vispie-AI/VisPie_backend/pull/2625) fix(nanobot): fix IndentationError crashing all nanobots
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：PR #2622 heartbeat 函数遗留 4 个多余空格缩进，导致所有 nanobot 启动时 IndentationError 崩溃循环。
- **修复**：重新缩进 heartbeat 函数内 33 行代码，消除语法错误，完成紧急热修复。
- **成果**：所有 nanobot 恢复正常运行，生产环境崩溃问题快速消除。

### [#2622](https://github.com/Vispie-AI/VisPie_backend/pull/2622) revert(nanobot): remove CardKit streaming, use 2s PATCH heartbeat
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：CardKit 流式传输（PR #2606/#2613/#2617）在生产环境不稳定，频繁引发异常。
- **修复**：移除全部 CardKit 流式代码（净减 195 行），将心跳间隔由 4s 降至 2s，改用简单 PATCH 更新。
- **成果**：nanobot 通信机制回归稳定，代码复杂度大幅降低。

### [#2621](https://github.com/Vispie-AI/VisPie_backend/pull/2621) fix: unified creator name search across creator_pool + actor_creators (P0)
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：创作者搜索仅查询单一数据表，导致部分创作者无法被检索，触发 P0 级用户反馈。
- **修复**：统一跨 creator_pool 和 actor_creators 两张表的创作者名称搜索逻辑。
- **成果**：创作者搜索召回率提升，P0 问题解决，用户可正常发送邀请。

### [#2618](https://github.com/Vispie-AI/VisPie_backend/pull/2618) fix(studio): remove sparkline curves
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：趋势格式卡片中的折线图 SVG 曲线影响界面整洁度与视觉一致性。
- **修复**：移除 Studio 趋势格式卡片中的折线图 SVG 曲线样式。
- **成果**：格式卡片 UI 更简洁，界面视觉一致性提升。

### [#2617](https://github.com/Vispie-AI/VisPie_backend/pull/2617) fix(nanobot): restore reasoning body in CardKit heartbeat without jumping
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：PR #2613 过度修正导致 CardKit 心跳中 reasoning body 内容丢失，影响展示完整性。
- **修复**：恢复 reasoning body 在 CardKit 心跳中的展示，仅排除 _stream_buf 实时预览部分。
- **成果**：CardKit 推理内容正常展示且无跳动问题，用户体验改善。
### [#2610](https://github.com/Vispie-AI/VisPie_backend/pull/2610) fix(mobile-app-web): hide creator_amount_cents from brands in ReviewTimeline
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：`ReviewTimeline.svelte` 无条件渲染创作者佣金金额，品牌端用户可见平台内部定价。
- **修复**：对非管理员用户隐藏 `Creator: $X` 字段，仅管理员可查看平台手续费详情。
- **成果**：品牌端审核页面不再泄露平台利润信息，保护平台定价隐私。

### [#2605](https://github.com/Vispie-AI/VisPie_backend/pull/2605) fix(mobile-app-web): hide creator-cut pricing from invite modal
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：邀请创作者弹窗向品牌用户显示了创作者实际收款金额，泄露平台内部定价结构。
- **修复**：移除 `InviteCreatorModal` 中调用 `calculateCreatorCut` 的子文本显示逻辑。
- **成果**：品牌端邀请弹窗不再暴露平台分成结构，保护商业敏感信息。

### [#2604](https://github.com/Vispie-AI/VisPie_backend/pull/2604) Fix Redis cache TTL argument
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：`cache_response` 传入 Redis-py 风格的 `ex=` 参数，本地封装的 `RedisClient.set()` 实际使用 `ttl=`，导致缓存失效。
- **修复**：将 TTL 参数由 `ex=` 改为 `ttl=`，并将 `redis` 改为可选导入以兼容轻量环境。
- **成果**：缓存功能恢复正常，轻量脚本及测试环境在无 redis 包时可自动禁用缓存。

### [#2598](https://github.com/Vispie-AI/VisPie_backend/pull/2598) fix(amy): align DeepSeek shadow budget with primary Opus
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：DeepSeek 影子模型超时/迭代上限远低于主模型 Opus，长问题在影子侧提前终止，A/B 对比数据失真。
- **修复**：将影子模型四项限制（超时/迭代/上下文等）对齐至与主模型 Opus 相同量级。
- **成果**：A/B 对比面板在长复杂问题场景下可正常完成 DeepSeek 侧的完整推理。

### [#2592](https://github.com/Vispie-AI/VisPie_backend/pull/2592) hotfix(video): expand_short_url manual single-hop — TikTok anti-bot 403
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：#2577 部署后服务端展开 TikTok 短链遭遇反爬 HTTP 403，所有短链提交返回 503 错误。
- **修复**：改为手动单跳解析短链，绕过 TikTok 反爬机制，避免触发自动重定向检测。
- **成果**：TikTok 短链展开功能恢复，用户提交视频链接不再返回服务不可用错误。

### [#2591](https://github.com/Vispie-AI/VisPie_backend/pull/2591) hotfix(env): drop PR_NOTIFY_TITLE — unblocks deploy-ec2
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：`PR_NOTIFY_TITLE` 环境变量值含空格，`deploy-ec2` CI 步骤解析失败，#2587 后所有主分支部署中断。
- **修复**：删除 `PR_NOTIFY_TITLE` 环境变量，解除对 `deploy-ec2` 工作流的阻塞。
- **成果**：主分支 EC2 自动部署恢复正常，CI 流水线不再因含空格的环境变量报错。
### [#2543](https://github.com/Vispie-AI/VisPie_backend/pull/2543) fix(insforge-fallback): correct wire format + cost attribution + observability
- **日期**：2026-04-27 | **状态**：✅ 已合并
- **问题**：InsForge 备用路径请求字段（maxTokens/toolChoice 等）未遵循 camelCase 规范，费用归因错误返回 $0，且无可观测日志。
- **修复**：修正参数命名、新增动态模型映射、修复费用路由至 OpenRouter 价格表，添加 `[bedrock-fallback]` 日志标记。
- **成果**：15 次 smoke 全部通过，InsForge 备用路径费用统计准确，可通过日志追踪调用频率。

### [#2541](https://github.com/Vispie-AI/VisPie_backend/pull/2541) fix(vio): deploy script port-conflict pre-check + reserved port docs
- **日期**：2026-04-27 | **状态**：✅ 已合并
- **问题**：Vio 部署脚本未检测端口占用，manus 切换时端口 3201 被 autoamy-prod 占用，健康检查误报导致流量路由至错误租户。
- **修复**：在 docker compose up 前新增端口预检（ss -tlnp），端口 <3210 直接拒绝，补充各端口保留说明。
- **成果**：端口冲突可在部署前发现并中止，避免错误路由造成用户影响。

### [#2540](https://github.com/Vispie-AI/VisPie_backend/pull/2540) fix(video-frontend): kill infinite analyze→upload→analyze loop (regression from PR #2535)
- **日期**：2026-04-27 | **状态**：✅ 已合并
- **问题**：PR #2535 修复上传后，前端将 EventSource 正常关闭误判为失败，触发 analyze→upload→analyze 无限循环。
- **修复**：引入显式状态机（attemptCount/completedSuccessfully/fallbackAttempted），限制 fallback 仅触发一次。
- **成果**：URL 和文件上传场景均可正常终止，不再出现无限循环或无效错误提示。

### [#2539](https://github.com/Vispie-AI/VisPie_backend/pull/2539) fix(amy): restore max_tool_result_chars in SubagentSyncTool → AgentRunSpec
- **日期**：2026-04-27 | **状态**：✅ 已合并
- **问题**：PR #2522 重构时遗漏 `max_tool_result_chars` 的三处传递，导致生产环境 subagent 工具调用崩溃。
- **修复**：恢复 `__init__` 接收参数、构造函数存储、`execute()` 传递至 AgentRunSpec 三处代码。
- **成果**：394 个测试全部通过，subagent 工具调用恢复正常，新增回归守卫防止再次丢失。

### [#2538](https://github.com/Vispie-AI/VisPie_backend/pull/2538) fix(env): quote DB URLs in .env so shell source parses them
- **日期**：2026-04-27 | **状态**：🚫 已关闭
- **问题**：.env 中 DB URL 未加引号，shell source 时特殊字符截断 URL，matrix mining pipeline 因端口解析失败无法启动。
- **修复**：将 AGENT_DATABASE_URL、POSTGRES_DSN、MYSQL_DSN 三行改用单引号包裹，与 PGPASSWORD 保持一致。
- **成果**：DB URL 可被 shell 正确解析，pipeline --stage 1 成功连接数据库。

### [#2537](https://github.com/Vispie-AI/VisPie_backend/pull/2537) fix(vio): docker-compose volume key — service ref must match top-level def
- **日期**：2026-04-27 | **状态**：✅ 已合并
- **问题**：compose 模板中 service 引用的 volume key 变量替换后与 top-level volumes 定义名不一致，导致启动报错。
- **修复**：将 volume key 改为抽象名 `shadow-state`，仅在 `name:` 字段保留变量替换。
- **成果**：compose 启动不再报 undefined volume 错误，manus cutover 可正常继续。

### [#2536](https://github.com/Vispie-AI/VisPie_backend/pull/2536) fix(vio): correct manus cutover paths + add copy-first parallel-safe model
- **日期**：2026-04-27 | **状态**：✅ 已合并
- **问题**：PR #2499 切换脚本中工作区路径、PG slug 及 LARK_VERIFICATION_TOKEN 必要性均与生产实际不符。
- **修复**：修正路径为 JuiceFS 实际挂载点、slug 改为 `vio-manus`，新增 copy-first 模型让 nanobot 使用独立工作区副本。
- **成果**：切换脚本可安全运行，OpenClaw 工作区不受影响，回滚时状态无损。

### [#2535](https://github.com/Vispie-AI/VisPie_backend/pull/2535) fix(video): unified platform parser + fetcher fixes 400 on /api/proxy/media/video
- **日期**：2026-04-27 | **状态**：✅ 已合并
- **问题**：前端 fallback 使用占位 URL `@tiktok`，后端 serializer 缺少 instagram_url 字段，无来源时返回 5xx 而非 4xx。
- **修复**：新增 platform_parser/platform_fetcher 统一解析层，扩展 serializer 支持多平台 URL，修复前端 fallback 及上传格式限制。
- **成果**：TikTok/Instagram/YouTube/Douyin 均可正常解析，28 个解析器单元测试通过。

### [#2533](https://github.com/Vispie-AI/VisPie_backend/pull/2533) fix(amy): token-expiry retry on Lark Drive comment API calls
- **日期**：2026-04-27 | **状态**：✅ 已合并
- **问题**：lark/comments.py 的 7 个 Drive API 函数缺少 token 过期重试，token 提前失效时 Amy 停止响应文档 @提及约 14 分钟。
- **修复**：将 client.py 已验证的 `for attempt in range(2)` + `_invalidate_token()` 重试模式移植到全部 7 个函数。
- **成果**：391 个测试通过，token 过期时自动刷新重试，Amy 响应不再中断。

### [#2532](https://github.com/Vispie-AI/VisPie_backend/pull/2532) fix(ci): smoke test pipes script via stdin — fixes YAML literal-block syntax error
- **日期**：2026-04-27 | **状态**：✅ 已合并
- **问题**：PR #2531 在 YAML run 块内嵌入 Python heredoc 导致三个 CI workflow YAML 解析失败并立即报错。
- **修复**：将 smoke 脚本提取为独立文件 smoke_find_spec.py，通过 stdin 管道传入容器执行。
- **成果**：三个 workflow YAML 语法验证通过，CI smoke 检查语义不变。

### [#2530](https://github.com/Vispie-AI/VisPie_backend/pull/2530) fix(matrix-mining): add missing entry point wrappers
- **日期**：2026-04-27 | **状态**：✅ 已合并
- **问题**：pipeline.py 引用的 run_stage1/run_stage2_sync/run_stage4_sync 入口函数在 #2529 合并后缺失，--run-all 无法执行。
- **修复**：补充三个入口函数，run_stage1 支持 DuckDB/PG fallback，其余两个为 async 同步包装器。
- **成果**：matrix mining pipeline 的 --run-all 入口可正常调用。

### [#2524](https://github.com/Vispie-AI/VisPie_backend/pull/2524) fix(amy): v3 — explicit shadow tool deny-list (config gates bypassed in prod)
- **日期**：2026-04-26 | **状态**：✅ 已合并
- **问题**：DeepSeek shadow v2 的配置层禁用在生产中被绕过，shadow agent 实际调用了 shell 命令，存在双写风险。
- **修复**：新增 `_SHADOW_BLOCKED_TOOLS` 常量，在注册默认工具后立即 unregister exec/message/spawn。
- **成果**：89 个测试通过，pinning 测试确保危险工具不会被意外恢复到 shadow agent。
### [#2488](https://github.com/Vispie-AI/VisPie_backend/pull/2488) fix(ci): auto-rerun deploy workflows cancelled by GitHub concurrency
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：GitHub 并发策略导致多个部署工作流竞争时互相取消，无法自动恢复。
- **修复**：新增调谐工作流，检测被取消的部署任务并自动重新触发。
- **成果**：多工作流并发部署时不再因竞争导致部署永久失败。

### [#2486](https://github.com/Vispie-AI/VisPie_backend/pull/2486) fix(hooks): mark lint-hook-paths.sh + pre-commit as executable
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：PR #2468 添加的脚本文件在 git 中缺少可执行权限，导致 pre-commit 钩子无法运行。
- **修复**：将三个脚本文件权限从 100644 更新为 100755，使其可被 git 直接调用。
- **成果**：pre-commit 钩子和路径检查脚本均可正常执行。

### [#2485](https://github.com/Vispie-AI/VisPie_backend/pull/2485) fix(ci): split deploy-nanobot concurrency to job-level (Amy/Eva/Mitchell)
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：工作流级别并发配置导致同一次推送中多个 nanobot 部署工作流相互取消。
- **修复**：将 Amy/Eva/Mitchell 并发控制从工作流级别拆分至 Job 级别，与 nanobot-fleet 模式一致。
- **成果**：构建阶段可并行运行，部署阶段通过共享锁串行，消除无效取消。

### [#2481](https://github.com/Vispie-AI/VisPie_backend/pull/2481) fix(dm): refresh creator list when switching campaign filter
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：新建私信弹窗中切换活动过滤器后，创作者列表未同步刷新，显示数据过时。
- **修复**：监听活动过滤变化事件，重新触发创作者列表加载并确保数据与当前活动范围一致。
- **成果**：切换活动过滤器后创作者列表实时刷新，"全选"功能与过滤结果保持一致。

### [#2478](https://github.com/Vispie-AI/VisPie_backend/pull/2478) fix(amy): strip markdown for Lark doc comment replies
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：Amy 回复飞书云文档评论时，Markdown 格式符号以纯文本形式显示，严重影响可读性。
- **修复**：在发送文档评论回复前对内容进行去 Markdown 处理，转换为纯文本格式。
- **成果**：Amy 在云文档评论中的回复内容格式正确，不再出现原始 Markdown 符号。

### [#2477](https://github.com/Vispie-AI/VisPie_backend/pull/2477) hotfix(amy): restore drive.notice.comment_add_v1 webhook routing
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：飞书云文档评论 Webhook 事件未匹配任何处理分支，静默返回 200 OK，Amy 完全无法感知 @-提及。
- **修复**：修复 Webhook 入口分支判断逻辑，正确路由 drive.notice.comment_add_v1 事件。
- **成果**：Amy 恢复响应飞书云文档 @-提及，Plan B 方案（#2476）功能正常生效。

### [#2474](https://github.com/Vispie-AI/VisPie_backend/pull/2474) fix(dm): restore campaign dropdown scrolling in New DM modal
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：新建私信弹窗的活动筛选下拉列表在活动数量多时无法滚动，内容超出视图范围。
- **修复**：为下拉列表添加内部滚动容器并限制最大高度，保持现有布局和选择逻辑不变。
- **成果**：活动列表较长时下拉框可正常滚动，弹窗使用体验恢复正常。

### [#2472](https://github.com/Vispie-AI/VisPie_backend/pull/2472) fix(nanobot): restore R3 group extraction + R4 MEMORY.md injection
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：#2465 的群组记忆提取（R3）和 MEMORY.md 注入（R4）在后续压缩合并中意外丢失。
- **修复**：恢复 R3 通过 DeepSeek V4 Pro 提取群聊记忆并写入 Insforge，以及 R4 MEMORY.md 注入系统上下文。
- **成果**：nanobot 群聊记忆提取和个人记忆注入功能全部恢复正常。

### [#2471](https://github.com/Vispie-AI/VisPie_backend/pull/2471) fix(nanobot): /compact load session from disk not just cache
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：/compact 命令仅从内存缓存读取会话，容器重启后缓存为空，导致始终返回"会话过短"错误。
- **修复**：将 _cache.get() 改为 get_or_create()，从磁盘 JSONL 文件加载历史会话。
- **成果**：容器重启后 /compact 可正常加载历史会话并执行压缩操作。

### [#2470](https://github.com/Vispie-AI/VisPie_backend/pull/2470) fix(auto-amy): inject INTERNAL_DEPLOYMENT=true into amy/eva nanobot workflows
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：使用独立部署工作流的 Amy/Eva 等 3 个 nanobot 未注入 INTERNAL_DEPLOYMENT=true，内部标识缺失。
- **修复**：在 Amy/Eva nanobot 专用部署工作流中补充注入 INTERNAL_DEPLOYMENT=true 环境变量。
- **成果**：全部 14 个 nanobot 均正确携带内部部署标识，内部流量识别完整。

### [#2469](https://github.com/Vispie-AI/VisPie_backend/pull/2469) fix(nanobot): /compact reply_text NameError
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：/compact 命令因 reply_text 变量未定义而崩溃，触发 NameError 异常。
- **修复**：将 reply_text 替换为 _real_add_done_reaction（完成表情）与 _real_send_card（卡片展示压缩结果）。
- **成果**：/compact 命令执行正常，不再出现 NameError 异常崩溃。

### [#2468](https://github.com/Vispie-AI/VisPie_backend/pull/2468) fix(hooks): use $CLAUDE_PROJECT_DIR for hook paths in settings.json
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：settings.json 中 Hook 使用相对路径，跨会话执行时频繁报"找不到脚本"警告。
- **修复**：将 Hook 路径改为 $CLAUDE_PROJECT_DIR/.claude/hooks/X.sh 绝对环境变量路径。
- **成果**：Hook 路径问题解决，不再出现脚本找不到的报错。

### [#2464](https://github.com/Vispie-AI/VisPie_backend/pull/2464) fix(amy): allow add_reply notice_type for repeat @-mentions in same thread
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：Amy 仅响应同一评论线程中的首次 @-提及，后续重复 @-提及被过滤器拦截。
- **修复**：在事件过滤逻辑中允许 add_reply 类型的 notice_type，使同线程重复 @-提及均能触发处理。
- **成果**：Amy 可正常响应同一评论线程中的所有 @-提及，不再仅处理首条。
### [#2449](https://github.com/Vispie-AI/VisPie_backend/pull/2449) fix(gateway-prod): remove sidecar deploy, fix session-sync fallback
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：生产网关的 session-sync sidecar 部署引入额外复杂性并导致回退逻辑异常。
- **修复**：移除 sidecar 部署方式，修复 session-sync 的回退处理逻辑。
- **成果**：生产环境 session-sync 服务更加稳定可靠。

### [#2448](https://github.com/Vispie-AI/VisPie_backend/pull/2448) fix(gateway-prod): session-sync sidecar deploy + default SESSION_SYNC_CHATS=*
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：生产网关缺少 SESSION_SYNC_CHATS 默认值导致会话同步范围不正确。
- **修复**：部署 session-sync sidecar 并将 SESSION_SYNC_CHATS 默认值设为 *。
- **成果**：所有会话均可被正确同步，生产网关稳定性提升。

### [#2445](https://github.com/Vispie-AI/VisPie_backend/pull/2445) fix(nanobot): bound image-block token estimation to avoid 52K overcount
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：nanobot 对图片块的 token 估算存在严重过计，最高偏差达 52K。
- **修复**：为图片块 token 估算增加上限约束，避免异常高值。
- **成果**：token 计量更加准确，防止因过估导致的请求异常。

### [#2440](https://github.com/Vispie-AI/VisPie_backend/pull/2440) fix: stabilize creator overview scraping sync
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：创作者概览数据抓取同步过程不稳定，存在竞争条件。
- **修复**：稳定化创作者概览抓取的同步逻辑，消除竞态问题。
- **成果**：创作者概览数据采集可靠性显著提高。

### [#2438](https://github.com/Vispie-AI/VisPie_backend/pull/2438) Fix profile logout cookie clearing
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：用户退出登录时 cookie 未被正确清除，导致会话残留安全风险。
- **修复**：修复退出登录接口中的 cookie 清除逻辑。
- **成果**：用户注销后 cookie 被彻底清理，账户安全性增强。

### [#2437](https://github.com/Vispie-AI/VisPie_backend/pull/2437) fix(coder-army): idle threshold 30s → 120s
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：coder-army 闲置检测阈值过短（30s），导致正常任务被误判为空闲超时。
- **修复**：将闲置检测阈值从 30 秒调整为 120 秒。
- **成果**：减少对长时间运行任务的误判，提升 agent 稳定性。

### [#2436](https://github.com/Vispie-AI/VisPie_backend/pull/2436) fix(coder-army): clean index.lock on kill too
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：coder-army 被强制终止时未清理 git index.lock 文件，导致后续操作卡死。
- **修复**：在 kill 操作时同步清理 git index.lock 文件。
- **成果**：确保 agent 被终止后 git 仓库状态干净，不影响后续任务。

### [#2435](https://github.com/Vispie-AI/VisPie_backend/pull/2435) fix(coder-army): clean stale git index.lock on create
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：创建新 coder-army 实例时，旧的 git index.lock 文件残留导致初始化失败。
- **修复**：在 coder-army 创建阶段自动清理过期的 index.lock 文件。
- **成果**：避免因残留锁文件导致初始化失败，提升创建成功率。

### [#2431](https://github.com/Vispie-AI/VisPie_backend/pull/2431) fix(coder-army): remove send delay cap for long text
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：coder-army 对长文本消息的发送存在延迟上限，导致大消息发送超时失败。
- **修复**：移除长文本发送的延迟上限限制。
- **成果**：长文本消息可正常发送，不再因延迟上限被截断或超时。

### [#2429](https://github.com/Vispie-AI/VisPie_backend/pull/2429) Fix local frontend dev startup
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：本地前端开发环境无法正常启动，影响开发效率。
- **修复**：修复本地前端开发环境启动配置问题。
- **成果**：开发者可正常在本地启动前端服务，开发效率恢复正常。

### [#2427](https://github.com/Vispie-AI/VisPie_backend/pull/2427) fix(coder-army): completed threshold 5s → 30s for DeepSeek thinking
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：coder-army 使用 DeepSeek 思考模式时完成判断阈值过短（5s），导致误报完成。
- **修复**：将 DeepSeek 思考模式的完成检测阈值从 5 秒提升至 30 秒。
- **成果**：DeepSeek 思考任务完成判断更准确，误报率降低。
### [#2418](https://github.com/Vispie-AI/VisPie_backend/pull/2418) Fix profile logout redirect
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：用户退出登录后被跳转到错误页面，导致体验中断。
- **修复**：修正退出登录后的跳转逻辑，确保重定向到正确目标页面。
- **成果**：退出登录流程恢复正常，用户体验得到保障。

### [#2416](https://github.com/Vispie-AI/VisPie_backend/pull/2416) fix(vio-ci): HOTFIX 'max expression length 21000' — extract bash helpers
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：PR #2415 修复 YAML 解析后，部署仍因 GitHub 表达式超过 21000 字符限制而持续失败。
- **修复**：将超长 Bash 辅助函数提取到独立脚本，彻底消除超长 YAML 表达式问题。
- **成果**：Vio 主分支部署流程完全恢复正常。

### [#2415](https://github.com/Vispie-AI/VisPie_backend/pull/2415) fix(vio-ci): HOTFIX workflow YAML parse — extract Python to separate script
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：PR #2389 合并后，Vio 主分支每次部署均因工作流 YAML 解析错误而中断。
- **修复**：将内嵌 Python 代码提取到独立脚本，修复 YAML 结构异常。
- **成果**：Vio 部署流水线恢复可用，生产环境不再停滞在旧版镜像。

### [#2414](https://github.com/Vispie-AI/VisPie_backend/pull/2414) fix(daily-agenda): use only calendar metadata — eliminate doc search hallucination
- **日期**：2026-04-23 | **状态**：✅ 已合并
- **问题**：文档搜索返回不相关结果，Gemini 基于错误文档为会议编造行动项。
- **修复**：摘要生成仅使用日历元数据，完全移除文档搜索步骤。
- **成果**：日程摘要内容准确，幻觉式行动项问题彻底消除。

### [#2412](https://github.com/Vispie-AI/VisPie_backend/pull/2412) fix(daily-agenda): per-meeting doc isolation to eliminate hallucination
- **日期**：2026-04-23 | **状态**：✅ 已合并
- **问题**：多会议文档内容混合，导致 Gemini 将无关文档内容错误归入其他会议。
- **修复**：为每个会议单独获取文档内容，实现严格的文档隔离处理。
- **成果**：每次会议摘要仅包含本次会议相关文档，准确性显著提升。

### [#2410](https://github.com/Vispie-AI/VisPie_backend/pull/2410) fix(coder-army): unique branch names + auto-push v2
- **日期**：2026-04-23 | **状态**：✅ 已合并
- **问题**：多代理槽复用同一分支名导致推送冲突，并发任务无法正常执行。
- **修复**：每次运行生成唯一分支名，同步优化自动推送逻辑。
- **成果**：多代理并发执行不再产生分支冲突，工作流稳定性提升。

### [#2399](https://github.com/Vispie-AI/VisPie_backend/pull/2399) fix(coder-army): agents must auto-push by default
- **日期**：2026-04-23 | **状态**：✅ 已合并
- **问题**：无头代理完成任务后未自动推送代码，需人工介入处理。
- **修复**：在 CLAUDE.md 中明确要求无头代理自动推送并创建 PR。
- **成果**：代理任务完成后代码自动上传，减少人工操作步骤。

### [#2398](https://github.com/Vispie-AI/VisPie_backend/pull/2398) fix(vio): Codex+Claude review followups + expand regression guard
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：PR #2389 代码审查发现 4 处问题，且回归测试覆盖不足。
- **修复**：处理所有审查意见并扩展回归测试，防止 OpenClaw 配置漂移。
- **成果**：Vio 监控模块代码质量提升，回归保障更加全面。

### [#2396](https://github.com/Vispie-AI/VisPie_backend/pull/2396) fix(vio-liveness): match plain 'node index.js' for vio-gateway L2 check
- **日期**：2026-04-23 | **状态**：✅ 已合并
- **问题**：L2 进程存活检测使用错误的 grep 关键词，无法匹配实际进程命令。
- **修复**：将 L2 检测条件改为匹配 `node index.js`，与实际进程命令保持一致。
- **成果**：vio-gateway 存活监控准确率恢复正常，误报问题消除。
### [#2382](https://github.com/Vispie-AI/VisPie_backend/pull/2382) fix(auto-amy): pre-analysis timeout + v3 test updates
- **日期**：2026-04-22 | **状态**：✅ 已合并
- **问题**：Gemini预分析超时600秒导致视频审核Step 4报错失败。
- **修复**：将Gemini预分析超时从600秒调整至900秒，并更新v3架构对应的测试用例。
- **成果**：视频审核流程超时问题得到解决，测试套件与最新OCR架构保持同步。

### [#2381](https://github.com/Vispie-AI/VisPie_backend/pull/2381) fix(vio): allow file sharing in Slack via mediaLocalRoots
- **日期**：2026-04-22 | **状态**：✅ 已合并
- **问题**：Slack频道无法共享本地文件，AI缺乏文件路径引导规范。
- **修复**：在openclaw.json中添加mediaLocalRoots配置，并在SOUL.md中补充文件共享规范。
- **成果**：实现通过Slack渠道正常共享工作区和临时目录文件的能力。

### [#2376](https://github.com/Vispie-AI/VisPie_backend/pull/2376) fix(auto-amy): apply 1.1x US inference-profile multiplier
- **日期**：2026-04-22 | **状态**：✅ 已合并
- **问题**：AWS Bedrock实际费率比Anthropic直连高约10%，但费用追踪未作修正。
- **修复**：在美区推理配置文件费用计算中统一加入1.1倍乘数系数。
- **成果**：成本追踪数据与实际AWS账单对齐，提升计费准确性。

### [#2373](https://github.com/Vispie-AI/VisPie_backend/pull/2373) fix(auto-amy): add ffmpeg + pyspellchecker to Mitchell Dockerfile for GLM-OCR
- **日期**：2026-04-22 | **状态**：✅ 已合并
- **问题**：Mitchell Docker镜像缺少ffmpeg和pyspellchecker，导致GLM-OCR帧提取和拼写检测失败。
- **修复**：在Mitchell Dockerfile中添加ffmpeg系统包及pyspellchecker pip依赖。
- **成果**：GLM-OCR所需的视频帧提取与英文拼写检查功能在容器内正常运行。

### [#2372](https://github.com/Vispie-AI/VisPie_backend/pull/2372) fix(auto-amy): Python 3.14 async test compat — asyncio.run() migration
- **日期**：2026-04-22 | **状态**：✅ 已合并
- **问题**：Python 3.14已废弃asyncio.get_event_loop().run_until_complete()，导致5个测试失败。
- **修复**：将2个测试文件中12处旧式调用全部迁移为asyncio.run()写法。
- **成果**：测试套件在Python 3.14下全部通过，异步兼容性问题得以消除。

### [#2370](https://github.com/Vispie-AI/VisPie_backend/pull/2370) fix(messages): image attachments clickable (open in new tab)
- **日期**：2026-04-22 | **状态**：✅ 已合并
- **问题**：品牌/管理员聊天面板中发送和接收的图片无法点击查看，视频和文件已正常处理但图片除外。
- **修复**：为图片元素添加可点击链接，点击后在新标签页中打开原图。
- **成果**：管理员聊天中图片附件支持点击查看，交互体验与视频附件保持一致。

### [#2363](https://github.com/Vispie-AI/VisPie_backend/pull/2363) fix(claude-md): add Think Before Coding, strengthen Evidence First
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：CLAUDE.md中缺少"先思考后编码"原则，以证为先原则表述不够清晰有力。
- **修复**：新增"Think Before Coding"为第一原则，强化以证为先表述，原则数由5条增至6条。
- **成果**：AI协作规范更加完善，开发流程中的思考优先和证据驱动文化得到强化。

### [#2361](https://github.com/Vispie-AI/VisPie_backend/pull/2361) fix(shadow): resolve model name from runtime config
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：Shadow回调因OpenClaw容器未设置DEFAULT_MODEL环境变量，始终上报model="unknown"。
- **修复**：改为从运行时配置api.config.agents.defaults.model动态读取模型名称。
- **成果**：Shadow回调准确上报实际使用的模型名称，监控数据可信度提升。

### [#2360](https://github.com/Vispie-AI/VisPie_backend/pull/2360) fix(creator-tracker): one row per creator×campaign instead of per-creator
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：原同步逻辑将每个创作者的所有活动合并为一行多选标签，不符合业务需求。
- **修复**：改为每个创作者×活动组合生成独立一行，每行仅对应单个活动。
- **成果**：Lark Bitable数据结构符合要求，便于按活动维度筛选和分析创作者数据。

### [#2359](https://github.com/Vispie-AI/VisPie_backend/pull/2359) fix(creator-tracker): filter active campaigns + multi-select format
- **日期**：2026-04-21 | **状态**：🔀 待合并
- **问题**：同步脚本拉取全部38个活动（含已结束），且活动字段格式不符合多选要求。
- **修复**：增加status=active过滤条件（仅13个活动），活动和分类字段改为数组格式写入。
- **成果**：仅同步进行中活动，多选字段格式正确，数据质量显著提升。
### [#2356](https://github.com/Vispie-AI/VisPie_backend/pull/2356) fix(auto-amy): correct Bedrock pricing — Opus was 3x too high
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：usage.py 中 Bedrock 定价表沿用 Claude-3 旧费率，导致每日报告 Opus 成本虚高约 3 倍。
- **修复**：按 2026-04-21 AWS Bedrock 官方定价更新 `_BEDROCK_PRICING` 中各型号费率。
- **成果**：每日 API 成本报告数据准确，不再虚报使用费用。

### [#2353](https://github.com/Vispie-AI/VisPie_backend/pull/2353) fix(auto-amy): hook streaming path + cache-aware pricing
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：#2351 上线后生产环境仍产生 0 条 usage_events，钩子挂载点错误且未区分缓存与非缓存价格。
- **修复**：将 UsageMeter 挂入 `chat_stream_with_retry()` 流式路径，并按输入/缓存命中分段计价。
- **成果**：Mitchell 生产环境 LLM 调用被正确记录，成本追踪管道正常运行。

### [#2352](https://github.com/Vispie-AI/VisPie_backend/pull/2352) fix(auto-amy): hook streaming path + cache-aware pricing
- **日期**：2026-04-21 | **状态**：🚫 已关闭
- **问题**：#2351 上线后生产环境未记录任何 usage 事件，钩子挂载点和缓存计价均有误。
- **修复**：尝试修复流式路径挂载与缓存感知定价，后被 #2353 替代并关闭。
- **成果**：此 PR 已关闭，相关修复由 #2353 最终落地。

### [#2350](https://github.com/Vispie-AI/VisPie_backend/pull/2350) fix(deploy): poll FastAPI healthcheck instead of racing 15s sleep
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：统一部署流程 FastAPI 验证步骤与 --force-recreate 存在竞态条件，近期 16 次中 11 次失败。
- **修复**：将硬编码的 15s sleep 改为轮询 FastAPI /health 端点直到服务就绪。
- **成果**：消除部署竞态条件，Unified Deployment CI 稳定通过。

### [#2343](https://github.com/Vispie-AI/VisPie_backend/pull/2343) Fix ads analytics Meta OAuth flow and readiness UI
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：广告分析页面 Meta OAuth 授权流程存在缺陷，就绪状态 UI 显示不正确。
- **修复**：修复 Meta OAuth 授权流程及就绪状态 UI 的展示逻辑。
- **成果**：用户可正常完成 Meta 广告账号授权，就绪状态显示准确。

### [#2342](https://github.com/Vispie-AI/VisPie_backend/pull/2342) fix(ads-analytics): restore date segment interactivity in TimeRangeSelector
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：广告分析日期选择器因 wrapper 的 readonly 模式添加了 pointer-events-none，导致日期分段点击无效。
- **修复**：在只读模式下解除对 TimeRangeSelector 内部日期分段的指针事件屏蔽。
- **成果**：广告分析页面日期输入交互恢复正常，用户可自由选择时间段。

### [#2341](https://github.com/Vispie-AI/VisPie_backend/pull/2341) fix(twilio-conversations): ROOT CAUSE — poll merge instead of replace
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：15s 轮询仅拉取第一页并整体替换数组，用户通过"加载更多"查看的后续对话每 15s 丢失一次。
- **修复**：轮询改为增量合并而非全量替换，保留用户已加载的全部对话条目。
- **成果**：彻底消除 Load More 数据丢失，定位并解决前序补丁（#2335~#2339）未触及的真正根因。

### [#2340](https://github.com/Vispie-AI/VisPie_backend/pull/2340) fix(twilio-conversations): ROOT CAUSE — poll merge instead of replace
- **日期**：2026-04-22 | **状态**：🚫 已关闭
- **问题**：15s 轮询整体替换对话列表，导致用户加载超出第一页的内容每 15s 消失。
- **修复**：尝试改为增量合并轮询，最终被 #2341 替代后关闭。
- **成果**：此 PR 已关闭，修复由 #2341 正式合并。

### [#2339](https://github.com/Vispie-AI/VisPie_backend/pull/2339) fix(twilio-conversations): tags poll skip re-render when unchanged
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：标签轮询（每 2 分钟）无条件替换 availableTags 数组，即使标签未变也触发下拉框重渲染。
- **修复**：用 JSON.stringify 对比新旧标签列表，只在实际变化时更新状态。
- **成果**：标签无变化时不再触发重渲染，完成零多余重渲染系列优化。

### [#2338](https://github.com/Vispie-AI/VisPie_backend/pull/2338) fix(twilio-conversations): detail poll skip re-render on metadata-only change
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：10s 详情轮询在仅有 metadata 变化（无新消息）时仍替换 conversationDetails，引发右侧面板重渲染。
- **修复**：只在消息数量、最新 SID 或状态实际变化时才更新 conversationDetails。
- **成果**：详情面板在无新消息时不再无效刷新，降低 UI 抖动频率。

### [#2337](https://github.com/Vispie-AI/VisPie_backend/pull/2337) fix(twilio-conversations): preserve scroll + minimize re-renders on poll
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：后台轮询期间滚动位置被重置到顶部，对话卡片短暂闪烁，影响用户操作连续性。
- **修复**：SID 变化时保留滚动位置，减少 Svelte 组件在轮询更新时的不必要重渲染。
- **成果**：轮询期间滚动位置稳定，界面闪烁明显减少。

### [#2335](https://github.com/Vispie-AI/VisPie_backend/pull/2335) fix(twilio-conversations): eliminate 15s poll UI flicker
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：每次 15s 对话列表轮询时显示约 200ms 的"Loading..."动画，导致明显 UI 抖动。
- **修复**：轮询时不再无条件触发加载状态，改为后台静默更新对话列表数据。
- **成果**：对话列表轮询完全无感知，用户工作流不再被 15s 闪屏打断。

### [#2334](https://github.com/Vispie-AI/VisPie_backend/pull/2334) fix(cc-army): default callback URL to production gateway
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：CODER_ARMY_CALLBACK_URL 默认值为 localhost:4000，但 cc-army 运行在 web_dev 容器，该端口无监听者。
- **修复**：将默认回调 URL 改为生产网关地址 https://amy.vispie-ai.com/coder-army/callback。
- **成果**：cc-army 回调在未配置环境变量时自动指向生产网关，无需手动干预。
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

### [#2735](https://github.com/Vispie-AI/VisPie_backend/pull/2735) feat(shadow-judge): upgrade judge model to gemini-3-pro-preview
- **日期**：2026-05-01 | **状态**：✅ 已合并
- **问题**：Shadow Arena使用gemini-2.5-flash评判，频繁产生winner:error，判决质量低。
- **修复**：将公平裁判模型从gemini-2.5-flash升级为gemini-3-pro-preview。
- **成果**：判决质量提升，error率降低，A/B对比结果更具参考价值。

### [#2733](https://github.com/Vispie-AI/VisPie_backend/pull/2733) feat: add weekly-campaign-tracker Prefect flow
- **日期**：2026-05-01 | **状态**：✅ 已合并
- **问题**：周度活动追踪依赖Node脚本，无法通过Prefect统一调度和监控。
- **修复**：将weekly-campaign-tracker移植为Python Prefect流程，每天08:00 UTC通过Cloud Run V2执行。
- **成果**：活动追踪自动化，支持MAX平台汇总、周同比对比及飞书表格自动写入。

### [#2728](https://github.com/Vispie-AI/VisPie_backend/pull/2728) feat(format-analysis): support Doubao Seed 2 video verify
- **日期**：2026-05-01 | **状态**：✅ 已合并
- **问题**：视频验证仅支持Gemini，缺乏国内多模态大模型备选方案。
- **修复**：添加BytePlus Ark/Doubao客户端，视频验证支持通过环境变量切换到Doubao Seed 2。
- **成果**：格式分析增加Doubao Seed 2视频验证能力，Gemini作为自动降级备用。

### [#2727](https://github.com/Vispie-AI/VisPie_backend/pull/2727) feat: prefer Codex for Coder Army auto routing
- **日期**：2026-05-01 | **状态**：✅ 已合并
- **问题**：Coder Army自动路由未明确设定各编码智能体的权重分配策略。
- **修复**：auto模式按Codex 80%、OpenCode 15%、Claude 5%权重路由，Codex默认使用gpt-5.5。
- **成果**：Coder Army任务优先由Codex处理，路由策略清晰且支持环境变量灵活配置。

### [#2726](https://github.com/Vispie-AI/VisPie_backend/pull/2726) feat: add Agent Doctor trace debug
- **日期**：2026-05-01 | **状态**：✅ 已合并
- **问题**：缺乏可视化工具对Agent trace进行在线诊断和调试。
- **修复**：新增只读Agent Doctor后端路由及前端内部工具页面，支持通过trace_id查询调试。
- **成果**：运维人员可通过Agent Doctor页面快速定位和诊断Agent异常trace。

### [#2721](https://github.com/Vispie-AI/VisPie_backend/pull/2721) feat(ci): GitHub Actions secret-injection for Vio per-tenant nanobot deploy
- **日期**：2026-05-01 | **状态**：✅ 已合并
- **问题**：Vio EC2上nanobot的GLM_API_KEY为空，OCR功能在每次审核时均输出警告。
- **修复**：通过GitHub Actions将GLM_API_KEY经Parameter Store安全注入各租户.env.nanobot。
- **成果**：OCR功能恢复正常，密钥注入流程安全可审计，支持多租户横向扩展。
### [#2704](https://github.com/Vispie-AI/VisPie_backend/pull/2704) feat(matrix-mining): auto-load .env, disable Redis, Prefect flow + local deploy
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：IG 矩阵挖掘需手动 source .env，频繁报 Redis 错误，无法通过 Prefect 调度。
- **修复**：添加启动自动读取 .env、默认禁用 Redis 和 Prefect 流封装及本地部署脚本。
- **成果**：28 个测试通过，流水线可直接运行并支持 Prefect 触发。

### [#2703](https://github.com/Vispie-AI/VisPie_backend/pull/2703) feat(matrix-mining): auto-load .env, disable Redis, Prefect flow + remove API key gate
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：使用 Vertex AI 服务账号鉴权，但代码仍要求 GEMINI_API_KEY 导致初始化报错。
- **修复**：移除 API Key 门控，新增 .env 自动加载和 Redis 默认禁用。
- **成果**：Vertex AI 鉴权流程打通，28 个测试通过，支持 Prefect 部署。

### [#2702](https://github.com/Vispie-AI/VisPie_backend/pull/2702) feat(mocks): Manus campaign share H5 flow mock
- **日期**：2026-04-30 | **状态**：🔀 待合并
- **问题**：Campaign 分享流程未实现，需可供利益相关方预览的完整 H5 原型。
- **修复**：制作单文件 375px 移动端 mock，覆盖活动详情、底栏弹窗及 10 个平台预览页。
- **成果**：原型已上传 S3，含 UTM 链接、OG 卡片预览和深色主题动效，无外部依赖。

### [#2699](https://github.com/Vispie-AI/VisPie_backend/pull/2699) feat(skills): mobile-ui-alignment v2 — Phase 0 + batch mode + conflict triage + wrapper scripts
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：v1 技能实际运行中出现 Phase 3 步骤丢失和 EAS 命令被重新生成错误等结构性故障。
- **修复**：引入 Phase 0 初始化、4 个封装脚本、批次模式和 Phase 5 冲突分诊，关键命令标记禁止重新生成。
- **成果**：技能升级至 11 规则 6 阶段，结构性故障封堵，沙箱隔离由脚本层强制执行。

### [#2688](https://github.com/Vispie-AI/VisPie_backend/pull/2688) feat: performance monitor — Newsbreak display, dashboard approved override, column rename
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Oxygeon AI 应显示为 Newsbreak，仪表板批准数计算和列名需修正。
- **修复**：更新展示名为 Newsbreak（含 +77 Scoopz 历史），仪表板改用 max(platform count) 计算批准数并重命名列头。
- **成果**：性能监控数据准确，仪表板指标与业务口径对齐。

### [#2687](https://github.com/Vispie-AI/VisPie_backend/pull/2687) feat: collect coder army delivery feedback automatically
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Coder Army 任务完成后缺少自动化交付评估，用户无法在原始会话收到反馈。
- **修复**：基于分支推送、PR 状态、CI 和 diff 记录 agent_evaluation 事件，结果回调至原始会话。
- **成果**：交付状态和失败原因自动回传到用户聊天，覆盖两个核心文件共 241 行。

### [#2683](https://github.com/Vispie-AI/VisPie_backend/pull/2683) feat(phyllo): creator-data integration foundation — backend client + endpoints + admin demo
- **日期**：2026-04-29 | **状态**：🔀 待合并
- **问题**：平台无法获取创作者私有数据（受众画像、真实互动率、收入等），依赖可抓取的公开数据存在明显局限。
- **修复**：集成 Phyllo API，实现 OAuth 授权、Token 管理及账户连接的完整后端客户端与管理员演示页。
- **成果**：基础集成已通过 staging 端到端验证，36 单元测试 + 3 集成测试全通过，为后续数据摄取奠定基础。

### [#2681](https://github.com/Vispie-AI/VisPie_backend/pull/2681) feat(vio-video-review): worker-side tenant guard + ADR — defense-in-depth (Option A+B)
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：仅依赖 Hatchet 任务名路由隔离存在盲区，手动调度或未预见的 bug 仍可导致跨租户数据污染。
- **修复**：在 task 函数最前端添加租户守卫，不匹配则立即返回 FAILED，并新增 ADR 文档固化多租户架构规范。
- **成果**：双层防护（任务名 + worker 守卫）消除跨租户语义污染风险，架构决策有文档可循。

### [#2680](https://github.com/Vispie-AI/VisPie_backend/pull/2680) feat(vio-video-review): per-tenant Hatchet task names — multi-tenant routing fix
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：所有租户共用同一个 Hatchet 任务名 vio-video-review，无关容器争抢任务导致多租户路由混乱（4.29 事故根因）。
- **修复**：每个容器根据 VIO_TENANT_SLUG 注册独立任务名，dispatcher 按 slug 精确路由，旧名称废弃。
- **成果**：租户间任务隔离从结构上得以保证，新增租户只需设置环境变量，无需改动代码。

### [#2679](https://github.com/Vispie-AI/VisPie_backend/pull/2679) feat(creators): default brand-scoped AI creator feed (precomputed)
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：品牌主访问创作者页面时展示全量 17K 创作者列表，缺乏与品牌自动匹配的智能推荐。
- **修复**：预计算品牌默认 persona 并缓存至数据库，首屏直接返回 AI 筛选列表，热路径响应约 50ms，Gemini 故障时平滑降级。
- **成果**：品牌主无需输入即可看到匹配自身品牌的创作者推荐，缓存命中响应速度提升约 50 倍。

### [#2677](https://github.com/Vispie-AI/VisPie_backend/pull/2677) feat(ci): add Deploy Vio Nanobot workflow — closes Phase 1 deploy gap
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Vio EC2 上的 nanobot 缺少自动化部署路径，代码合并后须手动 SSM 更新容器，4.29 事故中多次因旧代码干扰调试。
- **修复**：新增 CI workflow，在 Deploy Nanobot Fleet 成功后自动触发 Vio EC2 容器更新，并支持手动 dispatch 和健康验证。
- **成果**：Vio nanobot 部署纳入自动化流程，消除因手动遗漏旧代码导致的调试偏差。

### [#2672](https://github.com/Vispie-AI/VisPie_backend/pull/2672) feat: dashboard approved override for Manus/Newsbreak + Scoopz legacy offset (v4)
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Manus 和 Newsbreak 的活动审批进度数值未能反映 Creator Overview 仪表盘的真实视频数量。
- **修复**：为 manus 和 oxygeon ai 启用仪表盘计数覆盖，并为 Scoopz 历史视频添加 77 条遗留偏移量。
- **成果**：Manus 显示 651 条、Newsbreak 显示 128 条，与仪表盘实际数据保持一致。

### [#2663](https://github.com/Vispie-AI/VisPie_backend/pull/2663) feat(matrix-mining): switch Gemini to Vertex AI (360 RPM)
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：旧版 google.generativeai 免费层限制 15 RPM，频繁触发 429 错误影响 matrix-mining 管道稳定性。
- **修复**：切换至 google.genai SDK 使用 Vertex AI 服务账号认证（360 RPM），无需 API Key 轮换管理。
- **成果**：速率限制提升 24 倍，帖子间延迟从 5s 降至 2s，matrix-mining 管道更加稳定高效。

### [#2661](https://github.com/Vispie-AI/VisPie_backend/pull/2661) feat(matrix-mining): Gemini multi-key rotation + 429 auto-retry
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：单个 Gemini API Key 触发 429 限速时 matrix-mining 任务中断，缺乏自动重试和多密钥容错机制。
- **修复**：支持 MATRIX_IG_GEMINI_KEYS 环境变量配置多密钥轮换，遇 429 时自动切换并等待重试（最多 3 次）。
- **成果**：多密钥轮换降低 429 中断概率，任务连续性显著提升。
### [#2639](https://github.com/Vispie-AI/VisPie_backend/pull/2639) feat: use Creator Overview video counts for Manus and Oxygeon AI approved progress (v3)
- **日期**：2026-04-29 | **状态**：🚫 已关闭
- **问题**：Manus 和 Oxygeon AI 活动批准进度需改用 Creator Overview 仪表板视频数，前两次实现被关闭。
- **修复**：第三次尝试实现 Creator Overview 视频数作为批准进度来源，取跨平台最大值。
- **成果**：此版本已关闭，功能经迭代后由更稳定版本合入。

### [#2634](https://github.com/Vispie-AI/VisPie_backend/pull/2634) feat(shadow): Gemini 2.5 Flash judge + DeepSeek shadow A/B
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：原公平评判器使用 DeepSeek-chat 能力有限，需切换至更强模型并恢复 DeepSeek shadow 流。
- **修复**：将评判器切换为 Gemini 2.5 Flash，并将 DeepSeek shadow 接入 A/B 评判管道。
- **成果**：评判质量提升，DeepSeek shadow A/B 测试机制完善，多模型对比能力增强。

### [#2626](https://github.com/Vispie-AI/VisPie_backend/pull/2626) feat: use Creator Overview video counts for Manus & Oxygeon AI approved progress (v2)
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Manus 和 Oxygeon AI 活动使用数据库提交计数作为批准进度，与实际展示数据不一致。
- **修复**：引入 APPROVED_FROM_DASHBOARD 配置常量，对指定活动使用 Creator Overview 仪表板视频数。
- **成果**：特定活动批准进度数据来源统一，展示更准确，运营决策更可靠。

### [#2624](https://github.com/Vispie-AI/VisPie_backend/pull/2624) feat(vio): video review migration — Phase 1 (Path C hybrid, fail-loud, OCR parallel) + incident fix
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：遗留后端 fastapi 4 规则视频审核路径 100% 卡死，严重影响视频审核流程。
- **修复**：实施 Vio 视频审核迁移 Phase 1（1.1-1.4），构建按提交粒度、租户感知、事件驱动的审核流水线，OCR 并行处理。
- **成果**：视频审核基础架构完成迁移，遗留路径替换完成，处理效率显著提升。

### [#2623](https://github.com/Vispie-AI/VisPie_backend/pull/2623) feat(studio): add recreate resources and PR guardrails
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Format Studio 缺少活动资源，且无机制防止开发者直接向主分支推送代码。
- **修复**：添加根目录 AGENTS.md，配置 Claude/Codex push hooks 阻断直接推送，丰富 Manus/Fanka 活动资源。
- **成果**：分支保护机制生效，活动资源完善，开发流程更规范安全。

### [#2620](https://github.com/Vispie-AI/VisPie_backend/pull/2620) feat: use Creator Overview video counts for Manus & Oxygeon AI approved progress
- **日期**：2026-04-29 | **状态**：🚫 已关闭
- **问题**：需要将 Manus 和 Oxygeon AI 活动的批准进度改用 Creator Overview 视频数，首次实现存在问题。
- **修复**：首次尝试通过 APPROVED_FROM_DASHBOARD 集合覆盖批准计数逻辑，后被关闭。
- **成果**：此版本已关闭，功能由 v2（PR #2626）重新实现并合入主干。

### [#2619](https://github.com/Vispie-AI/VisPie_backend/pull/2619) feat(studio): Pro Templates video grid — 6 columns, APP/Ecom tabs
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：Campaign Brief 中 Pro Templates 展示形式老旧，缺少按应用场景分类的视频格网。
- **修复**：将 Pro Templates 替换为平铺视频格网（6 列、9:16 比例、悬停播放、点击展开），新增 APP/Ecom 分类 Tab。
- **成果**：28 个模板视频以直观格网展示，用户选模板效率大幅提升。
### [#2611](https://github.com/Vispie-AI/VisPie_backend/pull/2611) feat(studio): creator avatar badges on format cards
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：Studio 格式卡片缺乏创作者身份信息，品牌用户无法直观了解内容创作方背景。
- **修复**：为 `StudioFormat` 类型添加 `creator` 字段，在格式卡片上展示头像（20px）、姓名和专长标签。
- **成果**：Vizzy（3位）和 Manus（2位）活动的格式卡片现已显示创作者信息，提升品牌信任感。

### [#2609](https://github.com/Vispie-AI/VisPie_backend/pull/2609) feat(scripts): weekly campaign performance report -> Lark Sheet
- **日期**：2026-04-29 | **状态**：🚫 已关闭
- **问题**：每周活动视频表现数据分散，缺乏自动化汇总并导出至飞书电子表格的工具。
- **修复**：新增 `scripts/weekly_campaign_report.py`，从 FastAPI 后端拉取活跃活动数据并按均值排序后写入飞书表格。
- **成果**：周报脚本已实现，但该 PR 关闭未合并，功能暂未上线。

### [#2607](https://github.com/Vispie-AI/VisPie_backend/pull/2607) feat(studio): Vizzy Experiment — 3 Pixar animated ad formats
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：Studio 缺乏 Pixar 风格 AI 动画广告创意，无法满足 DTC 品牌对动画内容形式的需求。
- **修复**：新增 Vizzy 活动，基于爬取的 6 个参考视频合成 3 种 Pixar 风格动画广告格式。
- **成果**：Studio 上线 Vizzy 实验性活动，为品牌提供三种差异化的 AI 动画广告模板。

### [#2606](https://github.com/Vispie-AI/VisPie_backend/pull/2606) feat(nanobot): CardKit streaming heartbeat — 1s timer tick
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：Nanobot 心跳每 4 秒更新一次，用户无法及时感知机器人存活状态，体验差。
- **修复**：将心跳从 4s PATCH 消息改为每秒一次的 CardKit PUT，计时器逐秒递增显示。
- **成果**：用户界面计数器每秒滚动（1s→2s→3s...），可立即发现机器人卡死，提升实时感知能力。

### [#2599](https://github.com/Vispie-AI/VisPie_backend/pull/2599) feat(skills): add ui-alignment skill for designer workflow
- **日期**：2026-04-29 | **状态**：🚫 已关闭
- **问题**：设计师与 Coder Army 之间缺少标准化的 UI 对齐协作流程，Amy 无标准桥接规范。
- **修复**：新增 ui-alignment skill，定义分诊→定位→执行→验证四阶段设计师工作流规范。
- **成果**：该 PR 关闭未合并，功能暂未上线，后续可参考该方案重新实现。
### [#2542](https://github.com/Vispie-AI/VisPie_backend/pull/2542) feat(flows): Prefect deployment for matrix creator mining
- **日期**：2026-04-27 | **状态**：🚫 已关闭
- **问题**：matrix creator mining pipeline 缺乏 Prefect Cloud 调度部署能力，无法接入 my-ec2-pool 工作池。
- **修复**：添加 @flow 装饰器和 deploy.py 脚本，支持 Prefect Cloud 部署到 my-ec2-pool。
- **成果**：pipeline 可通过 Prefect Cloud 触发和调度，实现 EC2 工作池的自动化运行。

### [#2531](https://github.com/Vispie-AI/VisPie_backend/pull/2531) feat(ci): smoke + static checks prevent Dockerfile/imports drift (PR #2526 regression guard)
- **日期**：2026-04-27 | **状态**：✅ 已合并
- **问题**：PR #2526 修复了 Dockerfile.autoamy 遗漏 COPY 导致的 crashloop，但没有机制阻止同类问题再次发生。
- **修复**：新增静态检查脚本（check_dockerfile_imports.py）和 post-build smoke find-spec 测试，覆盖三个 CI 流程。
- **成果**：14 个测试通过，镜像在 smoke 失败时不推送，Dockerfile 与 imports 漂移可在构建前被发现。

### [#2529](https://github.com/Vispie-AI/VisPie_backend/pull/2529) feat(matrix-mining): v7 pipeline at flows/matrix_creator_mining/
- **日期**：2026-04-27 | **状态**：✅ 已合并
- **问题**：v5/v6 矩阵创作者识别流水线分别因低召回率（5%）和高误判率（22% FP）而失败，需要基于视频内容的新方案。
- **修复**：实现 5 阶段端到端流水线：DuckDB 候选提取→TikHub 异步抓取→Gemini Flash Lite 视频分类→IG 交叉查询→Lark 输出，全阶段可断点续跑。
- **成果**：5 个手动测试用例方向全部正确，预计成本约 $1.30/千账号，可产出实际矩阵账号数据。

### [#2523](https://github.com/Vispie-AI/VisPie_backend/pull/2523) feat(studio): Video Format AI Generate Studio
- **日期**：2026-04-27 | **状态**：✅ 已合并
- **问题**：平台缺乏面向创作者的视频格式 AI 生成工作台，无法满足品牌投放策略制定需求。
- **修复**：新增 /admin/format-generate-studio 页面，三栏布局支持格式选择、Brief/镜头方案生成和 Agent 执行日志，纯前端演示零后端调用。
- **成果**：格式工作室页面完整呈现，侧边栏新增入口，为 Higgsfield/Glif studio 级 UX 提供原型基础。

### [#2522](https://github.com/Vispie-AI/VisPie_backend/pull/2522) feat(amy): DeepSeek shadow v2 — full sandboxed AgentLoop with tools
- **日期**：2026-04-26 | **状态**：✅ 已合并
- **问题**：v1 的 DeepSeek shadow 是单轮对话且无工具调用，与 Bedrock AgentLoop 的对比不公平。
- **修复**：v2 为 DeepSeek 提供与 Bedrock 相同工具集的沙箱 AgentLoop，使用独立临时目录和独立 MessageBus 屏蔽对外输出。
- **成果**：82 个测试通过，DeepSeek shadow 现可进行多轮工具调用，费用自动计入每日/周/月报告。
### [#2487](https://github.com/Vispie-AI/VisPie_backend/pull/2487) [DO NOT MERGE] feat(amy): ChannelAdapter Protocol
- **日期**：2026-04-26 | **状态**：✅ 已合并
- **问题**：Amy 各渠道（IM、文档评论、Slack 等）处理逻辑分散，缺乏统一的渠道适配抽象层。
- **修复**：实现 ChannelAdapter 协议作为路线图文档，将渠道适配抽象为统一接口，代码已测试但未接入生产。
- **成果**：渠道适配协议设计完成，为后续多渠道统一接入奠定架构基础。

### [#2484](https://github.com/Vispie-AI/VisPie_backend/pull/2484) feat(twilio-batch): personalize batch SMS with {first_name}/{name} placeholders
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：批量短信发送使用统一模板内容，无法对每位接收者进行个性化内容替换。
- **修复**：在 /send-batch 接口中增加可选占位符替换功能，支持 {first_name}/{name} 变量。
- **成果**：批量短信现支持每位接收者个性化内容，提升创作者触达效果。

### [#2483](https://github.com/Vispie-AI/VisPie_backend/pull/2483) feat(amy): real Drive comment reactions (IM-OnIt parity, Lark v2 API)
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：Amy 在飞书云文档评论中使用占位回复方案，无法与 IM OnIt 反应行为真正统一。
- **修复**：接入飞书 Drive Comments v2 反应 API，将占位内容改为真实 Typing 反应，与 IM OnIt 行为完全对齐。
- **成果**：Amy 在文档评论和 IM 中的交互体验高度一致，用户可看到真实的处理状态反馈。

### [#2482](https://github.com/Vispie-AI/VisPie_backend/pull/2482) feat(amy): real Drive comment reactions (IM-OnIt parity, Lark v2 API)
- **日期**：2026-04-25 | **状态**：🚫 已关闭
- **问题**：#2480 的占位回复方案无法与 IM 的 OnIt 反应真正对齐，需升级为 v2 反应 API。
- **修复**：尝试接入 Drive Comments v2 API 进行真实反应，后被 rebase 版本 #2483 取代关闭。
- **成果**：此 PR 已由 #2483 替代，功能已在 #2483 中正式落地。

### [#2480](https://github.com/Vispie-AI/VisPie_backend/pull/2480) feat(amy): IM-style OnIt → done unification for Lark doc comments
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：用户在飞书云文档评论中 @-提及 Amy 时缺少即时处理状态反馈，体验割裂。
- **修复**：Amy 收到评论 @-提及后立即更新占位回复为"👀 thinking..."，完成后再更新为最终答复。
- **成果**：飞书文档评论中的 Amy 交互体验与 IM OnIt→done 模式对齐，用户有即时响应感知。

### [#2479](https://github.com/Vispie-AI/VisPie_backend/pull/2479) feat(amy): IM-style OnIt → done unification for Lark doc comments
- **日期**：2026-04-25 | **状态**：🚫 已关闭
- **问题**：Amy 在飞书文档评论中缺乏即时反馈机制，与 IM 的 OnIt→done 体验不一致。
- **修复**：尝试实现评论 OnIt→done 统一模式，后被更完善的 #2480 替代而关闭。
- **成果**：此 PR 已被 #2480 替代关闭，功能已在 #2480 中正式落地。

### [#2476](https://github.com/Vispie-AI/VisPie_backend/pull/2476) feat(amy): route doc comments through Hatchet pipeline (Plan B)
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：Amy 在飞书云文档评论中仅能发送静态占位内容，无法使用 nanobot 完整工具能力。
- **修复**：将文档评论 @-提及事件接入 Hatchet 管道（Plan B），与群聊 IM 使用相同的 nanobot agent 循环。
- **成果**：Amy 可在飞书文档评论中使用完整工具集（网络搜索、文档更新等）进行智能回复。

### [#2475](https://github.com/Vispie-AI/VisPie_backend/pull/2475) feat(metrics): add ctx_loss event — set the mirror before fixing context loss
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：缺少对 nanobot agent 上下文丢失情况的量化监控，无法定位高频丢失的会话。
- **修复**：新增 ctx_loss 事件类型，通过 4 个高精度正则检测 agent 最终回复，并在每日 Lark 卡片中展示统计。
- **成果**：可量化追踪上下文丢失频率、各 agent 分布及热点会话，为修复工作提供数据基础。

### [#2473](https://github.com/Vispie-AI/VisPie_backend/pull/2473) feat(daily-brief): multi-user system — 18 personalized DM cards
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：每日晨报 agent 仅向 George 一人发送日程简报，无法覆盖多位团队成员。
- **修复**：重构晨报流程为多用户系统，新增 recipients.py 支持 18 位接收者，分两档推送个性化内容。
- **成果**：18 位团队成员每日收到包含日历和 @提及等个性化内容的晨报卡片。

### [#2467](https://github.com/Vispie-AI/VisPie_backend/pull/2467) feat(amy): wire comment replies through nanobot agent (process_direct)
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：Amy 对飞书文档评论仅回复静态占位内容，未接入 nanobot agent 进行智能处理。
- **修复**：将文档评论事件路由至 process_direct，使用完整 nanobot agent 循环（Bedrock Claude + 全工具注册表 + 线程记忆）处理。
- **成果**：Amy 首次在文档评论中使用 nanobot 完整能力进行智能回复（后由 Plan B #2476 优化替代）。

### [#2466](https://github.com/Vispie-AI/VisPie_backend/pull/2466) feat(nanobot): add /compact slash command
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：nanobot 上下文压缩仅在自动触发时执行，用户无法主动触发压缩管理会话长度。
- **修复**：新增 /compact 斜杠命令，手动触发完整压缩流程（卸载→裁剪→截断→DeepSeek 索引→丢弃）。
- **成果**：用户可主动执行会话压缩，压缩前后 token 数量以卡片形式展示。

### [#2465](https://github.com/Vispie-AI/VisPie_backend/pull/2465) feat(nanobot): memory overhaul — DeepSeek extraction, group memory, MEMORY.md
- **日期**：2026-04-25 | **状态**：✅ 已合并
- **问题**：nanobot 记忆系统使用高成本模型生成压缩索引，群聊记忆未持久化，个人记忆未注入上下文。
- **修复**：改用 DeepSeek V4 Pro（降低 20 倍成本）；持久化至 history.jsonl 和 Insforge；启用群聊记忆提取；MEMORY.md 注入系统上下文。
- **成果**：记忆系统全面升级，成本大幅降低，群聊和个人记忆均可持久化并注入 nanobot 上下文。
### [#2447](https://github.com/Vispie-AI/VisPie_backend/pull/2447) feat: DEEP RESEARCH: PostHog + Stripe Integration Technical Feasib
- **日期**：2026-04-26 | **状态**：🚫 已关闭
- **问题**：需要评估 PostHog 与 Stripe 集成的技术可行性以支持产品决策。
- **修复**：开展深度研究，输出 PostHog + Stripe 集成技术可行性分析报告。
- **成果**：为团队提供集成路径参考，本 PR 未正式合入代码库。

### [#2434](https://github.com/Vispie-AI/VisPie_backend/pull/2434) feat: DEEP RESEARCH TASK — Do NOT modify any existing source code.
- **日期**：2026-04-24 | **状态**：🔀 待合并
- **问题**：需要进行深度研究任务，明确要求不涉及现有源代码修改。
- **修复**：创建独立研究分支，仅新增研究文档，不改动任何现有代码。
- **成果**：研究任务在隔离环境中进行，保证主干代码安全，待审核。

### [#2433](https://github.com/Vispie-AI/VisPie_backend/pull/2433) feat(coder-army): rich summary in hooks + callback
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：coder-army 的 hooks 和回调缺乏丰富的任务摘要信息，难以追踪执行状态。
- **修复**：在 hooks 和 callback 中添加结构化的富文本摘要内容。
- **成果**：任务执行后可获取详细摘要，便于监控和问题排查。

### [#2432](https://github.com/Vispie-AI/VisPie_backend/pull/2432) feat(dm): Message Brand card + file attachments + URL linkification
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：私信功能缺少品牌卡片、文件附件展示及 URL 自动链接化支持。
- **修复**：为 DM 模块新增 Brand 卡片展示、文件附件支持及 URL 自动转链功能。
- **成果**：私信消息展示更丰富，用户体验显著提升。

### [#2430](https://github.com/Vispie-AI/VisPie_backend/pull/2430) feat(coder-army): agent completion hooks — self-report done
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：coder-army agent 任务完成时缺乏主动上报机制，依赖外部轮询检测。
- **修复**：为 agent 添加任务完成 hooks，实现自主上报完成状态。
- **成果**：agent 任务完成后可及时通知系统，降低轮询开销，响应更及时。

### [#2426](https://github.com/Vispie-AI/VisPie_backend/pull/2426) feat(coder-army): switch OpenCode to DeepSeek v4 Pro
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：coder-army 使用的 OpenCode 模型能力不足，需升级至更强推理模型。
- **修复**：将 coder-army 的底层模型从 OpenCode 切换为 DeepSeek v4 Pro。
- **成果**：编程辅助能力显著增强，任务处理质量和速度均有提升。
### [#2417](https://github.com/Vispie-AI/VisPie_backend/pull/2417) feat(vio-ci): pre-merge regression coverage + size guard + SSOT docs
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：缺乏预合并回归测试和文件体积守护，难以在合并前拦截破坏性变更。
- **修复**：新增预合并 CI 覆盖、体积守护和单一真实来源文档。
- **成果**：Vio 部署前可自动检测回归问题，风险防控能力显著增强。

### [#2409](https://github.com/Vispie-AI/VisPie_backend/pull/2409) feat/codex final test 20260424
- **日期**：2026-04-23 | **状态**：🚫 已关闭
- **问题**：需要验证 Codex 代理的网络访问和推送能力是否正常。
- **修复**：添加最终验证文档及多项 Codex 能力测试用例。
- **成果**：Codex 代理功能验证完成，网络访问和推送操作均正常。

### [#2408](https://github.com/Vispie-AI/VisPie_backend/pull/2408) feat(george-ai): add GH_TOKEN for GitHub operations
- **日期**：2026-04-23 | **状态**：✅ 已合并
- **问题**：George AI 容器缺少 GitHub Token，无法执行 PR 验证等 GitHub 操作。
- **修复**：在 compose 配置中从 .env 文件读取 GH_TOKEN 和 GITHUB_TOKEN 环境变量。
- **成果**：George AI 具备 GitHub 操作权限，Coder Army PR 验证流程完整。

### [#2405](https://github.com/Vispie-AI/VisPie_backend/pull/2405) feat(daily-agenda): anti-hallucination + all groups + Slack digest
- **日期**：2026-04-23 | **状态**：✅ 已合并
- **问题**：Gemini 在无文档时编造行动项，Lark 群组过滤导致部分群消息被遗漏。
- **修复**：无文档时仅展示时间和标题，移除最小消息数过滤，优化 Slack 摘要格式。
- **成果**：日程播报内容准确，所有 Lark 群组消息均被纳入，Slack 摘要完整呈现。

### [#2404](https://github.com/Vispie-AI/VisPie_backend/pull/2404) feat(coder-army): system-level auto-push + PR after agent completes
- **日期**：2026-04-23 | **状态**：✅ 已合并
- **问题**：代理完成任务后需手动执行推送和 PR 创建，自动化链路存在断点。
- **修复**：在系统层新增 `_auto_push_and_pr()` 函数，任务完成后自动提交推送并创建 PR。
- **成果**：三类代理均支持全自动推送，人工干预大幅减少。

### [#2403](https://github.com/Vispie-AI/VisPie_backend/pull/2403) feat: Create file docs/coder-army-test/opencode-v3.md
- **日期**：2026-04-23 | **状态**：🚫 已关闭
- **问题**：需要验证 OpenCode 代理通过 Coder Army 自动创建文件的能力。
- **修复**：通过 Coder Army 自动生成并提交 opencode-v3.md 测试文档。
- **成果**：OpenCode 代理文件创建能力得到验证，自动化流程可行。

### [#2402](https://github.com/Vispie-AI/VisPie_backend/pull/2402) feat: Create file docs/coder-army-test/claude-v3.md
- **日期**：2026-04-23 | **状态**：🚫 已关闭
- **问题**：需要验证 Claude 代理通过 Coder Army 自动创建文件的能力。
- **修复**：通过 Coder Army 自动生成并提交 claude-v3.md 测试文档。
- **成果**：Claude 代理文件创建能力得到验证，自动化流程可行性确认。

### [#2395](https://github.com/Vispie-AI/VisPie_backend/pull/2395) feat(monitoring): extend liveness monitoring to Vio (dynamic + rollup)
- **日期**：2026-04-23 | **状态**：✅ 已合并
- **问题**：存活监控仅覆盖 Amy 服务，Vio 多租户平台（22+ 容器）缺乏监控保障。
- **修复**：将存活监控扩展至 Vio，新增 vio-gateway 及所有租户容器的动态检测。
- **成果**：Amy 和 Vio 均纳入统一存活监控，多服务健康状态汇总可见。
### [#2379](https://github.com/Vispie-AI/VisPie_backend/pull/2379) feat(auto-amy): track Gemini + Nova Micro + embedding costs
- **日期**：2026-04-22 | **状态**：✅ 已合并
- **问题**：Bedrock已追踪费用，但Gemini、Nova Micro、嵌入模型三条调用路径的成本未统计。
- **修复**：为Gemini视频分析、Nova Micro视频理解及Titan嵌入模型分别接入费用追踪。
- **成果**：LLM全链路成本实现完整监控，不再存在未追踪的模型调用盲区。

### [#2374](https://github.com/Vispie-AI/VisPie_backend/pull/2374) feat(auto-amy): OCR kill switch + concurrency limit + algorithm versioning
- **日期**：2026-04-22 | **状态**：✅ 已合并
- **问题**：OCR并发ffmpeg进程过多导致768MB容器OOM，且无法在不重新部署的情况下关闭OCR功能。
- **修复**：新增OCR_ENABLED开关、OCR_MAX_CONCURRENT并发上限及OCR_ALGO算法版本控制环境变量。
- **成果**：OCR功能可热切换，并发资源消耗可控，算法版本可追溯管理。

### [#2371](https://github.com/Vispie-AI/VisPie_backend/pull/2371) feat(mobile-app-web): design draft — messages2 + broadcast modal
- **日期**：2026-04-22 | **状态**：🔀 待合并
- **问题**：管理员消息页面需要重新设计，并增加向创作者群发消息的功能入口。
- **修复**：在/admin/messages2路由下构建沙盒页面，新增群发创作者弹窗草稿（仅Demo壳）。
- **成果**：消息页面改版设计稿上线沙盒路由，为后续正式开发提供视觉参考。

### [#2367](https://github.com/Vispie-AI/VisPie_backend/pull/2367) feat(auto-amy): GLM-OCR dual-signal C7 arbitration for video review
- **日期**：2026-04-22 | **状态**：✅ 已合并
- **问题**：GLM-OCR错字检测结果作为文本提示注入但未影响最终审核判定结论。
- **修复**：将OCR信号整合进C7仲裁决策逻辑，实现双信号驱动的视频审核裁决。
- **成果**：OCR检测结果正式参与审核裁决，视频合规判定准确性得到提升。

### [#2366](https://github.com/Vispie-AI/VisPie_backend/pull/2366) feat(video-gen): Seedance 2.0 skill + ARK_API_KEY deploy
- **日期**：2026-04-22 | **状态**：🔀 待合并
- **问题**：Amy和George缺少通过火山引擎Ark API调用Seedance 2.0生成视频的共享技能规范。
- **修复**：新增project_amy/shared/skills/video-gen/SKILL.md，涵盖文生视频和图生视频场景。
- **成果**：Amy和George均可使用Seedance 2.0/2.0 Fast进行视频生成，技能文档统一管理。

### [#2365](https://github.com/Vispie-AI/VisPie_backend/pull/2365) feat(scripts): Claude Code session analytics
- **日期**：2026-04-22 | **状态**：✅ 已合并
- **问题**：团队在EC2上的Claude Code会话缺乏token用量、成本和工具使用的可视化分析能力。
- **修复**：新增两个Python分析脚本，支持token消耗、成本明细、工具分布及每日活跃度统计。
- **成果**：团队可快速洞察Claude Code使用情况，为资源优化和成本控制提供数据支撑。

### [#2364](https://github.com/Vispie-AI/VisPie_backend/pull/2364) feat(cc-army): archive sessions before slot cleanup
- **日期**：2026-04-22 | **状态**：✅ 已合并
- **问题**：Agent槽位被复用或删除时历史会话数据丢失，无法追溯。
- **修复**：新增cc_army_sessions归档表，delete_agent()在级联删除前自动调用archive_agent()保存历史。
- **成果**：Agent会话历史得到持久化保存，槽位清理不再导致数据丢失。

### [#2358](https://github.com/Vispie-AI/VisPie_backend/pull/2358) feat(creator-submission-tracker): implement daily sync to Lark Bitable
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：creator-submission-tracker仅有SKILL.md规范文档，缺少实际同步脚本实现。
- **修复**：实现sync.js脚本，从数据库拉取所有创作者投稿数据并同步至Lark Bitable。
- **成果**：创作者投稿数据每日自动同步至飞书多维表格，完成规范到实现的落地。
### [#2357](https://github.com/Vispie-AI/VisPie_backend/pull/2357) feat(revenue-tracker): persistent cron daemon for Campaign Pricing sync
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：Campaign Pricing 同步任务依赖 nanobot 内存 cron，网关重启后任务丢失，导致表格连续 4 天未更新。
- **修复**：新增持久化 cron-daemon.sh 脚本，在后台循环调度定价同步任务，与进程生命周期解耦。
- **成果**：定价同步任务在网关重启后自动恢复，Campaign Pricing 数据持续按时更新。

### [#2355](https://github.com/Vispie-AI/VisPie_backend/pull/2355) feat(auto-amy): daily API cost report → Lark webhook
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：Amy 成本追踪管道已具备数据采集能力，但缺乏自动汇总与推送机制，团队无法及时感知 API 费用。
- **修复**：新增定时任务聚合 usage_events 中过去 24h 及 7d 数据，渲染为 Lark 卡片并推送至运营 webhook。
- **成果**：团队每日自动收到全舰队 API 成本报告，成本追踪反馈闭环正式形成。

### [#2354](https://github.com/Vispie-AI/VisPie_backend/pull/2354) feat(auto-amy): extend UsageMeter to full fleet (13 agents + amy + eva)
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：Mitchell 成本追踪验证通过后，其余 13 个 fleet agent 及 amy、eva 仍未接入 UsageMeter。
- **修复**：将相同的 LLM 调用采集管道扩展至全部 fleet agent，统一上报至 usage_events 表。
- **成果**：全舰队所有 LLM 调用均被记录，为每日成本报告提供完整数据来源。

### [#2351](https://github.com/Vispie-AI/VisPie_backend/pull/2351) feat(auto-amy): wire Mitchell Bedrock calls into UsageMeter
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：UsageMeter、usage_events schema 及 POST 事件接口均已就绪，但 Mitchell 的 Bedrock 调用从未向其发送事件。
- **修复**：在 Mitchell 的 Bedrock 调用路径中接入 UsageMeter，实现 LLM 用量事件的自动上报。
- **成果**：Mitchell 生产环境 LLM 调用开始产生 usage_events 记录，成本追踪管道正式打通。

### [#2345](https://github.com/Vispie-AI/VisPie_backend/pull/2345) feat(messages): fix campaign_agreements + regen types + rate limit
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：#2344 引用了不存在的 campaign_members 表，且缺少正确的类型定义和接口限流保护。
- **修复**：改用正确的 campaign_agreements 表，重新生成 Supabase 类型，并为广播接口添加速率限制。
- **成果**：广播后端可在生产环境正常运行，数据层引用正确，接口具备基础防刷保护。

### [#2344](https://github.com/Vispie-AI/VisPie_backend/pull/2344) feat(messages): backend for New DM & Broadcasting (brand + admin)
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：品牌/管理员消息页面的新建私信与广播功能缺乏后端支撑，无法按角色发现创作者或批量发送消息。
- **修复**：新增 POST /api/creators/search 创作者搜索端点及广播发送 API，支持品牌与管理员的角色隔离。
- **成果**：新建私信与广播功能后端接口就绪，可支撑前端模态框流程的接入。

### [#2336](https://github.com/Vispie-AI/VisPie_backend/pull/2336) feat: dashboard dynamic AI review + Supabase rules backup (P1+P2)
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：Dashboard 客户页面无法动态感知 Auto-Amy AI 审核状态，视频审核规则缺乏数据库级持久化备份。
- **修复**：P1 接入动态 AI 审核状态检测；P2 在每次 Step 4 运行时将规则备份至 campaigns.metadata。
- **成果**：客户页面实时反映 AI 审核结果，规则文件同时在数据库中保有备份，避免单点丢失。
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

### [#2723](https://github.com/Vispie-AI/VisPie_backend/pull/2723) Vizzyshubh
- **日期**：2026-05-01 | **状态**：🔀 待合并
- **问题**：Vizzyshubh相关功能模块需要合入主分支。
- **修复**：包含82个文件变更，合计新增16705行代码，涵盖多项功能改动。
- **成果**：PR待审核合并，具体功能范围待评审确认。

### [#2719](https://github.com/Vispie-AI/VisPie_backend/pull/2719) Prefer Cloud Run backend for format studio
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：Format Studio默认使用cc-army-api，但该服务仍返回旧版Seedance缩略图响应格式。
- **修复**：将Format Studio后端优先级调整为Cloud Run FastAPI，cc-army-api作为降级备用。
- **成果**：Format Studio正常获取最新Seedance thumbnail_url响应，功能恢复正常。

### [#2718](https://github.com/Vispie-AI/VisPie_backend/pull/2718) docs: abandoned revisions detail audit
- **日期**：2026-04-30 | **状态**：🔀 待合并
- **问题**：需了解217条废弃修订事件的分布及各活动的放弃率，发现数据质量缺口。
- **修复**：从Supabase拉取数据生成详细分析报告，标记10处数据质量问题并列出高放弃率活动。
- **成果**：Fanka（67%）、StudyX（83%）等活动的异常高放弃率得到量化，为干预提供依据。

### [#2717](https://github.com/Vispie-AI/VisPie_backend/pull/2717) Use generated thumbnails for Seedance videos
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：Seedance生成视频使用模板缩略图而非实际生成内容首帧。
- **修复**：提取生成视频首帧并上传为JPEG缩略图，API响应和前端均使用生成缩略图。
- **成果**：Seedance视频缩略图展示真实生成内容，点击后可在播放弹窗中查看完整视频。

### [#2716](https://github.com/Vispie-AI/VisPie_backend/pull/2716) Fix trending formats loading in studio
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：Format Studio趋势格式页面在API成功返回后仍停留在加载中状态。
- **修复**：将SvelteKit导航replaceState替换为原生history.replaceState，仅在URL变化时调用。
- **成果**：趋势格式页面正常加载渲染6个格式卡片，运行时崩溃问题消除。

### [#2715](https://github.com/Vispie-AI/VisPie_backend/pull/2715) Fix format studio initial video loading
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：Format Studio页面挂载时为所有历史任务加载video src，导致初始加载缓慢。
- **修复**：改为懒加载生成视频，并将每个活动的历史任务初始显示数量从20减少到12。
- **成果**：Format Studio初始加载速度提升，不必要的视频预加载请求大幅减少。

### [#2714](https://github.com/Vispie-AI/VisPie_backend/pull/2714) docs: Apr 2026 revision turnaround analysis for brands platform
- **日期**：2026-04-30 | **状态**：🔀 待合并
- **问题**：需了解品牌平台2026年4月修订周转效率，并定位35%放弃率的根本原因。
- **修复**：分析411个修订周期，生成含P50/P90周转时间的分角色对比报告。
- **成果**：中位周转7.6小时，客户角色P90比管理员长4倍，建议建立客户SLA机制。

### [#2713](https://github.com/Vispie-AI/VisPie_backend/pull/2713) Reapply FastAPI cutover with login/deploy fixes
- **日期**：2026-04-30 | **状态**：🔀 待合并
- **问题**：生产回滚后需重新合入FastAPI切换，同时修复登录失败和部署阻塞问题。
- **修复**：修复SvelteKit响应头变更导致登录失败、Cloud Run启动探针超时及Alembic重复版本头等问题。
- **成果**：全链路冒烟测试通过（公共37/内部81/移动25），等待CI验证后可重新部署。

### [#2712](https://github.com/Vispie-AI/VisPie_backend/pull/2712) Add per-project ignore-build.sh for Vercel selective builds
- **日期**：2026-04-30 | **状态**：🔀 待合并
- **问题**：Vercel单体仓库在未变更的前端项目上也会触发构建，浪费构建资源。
- **修复**：为5个前端项目分别添加ignore-build.sh，利用VERCEL_GIT_PREVIOUS_SHA进行精准目录diff。
- **成果**：各前端项目仅在对应目录有变更时触发Vercel构建，资源消耗显著降低。

### [#2711](https://github.com/Vispie-AI/VisPie_backend/pull/2711) Rollback Django/FastAPI cutover
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：FastAPI部署上线后生产出现问题，需紧急回退到Django旧路径稳定服务。
- **修复**：回退PR #2709和#2708，将代码库恢复至切换前的提交4fdbde815。
- **成果**：生产恢复至Django后端，FastAPI修复工作已暂存，待稳定后重新推进。
### [#2710](https://github.com/Vispie-AI/VisPie_backend/pull/2710) Add Vercel ignored build step for monorepo selective builds
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：Monorepo 中 5 个前端项目每次推送均全量重建，浪费构建时间和资源。
- **修复**：新增共享脚本通过 git diff 判断变更范围，仅触发涉及子项目的 Vercel 构建。
- **成果**：只有变更项目触发构建，节省构建资源和部署时间。

### [#2709](https://github.com/Vispie-AI/VisPie_backend/pull/2709) Fix FastAPI deploy image packaging
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：FastAPI 服务部署时镜像打包存在缺陷导致部署流程失败。
- **修复**：修正 2 个配置文件中的镜像打包逻辑。
- **成果**：FastAPI 部署镜像构建恢复正常，持续交付流程畅通。

### [#2708](https://github.com/Vispie-AI/VisPie_backend/pull/2708) Migrate legacy Django paths to FastAPI compatibility layer
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：遗留 Django 路径与 FastAPI 并存，维护成本高且路由不统一。
- **修复**：批量将遗留 Django 路径迁移至 FastAPI 兼容层，涉及 283 个文件大规模重构。
- **成果**：路由体系统一至 FastAPI，为后续完全弃用 Django 奠定基础。

### [#2707](https://github.com/Vispie-AI/VisPie_backend/pull/2707) docs(vio-video-review): Phase 1 retrospective + 5 corrections
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：Vio 视频审核 Phase 1 上线过程中的诊断链和决策依据缺少系统性文档记录。
- **修复**：新增 232 行复盘文档，涵盖 5 层 Bug 洋葱、14 个 PR 时间线及 SOAKED 验收 SQL，修正 5 处偏差。
- **成果**：Phase 1 知识完整保留，Phase 2 可直接引用此文档作为交叉索引。

### [#2706](https://github.com/Vispie-AI/VisPie_backend/pull/2706) ops(creator-pool): pre-warm brand personas + ops follow-up report
- **日期**：2026-04-30 | **状态**：🔀 待合并
- **问题**：20 个活跃品牌均无缓存 persona，品牌主首次登录触发 Gemini 冷启动延迟高。
- **修复**：编写预热脚本调用生产接口为所有活跃品牌生成并缓存高质量 persona（置信度 0.90-0.95）。
- **成果**：20 个品牌完成预热，后续登录缓存命中延迟低于 100ms，识别 3 项后续优化项。

### [#2701](https://github.com/Vispie-AI/VisPie_backend/pull/2701) Fix brand message campaign scope
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：品牌消息 Campaign 下拉框缺少无会话的活动选项，广播未包含已联系创作者。
- **修复**：从品牌关联活动加载 Campaign 选项，广播搜索和校验纳入已联系创作者，管理员保持全平台权限。
- **成果**：Campaign 选项完整，品牌广播可覆盖全部相关创作者，7 个文件完成更新。

### [#2700](https://github.com/Vispie-AI/VisPie_backend/pull/2700) Fix admin campaign dashboard generation flow
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：管理员活动仪表板生成流程存在缺陷导致数据无法正常展示。
- **修复**：修正仪表板生成流程逻辑，涉及 5 个文件调整。
- **成果**：管理员活动仪表板恢复正常生成和展示。

### [#2698](https://github.com/Vispie-AI/VisPie_backend/pull/2698) Fallback admin broadcast creator counts
- **日期**：2026-04-30 | **状态**：🔀 待合并
- **问题**：#2696 代码先于 Supabase 迁移上线时，缺少新 RPC 导致广播模块 500 报错。
- **修复**：添加回退逻辑，缺少新 RPC 时通过分页旧接口获取精确总数和全量 Select All。
- **成果**：广播功能在迁移应用前后均可正常工作，消除部署时序引发的中断风险。

### [#2696](https://github.com/Vispie-AI/VisPie_backend/pull/2696) Fix broadcast select-all total count
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：广播模态框仅显示 "200+"，Select All 只能选中前 200 条，影响大规模广播。
- **修复**：新增精确计数接口，Select All 解析完整结果集（上限 500），Supabase 迁移将 RPC 上限提至 501。
- **成果**：广播可选中全量匹配创作者，总数展示精确，用户体验改善。

### [#2695](https://github.com/Vispie-AI/VisPie_backend/pull/2695) P0 hotfix(creator-pool): /default-feed reads wrong Supabase project
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：fetch_brand_from_supabase 读取了错误的 Supabase 项目，所有品牌 ID 返回 404，推荐流静默回退至未过滤 17K 列表。
- **修复**：新增 MOBILE_APP_WEB_SUPABASE_URL/KEY 环境变量指向正确项目，修正 industry 字段下划线格式化。
- **成果**：品牌登录后正确看到 AI 过滤推荐流和 persona 横幅，单元测试增至 33 个。

### [#2694](https://github.com/Vispie-AI/VisPie_backend/pull/2694) Lazy load broadcast recipients on scroll
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：广播收件人列表仅加载最多 200 条，无法展示更多创作者。
- **修复**：改为滚动触底时分页追加加载，通过 offset 传递页码，超出当前页显示 200+ 样式计数。
- **成果**：收件人列表支持无限滚动，Select All 和 500 上限行为保持不变。

### [#2693](https://github.com/Vispie-AI/VisPie_backend/pull/2693) ci: split shared deploy group — give Amy/Eva nanobot priority queue
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：4 个部署 workflow 共享并发组，Amy/Eva Nanobot 频繁被取消，最坏延迟达 13 分钟。
- **修复**：Amy/Eva Nanobot 单独划为优先并发组，其余迁入次级组，两组各自串行互不影响。
- **成果**：Amy/Eva Nanobot 部署不再被取消，用户侧延迟降至纯部署耗时。

### [#2691](https://github.com/Vispie-AI/VisPie_backend/pull/2691) Scope brand broadcasts to campaign action creators
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：品牌广播范围覆盖全平台创作者，而非仅限活动下有投稿行为的创作者。
- **修复**：品牌搜索和广播发送均限定为所属活动有 action 记录的创作者，管理员保持全平台权限。
- **成果**：品牌广播精准触达活动相关创作者，6 个文件确保前后端范围一致。

### [#2684](https://github.com/Vispie-AI/VisPie_backend/pull/2684) docs(research): client persona audit — Fanka case study
- **日期**：2026-04-29 | **状态**：🔀 待合并
- **问题**：缺乏系统化的客户 persona 数据来源梳理，Fanka 客户沟通主要依赖难以获取的微信记录。
- **修复**：汇总 Vio 管理 API、客户 brief、内部飞书群（313 条消息）等数据源，形成 Fanka 客户 persona 审计文档。
- **成果**：明确各数据来源覆盖范围与缺口（最大缺口为客户微信记录），并给出优先级补全建议。

### [#2678](https://github.com/Vispie-AI/VisPie_backend/pull/2678) Mobile App Web optimizations: sign-in tabs, editable fields, remove dashboards, reference videos
- **日期**：2026-04-29 | **状态**：🔀 待合并
- **问题**：登录页密码入口隐蔽，活动描述不可编辑，内容板块命名不直观，生成仪表盘按钮存在 bug。
- **修复**：密码登录改为对等标签页，活动描述支持内联编辑，内容板块更名为"参考视频"，移除有问题的仪表盘生成按钮。
- **成果**：移动端 Web 登录流程更顺畅，活动管理操作更直观，参考视频板块语义更清晰。

### [#2670](https://github.com/Vispie-AI/VisPie_backend/pull/2670) Show newest generations first
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：生成卡片列表顺序与 tasks API 返回的最新优先顺序不一致，新生成内容未能优先展示。
- **修复**：将新创建的生成卡片插入到结果行首位，与 tasks API 的最新优先排序对齐。
- **成果**：用户可立即在列表最左侧看到最新生成结果，浏览体验更直观。

### [#2669](https://github.com/Vispie-AI/VisPie_backend/pull/2669) Move pro creator badge to bottom-left
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Pro 模板创作者头像与名称位于中央遮罩层，遮挡模板内容视觉呈现。
- **修复**：将创作者标识移至左下角，保留右下角悬停时的 Recreate 按钮。
- **成果**：模板卡片布局更简洁，创作者信息与操作按钮各占合理位置，不干扰主视觉。

### [#2666](https://github.com/Vispie-AI/VisPie_backend/pull/2666) Polish format studio templates and Seedance resilience
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Format Studio 模板网格 UI 不够精致，Seedance 生成缺乏容错，部分模板内容和 brief 描述不完善。
- **修复**：重设计模板网格为沉浸式 Higgsfield 风格，增强 Seedance 超时重试与 Gemini 兜底，补充 APP/Ecom 模板集与 brief 说明。
- **成果**：模板浏览体验更专业，Seedance 生成稳定性提升，客户 brief 需求描述更清晰。
### [#2641](https://github.com/Vispie-AI/VisPie_backend/pull/2641) design: campaigns2 sandbox — Creator Auto-Reminder & Offboard banner
- **日期**：2026-04-29 | **状态**：🔀 待合并
- **问题**：campaigns2 缺少创作者超期自动提醒和下线横幅功能，品牌管理创作者效率低。
- **修复**：新增 /dashboard/campaigns2/[id] 沙盒页面，实现 Creator Reminder Banner 和 Offboard 横幅设计。
- **成果**：设计稿已提交待审，功能合入后可提升品牌对创作者的管理效率。

### [#2637](https://github.com/Vispie-AI/VisPie_backend/pull/2637) Write IG discovery debug outputs
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Instagram 发现流程输出为空时缺少可解释的调试信息，问题排查困难。
- **修复**：始终将所有发现的 IG 候选人写入 debug CSV/JSON 文件，并记录资质拒绝原因计数。
- **成果**：调试可观测性显著提升，空结果问题可快速定位根因。

### [#2635](https://github.com/Vispie-AI/VisPie_backend/pull/2635) Qualify IG ambassador discovery results
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Instagram 大使发现结果缺乏资质过滤，包含官方账号/剪辑号/无脸账号等低质候选人。
- **修复**：用 Instagram 个人主页数据对候选人进行富化，过滤非 UGC 账号，输出合格大使外联名单。
- **成果**：大使发现结果质量提升，外联名单更精准，节约人工筛选成本。

### [#2633](https://github.com/Vispie-AI/VisPie_backend/pull/2633) Add mobile-ui-alignment skill (Phase 1–4 workflow)
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Amy agent 处理 Vizzy 移动端视觉对齐请求时缺少标准化技能文档与工作流程规范。
- **修复**：新增 mobile-ui-alignment SKILL.md 技能文件，定义 Phase 1–4 四阶段移动端 UI 对齐工作流。
- **成果**：Amy agent 具备规范化移动端 UI 对齐能力，可安全处理相关 UX 请求。

### [#2632](https://github.com/Vispie-AI/VisPie_backend/pull/2632) Codex/creator overview next
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Creator Overview 功能需持续迭代演进，Codex 集成待推进。
- **修复**：完成 Creator Overview 与 Codex 相关的下一步功能开发与集成工作。
- **成果**：Creator Overview 功能持续演进，Codex 集成推进落地。

### [#2628](https://github.com/Vispie-AI/VisPie_backend/pull/2628) Dev UI
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：开发界面需要补充 Stripe 无效账户检测等 UI 功能以支持支付异常处理。
- **修复**：实现 Stripe 无效账户检测及相关开发界面改进。
- **成果**：开发 UI 功能完善，支付相关异常处理能力增强。
### [#2612](https://github.com/Vispie-AI/VisPie_backend/pull/2612) chore(lark): weekly campaign report writer for Apr 21-27
- **日期**：2026-04-28 | **状态**：🔀 待合并
- **问题**：4 月 21-27 日活动周报数据需要自动写入飞书电子表格指定 Tab。
- **修复**：新增 Node.js 脚本 `scripts/lark/update_weekly_campaign_sheet.js`，拉取各活动指标并写入对应飞书表格。
- **成果**：周报自动化脚本已就绪，PR 待合并后可直接执行本周周报填写任务。

### [#2608](https://github.com/Vispie-AI/VisPie_backend/pull/2608) refactor(studio): remove Campaign Brief section
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：Studio 的 Campaign Brief / Game Plan 模块质量低下，影响整体产品体验。
- **修复**：完全移除 Campaign Brief 模块相关代码（导入、状态、UI 和样式），仅保留趋势视频格式网格。
- **成果**：Studio 页面结构更加简洁，专注展示高质量趋势格式卡片内容。

### [#2603](https://github.com/Vispie-AI/VisPie_backend/pull/2603) Handle IG discovery Lark output failures
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：IG 优先发现流水线在飞书 Bitable 输出失败时直接崩溃，且错误信息不可操作。
- **修复**：捕获飞书输出异常并记录 HTTP 响应详情，即使飞书失败也保留本地 JSON/CSV 产物路径。
- **成果**：流水线不再因飞书输出故障中断，同时输出更清晰的错误日志便于排查。

### [#2602](https://github.com/Vispie-AI/VisPie_backend/pull/2602) Add Instagram-first matrix discovery pipeline
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：现有矩阵挖掘依赖 TikTok 数据源，缺乏以 Instagram 为入口的品牌大使发现能力。
- **修复**：新增 IG 优先矩阵发现流水线，通过 Instagram RapidAPI 标签/帖子搜索从品牌种子发现大使账号。
- **成果**：矩阵发现流水线新增 Instagram 首选路径，扩大跨平台创作者覆盖范围。

### [#2601](https://github.com/Vispie-AI/VisPie_backend/pull/2601) Throttle matrix Stage 2 TikHub requests
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：矩阵挖掘 Stage 2 的 TikHub 请求并发过高，频繁触发 429 限速且无 Retry-After 处理。
- **修复**：添加共享请求节流（3并发/2rps），支持读取 `Retry-After` 头，设 30s 全局 429 冷却时间。
- **成果**：Stage 2 TikHub 请求稳定可控，429 错误率大幅降低，并发控制已有单元测试覆盖。

### [#2600](https://github.com/Vispie-AI/VisPie_backend/pull/2600) Use user-scoped matrix mining checkpoint path
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：矩阵挖掘检查点默认写入 `/tmp/matrix_mining_checkpoint.json`，多用户环境存在路径冲突风险。
- **修复**：改为用户作用域目录，`run_ec2.sh` 在启动前自动创建用户专属检查点和工作目录。
- **成果**：多用户 EC2 环境下检查点路径隔离，避免并发挖掘任务互相覆盖数据。

### [#2597](https://github.com/Vispie-AI/VisPie_backend/pull/2597) Run matrix creator mining directly on EC2
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：矩阵创作者挖掘流水线依赖 Prefect 调度框架，增加了 EC2 直接运行的复杂度。
- **修复**：将流水线改为纯 Python 实现，移除 Prefect 装饰器，新增 `run_ec2.sh` 设置 EC2 默认参数。
- **成果**：矩阵挖掘可直接在 EC2 上运行，无需 Prefect 依赖，部署链路更简洁高效。

### [#2596](https://github.com/Vispie-AI/VisPie_backend/pull/2596) Use local source for matrix mining deployment
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：矩阵挖掘部署默认从 GitHub 克隆代码，在无持久 PAT 的 EC2 环境中无法正常使用。
- **修复**：将默认源改为 EC2 本地仓库路径，保留 `MATRIX_MINING_SOURCE_MODE=github` 作为备选模式。
- **成果**：矩阵挖掘部署不再强依赖 GitHub 克隆，本地代码路径成为默认可靠来源。

### [#2595](https://github.com/Vispie-AI/VisPie_backend/pull/2595) Use default local DuckDB path at runtime
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：未设置 `STAGE1_DUCKDB_PATH` 时 Stage 1 无法自动回退到本地 DuckDB 文件路径。
- **修复**：添加运行时回退逻辑，缺少环境变量时默认使用 `/tmp/sourcing_creators.duckdb`。
- **成果**：Stage 1 在标准 EC2 环境中无需额外配置即可启动，降低部署门槛。

### [#2594](https://github.com/Vispie-AI/VisPie_backend/pull/2594) Guard matrix mining Stage 1 local DuckDB usage
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：矩阵挖掘 Stage 1 缺少 `STAGE1_DUCKDB_PATH` 时会静默回退远程 Postgres，存在意外全表扫描风险。
- **修复**：部署路径默认使用 EC2 本地 DuckDB，完整 Stage 1 运行若无该变量则快速失败报错。
- **成果**：防止意外触发远程 Postgres 全量扫描，Stage 1 本地 DuckDB 使用更加可控安全。

### [#2593](https://github.com/Vispie-AI/VisPie_backend/pull/2593) Optimize creator overview loading
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：创作者概览页面加载性能不足，影响用户等待体验。
- **修复**：优化创作者概览数据加载逻辑，减少不必要的数据处理开销。
- **成果**：创作者概览页面加载速度提升，用户访问体验得到改善。

### [#2590](https://github.com/Vispie-AI/VisPie_backend/pull/2590) Codex/creator overview followup
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：Codex 生成的创作者概览功能存在需要跟进修复的遗留问题。
- **修复**：针对创作者概览 Codex 实现进行跟进修复和完善调整。
- **成果**：创作者概览功能在 Codex 方案基础上进一步稳定，可靠性提升。

### [#2589](https://github.com/Vispie-AI/VisPie_backend/pull/2589) test(pr-bot): smoke — verify PR group commit-cards
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：需要验证 #2587 引入的 PR 分组提交卡片机器人功能是否正确运行。
- **修复**：添加仅含 `docs/changelog/` 文件变更的烟雾测试 PR，触发机器人验证流程。
- **成果**：PR 机器人功能验证通过，零代码变更确认新机器人行为符合预期。

### [#2588](https://github.com/Vispie-AI/VisPie_backend/pull/2588) Default creator card videos to date sort
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：创作者卡片视频列表默认排序方式不符合用户浏览习惯，影响内容发现体验。
- **修复**：将创作者卡片视频列表默认排序改为按日期降序排列。
- **成果**：用户浏览创作者卡片时默认看到最新视频，内容时效性得到保障。
### [#2534](https://github.com/Vispie-AI/VisPie_backend/pull/2534) docs(amy): roadmap — Lark token-retry decorator (Plan B, follow-up to #2533)
- **日期**：2026-04-27 | **状态**：🔀 待合并
- **问题**：#2533 在 client.py 和 comments.py 间引入了多处重复的 token 重试代码，缺乏统一抽象。
- **修复**：新增架构设计文档，规划 `with_token_retry` 装饰器的迁移路径、两种实现方案及验收标准，不含代码变更。
- **成果**：提供清晰的后续重构路线图，与 #2533 风险隔离，团队任何成员可按文档实施。

### [#2528](https://github.com/Vispie-AI/VisPie_backend/pull/2528) docs(matrix-mining): v7 spec + lessons + golden set + pipeline guide
- **日期**：2026-04-27 | **状态**：✅ 已合并
- **问题**：v5/v6 流水线的失败经验、80 个手动验证账号的黄金集及 v7 视频分类方案缺乏文档化，团队协作交接困难。
- **修复**：新增 docs/features/matrix-creator-mining/ 目录，包含需求、教训、实施指南、黄金集和验证脚本共 6 个文档。
- **成果**：为 Yifan/Bruce 团队提供完整交接材料，明确本周 100 个矩阵账号审核目标与验收标准。

### [#2527](https://github.com/Vispie-AI/VisPie_backend/pull/2527) chore(amy): disable OpenClaw Opus 4.6 shadow — keep DeepSeek shadow only
- **日期**：2026-04-27 | **状态**：✅ 已合并
- **问题**：OpenClaw shadow 存在 AsyncLocalStorage 上下文丢失导致双写 Lark 文档的隐患，mentor 决定暂时关闭。
- **修复**：将 fleet.json 中 amy 条目的 `shadow.enabled` 由 true 改为 false，一行配置翻转，不删除代码。
- **成果**：OpenClaw shadow 停止发送，双写风险在生产层面消除，回滚只需翻转同一行。

### [#2526](https://github.com/Vispie-AI/VisPie_backend/pull/2526) hotfix(amy): Dockerfile.autoamy must COPY vio_slack/ — autoamy-prod crashlooping
- **日期**：2026-04-27 | **状态**：✅ 已合并
- **问题**：PR #2522 编辑 Dockerfile.autoamy 时遗漏 `COPY vio_slack/`，导致 autoamy-prod 因 ModuleNotFoundError 崩溃重启 264 次。
- **修复**：在 Dockerfile.autoamy 中补充 `COPY vio_slack/ vio_slack/`，与 Dockerfile.nanobot 中已有修复保持一致。
- **成果**：autoamy-prod 构建恢复正常，crashloop 解除，线上 amy-nanobot 用户流量不受影响。

### [#2521](https://github.com/Vispie-AI/VisPie_backend/pull/2521) AWS snapshot mega cleanup — 180TB→77TB, ~$5.2k/mo savings
- **日期**：2026-04-27 | **状态**：🚫 已关闭
- **问题**：AWS Backup 保留期过长，账号积累了 638 个快照共 180TB，每月存储成本高达 $9,031。
- **修复**：将 Backup 保留期压缩至 14 天，删除 500 个恢复点，注销 4 个过期 AMI，并创建 DLM 策略。
- **成果**：快照数量降至 274 个，存储缩减至 77TB，每月节省约 $5,159（全年约 $61.9k）。

### [#2520](https://github.com/Vispie-AI/VisPie_backend/pull/2520) docs(security): AWS quick wins execution results
- **日期**：2026-04-27 | **状态**：🚫 已关闭
- **问题**：AWS 账号存在多个安全隐患：5 个孤立安全组、3 个 S3 桶公开访问、缺少只读审计用户及 VPC 流日志。
- **修复**：删除 5 个孤立安全组，锁定 3 个 S3 桶公开访问，创建 audit-readonly IAM 用户，启用 VPC 流日志并验证。
- **成果**：5 项安全加固全部完成并验证，结果记录在执行报告文档中。

### [#2519](https://github.com/Vispie-AI/VisPie_backend/pull/2519) ops(aws): stop vispie-flink-dev — ~$368/mo savings
- **日期**：2026-04-27 | **状态**：🚫 已关闭
- **问题**：vispie-flink-dev（r5.2xlarge）持续运行但无活跃作业使用，每月产生约 $368 的无效计算费用。
- **修复**：经 George 批准后停止（非终止）EC2 实例，保留 EBS 卷以便后续重启或调整规格。
- **成果**：实例从 running 变更为 stopped，每月节省约 $368 按需计算费用。

### [#2518](https://github.com/Vispie-AI/VisPie_backend/pull/2518) docs(ops): AWS EBS snapshot + AMI cleanup audit plan
- **日期**：2026-04-27 | **状态**：🚫 已关闭
- **问题**：账号 590184069312 的 638 个 EBS 快照（180.6TB）和 287 个 AMI 每月产生约 $9,031 存储费用，缺乏系统清理方案。
- **修复**：完成全量审计，制定 Tier 1/Tier 2 两阶段删除计划及配套脚本，所有脚本默认 dry-run 模式，提供 DLM 策略。
- **成果**：审计确认 6 个 AMI 和相关快照可安全删除，潜在节省约 $1,935/月，为正式清理提供执行方案。
### [#2446](https://github.com/Vispie-AI/VisPie_backend/pull/2446) docs(research): Multica deep analysis
- **日期**：2026-04-24 | **状态**：🔀 待合并
- **问题**：需要对 Multica 进行深度分析以支持产品或技术方向决策。
- **修复**：完成 Multica 平台深度分析报告，覆盖核心功能与竞争力评估。
- **成果**：研究成果可为产品方向提供参考依据，待正式审核合入。

### [#2444](https://github.com/Vispie-AI/VisPie_backend/pull/2444) docs(bugs): SMS blast history gap — root cause & fix options
- **日期**：2026-04-24 | **状态**：🔀 待合并
- **问题**：SMS 群发历史记录存在数据缺口，原因不明影响用户查阅历史记录。
- **修复**：分析 SMS 群发历史缺口的根本原因并提出多种可行修复方案。
- **成果**：明确了问题根因并给出可操作的修复建议，等待评审实施。

### [#2443](https://github.com/Vispie-AI/VisPie_backend/pull/2443) Codex/fix profile logout cookie
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：用户退出登录时 profile 相关 cookie 未被正确清除，存在安全隐患。
- **修复**：通过 Codex 修复退出登录流程中的 profile cookie 清除逻辑。
- **成果**：注销流程更加完整，消除了 cookie 残留的安全隐患。

### [#2442](https://github.com/Vispie-AI/VisPie_backend/pull/2442) Add new DM and broadcast flows to messages
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：消息系统缺少对新私信（DM）和广播消息发送流程的支持。
- **修复**：为消息模块新增私信和广播消息的完整业务流程。
- **成果**：消息功能更加完善，支持更多业务场景的消息发送需求。

### [#2441](https://github.com/Vispie-AI/VisPie_backend/pull/2441) Add ads media top10 recovery automation
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：广告媒体 top10 数据出现丢失时缺乏自动恢复机制，需人工介入。
- **修复**：新增广告媒体 top10 数据的自动恢复自动化流程。
- **成果**：top10 数据异常时可自动恢复，数据可靠性提升。

### [#2439](https://github.com/Vispie-AI/VisPie_backend/pull/2439) docs: PostHog/Stripe/App Mafia deep research
- **日期**：2026-04-24 | **状态**：🔀 待合并
- **问题**：团队需要深入了解 PostHog、Stripe 及 App Mafia 的技术与商业模式。
- **修复**：完成上述三个产品的深度研究报告并整理成文档。
- **成果**：为产品集成和商业决策提供参考依据，待审核合入。

### [#2428](https://github.com/Vispie-AI/VisPie_backend/pull/2428) docs(research): PostHog deep source analysis
- **日期**：2026-04-24 | **状态**：🚫 已关闭
- **问题**：需要对 PostHog 源码进行深度分析以评估与平台集成的可行性。
- **修复**：完成 PostHog 源码深度分析，输出详细研究文档。
- **成果**：分析结论未合入主干，PR 已关闭。

### [#2425](https://github.com/Vispie-AI/VisPie_backend/pull/2425) Support document uploads in direct messages
- **日期**：2026-04-24 | **状态**：✅ 已合并
- **问题**：私信功能不支持用户上传文档附件，限制了内容共享能力。
- **修复**：为私信模块添加文档上传支持功能。
- **成果**：用户可在私信中发送文档附件，消息功能更加完整。
### [#2413](https://github.com/Vispie-AI/VisPie_backend/pull/2413) perf-monitor: complete CAMPAIGN_MAPPING with all Supabase UUIDs
- **日期**：2026-04-23 | **状态**：✅ 已合并
- **问题**：多个活跃广告活动的 Supabase UUID 未录入映射表，导致统计数据缺失。
- **修复**：新增 10 个 Supabase 活动 UUID 映射，覆盖所有当前活跃活动。
- **成果**：所有活跃活动的已审批和已发布计数均可正确显示。

### [#2411](https://github.com/Vispie-AI/VisPie_backend/pull/2411) perf-monitor: add missing Supabase campaign mappings (wearwow, genstore)
- **日期**：2026-04-23 | **状态**：✅ 已合并
- **问题**：wearwow 和 genstore 活动因映射键名不匹配导致 Supabase 数据统计为零。
- **修复**：在 CAMPAIGN_MAPPING 中补充 wearwow_v1.8 和 genstore 的完整映射条目。
- **成果**：两个活动的审批和发布计数恢复正常显示。

### [#2407](https://github.com/Vispie-AI/VisPie_backend/pull/2407) Add codex final test file
- **日期**：2026-04-23 | **状态**：🚫 已关闭
- **问题**：需要验证 Codex 代理最终测试文档的创建和推送能力。
- **修复**：通过 Codex 代理添加 docs/coder-army-test/codex-final.md 测试文件。
- **成果**：Codex 代理文档创建验证完成，最终测试通过。

### [#2406](https://github.com/Vispie-AI/VisPie_backend/pull/2406) test(coder-army): opencode final verification test
- **日期**：2026-04-23 | **状态**：🚫 已关闭
- **问题**：需要对 OpenCode 代理进行最终端到端功能验证。
- **修复**：提交 OpenCode 最终验证测试文件，覆盖核心操作路径。
- **成果**：OpenCode 代理最终测试完成，功能验证通过。

### [#2401](https://github.com/Vispie-AI/VisPie_backend/pull/2401) docs: add codex push test marker
- **日期**：2026-04-23 | **状态**：🚫 已关闭
- **问题**：需要验证 Codex 代理的推送工作流自动化是否正常运行。
- **修复**：添加带时间戳的 docs/coder-army-test/codex-push-test.md 标记文件。
- **成果**：Codex 推送工作流验证成功，自动化流程可用。

### [#2400](https://github.com/Vispie-AI/VisPie_backend/pull/2400) test: push test marker file
- **日期**：2026-04-23 | **状态**：🚫 已关闭
- **问题**：需要验证推送工作流自动化是否正常运行。
- **修复**：创建 docs/coder-army-test/opencode-push-test.md 并记录验证时间戳。
- **成果**：推送自动化工作流验证完成，流程运行正常。

### [#2397](https://github.com/Vispie-AI/VisPie_backend/pull/2397) chore: slim project .claude/settings.json to team-only rules
- **日期**：2026-04-23 | **状态**：✅ 已合并
- **问题**：项目级配置包含大量应属于用户级的模型、权限等设置，职责不清晰。
- **修复**：将模型/环境/权限/插件配置迁移至用户级，项目级仅保留团队规范钩子。
- **成果**：配置职责分离明确，项目级配置精简为仅包含 enforce-respecv 钩子。

### [#2394](https://github.com/Vispie-AI/VisPie_backend/pull/2394) Onboarding: Figma UI alignment polish + missing analytics fix
- **日期**：2026-04-23 | **状态**：✅ 已合并
- **问题**：引导流程与 Figma 设计稿存在像素级偏差，同时缺失部分埋点统计。
- **修复**：对引导流程进行全面 UI 对齐打磨，并补充缺失的埋点分析事件。
- **成果**：引导页面与设计稿像素级一致，埋点数据完整覆盖。
### [#2380](https://github.com/Vispie-AI/VisPie_backend/pull/2380) Codex/ads meta auto sync no data state
- **日期**：2026-04-22 | **状态**：✅ 已合并
- **问题**：Meta广告自动同步在无数据时缺少对应的空状态展示处理。
- **修复**：添加Meta广告自动同步无数据状态的前端空状态展示逻辑。
- **成果**：Meta广告页面无数据时呈现友好提示，用户体验得到改善。

### [#2378](https://github.com/Vispie-AI/VisPie_backend/pull/2378) refactor(auto-amy): OCR as ground truth for Gemini — eliminates 88% false positive rate
- **日期**：2026-04-22 | **状态**：✅ 已合并
- **问题**：独立typo_detector与Gemini各自判断互不相关，导致高达88%的误判率。
- **修复**：架构重构为OCR提取文字后注入Gemini提示词作为事实基准，由Gemini结合OCR证据综合判断。
- **成果**：消除独立typo_detector模块，误报率大幅下降，视频审核精准度显著提升。

### [#2377](https://github.com/Vispie-AI/VisPie_backend/pull/2377) perf(fastapi): fix async event loop blocking + add lint scanner
- **日期**：2026-04-22 | **状态**：🔀 待合并
- **问题**：QC路由在异步函数内使用同步ThreadPoolExecutor阻塞事件循环，影响并发性能。
- **修复**：将相关路由改为asyncio.gather + run_in_executor模式，并新增异步阻塞lint扫描器。
- **成果**：FastAPI路由异步性能得到改善，lint工具可主动发现潜在的事件循环阻塞问题。

### [#2375](https://github.com/Vispie-AI/VisPie_backend/pull/2375) refactor(coder-army): unify naming — cc_army → coder_army
- **日期**：2026-04-22 | **状态**：✅ 已合并
- **问题**：代码库中cc_army和coder_army命名混用，DB表名、Python类名、环境变量均不统一。
- **修复**：全量重命名cc_army→coder_army，涵盖数据库表、Python类、环境变量及相关文件。
- **成果**：命名体系统一为coder_army，代码一致性和可维护性显著提升。

### [#2369](https://github.com/Vispie-AI/VisPie_backend/pull/2369) Reduce local dev analytics noise
- **日期**：2026-04-22 | **状态**：✅ 已合并
- **问题**：本地开发环境产生大量无效埋点数据，干扰线上分析统计准确性。
- **修复**：在本地开发环境中过滤或屏蔽分析埋点上报，减少噪声数据。
- **成果**：线上分析数据质量提升，本地调试不再污染生产埋点统计。

### [#2368](https://github.com/Vispie-AI/VisPie_backend/pull/2368) Codex/top performers rank badge safe corner
- **日期**：2026-04-22 | **状态**：✅ 已合并
- **问题**：顶级表现者排名徽章在某些布局下边角显示异常，存在视觉问题。
- **修复**：调整排名徽章的边角安全区样式，确保在各种布局下正确显示。
- **成果**：顶级表现者排名徽章在所有场景下边角显示正常，视觉一致性得到保证。

### [#2362](https://github.com/Vispie-AI/VisPie_backend/pull/2362) refactor: slim CLAUDE.md from 355 to 57 lines
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：CLAUDE.md文件过长（355行），内容冗余，AI行为引导效率低下。
- **修复**：将文件精简至57行（削减84%），行为原则替代项目百科，领域详情迁移至.claude/rules/。
- **成果**：AI协作文档极度精简，Andrej Karpathy风格+DAO融合原则直接指导AI行为。
### [#2349](https://github.com/Vispie-AI/VisPie_backend/pull/2349) chore(ci): remove 4 dead/deprecated workflow files
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：仓库中存在 4 个超过 1 年未成功运行或已明确标记废弃的 GitHub Actions workflow 文件，增加维护负担。
- **修复**：直接删除 deploy-dev_yluo.yml 等 4 个死亡/废弃 workflow，未触及任何活跃部署路径。
- **成果**：CI 配置更整洁，减少误触发风险，仓库维护成本降低。

### [#2348](https://github.com/Vispie-AI/VisPie_backend/pull/2348) Codex/ads analytics clients lite fix
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：广告分析客户端在 Codex 框架下存在若干影响数据展示或交互体验的轻量级缺陷。
- **修复**：针对广告分析客户端的已知问题进行修复，恢复正常功能。
- **成果**：广告分析客户端功能恢复正常，相关缺陷得到解决。

### [#2347](https://github.com/Vispie-AI/VisPie_backend/pull/2347) chore(messages): sync ensure_conversation migration + regen types + drop rpc casts
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：#2344/#2345 版本的 ensure_conversation RPC 迁移文件在 Supabase 执行时报错，类型转换语法不兼容。
- **修复**：同步迁移文件至实际在生产执行的版本，重新生成类型，移除不兼容的 RPC 类型转换语句。
- **成果**：ensure_conversation 迁移与生产环境保持一致，类型生成正确，后续开发可顺利推进。

### [#2346](https://github.com/Vispie-AI/VisPie_backend/pull/2346) Add local Nginx gateway for dev workflows
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：开发工作流中缺乏本地统一网关，各服务端口分散，开发与调试体验不一致。
- **修复**：新增本地 Nginx 网关配置，为开发流程提供统一的请求入口。
- **成果**：本地开发环境具备与生产一致的网关路由层，提升调试效率。

### [#2333](https://github.com/Vispie-AI/VisPie_backend/pull/2333) Support document uploads in direct messages
- **日期**：2026-04-21 | **状态**：🔀 待合并
- **问题**：私信功能仅支持图片/视频附件，无法发送 PDF、Word、Excel 等常见文档格式。
- **修复**：新增对 PDF、Word、Excel、PowerPoint、CSV、TXT、RTF 等文档类型的上传支持，分类为 file 媒体类型。
- **成果**：私信附件类型更丰富，文档类附件以可下载文件形式在聊天中展示（待合并）。
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

**合计：19 个 PR | 已合并 290 | 关闭未合并 28 | 待合并 25**
