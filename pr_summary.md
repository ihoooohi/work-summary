# 工作成果总结

> 统计周期：2026-04-11 ~ 2026-06-09 | 共 156 个 PR（已合并 143 · 关闭未合并 8 · 待合并 3）
> 最后更新：2026-06-09
> 作者：@ihoooohi · 仓库：Vispie-AI/VisPie_backend

---

## 一、Bug 修复（fix:）

### [#4188](https://github.com/Vispie-AI/VisPie_backend/pull/4188) fix(nanobot): deliver message tool text content — it was silently dropped
- **日期**：2026-06-03 | **状态**：✅ 已合并
- **问题**：nanobot _drain_outbound只遍历media附件，msg.content文本从未发送；loop.py的防重复发送机制也将最终文本丢弃，Lark文档链接从未到达用户。
- **修复**：在lark/client.py新增send_text_message，nanobot_server.py的_drain_outbound先发content文本再发媒体附件。
- **成果**：修复影响全部13个fleet机器人，message tool文本内容从此正常送达用户，无双发问题。

### [#4187](https://github.com/Vispie-AI/VisPie_backend/pull/4187) fix(creative-agent): deliver Lark doc link only + accept en/em-dash timestamps
- **日期**：2026-06-03 | **状态**：✅ 已合并
- **问题**：用户反馈创作方案应只发Lark文档链接而非markdown附件；两次实测中em-dash时间戳触发门禁误报浪费一轮检查。
- **修复**：SKILL.md及wrapper改为只发送Lark文档链接，filming_timestamps正则接受连字符及全角破折号变体。
- **成果**：en-dash格式好样本通过测试，回归全部通过，用户交付体验更简洁直接。

### [#4186](https://github.com/Vispie-AI/VisPie_backend/pull/4186) fix(creative-agent): parameterize gate format count + --help + doc URL delivery
- **日期**：2026-06-03 | **状态**：✅ 已合并
- **问题**：发布门禁对format数量硬编码≥6，用户要求5个format时Amy无法通过门禁并违反铁规则绕过校验。
- **修复**：self-check.js添加--formats N参数，publish-gameplan.sh增加--help及正整数校验，SKILL.md明确合法退出路径防止绕过行为。
- **成果**：5格式文档使用--formats 5可通过，非法参数正确退出，首次实测完整pipeline运行成功。
### [#4135](https://github.com/Vispie-AI/VisPie_backend/pull/4135) fix(amy): expose scraper creds to exec env + fix Langfuse trace observability
- **日期**：2026-06-02 | **状态**：✅ 已合并
- **问题**：ExecTool 白名单缺少爬虫 API Key，#4115 的容器注入未完全生效；Langfuse trace 输入取最旧消息且工具 span 缺失 args/result。
- **修复**：将三个爬虫 Key 加入 exec 环境白名单；修正 trace.input 取最新用户消息；工具事件附加 args/result 供追踪钩子使用。
- **成果**：exec 正确传递爬虫凭证，Langfuse trace 输入与工具调用信息准确，有机视频计划排障效率显著提升。

### [#4115](https://github.com/Vispie-AI/VisPie_backend/pull/4115) fix(amy-nanobot): inject social-scraper API keys (RAPIDAPI/TIKHUB/YOUTUBE) into .env.nanobot
- **日期**：2026-06-02 | **状态**：✅ 已合并
- **问题**：部署脚本 heredoc 未将三个社交爬虫 API Key 写入 .env.nanobot，导致 social-scraper 技能无凭证，有机视频计划退化为通用网页搜索。
- **修复**：在 .env.nanobot heredoc 中补充 RAPIDAPI_KEY、TIKHUB_API_KEY、YOUTUBE_API_KEY，与 LANGFUSE_* 同模式注入，无新建密钥。
- **成果**：重新部署后 amy-nanobot 可调用 TikTok/YouTube 爬虫，有机视频排名分析恢复正常。

### [#4051](https://github.com/Vispie-AI/VisPie_backend/pull/4051) fix(shadow-judge): switch fair-judge to Gemini (DeepSeek key out of balance)
- **日期**：2026-05-31 | **状态**：✅ 已合并
- **问题**：DeepSeek 余额耗尽，fair-judge 全量返回 `error`，shadow arena 裁判层瘫痪。
- **修复**：切换为 Gemini，模型名改读 `GEMINI_JUDGE_MODEL` env，workflow 中 `JUDGE_BACKEND` 改为 repo variable。
- **成果**：实测 Gemini 正常返回 verdict，裁判层恢复，后端和模型均可无代码变更切换。

### [#4047](https://github.com/Vispie-AI/VisPie_backend/pull/4047) fix(monitoring): vio-liveness skips ephemeral *-staging tenants
- **日期**：2026-05-31 | **状态**：🚫 已关闭
- **问题**：CI 短暂创建的 staging 容器被采集入库，销毁后约 1 小时触发虚假 `liveness_silent` 告警。
- **修复**：在采集层以 `-staging($|-)` 正则过滤 staging 租户，使其不写入 DB 和自动发现。
- **成果**：staging 容器完全排除于监控链路，虚假告警消除，不误伤含 staging 子串的真实租户名。
### [#4030](https://github.com/Vispie-AI/VisPie_backend/pull/4030) fix(amy-lark): auto-refresh tenant_access_token on 99991663 for card send/update
- **日期**：2026-05-30 | **状态**：✅ 已合并
- **问题**：Lark 长任务超过 token 有效期后，send_card/update_card 未自动刷新令牌，连续触发 47 次 99991663 错误，卡片卡死在执行中状态。
- **修复**：在 update_card 和 send_card 中各加外层 range(2) token 刷新重试，与内层 range(3) 网络重试正交，新增 test_card_token_retry.py 覆盖 9 个场景。
- **成果**：彻底消除第三次同类卡片卡死复现，9 项新测试全部通过，53 项回归测试无回归。

### [#3925](https://github.com/Vispie-AI/VisPie_backend/pull/3925) fix(amy-codex): re-derive prior_messages from Lark chat history
- **日期**：2026-05-28 | **状态**：✅ 已合并
- **问题**：amy-codex 每次响应均为冷启动，`prior_messages` 被硬编码为空列表，导致对话无历史上下文。
- **修复**：从 Lark `/im/v1/messages` 接口拉取近期消息，构建时序化的 `prior_messages` 注入 Codex 提示。
- **成果**：Amy 可跨轮次正确引用历史对话，"你怎么知道的？" 等追问问题得以准确回答。
### [#3908](https://github.com/Vispie-AI/VisPie_backend/pull/3908) fix(vio): register correct OpenClaw event names for langfuse-tracer
- **日期**：2026-05-27 | **状态**：✅ 已合并
- **问题**：Hook 注册了错误的事件名 `notification:receive/send`，OpenClaw 从未将消息事件分发给该 Hook，追踪始终为 0。
- **修复**：将 HOOK.md 事件名改为 OpenClaw 实际派发的 `message:received/sent`，并补充 sessionKey 回退逻辑。
- **成果**：彻底关闭从 #3864 延续的 Bug 链，studyx/shesaid 的 Langfuse 追踪正式接通。

### [#3906](https://github.com/Vispie-AI/VisPie_backend/pull/3906) fix(amy-codex): parse codex --json event stream for accurate tool count
- **日期**：2026-05-27 | **状态**：✅ 已合并
- **问题**：Activity 卡片始终显示"0 calls"，Langfuse 核查 66/66 条记录 tool_count 均为 0，确认为展示层 Bug。
- **修复**：改用 `codex exec --json` 解析 JSONL 事件流，以 `item.completed` 类型统计工具调用数，保留旧格式回退。
- **成果**：工具调用次数准确上报，Activity 卡片与 Langfuse metadata.tool_count 均显示正确数值。

### [#3905](https://github.com/Vispie-AI/VisPie_backend/pull/3905) fix(vio): correct OpenClaw event field mapping in langfuse-tracer
- **日期**：2026-05-27 | **状态**：✅ 已合并
- **问题**：sessionKey 取值路径错误导致每条消息都提前返回，Hook 加载正常但 Langfuse 追踪仍为 0。
- **修复**：将 sessionKey 读取改为 `event.sessionKey`，并修正 received/sent 两类事件的上下文字段名。
- **成果**：字段映射修正后，studyx/shesaid 的消息事件可正常生成 Langfuse Trace。

### [#3901](https://github.com/Vispie-AI/VisPie_backend/pull/3901) fix(vio): use cp not rsync in sync_hooks (JuiceFS EPERM)
- **日期**：2026-05-27 | **状态**：✅ 已合并
- **问题**：`sync_hooks` 使用 `rsync -a` 时，JuiceFS 以 EPERM 拒绝 chown/chmod 调用，新 Hook 实际从未写入租户工作区。
- **修复**：将 rsync 替换为 `cp -rf`，仅复制内容不保留属性，并加入写入验证与逐 Hook 错误上报。
- **成果**：langfuse-tracer 成功同步至所有租户 workspace/hooks，解决 #3864/#3872 以来的部署失败链。
### [#3877](https://github.com/Vispie-AI/VisPie_backend/pull/3877) fix(amy-codex): inject reply parent text into prompt server-side
- **日期**：2026-05-26 | **状态**：✅ 已合并
- **问题**：`lark_webhook.py` 只读取 `content.text`，未提取 `parent_id`，引用消息上下文完全丢失，Codex 产生幻觉回复"I don't see the quoted message"。
- **修复**：在 Python 层服务端获取父消息文本，以 `[reply_to: …]` 块形式预注入提示词，绕过对模型主动工具调用的依赖。
- **成果**：Amy-Codex 在 Lark 引用回复场景下可正确感知被引用内容，消除了因上下文缺失导致的回复漂移。

### [#3872](https://github.com/Vispie-AI/VisPie_backend/pull/3872) fix(vio): close langfuse-tracer rollout gap on existing tenants
- **日期**：2026-05-26 | **状态**：✅ 已合并
- **问题**：#3864/#3868 合并后 langfuse-tracer 对全部 22 个租户均未生效，排查发现三层独立缺陷：注入脚本未注册钩子、并行子 shell 未导出环境变量、rsync 未将钩子目录同步至 JFS。
- **修复**：独立添加 langfuse-tracer 的幂等注入块、显式 `export LANGFUSE_*` 变量、并在 rsync 后校验目标目录是否存在。
- **成果**：langfuse-tracer 在 StudyX 和 SheSaid 租户上成功激活，Langfuse 可正常接收 Vio 对话追踪数据。

### [#3868](https://github.com/Vispie-AI/VisPie_backend/pull/3868) fix(vio): replace heredoc with echo block for Langfuse .env upsert
- **日期**：2026-05-26 | **状态**：✅ 已合并
- **问题**：#3864 在 YAML run-block 中使用 heredoc，bash 要求终止符须在第 0 列，缩进导致终止符被误读，部署静默成功但容器从未重建。
- **修复**：将 heredoc 替换为花括号分组的 `echo` 块重定向写文件，消除缩进约束并添加注释防止再次踩坑。
- **成果**：vio-studyx 和 vio-shesaid 容器在后续部署中正确重建，LANGFUSE_* 环境变量成功注入。

### [#3863](https://github.com/Vispie-AI/VisPie_backend/pull/3863) fix(amy-prompt): remove .env.merged hallucination in fleet AGENTS.md
- **日期**：2026-05-26 | **状态**：✅ 已合并
- **问题**：舰队 AGENTS.md 指示 Amy 从不存在的 `.env.merged` 读取 API 密钥，6 天内造成 15 次工具调用失败，占全部 ERROR 级观测的 18.5%。
- **修复**：删除 AGENTS.md 中 `.env.merged` 相关条目，将密钥来源说明更新为 `docker run -e` 注入和 GitHub Variables。
- **成果**：消除最高频工具调用错误，每次迭代节省约 40K prompt token 和 7–10s 延迟。

### [#3859](https://github.com/Vispie-AI/VisPie_backend/pull/3859) fix(amy-codex): heartbeat card refresh + anti-realtime-hallucination guard
- **日期**：2026-05-26 | **状态**：✅ 已合并
- **问题**：纯推理轮次（无工具调用）因卡片更新仅依赖 exec/tool 标记而冻结不动；同时 Codex 在零工具调用下伪造了实时天气数据"28°C 小雨"。
- **修复**：引入后台 heartbeat 任务每 3 秒渲染工作卡片，在系统提示中明确列出 Codex 无法获取的实时信息类型并添加气候知识豁免条款。
- **成果**：用户在纯推理轮次也能看到卡片定时刷新，Amy 在被问及实时数据时能正确声明无法获取。

### [#3858](https://github.com/Vispie-AI/VisPie_backend/pull/3858) fix(amy-codex): heartbeat card refresh + anti-realtime-hallucination guard
- **日期**：2026-05-26 | **状态**：🚫 已关闭
- **问题**：#3855 部署后纯推理轮次卡片静止不动，且 Codex 幻觉生成实时天气数据，两个 bug 需同时修复。
- **修复**：提交了 heartbeat 后台任务和 prompt 加固方案，但因与 #3855 squash-merge 产生冲突，本 PR 被关闭并由 rebased 副本 #3859 替代。
- **成果**：作为过渡 PR 推动了 heartbeat 机制和反幻觉 prompt 的最终落地（由 #3859 实际合并）。

