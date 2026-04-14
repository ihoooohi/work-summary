# 工作成果总结

> **更新日期**：2026-04-14
> **统计周期**：2025-11-28 ～ 2026-04-14
> **PR 总数**：9（已合并 7，进行中 1，已关闭未合并 1）

---

## 一、Bug 修复

### 1. fix(nanobot): use AGENT_NAME as bot_name fallback in feedback records

#### [VisPie_backend #1969](https://github.com/Vispie-AI/VisPie_backend/pull/1969)
- **日期**：2026-04-10 创建 / 2026-04-11 合并
- **状态**：✅ 已合并

| 维度 | 内容 |
|------|------|
| **问题** | `agent_feedback` 表中绝大多数记录 `bot_name="nanobot"`，无法区分实际 agent（george-ai、zane-ai 等） |
| **根因** | 所有 fleet bot 的 docker-compose 均设置了 `AGENT_NAME` 但从未设置 `BOT_NAME`，代码回退到硬编码字符串 `"nanobot"` |
| **修复方案** | 将回退逻辑改为 `os.environ.get("BOT_NAME") or os.environ.get("AGENT_NAME", "nanobot")`，无需修改 docker-compose 配置 |
| **成果** | `nanobot_server.py`、`server.py` 各 3 处统一修复；反馈记录正确归因到各 fleet agent |

---

### 2. fix(nanobot): use env var for bot_name in feedback — fixes stale card data

#### [VisPie_backend #2017](https://github.com/Vispie-AI/VisPie_backend/pull/2017)
- **日期**：2026-04-11 创建 / 2026-04-11 合并
- **状态**：✅ 已合并

| 维度 | 内容 |
|------|------|
| **问题** | 点击旧 Lark 消息卡片的反馈按钮，仍记录 `bot_name="nanobot"` |
| **根因** | 旧卡片 action payload 中硬编码了 `bot_name="nanobot"`；`handle_card_action` 优先读取 `value.get("bot_name")`，返回陈旧值，#1969 的修复被绕过 |
| **修复方案** | 从 `handle_card_action` 回退链中彻底移除 `value.get("bot_name")`，仅使用环境变量 `BOT_NAME` → `AGENT_NAME`；Lark 反馈回调始终路由到发送方容器，`AGENT_NAME` 是权威来源 |
| **成果** | `nanobot_server.py`、`server.py` 各 1 行修复；历史卡片反馈也能正确记录 agent 名称 |

---

### 3. fix(nanobot): align context window and max output tokens with OpenClaw

#### [VisPie_backend #2023](https://github.com/Vispie-AI/VisPie_backend/pull/2023)
- **日期**：2026-04-11 创建 / 2026-04-11 合并
- **状态**：✅ 已合并

| 维度 | 内容 |
|------|------|
| **问题** | Shadow A/B 中 nanobot 系统性慢于 OpenClaw primary，A/B 评判结果严重偏斜 |
| **根因** | `CONTEXT_WINDOW` 618K vs OpenClaw 200K（3×），`max_tokens` 64K vs OpenClaw 16,384（4×）；nanobot 加载 3× 历史 + 输出更长回复，TTFT 多出 5-10s，生成多出 1-5s |
| **修复方案** | `nanobot_server.py` 默认值 618K→200K；`bedrock_provider.py` max_tokens 64K→16,384；两份 docker-compose 同步更新环境变量 |
| **成果** | nanobot 与 OpenClaw 资源对等，A/B 对比结果回到公平基线 |

---

### 4. fix(vizzy-lark): align elapsed_sec timing with nanobot for fair shadow A/B

#### [VisPie_backend #2024](https://github.com/Vispie-AI/VisPie_backend/pull/2024)
- **日期**：2026-04-11 创建 / 2026-04-11 合并
- **状态**：✅ 已合并

| 维度 | 内容 |
|------|------|
| **问题** | Shadow 测试中 vizzy-lark（OpenClaw）`elapsed_sec` 始终偏短，A/B judge 持续低估其耗时 |
| **根因** | OpenClaw 从 `streamStartTime`（模型首 token 输出）计时，不含 session 加载、prompt 组装、context 路由；nanobot 从 `start_time`（handler 入口）计时，包含全链路——范围不一致 |
| **修复方案** | 改为从 `routeMessage()` 入口（`routeStartTime`）计时，与 nanobot `start_time` 对齐；修改 `vizzy-lark/src/message-router.ts` 的正常 finalize 和孤儿卡片两处 `elapsedSec` 计算 |
| **成果** | 两侧计时口径统一，A/B 延迟对比可信 |

---

## 二、文档建设

### 5. docs: add bilingual system map for auto-amy architecture

#### [VisPie_backend #2027](https://github.com/Vispie-AI/VisPie_backend/pull/2027)
- **日期**：2026-04-12 创建 / 2026-04-12 合并
- **状态**：✅ 已合并

| 维度 | 内容 |
|------|------|
| **背景** | auto-amy 架构缺乏系统性文档，新成员上手和跨团队协作成本高 |
| **实现方案** | 新增中英双语系统地图共 8 个文件（`docs/system-map/` 英文版、`docs/system-map-cn/` 中文版），包含：① 模块地图（7 大模块、优先级索引、文件树）② 数据流地图（3 条管道：消息/记忆/响应，存储位置，数据生命周期）③ 调用链地图（各请求路径函数调用：启动、Feishu 收消息、agent loop、流式、子 agent）④ 约束地图（绿/黄/红三色安全区，9 项红区约束及根因） |
| **成果** | 完整双语架构文档上线，降低系统理解门槛，支撑后续开发与审查 |

