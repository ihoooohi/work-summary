# 工作成果总结

> **更新日期**：2026-04-14
> **统计周期**：2025-11-28 ～ 2026-03-06
> **PR 总数**：3（已合并 2，已关闭未合并 1）

---

> ⚠️ **说明**：公司仓库 `Vispie-AI/VisPie_backend` 通过 API 查询返回 404，
> 当前 PAT 令牌暂无该仓库访问权限。以下内容基于 GitHub 搜索 API 可检索到的公开 PR 记录。

---

## 一、新功能开发

### 1. Duke-ECE/MIPS-compiler — 自定义 RISC ISA 核心实现

#### PR #3 [Dev/assembler](https://github.com/Duke-ECE/MIPS-compiler/pull/3)
- **日期**：2025-11-28
- **状态**：✅ 已合并

| 维度 | 内容 |
|------|------|
| **背景/问题** | MIPS 编译器项目缺少汇编器模块，无法将汇编指令转换为机器码 |
| **根因** | 项目仅有高层编译器逻辑，缺少 ISA 操作码定义、寄存器映射及指令编码/解码核心层 |
| **实现方案** | 新增 `Opcode.hpp`（枚举全部 ISA 操作码、指令类型分类及字符串转换工具）和 `Registers.hpp`（定义 32 个通用寄存器、MIPS 风格寄存器映射）；实现操作码处理、寄存器验证和指令编码/解码/反汇编完整逻辑；同步更新构建系统以涵盖 ISA 实现的新测试用例 |
| **成果** | 完成汇编器模块基础架构，支持自定义 RISC 指令集的编码与反汇编，为后续代码生成阶段奠定基础 |

---

#### PR #2 [完成isa相关功能](https://github.com/Duke-ECE/MIPS-compiler/pull/2)
- **日期**：2025-11-28
- **状态**：🔴 已关闭（未合并，内容已通过 PR #3 重新提交合并）

| 维度 | 内容 |
|------|------|
| **背景/问题** | 与 PR #3 相同，为同一 ISA 实现工作的早期提交版本 |
| **根因** | 提交分支策略调整，相关代码改用 `Dev/assembler` 分支提交 |
| **处理方式** | 关闭本 PR，变更通过 PR #3 正式合并 |
| **待办** | 无（已由 PR #3 覆盖） |

---

## 二、测试 / 其他

### 2. Duke-ECE/TalkHub

#### PR #2 [test: test](https://github.com/Duke-ECE/TalkHub/pull/2)
- **日期**：2026-03-06
- **状态**：✅ 已合并

| 维度 | 内容 |
|------|------|
| **背景/问题** | 仓库功能验证测试 PR |
| **根因** | — |
| **实现方案** | 测试提交，验证 PR 工作流 |
| **成果** | 确认仓库 PR 流程正常 |

---

## 总览

| 类别 | 工作内容 | PR |
|------|----------|----|
| 新功能开发 | 自定义 RISC ISA 核心模块：操作码定义、寄存器映射、指令编码/解码/反汇编、汇编器架构 | [MIPS-compiler#3](https://github.com/Duke-ECE/MIPS-compiler/pull/3) |
| 新功能开发（重提） | ISA 功能早期版本（已关闭，由 PR #3 替代） | [MIPS-compiler#2](https://github.com/Duke-ECE/MIPS-compiler/pull/2) |
| 测试 | TalkHub 仓库 PR 工作流验证 | [TalkHub#2](https://github.com/Duke-ECE/TalkHub/pull/2) |

---

*本文件由 Scheduled Agent 自动生成，最后更新：2026-04-14*