### [#3855](https://github.com/Vispie-AI/VisPie_backend/pull/3855) fix(amy-codex): stream codex stdout + fix Activity 0-call counter
- **日期**：2026-05-26 | **状态**：✅ 已合并
- **问题**：Activity 面板始终显示"0 calls"（正则未匹配 Codex CLI 实际输出格式），且 `proc.communicate()` 阻塞导致复杂任务期间用户只能看到静态思考卡片长达 30–120 秒。
- **修复**：将 `_run_codex` 改为逐行流式读取 stdout，新增 `^(?:exec|tool)\b` 正则，通过 `on_progress` 回调限速触发 Lark 卡片更新（默认间隔 3 秒）。
- **成果**：工具调用计数准确，用户在长耗时任务中可实时看到卡片从"思考中"变为"进行中"并刷新工具数量。

### [#3746](https://github.com/Vispie-AI/VisPie_backend/pull/3746) fix(amy-nanobot): retry update_card on transient httpx errors
- **日期**：2026-05-22 | **状态**：✅ 已合并
- **问题**：Lark `update_card` 在 TLS 握手超时时直接失败，导致成功的 nanobot 响应被覆盖为空白错误卡片。
- **修复**：为 `update_card` 添加指数退避重试（最多 3 次），并在顶层异常处理中改用异常类名显示错误信息。
- **成果**：避免因短暂网络抖动将成功操作（如充值）误报为错误，防止用户重复提交。

### [#3745](https://github.com/Vispie-AI/VisPie_backend/pull/3745) fix(amy-codex): tool guide — feishu-doc skill actually supports --action create
- **日期**：2026-05-22 | **状态**：✅ 已合并
- **问题**：身份提示词错误指引 codex 通过 curl 创建飞书文档，而技能脚本已原生支持 `--action create`。
- **修复**：更新 `identity.py` 工具指引，将 `--action create` 作为正式选项并说明创建→追加内容的完整链路。
- **成果**：codex 可直接调用飞书文档技能创建文档，消除了幻觉返回"已完成"而不实际执行的风险。

### [#3744](https://github.com/Vispie-AI/VisPie_backend/pull/3744) fix(amy-codex): include skills/common/ so feishu-doc can require feishu-client
- **日期**：2026-05-22 | **状态**：✅ 已合并
- **问题**：PR #3743 部署后，feishu-doc 技能在启动时因缺少 `skills/common/feishu-client.js` 而崩溃。
- **修复**：在 Dockerfile 中补充 `COPY project_amy/shared/skills/common /skills/common` 一行。
- **成果**：feishu-doc 技能在 amy-codex 容器中可正常加载并执行飞书文档操作。

### [#3742](https://github.com/Vispie-AI/VisPie_backend/pull/3742) fix(gateway): card.action.trigger must skip ab_split routing
- **日期**：2026-05-22 | **状态**：✅ 已合并
- **问题**：Lark 按钮点击回调经过 `resolveAbSplit()` 路由，会被分配到无卡片处理器的 amy-codex，导致反馈按钮失效。
- **修复**：在 `lark-webhook.ts` 中提前检测 `isCardAction`，跳过 ab_split 路由逻辑，将所有回调固定发往 nanobot。
- **成果**：codex 分桶用户的反馈按钮点击可正确到达 nanobot 的 `handle_card_action`，卡片状态更新正常。
### [#3728](https://github.com/Vispie-AI/VisPie_backend/pull/3728) fix(amy-codex): roll codex bucket back to 0% pending feature parity
- **日期**：2026-05-21 | **状态**：✅ 已合并
- **问题**：Codex桶在30%实流量下暴露出反馈按钮缺失、Lark文档工具未挂载等功能差距。
- **修复**：将fleet.json中ab_split.percent由30回滚至0，暂停实流量直至功能对齐完成。
- **成果**：真实用户流量恢复全量走nanobot桶，codex基础设施保留，回滚仅改一处配置。

### [#3727](https://github.com/Vispie-AI/VisPie_backend/pull/3727) fix(amy-codex): ab_split.target double-appended path = 404
- **日期**：2026-05-21 | **状态**：✅ 已合并
- **问题**：Day 6上线后网关将请求转发至/lark/webhook/lark/webhook（路径重复），amy-codex返回404导致bot无响应。
- **修复**：从fleet.json的ab_split.target中去掉多余的/lark/webhook前缀。
- **成果**：路由路径恢复正确，codex桶请求成功到达目标端点。

### [#3726](https://github.com/Vispie-AI/VisPie_backend/pull/3726) fix(amy-codex): land ab_split on fleet.json (actual source of truth)
- **日期**：2026-05-21 | **状态**：✅ 已合并
- **问题**：Day 6 PR修改了routes.json，但网关部署流程实际通过fleet.json生成routes.json，导致ab_split未真正生效。
- **修复**：将相同的ab_split配置同步写入fleet.json（真正的配置源）。
- **成果**：ab_split配置在正式部署中真正落地，A/B分流开关按预期运行。

### [#3696](https://github.com/Vispie-AI/VisPie_backend/pull/3696) fix(amy-codex): hard-code main Amy app_id, only read app_secret from CI
- **日期**：2026-05-21 | **状态**：✅ 已合并
- **问题**：GitHub Secret AMY_LARK_APP_ID实际存储的是amy-fin的app_id，错误使用导致Lark卡片通过错误租户凭证发送，破坏"用户只见一个Bot"的目标。
- **修复**：将main Amy的app_id硬编码到部署Workflow中，只保留app_secret读取自CI Secrets。
- **成果**：amy-codex容器使用正确的Amy身份发送消息，与SPEC §A.1设计保持一致。

### [#3695](https://github.com/Vispie-AI/VisPie_backend/pull/3695) fix(amy-codex): add httpx to requirements
- **日期**：2026-05-21 | **状态**：✅ 已合并
- **问题**：Day 1新增的lark_client.py依赖httpx，但requirements.txt未声明，导致容器健康检查失败（ModuleNotFoundError）。
- **修复**：在requirements.txt中固定httpx==0.28.1，与amy-nanobot保持版本一致。
- **成果**：amy-codex容器可正常启动并通过健康检查，服务恢复可用。
### [#3633](https://github.com/Vispie-AI/VisPie_backend/pull/3633) fix(openclaw-base): install long as root so NODE_PATH actually resolves it
- **日期**：2026-05-20 | **状态**：✅ 已合并
- **问题**：#3630中全局安装`long`因`USER openclaw`切换落入用户目录，不在`NODE_PATH`扫描路径内，模块仍无法解析。
- **修复**：将`npm install -g long`移至`USER root`阶段执行，确保包安装在全局路径且`NODE_PATH`可识别。
- **成果**：`long`模块现在可通过`NODE_PATH`正确解析，OpenClaw容器启动稳定，依赖问题彻底修复。

### [#3630](https://github.com/Vispie-AI/VisPie_backend/pull/3630) fix(openclaw-base): install long to NODE_PATH as baileys transitive shim
- **日期**：2026-05-20 | **状态**：✅ 已合并
- **问题**：OpenClaw的Slack扩展通过baileys传递依赖引入`long`，pnpm符号链接布局无法正确提升该包，启动时报`Cannot find module 'long'`。
- **修复**：在Dockerfile中全局安装`long`并将路径加入`NODE_PATH`，绕过pnpm符号链接提升失败的问题。
- **成果**：OpenClaw容器可正确找到`long`模块，Slack扩展不再因传递依赖缺失而崩溃启动。

### [#3558](https://github.com/Vispie-AI/VisPie_backend/pull/3558) fix(ci/amy-codex): drop sudo on git+docker (ubuntu user has perms)
- **日期**：2026-05-19 | **状态**：✅ 已合并
- **问题**：CI 工作流中 sudo 执行 git 命令导致 root 无 SSH 密钥而鉴权失败。
- **修复**：去掉 git fetch/reset 和 docker 命令前的 sudo，与 nanobot 部署流程保持一致。
- **成果**：amy-codex 的 CI 自动部署流水线可正常拉取代码并构建镜像。

### [#3557](https://github.com/Vispie-AI/VisPie_backend/pull/3557) fix(ci/amy-codex): drop sudo on git+docker (ubuntu user has perms)
- **日期**：2026-05-19 | **状态**：🚫 已关闭
- **问题**：CI 工作流中 sudo git 命令因 root 无 SSH 密钥而触发权限拒绝错误。
- **修复**：移除 git 和 docker 命令前的 sudo 前缀（已在 #3558 以干净分支重新提交）。
- **成果**：PR 因历史冲突关闭，修复内容由 #3558 合入主干。

### [#3555](https://github.com/Vispie-AI/VisPie_backend/pull/3555) fix(amy-codex): pipe prompt on stdin + isolate auth volume
- **日期**：2026-05-19 | **状态**：✅ 已合并
- **问题**：多词 prompt 通过命令行参数传入时触发 clap 解析错误，且共享 OAuth 卷存在令牌竞争导致刷新失败。
- **修复**：将 prompt 改为通过 stdin 传入，并为 amy-codex 挂载独立 Docker 卷以隔离认证文件。
- **成果**：amy-codex 可正确接收完整提示词并完成端到端 smoke test。

### [#3554](https://github.com/Vispie-AI/VisPie_backend/pull/3554) fix(amy-codex): pipe prompt on stdin + isolate auth volume
- **日期**：2026-05-19 | **状态**：🚫 已关闭
- **问题**：codex exec 不支持多词 positional 参数，且两个容器共享 OAuth 卷导致令牌刷新竞争。
- **修复**：切换为 stdin 传参并隔离认证卷（已在 #3555 以干净分支重新提交）。
- **成果**：PR 因分支冲突关闭，有效修复已通过 #3555 合入主干。

### [#3550](https://github.com/Vispie-AI/VisPie_backend/pull/3550) fix(amy-codex): drop --ask-for-approval from codex exec args
- **日期**：2026-05-19 | **状态**：✅ 已合并
- **问题**：`--ask-for-approval` 是 codex 根命令参数，误传给 codex exec 导致退出码 2，每次 shadow 调用均失败。
- **修复**：从 codex exec 参数列表中删除该标志及无用环境变量 CODEX_APPROVAL_POLICY。
- **成果**：amy-codex 端到端 smoke test 可正常触发 Codex 模型调用。

### [#3546](https://github.com/Vispie-AI/VisPie_backend/pull/3546) fix(amy-codex): drop --ask-for-approval from codex exec args
- **日期**：2026-05-19 | **状态**：🚫 已关闭
- **问题**：codex exec 不识别 --ask-for-approval 参数，导致所有 shadow 调用在到达模型前即失败。
- **修复**：移除错误标志（已在 #3550 以干净分支重新提交）。
- **成果**：PR 因分支状态关闭，有效修复已在 #3550 合并入主干。
### [#3298](https://github.com/Vispie-AI/VisPie_backend/pull/3298) fix(vio): hide feedback buttons by default for ISV apps (avoids code:200340)
- **日期**：2026-05-14 | **状态**：✅ 已合并
- **问题**：Lark ISV/store app 有独立 Callback Configuration URL slot，未配置时点反馈按钮报 `code:200340` 错误，vio-test 用户每次点都失败。
- **修复**：把 `LARK_FEEDBACK_BUTTONS` 默认值按 `LARK_APP_TYPE` gate —— selfbuild→true、isv→false，运维 ISV 配好回调后可显式 `=true`。
- **成果**：ISV 用户点击不再报错，amy/eva/skin-bots 行为保持原样；9/9 单元测试覆盖各 env 组合。

### [#3288](https://github.com/Vispie-AI/VisPie_backend/pull/3288) fix(cost-report): empty webhook short-circuits with rc=6 (don't None-poison urllib)
- **日期**：2026-05-14 | **状态**：✅ 已合并
- **问题**：PR #3141 把 `DEFAULT_WEBHOOK` 改为 `os.environ.get(...)` 无默认，mitchell 容器未注入 env 导致 None → urllib 报 `unknown url type: 'None'`，日报静默丢失三天。
- **修复**：默认改为空字符串，启动时检测为空立即 rc=6 退出，把静默失败变成响亮失败；保留 #3141 的 secret 清理意图。
- **成果**：cost report 失败可观测可告警，配合 PR #3312 的 CI 注入彻底关闭这条静默丢失通路。

### [#3153](https://github.com/Vispie-AI/VisPie_backend/pull/3153) fix(monitoring): amy-liveness L3 — auto-discover gateway port
- **日期**：2026-05-10 | **状态**：✅ 已合并
- **问题**：amy-liveness.sh L3 探针硬编码 `localhost:3000`，部署切换至 `OPENCLAW_GATEWAY_PORT=18789` 后持续报告 L3 不健康并触发误报告警。
- **修复**：探针运行时从容器环境变量读取端口，缺省回退至 3000，无需重启脚本。
- **成果**：L3 探针恢复返回 200，R1 告警停止误报，监控状态恢复正常。

### [#3137](https://github.com/Vispie-AI/VisPie_backend/pull/3137) fix(vio): Gemini API key runtime-exposure mitigation (URL→header + per-call read)
- **日期**：2026-05-10 | **状态**：✅ 已合并
- **问题**：Gemini API Key 以 `?key=` 参数暴露在请求 URL 中并在模块加载时缓存，密钥泄露后须重启容器才能切换新密钥。
- **修复**：改为每次调用从环境变量读取密钥并通过 `x-goog-api-key` 请求头传递，401/403 快速失败不再重试。
- **成果**：密钥不再出现于日志 URL，轮换后无需重启即可生效，12 项安全单元测试全部通过。

