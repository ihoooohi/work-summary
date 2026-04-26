# 工作成果总结

> 统计周期：2026-04-10 ~ 2026-04-26 | 共 169 个 PR（已合并 144 · 关闭未合并 15 · 待合并 9）
> 最后更新：2026-04-26

---

## 一、Bug 修复（fix:）

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

**合计：19 个 PR | 已合并 144 | 关闭未合并 15 | 待合并 9**