---

## 三、新功能开发

### 6. feat(monitoring): add error_reason tracking to SLA probes

#### [VisPie_backend #2056](https://github.com/Vispie-AI/VisPie_backend/pull/2056)
- **日期**：2026-04-13 创建
- **状态**：🔄 进行中（等待审查合并）

| 维度 | 内容 |
|------|------|
| **背景** | 生产 SLA 报表页面只显示"Amy 24h: 87%"，无法得知 13% 失败的具体原因（503 超时还是连接错误） |
| **实现方案** | DB ORM 新增 3 个 nullable 列 + 1 个索引；手写 Alembic migration（仅 ADD COLUMN）；Pydantic 新增 `ErrorSummary` 类并扩展 `ProbeRecord`；7 commits，322 insertions / 4 deletions |
| **预期成果** | SLA 报表可展示失败原因分布（如"47× HTTP 503 vs 6× timeout"），从"知道挂了"升级为"知道为什么挂" |

---

### 7. Duke-ECE/MIPS-compiler — 自定义 RISC ISA 核心实现

#### [MIPS-compiler #3 Dev/assembler](https://github.com/Duke-ECE/MIPS-compiler/pull/3)
- **日期**：2025-11-28
- **状态**：✅ 已合并

| 维度 | 内容 |
|------|------|
| **问题** | MIPS 编译器项目缺少汇编器模块，无法将汇编指令转换为机器码 |
| **根因** | 项目仅有高层编译器逻辑，缺少 ISA 操作码定义、寄存器映射及指令编码/解码核心层 |
| **实现方案** | 新增 `Opcode.hpp`（枚举全部 ISA 操作码、指令类型分类及字符串转换工具）和 `Registers.hpp`（定义 32 个通用寄存器、MIPS 风格寄存器映射）；实现操作码处理、寄存器验证和指令编码/解码/反汇编完整逻辑；同步更新构建系统 |
| **成果** | 完成汇编器模块基础架构，支持自定义 RISC 指令集的编码与反汇编 |

---

#### [MIPS-compiler #2 完成isa相关功能](https://github.com/Duke-ECE/MIPS-compiler/pull/2)
- **日期**：2025-11-28
- **状态**：🔴 已关闭（未合并，内容由 PR #3 重新提交合并）

| 维度 | 内容 |
|------|------|
| **说明** | 与 PR #3 相同工作的早期提交版本，因分支策略调整关闭，变更通过 PR #3 正式合并 |

---

### 8. Duke-ECE/TalkHub

#### [TalkHub #2 test: test](https://github.com/Duke-ECE/TalkHub/pull/2)
- **日期**：2026-03-06
- **状态**：✅ 已合并

| 维度 | 内容 |
|------|------|
| **说明** | 仓库 PR 工作流验证测试，确认流程正常 |

---

## 总览

| # | 仓库 | PR | 类别 | 状态 | 日期 | 核心成果 |
|---|------|----|------|------|------|----------|
| 1 | VisPie_backend | [#1969](https://github.com/Vispie-AI/VisPie_backend/pull/1969) | Bug修复 | ✅ 已合并 | 2026-04-11 | nanobot feedback bot_name 正确归因 |
| 2 | VisPie_backend | [#2017](https://github.com/Vispie-AI/VisPie_backend/pull/2017) | Bug修复 | ✅ 已合并 | 2026-04-11 | 修复旧卡片反馈绕过 AGENT_NAME 问题 |
| 3 | VisPie_backend | [#2023](https://github.com/Vispie-AI/VisPie_backend/pull/2023) | Bug修复 | ✅ 已合并 | 2026-04-11 | context window & max_tokens 对齐，A/B 公平基线 |
| 4 | VisPie_backend | [#2024](https://github.com/Vispie-AI/VisPie_backend/pull/2024) | Bug修复 | ✅ 已合并 | 2026-04-11 | elapsed_sec 计时口径统一，A/B 延迟对比可信 |
| 5 | VisPie_backend | [#2027](https://github.com/Vispie-AI/VisPie_backend/pull/2027) | 文档建设 | ✅ 已合并 | 2026-04-12 | auto-amy 中英双语架构系统地图（8 文件） |
| 6 | VisPie_backend | [#2056](https://github.com/Vispie-AI/VisPie_backend/pull/2056) | 新功能 | 🔄 进行中 | 2026-04-13 | SLA 探针错误原因结构化追踪 |
| 7 | MIPS-compiler | [#3](https://github.com/Duke-ECE/MIPS-compiler/pull/3) | 新功能 | ✅ 已合并 | 2025-11-28 | 自定义 RISC ISA 汇编器核心模块 |
| 8 | MIPS-compiler | [#2](https://github.com/Duke-ECE/MIPS-compiler/pull/2) | — | 🔴 已关闭 | 2025-11-28 | ISA 早期版本（由 #3 替代） |
| 9 | TalkHub | [#2](https://github.com/Duke-ECE/TalkHub/pull/2) | 测试 | ✅ 已合并 | 2026-03-06 | PR 工作流验证 |

---

*本文件由 Scheduled Agent 自动生成，最后更新：2026-04-14*