### [#3120](https://github.com/Vispie-AI/VisPie_backend/pull/3120) fix(deploy-vio-tenant): allow nanobot-only tenants (no OpenClaw workspace)
- **日期**：2026-05-09 | **状态**：✅ 已合并
- **问题**：部署脚本强制要求 `workspace/` 目录存在，ISV 直接配置的 nanobot 租户仅有 `workspace-nanobot/`，导致部署以 exit 2 中止。
- **修复**：允许三种合法目录布局，仅当两种工作区均缺失时才报错退出。
- **成果**：ISV nanobot 专属租户可正常部署，vio-test 租户阻塞问题解除。

### [#3119](https://github.com/Vispie-AI/VisPie_backend/pull/3119) fix(nanobot/lark): bypass local mention gate for ISV apps (follow-up to #3117)
- **日期**：2026-05-09 | **状态**：✅ 已合并
- **问题**：nanobot `channel.py` 本地 @mention 校验将 `BOT_OPEN_ID` 与 ISV 容器的 open_id 比对始终为假，群组 webhook 全部被丢弃。
- **修复**：读取 `LARK_APP_TYPE` 环境变量，ISV 模式下跳过本地 mention 校验，依赖 Lark 服务端过滤保障准确性。
- **成果**：ISV 租户群组消息成功触发 Hatchet 任务，机器人可在群组中正常回复，7 项新测试通过。

### [#3117](https://github.com/Vispie-AI/VisPie_backend/pull/3117) fix(vio-gateway): trust Lark scope filter for ISV-app group webhooks
- **日期**：2026-05-09 | **状态**：✅ 已合并
- **问题**：网关将 ISV 群组 @mention 与自身 `larkBotOpenId`（仅属于 SelfBuild 应用）比对，导致 ISV 群消息全部静默丢弃长达 48 小时。
- **修复**：新增 `lib/mention.js` 辅助函数，ISV 跨应用 webhook 直接信任 Lark 服务端范围过滤，跳过本地 open_id 校验。
- **成果**：ISV 群组 @mention 成功转发，8 项新单元测试全部通过，SelfBuild 路径无回归。

### [#3003](https://github.com/Vispie-AI/VisPie_backend/pull/3003) fix(alerter): R1 schema guard — stop false-positive Critical from foreign liveness probes
- **日期**：2026-05-07 | **状态**：✅ 已合并
- **问题**：`cloud-run-health-probe.py` 写入的 `details` JSON 格式与宿主机 liveness 脚本不兼容，R1 规则误将所有 cloud-run 行判定为 `infra_down`。
- **修复**：新增 `_has_host_liveness_schema()` 守卫，R1 仅评估含 `l1_health` 和 `l2_process` 字段的行，其余 schema 默认返回 False。
- **成果**：cloud-run-fastapi 误报 Critical 告警消除，新增 1 项测试，共 67 项通过。

### [#2945](https://github.com/Vispie-AI/VisPie_backend/pull/2945) fix(auto-amy): cost report — detect Lark biz errors, retry on rate-limit
- **日期**：2026-05-06 | **状态**：✅ 已合并
- **问题**：Lark 限流返回 `HTTP 200 + {"code":9499,"msg":"frequency limited..."}`，旧 `post_to_webhook` 只 catch `HTTPError` 误判成功 → 5/4、5/6 cost 报表静默丢失。
- **修复**：新增 `WebhookError(retryable)` 区分瞬时/永久错误，`post_with_retry` 3 次指数退避（0/4/16s ± jitter），业务码非零视为失败；cron 时间错开拥挤的 :00/:30 分钟。
- **成果**：限流自动重试成功，永久错误立即 fail loud；cron-storm 错开后再未观察到 5/6 类静默漏发。

### [#2740](https://github.com/Vispie-AI/VisPie_backend/pull/2740) fix(shadow-judge): align GeminiJudge URL regex with gemini-3-pro-preview
- **日期**：2026-05-01 | **状态**：✅ 已合并
- **问题**：PR #2738 只更新了 modelName 断言，`fair-judge.test.ts:222` 的 URL regex 还 pin 在 `gemini-2.5-flash:generateContent`，Deploy Amy Gateway Router 持续红。
- **修复**：把 L222 regex 改为 `gemini-3-pro-preview:generateContent`。
- **成果**：gateway 62/62 测试通过，CI 恢复绿，可继续 dev → prod 部署。

### [#2739](https://github.com/Vispie-AI/VisPie_backend/pull/2739) fix(auto-amy): DeepSeek cost — cache-hit + active promo pricing
- **日期**：2026-05-01 | **状态**：✅ 已合并
- **问题**：DeepSeek shadow 月报 \$565 是实际账单 10-30 倍 —— 两 bug 叠加：emit 事件丢失 `prompt_cache_hit_tokens` 字段全按 cache-miss 计价；`_DEEPSEEK_PRICING` 用了过期 post-promo 价。
- **修复**：emit_usage_event 读取并 carry cache hit token，pricing table 切换到当前 75% off 促销价（V4-Pro \$0.435 input / \$0.87 output / \$0.003625 cache）。
- **成果**：DeepSeek 月报从 \$565 修正为 ~\$22 真实值；附 `test_pricing_v4_pro_real_world_overestimate_fix` 用 prod 数据做回归 sentinel。

### [#2738](https://github.com/Vispie-AI/VisPie_backend/pull/2738) fix(shadow-judge): align GeminiJudge test assertion with gemini-3-pro-preview
- **日期**：2026-05-01 | **状态**：✅ 已合并
- **问题**：PR #2735 升级 `GEMINI_MODEL` 到 `gemini-3-pro-preview` 但 `fair-judge.test.ts:182` 仍断言 `gemini-2.5-flash`，Gateway Router 部署 fail-closed。
- **修复**：把 L182 断言改为 `'gemini-3-pro-preview'`，无 production-code 改动。
- **成果**：Gateway Router build+test 通过，可触发 amy-gateway-prod 镜像滚动。

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

### [#2662](https://github.com/Vispie-AI/VisPie_backend/pull/2662) fix(amy): strip Bedrock thinking artifacts at shadow seed boundary
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：PR #2658 错误地在 provider 层剥除 reasoning_content，导致 DeepSeek 自身 thinking 链路断裂，相同错误在生产复现。
- **修复**：还原 provider 层修改，改在种子边界精准剥除 Bedrock 注入的 reasoning_content 和 thinking_blocks。
- **成果**：种子边界处理更精准，不影响 DeepSeek 正常 thinking 轮次，测试覆盖 22 个用例。

### [#2658](https://github.com/Vispie-AI/VisPie_backend/pull/2658) fix(lark-isv): correct app_ticket schema (V1 event_callback wrapper)
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：vio-isv-lark PR1+PR3 都把 `app_ticket` 当作 header.type 顶层事件解析，但 Lark Open Platform 实际发的是 V1 schema `{type:"event_callback", event:{type:"app_ticket", app_id, app_ticket}}`，真实推送静默丢弃。
- **修复**：在 gateway parseLarkWebhook + nanobot `/webhook` handler 用 `event.type || header.event_type || body.type` 衍生 `_event_type`，同时覆盖 V1 与未来 schema；更新测试 fixtures。
- **成果**：真实 Lark app_ticket 推送可被识别，gateway 9/9 + nanobot 18/19 测试通过。

### [#2656](https://github.com/Vispie-AI/VisPie_backend/pull/2656) fix(ci): recursive S3 sync for vio-gateway deploy (P0)
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：`vizbot-deploy-vio-gateway.yml` 用 `for f in .../*.js` flat glob 上传 S3，PR2 新增的 `lib/proxy.js` 子目录被静默跳过 → 容器 `ERR_MODULE_NOT_FOUND` 进入 crash loop，14 OpenClaw 租户 + manus-nanobot 全断 webhook。
- **修复**：换成 `aws s3 sync` 递归同步整个 gateway 目录（排除 test/、node_modules/），未来子目录零配置覆盖。
- **成果**：vio-gateway 恢复健康，所有租户 Lark webhook 通路恢复；约 10min 下线。

### [#2636](https://github.com/Vispie-AI/VisPie_backend/pull/2636) fix(amy): seed DeepSeek shadow with primary's session history
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：DeepSeek shadow 以全新会话运行，导致回复式对话缺少上下文，答复"没有之前的上下文"。
- **修复**：为 DeepSeek shadow 注入主模型的历史会话记录，使其具备上下文感知能力。
- **成果**：shadow 回复质量与主模型对齐，多轮对话连贯性显著提升。

### [#2598](https://github.com/Vispie-AI/VisPie_backend/pull/2598) fix(amy): align DeepSeek shadow budget with primary Opus
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：DeepSeek 影子模型超时/迭代上限远低于主模型 Opus，长问题在影子侧提前终止，A/B 对比数据失真。
- **修复**：将影子模型四项限制（超时/迭代/上下文等）对齐至与主模型 Opus 相同量级。
- **成果**：A/B 对比面板在长复杂问题场景下可正常完成 DeepSeek 侧的完整推理。

### [#2591](https://github.com/Vispie-AI/VisPie_backend/pull/2591) hotfix(env): drop PR_NOTIFY_TITLE — unblocks deploy-ec2
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：`PR_NOTIFY_TITLE` 环境变量值含空格，`deploy-ec2` CI 步骤解析失败，#2587 后所有主分支部署中断。
- **修复**：删除 `PR_NOTIFY_TITLE` 环境变量，解除对 `deploy-ec2` 工作流的阻塞。
- **成果**：主分支 EC2 自动部署恢复正常，CI 流水线不再因含空格的环境变量报错。

### [#2524](https://github.com/Vispie-AI/VisPie_backend/pull/2524) fix(amy): v3 — explicit shadow tool deny-list (config gates bypassed in prod)
- **日期**：2026-04-26 | **状态**：✅ 已合并
- **问题**：DeepSeek shadow v2 的配置层禁用在生产中被绕过，shadow agent 实际调用了 shell 命令，存在双写风险。
- **修复**：新增 `_SHADOW_BLOCKED_TOOLS` 常量，在注册默认工具后立即 unregister exec/message/spawn。
- **成果**：89 个测试通过，pinning 测试确保危险工具不会被意外恢复到 shadow agent。

### [#2506](https://github.com/Vispie-AI/VisPie_backend/pull/2506) fix(auto-amy): switch weekly cost card to purple
- **日期**：2026-04-26 | **状态**：✅ 已合并
- **问题**：weekly cost 卡片用蓝色与 daily 的靛蓝色辨识度低，用户希望在 Lark group 一眼区分三种节奏。
- **修复**：`REPORT_TYPE_DEFAULTS["weekly"]["template"]` 改为 purple，同步更新 README 配色表与对应测试 `test_template_purple`。
- **成果**：daily 靛/weekly 紫/monthly 绿三档颜色清晰可分；41/41 测试通过；立即热修 prod `/opt/amy/scripts` 让用户当天看到效果。

### [#2396](https://github.com/Vispie-AI/VisPie_backend/pull/2396) fix(vio-liveness): match plain 'node index.js' for vio-gateway L2 check
- **日期**：2026-04-23 | **状态**：✅ 已合并
- **问题**：L2 进程存活检测使用错误的 grep 关键词，无法匹配实际进程命令。
- **修复**：将 L2 检测条件改为匹配 `node index.js`，与实际进程命令保持一致。
- **成果**：vio-gateway 存活监控准确率恢复正常，误报问题消除。

### [#2376](https://github.com/Vispie-AI/VisPie_backend/pull/2376) fix(auto-amy): apply 1.1x US inference-profile multiplier
- **日期**：2026-04-22 | **状态**：✅ 已合并
- **问题**：AWS Bedrock实际费率比Anthropic直连高约10%，但费用追踪未作修正。
- **修复**：在美区推理配置文件费用计算中统一加入1.1倍乘数系数。
- **成果**：成本追踪数据与实际AWS账单对齐，提升计费准确性。

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

### [#4275](https://github.com/Vispie-AI/VisPie_backend/pull/4275) feat: user_dissatisfaction LLM-as-judge for Amy fleet
- **日期**：2026-06-07 | **状态**：✅ 已合并
- **问题**：Amy 对话无法自动识别用户不满信号。
- **修复**：Langfuse 新增 LLM-as-judge 评估器，对 trace 打分 0–1。
- **成果**：回测 30 条全部通过，精确率 100%，阈值 0.5。
### [#4189](https://github.com/Vispie-AI/VisPie_backend/pull/4189) feat(creative-agent): Part 2 paid competitor-ad intelligence — backend endpoint repair + paid pipeline + gate mode
- **日期**：2026-06-03 | **状态**：✅ 已合并
- **问题**：Amy付费创作方案只读HTML广告文案未逐帧分析视频；后端API路径和参数与Provider不符导致404或参数被静默忽略。
- **修复**：修复广告客户端路径和参数命名，新增pull-competitor-ads.py付费广告拉取脚本，self-check.js支持--mode paid，SKILL.md新增有机/付费意图分叉逻辑。
- **成果**：付费模式测试14/14通过，有机模式回归不变，每个Format现包含广告库链接和Why top证明及帧分析。

### [#4185](https://github.com/Vispie-AI/VisPie_backend/pull/4185) feat(creative-agent): pipeline-driven gameplan grounding — entry pipeline + publish gate
- **日期**：2026-06-03 | **状态**：✅ 已合并
- **问题**：Amy生成的创作方案使用通用知识而非实际视频分析，两个生产日志证明doc-only方式双向失效。
- **修复**：新增pull-viral-videos.py入口pipeline和publish-gameplan.sh发布门禁脚本，改写SKILL.md为157行操作卡，强制正确路径最短、错误路径硬退出。
- **成果**：self-check测试13/13通过，发布门禁拦截不达标内容，实测从67s/2工具提升至331s/30工具的完整分析。
### [#4050](https://github.com/Vispie-AI/VisPie_backend/pull/4050) feat(amy): cut Multica skill friction — one-shot recipe + anti-detour rules
- **日期**：2026-05-31 | **状态**：✅ 已合并
- **问题**：Amy 创建单个 Multica issue 需 15 次工具调用，技能文档引导不足导致反复绕路。
- **修复**：将技能文档重写为行动优先，提供直接可用的 curl 示例，并明确列出禁止绕路规则。
- **成果**：显式覆盖常见误操作路径，LLM 绕路概率降低，单次创建工单所需调用数大幅减少。

### [#4045](https://github.com/Vispie-AI/VisPie_backend/pull/4045) feat(amy): give Amy a Multica issue-creation skill + token
- **日期**：2026-05-31 | **状态**：✅ 已合并
- **问题**：Amy 缺少 Multica API 令牌和技能文档，无法自主在 vizzy 工作区创建任务工单。
- **修复**：注入 `MULTICA_API_TOKEN` 至部署流程，并新增 multica 技能描述 API 合约与字段枚举。
- **成果**：Amy 可用现有 shell/web_fetch 操作 Multica，技能通过环境变量门控，令牌缺失时自动隐藏。
### [#3940](https://github.com/Vispie-AI/VisPie_backend/pull/3940) feat(amy-codex): auto-reset codex session after 30min of chat idle
- **日期**：2026-05-28 | **状态**：✅ 已合并
- **问题**：PR #3931 引入会话续连后，空闲数小时的对话仍会恢复旧线程，导致模型携带过期上下文污染。
- **修复**：为 `chat_id → thread_id` 映射增加 30 分钟空闲过期机制，超时后自动冷启动新 Codex 会话。
- **成果**：用户重返时获得全新会话，消除陈旧上下文污染，同时正常连续多轮对话体验不受影响。
### [#3887](https://github.com/Vispie-AI/VisPie_backend/pull/3887) Pet-nutrition video agent (hackathon demo)
- **日期**：2026-05-27 | **状态**：🔀 待合并
- **问题**：需要一个能将品牌 URL 转换为 30–60s 竖版付费社媒视频的端到端演示 Pipeline。
- **修复**：实现五阶段流水线（策略师→导演→生成→剪辑→评审），前端通过 SSE 实时驱动各面板，QC 由 Gemini 多模态打分。
- **成果**：Hackathon 演示可运行，Demo Stub 模式下无需 API Key 即可在约 30 秒内完成全流程。
### [#3876](https://github.com/Vispie-AI/VisPie_backend/pull/3876) feat(amy-codex): expose lark-messages skill to fix reply-to drift
- **日期**：2026-05-26 | **状态**：✅ 已合并
- **问题**：Amy-Codex 容器工具面仅有文件系统工具，引用回复时无法获取父消息内容，Codex 误用 /workspace 残留文件产生严重上下文漂移，输出了与实际任务毫不相关的 Manus Canvas 分析。
- **修复**：在 Dockerfile 中 COPY lark-messages 技能并安装依赖，在提示词头部注入可用技能清单，引导 Codex 优先调用 `lark-messages --message_id` 获取引用上下文。
- **成果**：Amy-Codex 容器具备主动查询 Lark 历史消息的能力，为服务端进一步加固（#3877）提供工具基础。

### [#3875](https://github.com/Vispie-AI/VisPie_backend/pull/3875) feat(amy-codex): resolve Lark open_id to display name in Langfuse traces
- **日期**：2026-05-26 | **状态**：✅ 已合并
- **问题**：amy-codex Langfuse 追踪的 `user_id` 字段显示原始 `ou_…` open_id，导致 Langfuse Users/Sessions 面板完全不可读。
- **修复**：在 `lark_client.py` 中新增带 1 小时 TTL 缓存的异步 `resolve_user_display_name`，调用飞书 Contact API 将 open_id 解析为显示名，失败时兜底返回原始 open_id 确保不阻断追踪。
- **成果**：Langfuse 用户列表显示可读的姓名，open_id 仍保存在 metadata 中供精确查询，与 nanobot 路径行为对齐。

### [#3864](https://github.com/Vispie-AI/VisPie_backend/pull/3864) feat(vio): langfuse-tracer hook for StudyX + SheSaid observability
- **日期**：2026-05-26 | **状态**：✅ 已合并
- **问题**：Vio 租户（StudyX、SheSaid）缺乏对话级可观测性，无法在 Langfuse 中追踪用户消息和工具调用链路，影响问题排查和效果分析。
- **修复**：新增 OpenClaw `langfuse-tracer` 钩子，监听 agent:bootstrap/notification/tool 事件，每条用户消息生成一条 Langfuse trace（工具调用为子 span），通过白名单仅对 studyx 和 shesaid 生效，fire-and-forget 设计不影响代理性能。
- **成果**：StudyX 和 SheSaid 对话数据可在 Langfuse 实时可视化，支持按 agent_slug/platform/chat_id 过滤分析。

### [#3832](https://github.com/Vispie-AI/VisPie_backend/pull/3832) feat: Langfuse agent eval P0
- **日期**：2026-05-25 | **状态**：✅ 已合并
- **问题**：fleet 缺 Langfuse 集成，用户反馈无法转为 trace 评分。
- **修复**：注入密钥，trace_id 串联反馈按钮，新增 DB 字段。
- **成果**：11 个机器人接入，反馈自动写分，历史数据可回填。
### [#3779](https://github.com/Vispie-AI/VisPie_backend/pull/3779) feat(amy-codex): pilot switch buttons (codex ⇄ nanobot, runtime opt-in/opt-out)
- **日期**：2026-05-22 | **状态**：✅ 已合并
- **问题**：飞行员用户在 codex 与 nanobot 之间切换需要修改 fleet.json 并等待 PR 合并，无法实时操作。
- **修复**：在 amy-codex 和 nanobot 卡片上为飞行员添加切换按钮，通过容器间 HTTP 转发实现运行时路由切换。
- **成果**：飞行员可一键切换 AI 框架，下条消息立即生效，大幅降低 A/B 测试调试成本。

### [#3774](https://github.com/Vispie-AI/VisPie_backend/pull/3774) feat(amy-codex): expand pilot — add George Gui + Zane Wang to codex bucket
- **日期**：2026-05-22 | **状态**：✅ 已合并
- **问题**：codex 飞行员仅 Zuocan Ying 一人，端到端测试覆盖面不足。
- **修复**：在 `fleet.json` 的 `sender_overrides` 中新增 George Gui 和 Zane Wang 的 open_id，路由至 amy-codex 容器。
- **成果**：codex 飞行员扩展至 3 人，ab_split.percent 保持为 0，其余用户不受影响。

### [#3764](https://github.com/Vispie-AI/VisPie_backend/pull/3764) feat(amy-codex): stability tuning — high effort, 300s timeout, collapsible Activity
- **日期**：2026-05-22 | **状态**：✅ 已合并
- **问题**：飞行员首次测试"写飞书文档"时，因 180s 超时加 `xhigh` 推理耗尽预算，导致 0 次工具调用即结束。
- **修复**：将默认推理强度从 `xhigh` 改为 `high`，超时提升至 300s，Activity 区域改为可折叠面板。
- **成果**：飞书文档等多步任务可在约 60s 内完成，卡片 UX 与 nanobot 保持一致。

### [#3747](https://github.com/Vispie-AI/VisPie_backend/pull/3747) feat(amy-codex): pilot routing — Zuocan Ying → codex bucket via sender_overrides
- **日期**：2026-05-22 | **状态**：✅ 已合并
- **问题**：ab_split.percent 设为 0 后，无法在保持其余用户走 nanobot 的同时对 codex 进行端到端测试。
- **修复**：在 `fleet.json` 的 `sender_overrides` 中添加 Zuocan Ying 的 open_id，使其绕过 ab_split 直接路由到 amy-codex。
- **成果**：飞行员可独立验证反馈按钮、Doctor 链接、飞书文档技能等所有 codex 功能，不影响生产流量。

### [#3743](https://github.com/Vispie-AI/VisPie_backend/pull/3743) feat(amy-codex): feedback buttons + Doctor link + feishu-doc skill (parity gaps 1-3)
- **日期**：2026-05-22 | **状态**：✅ 已合并
- **问题**：30% A/B 测试期间发现 codex 分桶缺少反馈按钮、Activity Doctor 链接和飞书文档技能，与 nanobot 存在明显功能差距。
- **修复**：移植 `build_feedback_buttons`、`build_trace_debug_footer`，并在 Dockerfile 中集成 feishu-doc Node 技能及对应环境变量。
- **成果**：codex 分桶与 nanobot 卡片功能达到同等水平，具备重新启动 A/B 测试的前置条件。
### [#3725](https://github.com/Vispie-AI/VisPie_backend/pull/3725) feat(amy-codex): Day 6 — 30% A/B cutover + post-verify + RUNBOOK
- **日期**：2026-05-21 | **状态**：✅ 已合并
- **问题**：Day 1-5管道全部合并部署后，需正式开启A/B流量并建立观测与回滚机制。
- **修复**：将ab_split.percent从0调整为30，新增后验证脚本amy_codex_post_verify.py与运维手册RUNBOOK.md。
- **成果**：30%真实Amy流量路由至codex桶，A/B实验正式启动，具备一键回滚能力。

### [#3724](https://github.com/Vispie-AI/VisPie_backend/pull/3724) feat(amy-codex): Day 5 — daily judge + followup detector + dashboard doc
- **日期**：2026-05-21 | **状态**：✅ 已合并
- **问题**：A/B两桶的回复质量缺乏自动化评判与用户跟进行为检测，无法支撑SPEC D.2/D.3层评估。
- **修复**：新增独立可cron运行的每日Gemini评判脚本、5分钟跟进检测脚本及Langfuse仪表盘文档。
- **成果**：评估管道Layer 2/3就绪，可按天积累judge数据对比两桶回复质量。

### [#3722](https://github.com/Vispie-AI/VisPie_backend/pull/3722) feat(amy-codex): Day 4 — Langfuse tracing on both buckets
- **日期**：2026-05-21 | **状态**：✅ 已合并
- **问题**：A/B两桶缺乏统一的可观测性，无法在同一Langfuse实例中对比延迟、成本与错误率。
- **修复**：amy-codex新增langfuse_client封装及webhook追踪，amy-nanobot的LangfuseHook增加桶参数，均向同一自托管Langfuse v3写入追踪数据。
- **成果**：两桶Trace同步上报，运维可在Langfuse中进行并排对比分析。

### [#3720](https://github.com/Vispie-AI/VisPie_backend/pull/3720) feat(amy-codex): Day 3 — amy-gateway ab_split + nanobot bucket footer
- **日期**：2026-05-21 | **状态**：✅ 已合并
- **问题**：A/B路由逻辑尚未在网关实现，nanobot桶也缺少桶标签，无法对用户透明展示所在桶。
- **修复**：amy-gateway基于SHA1哈希实现确定性A/B分桶路由，nanobot回复卡片渲染桶Footer（开关默认关闭）。
- **成果**：Day 3管道就绪，ab_split开关可一行配置切换，两桶均有可视化桶标签。

### [#3718](https://github.com/Vispie-AI/VisPie_backend/pull/3718) feat(amy-codex): Day 2 — MCP memory_search + Amy workspace + identity context
- **日期**：2026-05-21 | **状态**：✅ 已合并
- **问题**：amy-codex缺少记忆检索能力、Amy工作区文件访问及身份上下文注入，无法复现nanobot的核心对话能力。
- **修复**：通过官方Python SDK接入MCP memory_search，只读挂载Amy工作区至/amy-workspace，并将SOUL/USER/DAO等身份上下文注入到codex提示词前缀。
- **成果**：codex桶具备记忆、工作区访问与身份感知能力，Day 2里程碑完成。

### [#3691](https://github.com/Vispie-AI/VisPie_backend/pull/3691) feat(amy-codex): Day 1 — /lark/webhook A/B bucket target
- **日期**：2026-05-21 | **状态**：✅ 已合并
- **问题**：amy-codex容器尚不具备接收Lark Webhook事件的能力，无法作为A/B实验的目标端点。
- **修复**：新增POST /lark/webhook端点，立即ACK后通过BackgroundTask异步处理，含bot自循环过滤、幂等消息ID检测及桶Footer标注。
- **成果**：amy-codex可独立接收并处理Lark事件，Day 1零流量安全就绪，经代码审查后加固7项问题。
### [#3595](https://github.com/Vispie-AI/VisPie_backend/pull/3595) feat(amy-codex): show reasoning effort in shadow panel label
- **日期**：2026-05-20 | **状态**：✅ 已合并
- **问题**：操作人员无法从Lark卡片直观看出Amy Codex影子回答使用了哪个推理强度档位。
- **修复**：在Lark影子卡片标签中展示当前`reasoning_effort`档位（如xhigh/high/medium等）。
- **成果**：操作人员可一眼判断Codex影子答案的推理强度来源，提升问题排查效率。

### [#3594](https://github.com/Vispie-AI/VisPie_backend/pull/3594) feat(amy-codex): expose reasoning_effort env, default xhigh
- **日期**：2026-05-20 | **状态**：✅ 已合并
- **问题**：Amy Codex缺少推理强度开关，无法灵活控制Codex CLI在框架对比阶段的推理深度。
- **修复**：新增`AMY_CODEX_REASONING_EFFORT`环境变量，支持six档位（none到xhigh），默认值为xhigh。
- **成果**：Codex CLI代理可以最高推理强度运行Phase 1对比，操作人员也可按需降低档位节省资源。

### [#3559](https://github.com/Vispie-AI/VisPie_backend/pull/3559) feat(amy-codex): reuse nanobot's Codex OAuth token via on-disk translation
- **日期**：2026-05-19 | **状态**：✅ 已合并
- **问题**：amy-codex 需要独立的 device-auth 流程，且两端共享刷新令牌存在 OAuth 竞争导致 401 错误。
- **修复**：容器启动及每次 /run 调用时自动将 nanobot 的 OAuth 格式转换为 Codex CLI 原生格式，并将 last_refresh 置为当前时间阻止 CLI 自行刷新。
- **成果**：amy-codex 复用 nanobot 的已刷新令牌，端到端 smoke test 首次成功返回真实模型响应。

### [#3545](https://github.com/Vispie-AI/VisPie_backend/pull/3545) feat(amy-codex): Codex CLI framework shadow alongside DeepSeek
- **日期**：2026-05-19 | **状态**：✅ 已合并
- **问题**：现有 shadow 仅替换 LLM 提供商，无法对比 Codex CLI 框架本身的 agent 行为与 nanobot 框架的差异。
- **修复**：新增 amy-codex sidecar 容器（FastAPI + codex exec），并在 nanobot_server.py 中接入独立 shadow 触发门，支持与 DeepSeek shadow 并行运行。
- **成果**：可在相同用户消息下同时运行 Codex CLI 与 nanobot 框架的 shadow 对比，Lark 卡片展示双路 shadow 面板。
### [#3450](https://github.com/Vispie-AI/VisPie_backend/pull/3450) feat(amy): Lark open_id 解析为显示名
- **日期**：2026-05-18 | **状态**：✅ 已合并
- **问题**：Langfuse User 标签显示原始 open_id，无法识别具体用户。
- **修复**：新增含 TTL 缓存的解析器，将 open_id 映射为「姓名(ou_短码)」，失败回退原值。
- **成果**：Langfuse 用户标签变为可读，API 故障时追踪写入不中断。

### [#3429](https://github.com/Vispie-AI/VisPie_backend/pull/3429) feat(amy): wire LangfuseHook into nanobot agent loop + fix pkg install
- **日期**：2026-05-17 | **状态**：✅ 已合并
- **问题**：第一阶段仅添加脚手架，Langfuse 未接入 agent 循环，且 Dockerfile 缺少依赖包。
- **修复**：新增 LangfuseHook 并接入 Lark 消息处理器，修复 Dockerfile 中 langfuse 包未安装问题。
- **成果**：每次 LLM 调用均生成含 user_id/session_id 的 trace，设置三个环境变量即可启用，不影响其余 12 个 bot。

### [#3428](https://github.com/Vispie-AI/VisPie_backend/pull/3428) feat(amy): add Langfuse v2 self-host scaffold for LLM observability
- **日期**：2026-05-17 | **状态**：✅ 已合并
- **问题**：Amy nanobot 缺乏 LLM 调用可观测性，无法追踪 Prompt 数据与 token 成本。
- **修复**：引入 Langfuse v2 自托管 scaffold，部署双容器服务栈并添加无副作用初始化模块。
- **成果**：未配置环境变量时完全无操作，合并至主线不影响生产，为第二阶段埋点奠定基础。

### [#3122](https://github.com/Vispie-AI/VisPie_backend/pull/3122) feat(vio-gateway): auto-register chat_route on first ISV cross-app webhook
- **日期**：2026-05-09 | **状态**：✅ 已合并
- **问题**：ISV 机器人可加入任意群组，运营无法提前在 `chat_routes` 中枚举所有 `chat_id`，首条消息因无路由而静默丢弃。
- **修复**：首次收到未知 `chat_id` 的 ISV webhook 时，根据 `app_id` 自动匹配代理并 UPSERT 路由记录，成功后热更新内存缓存。
- **成果**：ISV 新群组无需手动 SQL，机器人邀入后首条 @消息即可自动触发回复，10 项新测试全部通过。

### [#3046](https://github.com/Vispie-AI/VisPie_backend/pull/3046) feat(auto-amy): FallbackLLMProvider Lark alert + cost-report fallback row
- **日期**：2026-05-07 | **状态**：✅ 已合并
- **问题**：`FallbackLLMProvider` 切换至 Bedrock 备用路径时无任何实时告警，每日约 28 次降级调用（~$15/天）完全不可见。
- **修复**：新增每主模型 30 分钟冷却期 Lark 告警，并在 `usage_events` 中标记 `triggered_by=primary_failure` 以供成本报告统计。
- **成果**：降级事件实时可见，成本报告新增 Fallback 行，12 项新单元测试全部通过。

### [#3041](https://github.com/Vispie-AI/VisPie_backend/pull/3041) feat(auto-amy): wire UsageMeter for Codex OAuth — token usage only
- **日期**：2026-05-07 | **状态**：✅ 已合并
- **问题**：`OpenAICodexProvider` 缺少用量上报链路，每日 1500+ 次 Codex 调用在 `usage_events` 中完全不可见，成本报告严重低估。
- **修复**：`consume_sse` 解析 usage 字段，`_emit_codex_usage()` 以 `provider=openai_codex_oauth` 写入用量事件，成本报告新增 Codex token 明细行。
- **成果**：Codex 调用量数据入库，成本卡显示真实 token 用量，54 项现有测试全部通过。

### [#2964](https://github.com/Vispie-AI/VisPie_backend/pull/2964) feat(nanobot): session-aware memory tier loading (DM/group isolation, P1)
- **日期**：2026-05-06 | **状态**：✅ 已合并
- **问题**：nanobot 在 group 会话冷启动时也加载 `MEMORY.md` 等 DM-only 私有 memory，曾导致私人信息泄漏到群（三类 memory 污染案例之一）。
- **修复**：新增 `system_context.py`，按 channel type 选择 tier —— DM 加载 base+MEMORY.md+USER_MEMORY，group 仅 base，unknown 失败关闭；与 OpenClaw `session-memory-isolation` hook 对齐。
- **成果**：DM/group memory 结构性隔离，关闭一例 memory 污染来源；附 reset_caches 支持单测。

### [#2961](https://github.com/Vispie-AI/VisPie_backend/pull/2961) feat(nanobot): active memory_search tool with chat scope (P0 follow-up to #2741)
- **日期**：2026-05-06 | **状态**：✅ 已合并
- **问题**：PR #2741 关掉 passive RAG memory injection 后 nanobot 失去语义召回能力，只能 grep 做精确匹配，老的"active search tool to follow"承诺一直没兑现。
- **修复**：新增 `memory_search(query, scope, limit)` 工具调 Insforge pgvector，默认 `scope="current_chat"` 用 chat_id 过滤，`"all"` 显式跨 chat；agent loop 通过 `_set_tool_context` 注入 channel/chat_id。
- **成果**：agent 重获语义 memory 召回且默认按 chat 隔离，SKILL.md 教学何时用 memory_search vs grep；保留写侧 namespace 隔离与 hybrid 检索作为后续工作。

### [#2735](https://github.com/Vispie-AI/VisPie_backend/pull/2735) feat(shadow-judge): upgrade judge model to gemini-3-pro-preview
- **日期**：2026-05-01 | **状态**：✅ 已合并
- **问题**：Shadow Arena使用gemini-2.5-flash评判，频繁产生winner:error，判决质量低。
- **修复**：将公平裁判模型从gemini-2.5-flash升级为gemini-3-pro-preview。
- **成果**：判决质量提升，error率降低，A/B对比结果更具参考价值。

### [#2657](https://github.com/Vispie-AI/VisPie_backend/pull/2657) feat(vio-gateway): app_ticket fan-out for ISV nanobot tenants (vio-isv-lark PR3/3)
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：gateway `parseLarkWebhook` 无 chat_id 时把 `app_ticket` 事件落入 unknown 分支静默丢弃，ISV token chain 断裂。
- **修复**：新增 app_ticket 识别分支，按 `app_id` 扇出到所有匹配的 ISV nanobot 租户容器；ticket 由 PR1 在容器侧原子持久化。
- **成果**：cli_a939bf store app + 未来 ISV 安装企业的 token chain 解锁，gateway 9/9 node:test 通过。

### [#2653](https://github.com/Vispie-AI/VisPie_backend/pull/2653) feat(vio-gateway): Lark raw-body + sig header passthrough (vio-isv-lark PR2/2)
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：gateway `JSON.stringify(body)` 重序列化破坏 `sha256(timestamp+nonce+verification_token+body)` 校验，ISV nanobot 无法验签；"未配置"兜底回复会污染不属于本网关的 ISV 群。
- **修复**：Lark 路径改用 `incomingRawBody` 原字节转发并强制要求；passthrough `x-lark-request-timestamp/-nonce/-signature` headers；兜底回复加 `body.header.app_id === LARK_APP_ID` 守卫；重构 `proxyToAgent` 到 `lib/proxy.js` 工厂方便单测。
- **成果**：cli_a939bf store-app webhook 可在 ISV 容器完成 HMAC 校验；老 OpenClaw vizzy-lark + manus-nanobot 零回归（无 `LARK_VERIFICATION_TOKEN` 时跳过验签）。

### [#2648](https://github.com/Vispie-AI/VisPie_backend/pull/2648) feat(lark): ISV mode for store-app token chain (vio-isv-lark PR1/2)
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：Vio 要接 Lark store app（cli_a939bf）做 ISV 多租户分发，缺 `app_id+secret+app_ticket → app_access_token → tenant_access_token` 的 token chain。
- **修复**：`lark/client.py` 新增 `LARK_APP_TYPE=isv` 路径，`app_ticket` 原子写入 `/data/state/lark_app_ticket.json` 防容器重启锁死；`tenant_key` 通过 ContextVar 流转保持 caller 签名不变；`/webhook` 加 app_ticket 事件 + tenant_key 注入 shim；`LARK_DOMAIN` env 驱动。
- **成果**：mitchell+amy+eva+11 skin-bots+manus-nanobot SelfBuild 零回归；store-app ISV 接入容器侧 token chain 完成，配合 PR #2653 完成端到端；SPEC 经 Codex 4 轮 review 抓出 14 个真 bug。

### [#2634](https://github.com/Vispie-AI/VisPie_backend/pull/2634) feat(shadow): Gemini 2.5 Flash judge + DeepSeek shadow A/B
- **日期**：2026-04-29 | **状态**：✅ 已合并
- **问题**：原公平评判器使用 DeepSeek-chat 能力有限，需切换至更强模型并恢复 DeepSeek shadow 流。
- **修复**：将评判器切换为 Gemini 2.5 Flash，并将 DeepSeek shadow 接入 A/B 评判管道。
- **成果**：评判质量提升，DeepSeek shadow A/B 测试机制完善，多模型对比能力增强。

### [#2587](https://github.com/Vispie-AI/VisPie_backend/pull/2587) feat(github-webhook): PR group commit cards — replace Zapier
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：Zapier 试用到期不续，每次 master squash-merge 给 PR group 发"Back-end Prod New Commit"卡片这条能力要内置替换。
- **修复**：复用 `/api/v1/webhooks/github/push` 端点新增 `_send_pr_group_commit_cards`，从 `INTERN_MAP` 解析 author 做 `<at>` mention，按 `PR_NOTIFY_BRANCH` 白名单触发，失败兜底不重抛防 GitHub 重试双写 Bitable。
- **成果**：master 每个 squash 合并自动发 commit 卡片到 PR group `oc_05471855...`，完全脱离 Zapier；同时新增 4 个环境变量配置入口。

### [#2566](https://github.com/Vispie-AI/VisPie_backend/pull/2566) feat(amy): show trace_id in shadow panels for log correlation
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：禁用 OpenClaw shadow (#2527) 后 DeepSeek shadow panel 不显示 trace_id，运维只能靠时间戳猜 trace 来 `docker logs amy-nanobot | grep <trace>` 做关联。
- **修复**：`auto-amy/lark/cards.py::_append_shadow` 在 panel content 开头 prepend `` `trace={trace_id}` ``，仅当 `shadow.get("trace_id")` 有值时加，缺失 silent fallthrough。
- **成果**：DeepSeek + 未来任何 shadow source 自动显示 trace_id，跟老 OpenClaw shadow panel 风格一致；93 个相关测试 pass。

### [#2522](https://github.com/Vispie-AI/VisPie_backend/pull/2522) feat(amy): DeepSeek shadow v2 — full sandboxed AgentLoop with tools
- **日期**：2026-04-26 | **状态**：✅ 已合并
- **问题**：v1 的 DeepSeek shadow 是单轮对话且无工具调用，与 Bedrock AgentLoop 的对比不公平。
- **修复**：v2 为 DeepSeek 提供与 Bedrock 相同工具集的沙箱 AgentLoop，使用独立临时目录和独立 MessageBus 屏蔽对外输出。
- **成果**：82 个测试通过，DeepSeek shadow 现可进行多轮工具调用，费用自动计入每日/周/月报告。

### [#2514](https://github.com/Vispie-AI/VisPie_backend/pull/2514) feat(amy): DeepSeek V4-Pro shadow panel — v1 single-turn
- **日期**：2026-04-26 | **状态**：✅ 已合并
- **问题**：要在 Amy 主答之外影子运行 DeepSeek V4-Pro 做 A/B 对比，并把 cost 接入日/周/月报表。
- **修复**：新增 `🔬 Shadow: deepseek-v4-pro` collapsible panel，in-process 同步调 DeepSeek，结果走老 `/shadow-result/{trace_id}` 管线无需改卡片渲染；`track_llm_call(provider="deepseek", metadata.is_shadow=True)` 自动拆 shadow vs primary 花费；默认 OFF，仅 amy-nanobot 通过 `DEEPSEEK_SHADOW=true` 开启。
- **成果**：v1 single-turn 验证端到端管线；13 fleet 共用镜像但其他 12 个容器短路；7 新+70 老测试全过；v2 sandboxed AgentLoop 留作 follow-up。

### [#2500](https://github.com/Vispie-AI/VisPie_backend/pull/2500) feat(auto-amy): weekly + monthly cost reports
- **日期**：2026-04-26 | **状态**：✅ 已合并
- **问题**：cost report 只有日报（#2355），缺乏 7d/30d 周月汇总，用户与运维要靠人工跑 SQL 估算。
- **修复**：CLI 新增 `--report-type {daily,weekly,monthly}`，`REPORT_TYPE_DEFAULTS` 表驱动 since_hours/template/chart 可见性，新增 `_render_daily_bar_chart` 与 `_window_label` 显示 "Today"/"Last 7 days"/"Last 30 days"，30d projection 通用化。
- **成果**：周报蓝（5/9 deploy）/月报绿同管线产出，daily 行为零变化；README 列三套 cron + 故障排查；新增 21 测试。

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

### [#4376](https://github.com/Vispie-AI/VisPie_backend/pull/4376) docs(amy-framework-eval): industry practice review x project audit (mid-term)
- **日期**：2026-06-09 | **状态**：🔀 待合并
- **问题**：Amy 框架评估规范（SPEC v0.5）缺乏与业界主流实践的系统性对比验证。
- **修复**：调研 8 个行业一手资料，逐条审计 SPEC v0.5 与 offline_runner 的设计与执行。
- **成果**：核心设计与 2026 年业界实践一致，明确标出评委标定集、负向任务等待补充的优先差距项。
### [#4134](https://github.com/Vispie-AI/VisPie_backend/pull/4134) docs(amy-framework-eval): framework A/B eval SPEC v0.3 + smoke runner
- **日期**：2026-06-02 | **状态**：🔀 待合并
- **问题**：缺少对 Amy 框架（codex CLI vs nanobot）A/B 评测方法论的系统文档，评测管道搭建缺乏依据。
- **修复**：沉淀 SPEC v0.3 方法论文档及 smoke_runner.py 参考脚本，明确离线沙盒台与在线 shadow 两条路径及三层判分逻辑。
- **成果**：评测方法论落地 git，为 20 任务数据集正式 runner 提供规范，无生产代码变更。

### [#3931](https://github.com/Vispie-AI/VisPie_backend/pull/3931) refactor(amy-codex): use Codex native session resume, drop Lark history fetch
- **日期**：2026-05-28 | **状态**：✅ 已合并
- **问题**：PR #3925 通过重新拉取 Lark 消息重建上下文，但 Codex CLI 已原生支持会话续连，属于重复造轮子。
- **修复**：改用 `codex exec resume <session_id>` 续接原生 Codex 会话，删除 Lark 历史拉取相关代码 127 行。
- **成果**：上下文保真度更高（含中间工具调用记录），代码量显著减少，整体架构更简洁。
### [#3669](https://github.com/Vispie-AI/VisPie_backend/pull/3669) docs(amy-codex): SPEC v2 — E2 use raw open_id, drop hashing
- **日期**：2026-05-21 | **状态**：✅ 已合并
- **问题**：SPEC v1要求对open_id进行SHA256哈希存入Langfuse，但在私有部署环境下哈希增加了调试成本而无实际安全收益。
- **修复**：更新SPEC为v2，E2改为直接存储raw open_id，同步修正D.1字段名（user_id_hash→user_open_id）和会话聚合规则。
- **成果**：Langfuse用户追踪更便于运维debug，跨系统与nanobot日志的关联不再依赖反向哈希表。

### [#3668](https://github.com/Vispie-AI/VisPie_backend/pull/3668) docs(amy-codex): SPEC for A/B bucketing + evaluation framework
- **日期**：2026-05-21 | **状态**：✅ 已合并
- **问题**：amy-codex A/B实验技术方案经多轮对齐后需要一份完整SPEC文档锁定所有设计决策，避免后续实施时重复讨论。
- **修复**：新增SPEC文档，锁定P1-P4（工具/卡片/流量/对照桶）与E1-E3（评判模型/用户ID/每日judge量）共7项关键决策。
- **成果**：后续实施PR均可引用本SPEC的模块ID，4层评估管道（自动指标→用户信号→Gemini评判→人工标注）设计完整落地。
### [#3596](https://github.com/Vispie-AI/VisPie_backend/pull/3596) ci(amy-nanobot): include amy_codex_shadow.py in deploy path filter
- **日期**：2026-05-20 | **状态**：✅ 已合并
- **问题**：CI路径过滤器仅监听`deepseek_shadow.py`，`amy_codex_shadow.py`变更后nanobot镜像不会自动重建。
- **修复**：将`amy_codex_shadow.py`加入CI部署路径过滤器，确保影子客户端变更能触发自动重建流程。
- **成果**：Amy Codex相关改动现在能正确触发nanobot镜像自动构建，持续集成覆盖更加完整。

### [#3560](https://github.com/Vispie-AI/VisPie_backend/pull/3560) ci(amy-nanobot): inject AMY_CODEX_SHADOW + AMY_CODEX_URL env
- **日期**：2026-05-19 | **状态**：✅ 已合并
- **问题**：nanobot_server.py 中控制 amy-codex shadow 触发的环境变量未注入 CI 部署配置，功能无法在生产中启用。
- **修复**：在 amy-nanobot 的 CI 工作流中添加 AMY_CODEX_SHADOW=true 和 AMY_CODEX_URL 两个环境变量。
- **成果**：amy-nanobot 部署后自动开启 Codex shadow，Lark 消息可触发 🔬 Shadow: codex-cli 面板。

### [#3556](https://github.com/Vispie-AI/VisPie_backend/pull/3556) ci(amy-codex): dedicated deploy workflow
- **日期**：2026-05-19 | **状态**：✅ 已合并
- **问题**：amy-codex 相关源文件的变更未触发任何自动部署，EC2 宿主机长期停留在 #3545 的提交状态。
- **修复**：新增专用 GitHub Actions 工作流，监听 amy-codex 相关路径变更后 SSH 登录 EC2 执行构建和部署脚本。
- **成果**：amy-codex sidecar 可随代码变更自动部署，健康检查通过后完成持续交付闭环。
### [#3430](https://github.com/Vispie-AI/VisPie_backend/pull/3430) chore(amy-deploy): inject LANGFUSE_* env vars into amy-nanobot
- **日期**：2026-05-17 | **状态**：✅ 已合并
- **问题**：CI/CD 部署时 heredoc 覆盖手动注入的 LANGFUSE 环境变量，每次部署后链路追踪失效。
- **修复**：在部署 workflow 的 heredoc 中写入三个 LANGFUSE 变量，值来自 GitHub Repo 变量/密钥。
- **成果**：每次自动部署后 amy-nanobot 均持久读取 LANGFUSE 配置，其他 12 个 bot 不受影响。

### [#3312](https://github.com/Vispie-AI/VisPie_backend/pull/3312) ci(mitchell): inject AMY_COST_REPORT_WEBHOOK from repo Variable on every deploy
- **日期**：2026-05-14 | **状态**：✅ 已合并
- **问题**：PR #3288 修了脚本侧空 webhook 退出，但 EC2 上 `.env` 仍靠手动 SSM 注入，首次部署的 `.env` fallback 可能覆盖手编值且无审计记录。
- **修复**：deploy workflow 新增 `ensure_env_line` helper，每次 SSH 部署从 repo Variable 注入 `AMY_COST_REPORT_WEBHOOK` 写入 mitchell 容器 `.env`。
- **成果**：成本报表 webhook 通过 CI/CD 持久化为单一来源，部署日志可审计，再无手工注入风险。

### [#3250](https://github.com/Vispie-AI/VisPie_backend/pull/3250) test(pr-bot): smoke — verify PR group restoration
- **日期**：2026-05-13 | **状态**：✅ 已合并
- **问题**：PR #3141 把 `PR_GROUP_CHAT_ID` 默认值从硬编码改成 `""`，新 deploy 流没在 repo 配 vars，Cloud Run 上为空导致 PR 通知卡静默不发。
- **修复**：在 GitHub Actions repository variables 加 `PR_GROUP_CHAT_ID=oc_05471855...`，发本 smoke PR 验证恢复后 PR group 是否再收到 commit 卡片。
- **成果**：PR group 重新收卡片即代表 GitHub webhook → Lark 链路全恢复，同时排查同类 fallback 漏洞清单待跟进。

### [#3039](https://github.com/Vispie-AI/VisPie_backend/pull/3039) docs(amy): add 'Grep First' anti-speculation rule to AGENTS.md
- **日期**：2026-05-07 | **状态**：✅ 已合并
- **问题**：Amy 在被问及代码中标签或变量含义时常凭已有认知推测而非查阅源码，产生置信但错误的回答。
- **修复**：在 `AGENTS.md` Data Source Map 块后新增"Don't Speculate — Grep First"强制规则（3 句话，+4/-0 行）。
- **成果**：Amy 处理标签含义查询时须先 `rg` 定位源码，减少幻觉，规则随每次 nanobot 会话启动时加载。

### [#2967](https://github.com/Vispie-AI/VisPie_backend/pull/2967) docs(amy): add Data Source Map to AGENTS.md — fix H2 DB confusion
- **日期**：2026-05-06 | **状态**：✅ 已合并
- **问题**：用户反馈 Amy 长期分不清后台几个数据库，AGENTS.md/SOUL.md 都没指向 `.env` 里关于 6 个数据存储（4 Supabase + RDS MySQL + RDS Postgres）的注解。
- **修复**：在 `AGENTS.md` 新增 Data Source Map 章节，列出每个 store 的角色、schema 差异，并明确"重复信息"是不同 store 的不同模型表（auth.users / vispie.users / profiles）。
- **成果**：Amy 回答数据问题时有了数据库定位索引，关闭一例长期 H2 误判，知识缺口而非能力缺口被对齐。

### [#2956](https://github.com/Vispie-AI/VisPie_backend/pull/2956) ci: auto-sync /opt/amy/scripts/amy_cost_daily_report.py via SSM
- **日期**：2026-05-06 | **状态**：✅ 已合并
- **问题**：cost report 脚本住在 amy EC2 host 而非 mitchell 容器内，`vizbot-deploy-amy-nanobot.yml` 的 paths 过滤排除 `auto-amy/scripts/*.py`，git 修复完 host 文件依旧老版本（5/6 incident 险些回归）。
- **修复**：新增 `Deploy Amy Host Scripts` workflow，master push 触及该文件时 base64 + `aws ssm send-command` 同步到 `/opt/amy/scripts/`，保留 5 份备份并校验 md5。
- **成果**：cost report 脚本 source-of-truth 进入 CI 流，自动同步 host 文件，关闭 5/6 类回归路径；README 增加 Deploy + 手动回滚 SOP。

### [#2693](https://github.com/Vispie-AI/VisPie_backend/pull/2693) ci: split shared deploy group — give Amy/Eva nanobot priority queue
- **日期**：2026-04-30 | **状态**：✅ 已合并
- **问题**：4 个部署 workflow 共享并发组，Amy/Eva Nanobot 频繁被取消，最坏延迟达 13 分钟。
- **修复**：Amy/Eva Nanobot 单独划为优先并发组，其余迁入次级组，两组各自串行互不影响。
- **成果**：Amy/Eva Nanobot 部署不再被取消，用户侧延迟降至纯部署耗时。

### [#2589](https://github.com/Vispie-AI/VisPie_backend/pull/2589) test(pr-bot): smoke — verify PR group commit-cards
- **日期**：2026-04-28 | **状态**：✅ 已合并
- **问题**：需要验证 #2587 引入的 PR 分组提交卡片机器人功能是否正确运行。
- **修复**：添加仅含 `docs/changelog/` 文件变更的烟雾测试 PR，触发机器人验证流程。
- **成果**：PR 机器人功能验证通过，零代码变更确认新机器人行为符合预期。

### [#2527](https://github.com/Vispie-AI/VisPie_backend/pull/2527) chore(amy): disable OpenClaw Opus 4.6 shadow — keep DeepSeek shadow only
- **日期**：2026-04-27 | **状态**：✅ 已合并
- **问题**：OpenClaw shadow 存在 AsyncLocalStorage 上下文丢失导致双写 Lark 文档的隐患，mentor 决定暂时关闭。
- **修复**：将 fleet.json 中 amy 条目的 `shadow.enabled` 由 true 改为 false，一行配置翻转，不删除代码。
- **成果**：OpenClaw shadow 停止发送，双写风险在生产层面消除，回滚只需翻转同一行。

### [#2515](https://github.com/Vispie-AI/VisPie_backend/pull/2515) docs(amy): close DeepSeek shadow v1 SPEC — deployed + lessons learned
- **日期**：2026-04-26 | **状态**：✅ 已合并
- **问题**：PR #2514 已上线但 SPEC 仍标 Draft，六轮 codex review 的多层 deploy 踩坑（race、cold-start、镜像、env_file、并发部署）没沉淀。
- **修复**：状态 Draft → Implemented（2026-04-26 14:19 UTC, SHA `6e85d44`），§7 acceptance 勾选已验证项，新增 §11 Lessons Learned 总结代码/部署/并发/过程四象限。
- **成果**：v2 (full sandboxed AgentLoop) 与未来 Gemini judge / GPT 选手作者可直接复用经验单，节省下一轮 review；docs-only，git diff 仅 1 文件 +47/-13。

### [#2349](https://github.com/Vispie-AI/VisPie_backend/pull/2349) chore(ci): remove 4 dead/deprecated workflow files
- **日期**：2026-04-21 | **状态**：✅ 已合并
- **问题**：仓库中存在 4 个超过 1 年未成功运行或已明确标记废弃的 GitHub Actions workflow 文件，增加维护负担。
- **修复**：直接删除 deploy-dev_yluo.yml 等 4 个死亡/废弃 workflow，未触及任何活跃部署路径。
- **成果**：CI 配置更整洁，减少误触发风险，仓库维护成本降低。

### [#2027](https://github.com/Vispie-AI/VisPie_backend/pull/2027) docs: 新增 auto-amy 架构双语系统地图
- **日期**：2026-04-12 | **状态**：✅ 已合并
- **内容**：新增 4 部分架构文档（英文 `docs/system-map/` + 中文 `docs/system-map-cn/`），共 8 个文件，涵盖模块地图、数据流图、调用链、部署拓扑。
- **成果**：系统架构知识文档化，降低团队新成员理解成本。

---

## 四、总览

| # | 日期 | 类型 | 状态 | 标题 | 一句话总结 |
|---|---|---|---|---|---|
| [#3312](https://github.com/Vispie-AI/VisPie_backend/pull/3312) | 2026-05-14 | misc | ✅ | ci(mitchell): inject AMY_COST_REPORT_WEBHOOK from repo Variable on every deploy | 成本报表 webhook 通过 CI/CD 持久化为单一来源，部署日志可审计，再无手工注入风险 |
| [#3298](https://github.com/Vispie-AI/VisPie_backend/pull/3298) | 2026-05-14 | fix | ✅ | fix(vio): hide feedback buttons by default for ISV apps (avoids code:200340) | ISV 用户点击不再报错，amy/eva/skin-bots 行为保持原样；9/9 单元测试覆盖各 env 组合 |
| [#3288](https://github.com/Vispie-AI/VisPie_backend/pull/3288) | 2026-05-14 | fix | ✅ | fix(cost-report): empty webhook short-circuits with rc=6 (don't None-poison urllib) | cost report 失败可观测可告警，配合 PR #3312 的 CI 注入彻底关闭这条静默丢失通路 |
| [#3250](https://github.com/Vispie-AI/VisPie_backend/pull/3250) | 2026-05-13 | misc | ✅ | test(pr-bot): smoke — verify PR group restoration | PR group 重新收卡片即代表 GitHub webhook → Lark 链路全恢复，同时排查同类 fallback 漏洞清单待跟进 |
| [#3153](https://github.com/Vispie-AI/VisPie_backend/pull/3153) | 2026-05-10 | fix | ✅ | fix(monitoring): amy-liveness L3 — auto-discover gateway port | L3 探针恢复返回 200，R1 告警停止误报，监控状态恢复正常 |
| [#3137](https://github.com/Vispie-AI/VisPie_backend/pull/3137) | 2026-05-10 | fix | ✅ | fix(vio): Gemini API key runtime-exposure mitigation (URL→header + per-call read) | 密钥不再出现于日志 URL，轮换后无需重启即可生效，12 项安全单元测试全部通过 |
| [#3122](https://github.com/Vispie-AI/VisPie_backend/pull/3122) | 2026-05-09 | feat | ✅ | feat(vio-gateway): auto-register chat_route on first ISV cross-app webhook | ISV 新群组无需手动 SQL，机器人邀入后首条 @消息即可自动触发回复，10 项新测试全部通过 |
| [#3120](https://github.com/Vispie-AI/VisPie_backend/pull/3120) | 2026-05-09 | fix | ✅ | fix(deploy-vio-tenant): allow nanobot-only tenants (no OpenClaw workspace) | ISV nanobot 专属租户可正常部署，vio-test 租户阻塞问题解除 |
| [#3119](https://github.com/Vispie-AI/VisPie_backend/pull/3119) | 2026-05-09 | fix | ✅ | fix(nanobot/lark): bypass local mention gate for ISV apps (follow-up to #3117) | ISV 租户群组消息成功触发 Hatchet 任务，机器人可在群组中正常回复，7 项新测试通过 |
| [#3117](https://github.com/Vispie-AI/VisPie_backend/pull/3117) | 2026-05-09 | fix | ✅ | fix(vio-gateway): trust Lark scope filter for ISV-app group webhooks | ISV 群组 @mention 成功转发，8 项新单元测试全部通过，SelfBuild 路径无回归 |
| [#3046](https://github.com/Vispie-AI/VisPie_backend/pull/3046) | 2026-05-07 | feat | ✅ | feat(auto-amy): FallbackLLMProvider Lark alert + cost-report fallback row | 降级事件实时可见，成本报告新增 Fallback 行，12 项新单元测试全部通过 |
| [#3041](https://github.com/Vispie-AI/VisPie_backend/pull/3041) | 2026-05-07 | feat | ✅ | feat(auto-amy): wire UsageMeter for Codex OAuth — token usage only | Codex 调用量数据入库，成本卡显示真实 token 用量，54 项现有测试全部通过 |
| [#3039](https://github.com/Vispie-AI/VisPie_backend/pull/3039) | 2026-05-07 | misc | ✅ | docs(amy): add 'Grep First' anti-speculation rule to AGENTS.md | Amy 处理标签含义查询时须先 `rg` 定位源码，减少幻觉，规则随每次 nanobot 会话启动时加载 |
| [#3003](https://github.com/Vispie-AI/VisPie_backend/pull/3003) | 2026-05-07 | fix | ✅ | fix(alerter): R1 schema guard — stop false-positive Critical from foreign liveness probes | cloud-run-fastapi 误报 Critical 告警消除，新增 1 项测试，共 67 项通过 |
| [#2967](https://github.com/Vispie-AI/VisPie_backend/pull/2967) | 2026-05-06 | misc | ✅ | docs(amy): add Data Source Map to AGENTS.md — fix H2 DB confusion | Amy 回答数据问题时有了数据库定位索引，关闭一例长期 H2 误判，知识缺口而非能力缺口被对齐 |
| [#2964](https://github.com/Vispie-AI/VisPie_backend/pull/2964) | 2026-05-06 | feat | ✅ | feat(nanobot): session-aware memory tier loading (DM/group isolation, P1) | DM/group memory 结构性隔离，关闭一例 memory 污染来源；附 reset_caches 支持单测 |
| [#2961](https://github.com/Vispie-AI/VisPie_backend/pull/2961) | 2026-05-06 | feat | ✅ | feat(nanobot): active memory_search tool with chat scope (P0 follow-up to #2741) | agent 重获语义 memory 召回且默认按 chat 隔离，SKILL.md 教学何时用 memory_search vs grep；保留写侧 namespace 隔离与 hybrid 检索作为后续工作 |
| [#2956](https://github.com/Vispie-AI/VisPie_backend/pull/2956) | 2026-05-06 | misc | ✅ | ci: auto-sync /opt/amy/scripts/amy_cost_daily_report.py via SSM | cost report 脚本 source-of-truth 进入 CI 流，自动同步 host 文件，关闭 5/6 类回归路径；README 增加 Deploy + 手动回滚 SOP |
| [#2945](https://github.com/Vispie-AI/VisPie_backend/pull/2945) | 2026-05-06 | fix | ✅ | fix(auto-amy): cost report — detect Lark biz errors, retry on rate-limit | 限流自动重试成功，永久错误立即 fail loud；cron-storm 错开后再未观察到 5/6 类静默漏发 |
| [#2740](https://github.com/Vispie-AI/VisPie_backend/pull/2740) | 2026-05-01 | fix | ✅ | fix(shadow-judge): align GeminiJudge URL regex with gemini-3-pro-preview | gateway 62/62 测试通过，CI 恢复绿，可继续 dev → prod 部署 |
| [#2739](https://github.com/Vispie-AI/VisPie_backend/pull/2739) | 2026-05-01 | fix | ✅ | fix(auto-amy): DeepSeek cost — cache-hit + active promo pricing | DeepSeek 月报从 \$565 修正为 ~\$22 真实值；附 `test_pricing_v4_pro_real_world_overestimate_fix` 用 prod 数据做回归 sentinel |
| [#2738](https://github.com/Vispie-AI/VisPie_backend/pull/2738) | 2026-05-01 | fix | ✅ | fix(shadow-judge): align GeminiJudge test assertion with gemini-3-pro-preview | Gateway Router build+test 通过，可触发 amy-gateway-prod 镜像滚动 |
| [#2735](https://github.com/Vispie-AI/VisPie_backend/pull/2735) | 2026-05-01 | feat | ✅ | feat(shadow-judge): upgrade judge model to gemini-3-pro-preview | 判决质量提升，error率降低，A/B对比结果更具参考价值 |
| [#2697](https://github.com/Vispie-AI/VisPie_backend/pull/2697) | 2026-04-30 | fix | ✅ | fix(ci): repair jq filter quoting in Deploy Vio Nanobot workflow | 部署 workflow 恢复正常，合并后自动补齐了落下的镜像更新 |
| [#2693](https://github.com/Vispie-AI/VisPie_backend/pull/2693) | 2026-04-30 | misc | ✅ | ci: split shared deploy group — give Amy/Eva nanobot priority queue | Amy/Eva Nanobot 部署不再被取消，用户侧延迟降至纯部署耗时 |
| [#2692](https://github.com/Vispie-AI/VisPie_backend/pull/2692) | 2026-04-30 | fix | ✅ | fix(amy): bump primary timeout 600s→1500s, Hatchet 15m→30m | Amy 可完整执行复杂多源任务，根因技能发现优化跟进中 |
| [#2690](https://github.com/Vispie-AI/VisPie_backend/pull/2690) | 2026-04-30 | fix | ✅ | fix(alert-card): action items use Vio host paths for Vio agents | Vio 告警操作指引指向正确主机，新增 4 个回归测试 |
| [#2671](https://github.com/Vispie-AI/VisPie_backend/pull/2671) | 2026-04-29 | fix | ✅ | fix(monitoring): nanobot L2 pattern + de-Amyfy alert card title | nanobot 容器健康时不再触发误报，告警卡片准确显示受影响的 agent 名称 |
| [#2668](https://github.com/Vispie-AI/VisPie_backend/pull/2668) | 2026-04-29 | fix | ✅ | fix(amy): synthesize reasoning_content for seeded assistant turns (3rd attempt) | DeepSeek shadow 有种子时不再触发 4xx，回复类场景（"撤回/不对"）恢复正常响应 |
| [#2667](https://github.com/Vispie-AI/VisPie_backend/pull/2667) | 2026-04-29 | fix | 🚫 | fix(amy): synthesize reasoning_content for seeded assistant turns (3rd attempt) | 本 PR 已关闭，核心修复思路通过无冲突的 #2668 落地 |
| [#2662](https://github.com/Vispie-AI/VisPie_backend/pull/2662) | 2026-04-29 | fix | ✅ | fix(amy): strip Bedrock thinking artifacts at shadow seed boundary | 种子边界处理更精准，不影响 DeepSeek 正常 thinking 轮次，测试覆盖 22 个用例 |
| [#2658](https://github.com/Vispie-AI/VisPie_backend/pull/2658) | 2026-04-29 | fix | ✅ | fix(lark-isv): correct app_ticket schema (V1 event_callback wrapper) | 真实 Lark app_ticket 推送可被识别，gateway 9/9 + nanobot 18/19 测试通过 |
| [#2657](https://github.com/Vispie-AI/VisPie_backend/pull/2657) | 2026-04-29 | feat | ✅ | feat(vio-gateway): app_ticket fan-out for ISV nanobot tenants (vio-isv-lark PR3/3) | cli_a939bf store app + 未来 ISV 安装企业的 token chain 解锁，gateway 9/9 node:test 通过 |
| [#2656](https://github.com/Vispie-AI/VisPie_backend/pull/2656) | 2026-04-29 | fix | ✅ | fix(ci): recursive S3 sync for vio-gateway deploy (P0) | vio-gateway 恢复健康，所有租户 Lark webhook 通路恢复；约 10min 下线 |
| [#2653](https://github.com/Vispie-AI/VisPie_backend/pull/2653) | 2026-04-29 | feat | ✅ | feat(vio-gateway): Lark raw-body + sig header passthrough (vio-isv-lark PR2/2) | cli_a939bf store-app webhook 可在 ISV 容器完成 HMAC 校验；老 OpenClaw vizzy-lark + manus-nanobot 零回归（无 `LARK_VERIFICATION_TOKEN` 时跳过验签） |
| [#2648](https://github.com/Vispie-AI/VisPie_backend/pull/2648) | 2026-04-29 | feat | ✅ | feat(lark): ISV mode for store-app token chain (vio-isv-lark PR1/2) | mitchell+amy+eva+11 skin-bots+manus-nanobot SelfBuild 零回归；store-app ISV 接入容器侧 token chain 完成，配合 PR #2653 完成端到端；SPEC 经 Codex 4 轮 review 抓出 14 个真 bug |
| [#2636](https://github.com/Vispie-AI/VisPie_backend/pull/2636) | 2026-04-29 | fix | ✅ | fix(amy): seed DeepSeek shadow with primary's session history | shadow 回复质量与主模型对齐，多轮对话连贯性显著提升 |
| [#2634](https://github.com/Vispie-AI/VisPie_backend/pull/2634) | 2026-04-29 | feat | ✅ | feat(shadow): Gemini 2.5 Flash judge + DeepSeek shadow A/B | 评判质量提升，DeepSeek shadow A/B 测试机制完善，多模型对比能力增强 |
| [#2598](https://github.com/Vispie-AI/VisPie_backend/pull/2598) | 2026-04-28 | fix | ✅ | fix(amy): align DeepSeek shadow budget with primary Opus | A/B 对比面板在长复杂问题场景下可正常完成 DeepSeek 侧的完整推理 |
| [#2591](https://github.com/Vispie-AI/VisPie_backend/pull/2591) | 2026-04-28 | fix | ✅ | hotfix(env): drop PR_NOTIFY_TITLE — unblocks deploy-ec2 | 主分支 EC2 自动部署恢复正常，CI 流水线不再因含空格的环境变量报错 |
| [#2589](https://github.com/Vispie-AI/VisPie_backend/pull/2589) | 2026-04-28 | misc | ✅ | test(pr-bot): smoke — verify PR group commit-cards | PR 机器人功能验证通过，零代码变更确认新机器人行为符合预期 |
| [#2587](https://github.com/Vispie-AI/VisPie_backend/pull/2587) | 2026-04-28 | feat | ✅ | feat(github-webhook): PR group commit cards — replace Zapier | master 每个 squash 合并自动发 commit 卡片到 PR group `oc_05471855...`，完全脱离 Zapier；同时新增 4 个环境变量配置入口 |
| [#2566](https://github.com/Vispie-AI/VisPie_backend/pull/2566) | 2026-04-28 | feat | ✅ | feat(amy): show trace_id in shadow panels for log correlation | DeepSeek + 未来任何 shadow source 自动显示 trace_id，跟老 OpenClaw shadow panel 风格一致；93 个相关测试 pass |
| [#2527](https://github.com/Vispie-AI/VisPie_backend/pull/2527) | 2026-04-27 | misc | ✅ | chore(amy): disable OpenClaw Opus 4.6 shadow — keep DeepSeek shadow only | OpenClaw shadow 停止发送，双写风险在生产层面消除，回滚只需翻转同一行 |
| [#2524](https://github.com/Vispie-AI/VisPie_backend/pull/2524) | 2026-04-26 | fix | ✅ | fix(amy): v3 — explicit shadow tool deny-list (config gates bypassed in prod) | 89 个测试通过，pinning 测试确保危险工具不会被意外恢复到 shadow agent |
| [#2522](https://github.com/Vispie-AI/VisPie_backend/pull/2522) | 2026-04-26 | feat | ✅ | feat(amy): DeepSeek shadow v2 — full sandboxed AgentLoop with tools | 82 个测试通过，DeepSeek shadow 现可进行多轮工具调用，费用自动计入每日/周/月报告 |
| [#2515](https://github.com/Vispie-AI/VisPie_backend/pull/2515) | 2026-04-26 | misc | ✅ | docs(amy): close DeepSeek shadow v1 SPEC — deployed + lessons learned | v2 (full sandboxed AgentLoop) 与未来 Gemini judge / GPT 选手作者可直接复用经验单，节省下一轮 review；docs-only，git diff 仅 1 文件 +47/-13 |
| [#2514](https://github.com/Vispie-AI/VisPie_backend/pull/2514) | 2026-04-26 | feat | ✅ | feat(amy): DeepSeek V4-Pro shadow panel — v1 single-turn | v1 single-turn 验证端到端管线；13 fleet 共用镜像但其他 12 个容器短路；7 新+70 老测试全过；v2 sandboxed AgentLoop 留作 follow-up |
| [#2506](https://github.com/Vispie-AI/VisPie_backend/pull/2506) | 2026-04-26 | fix | ✅ | fix(auto-amy): switch weekly cost card to purple | daily 靛/weekly 紫/monthly 绿三档颜色清晰可分；41/41 测试通过；立即热修 prod `/opt/amy/scripts` 让用户当天看到效果 |
| [#2500](https://github.com/Vispie-AI/VisPie_backend/pull/2500) | 2026-04-26 | feat | ✅ | feat(auto-amy): weekly + monthly cost reports | 周报蓝（5/9 deploy）/月报绿同管线产出，daily 行为零变化；README 列三套 cron + 故障排查；新增 21 测试 |
| [#2396](https://github.com/Vispie-AI/VisPie_backend/pull/2396) | 2026-04-23 | fix | ✅ | fix(vio-liveness): match plain 'node index.js' for vio-gateway L2 check | vio-gateway 存活监控准确率恢复正常，误报问题消除 |
| [#2395](https://github.com/Vispie-AI/VisPie_backend/pull/2395) | 2026-04-23 | feat | ✅ | feat(monitoring): extend liveness monitoring to Vio (dynamic + rollup) | Amy 和 Vio 均纳入统一存活监控，多服务健康状态汇总可见 |
| [#2379](https://github.com/Vispie-AI/VisPie_backend/pull/2379) | 2026-04-22 | feat | ✅ | feat(auto-amy): track Gemini + Nova Micro + embedding costs | LLM全链路成本实现完整监控，不再存在未追踪的模型调用盲区 |
| [#2376](https://github.com/Vispie-AI/VisPie_backend/pull/2376) | 2026-04-22 | fix | ✅ | fix(auto-amy): apply 1.1x US inference-profile multiplier | 成本追踪数据与实际AWS账单对齐，提升计费准确性 |
| [#2356](https://github.com/Vispie-AI/VisPie_backend/pull/2356) | 2026-04-21 | fix | ✅ | fix(auto-amy): correct Bedrock pricing — Opus was 3x too high | 每日 API 成本报告数据准确，不再虚报使用费用 |
| [#2355](https://github.com/Vispie-AI/VisPie_backend/pull/2355) | 2026-04-21 | feat | ✅ | feat(auto-amy): daily API cost report → Lark webhook | 团队每日自动收到全舰队 API 成本报告，成本追踪反馈闭环正式形成 |
| [#2354](https://github.com/Vispie-AI/VisPie_backend/pull/2354) | 2026-04-21 | feat | ✅ | feat(auto-amy): extend UsageMeter to full fleet (13 agents + amy + eva) | 全舰队所有 LLM 调用均被记录，为每日成本报告提供完整数据来源 |
| [#2353](https://github.com/Vispie-AI/VisPie_backend/pull/2353) | 2026-04-21 | fix | ✅ | fix(auto-amy): hook streaming path + cache-aware pricing | Mitchell 生产环境 LLM 调用被正确记录，成本追踪管道正常运行 |
| [#2352](https://github.com/Vispie-AI/VisPie_backend/pull/2352) | 2026-04-21 | fix | 🚫 | fix(auto-amy): hook streaming path + cache-aware pricing | 此 PR 已关闭，相关修复由 #2353 最终落地 |
| [#2351](https://github.com/Vispie-AI/VisPie_backend/pull/2351) | 2026-04-21 | feat | ✅ | feat(auto-amy): wire Mitchell Bedrock calls into UsageMeter | Mitchell 生产环境 LLM 调用开始产生 usage_events 记录，成本追踪管道正式打通 |
| [#2350](https://github.com/Vispie-AI/VisPie_backend/pull/2350) | 2026-04-21 | fix | ✅ | fix(deploy): poll FastAPI healthcheck instead of racing 15s sleep | 消除部署竞态条件，Unified Deployment CI 稳定通过 |
| [#2349](https://github.com/Vispie-AI/VisPie_backend/pull/2349) | 2026-04-21 | misc | ✅ | chore(ci): remove 4 dead/deprecated workflow files | CI 配置更整洁，减少误触发风险，仓库维护成本降低 |
| [#2282](https://github.com/Vispie-AI/VisPie_backend/pull/2282) | 2026-04-20 | fix | 🚫 | fix(daily-report): retry once on HTTP 4xx from Lark send_lark_card | 最小化改动，消除瞬时 4xx 导致日报丢失的场景；PR 关闭未合并，待后续决策 |
| [#2241](https://github.com/Vispie-AI/VisPie_backend/pull/2241) | 2026-04-18 | feat | ✅ | feat(daily-report): integrate Amy liveness into George AI daily card | 19 个单元测试全通过；日报卡片可一眼区分"业务平静"与"容器宕机"，信息误导率显著降低 |
| [#2228](https://github.com/Vispie-AI/VisPie_backend/pull/2228) | 2026-04-17 | fix | ✅ | fix(monitoring): add 'liveness' probe_type + correct L2 process check | liveness 探针数据正常入库，L2 进程检测 100% 可靠 |
| [#2227](https://github.com/Vispie-AI/VisPie_backend/pull/2227) | 2026-04-17 | feat | ✅ | feat(monitoring): liveness-based alerter + persistent dedup | 告警误报率大幅降低，冷却窗口跨进程生效，监控可靠性质的提升 |
| [#2226](https://github.com/Vispie-AI/VisPie_backend/pull/2226) | 2026-04-17 | feat | 🚫 | feat(monitoring): add monitoring_alert_dedup table (migration) | feat(monitoring): add monitoring_alert_dedup table (migration) |
| [#2225](https://github.com/Vispie-AI/VisPie_backend/pull/2225) | 2026-04-17 | fix | ✅ | fix(vizzy-lark): render headings & tables inside collapsible panels | 13 个单元测试全通过；折叠面板内容可读性大幅提升 |
| [#2224](https://github.com/Vispie-AI/VisPie_backend/pull/2224) | 2026-04-17 | fix | ✅ | fix(nanobot/feishu): render tables & headings in final streamed card | nanobot 最终回复卡片渲染效果与 amy-prod/eva-prod 一致 |
| [#2177](https://github.com/Vispie-AI/VisPie_backend/pull/2177) | 2026-04-16 | feat | ✅ | feat(alerter): Synthetic Health Alerter — every-15-min bot availability monitoring | 建立系统健康自动监控基础设施 |
| [#2176](https://github.com/Vispie-AI/VisPie_backend/pull/2176) | 2026-04-16 | fix | ✅ | fix(migration): correct down_revision for error_reason migration | 数据库迁移链恢复正常，无多头分叉 |
| [#2175](https://github.com/Vispie-AI/VisPie_backend/pull/2175) | 2026-04-16 | fix | ✅ | fix(shadow): notify OpenClaw on error/cancel + extend cardCache TTL | 所有路径下 shadow 面板均能正确更新，消除永久卡死问题 |
| [#2174](https://github.com/Vispie-AI/VisPie_backend/pull/2174) | 2026-04-16 | feat | ✅ | feat(monitoring): add error_reason fields and drop unused indexes (PR #1) | 监控数据具备错误溯源能力，数据库存储优化约 1 GB |
| [#2143](https://github.com/Vispie-AI/VisPie_backend/pull/2143) | 2026-04-15 | fix | ✅ | fix(shadow): preserve trace_id across queue drain for shadow-done fan-out | 多消息并发场景下所有 shadow 面板均能正常解锁 |
| [#2140](https://github.com/Vispie-AI/VisPie_backend/pull/2140) | 2026-04-15 | fix | ✅ | fix(shadow): raise shadow response display cap 1500 → 4000 chars | shadow 面板完整展示，用户体验显著改善 |
| [#2056](https://github.com/Vispie-AI/VisPie_backend/pull/2056) | 2026-04-13 | feat | 🔀 | feat(monitoring): add error_reason tracking to SLA probes | feat(monitoring): add error_reason tracking to SLA probes |
| [#2027](https://github.com/Vispie-AI/VisPie_backend/pull/2027) | 2026-04-12 | misc | ✅ | docs: add bilingual system map for auto-amy architecture | 系统架构知识文档化，降低团队新成员理解成本 |
| [#2024](https://github.com/Vispie-AI/VisPie_backend/pull/2024) | 2026-04-11 | fix | ✅ | fix(vizzy-lark): align elapsed_sec timing with nanobot for fair shadow A/B | A/B 性能对比数据更客观准确 |
| [#2023](https://github.com/Vispie-AI/VisPie_backend/pull/2023) | 2026-04-11 | fix | ✅ | fix(nanobot): align context window and max output tokens with OpenClaw | 消除 A/B 比较中最大的两项系统性偏差 |
| [#2017](https://github.com/Vispie-AI/VisPie_backend/pull/2017) | 2026-04-11 | fix | ✅ | fix(nanobot): use env var for bot_name in feedback — fixes stale card data | feedback 记录 bot_name 完全准确，旧卡片不再污染数据 |
| [#1969](https://github.com/Vispie-AI/VisPie_backend/pull/1969) | 2026-04-11 | fix | ✅ | fix(nanobot): use AGENT_NAME as bot_name fallback in feedback records | feedback 数据中 agent 归因准确，支持后续分析 |
