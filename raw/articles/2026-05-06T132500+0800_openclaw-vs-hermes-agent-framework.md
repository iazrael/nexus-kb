---
id: "raw-articles-20260506-openclaw-vs-hermes"
title: "【万字】OpenClaw vs Hermes：一文深入拆解两大 Agent 框架"
type: "article"
created_at: "2026-05-06T13:25:00+0800"
source_url: "https://mp.weixin.qq.com/s/wPuKUlajb6IaIL3uH7gSAA"
source_author: "叶小钗"
source_platform: "wechat"
tags:
  - "openclaw"
  - "hermes"
  - "agent"
  - "framework"
  - "comparison"
  - "skills"
  - "memory"
  - "security"
status: "compiled"
compiled_to:
  - "wiki/concepts/AgentSelfEvolution.md"
  - "wiki/concepts/AgentMemorySystem.md"
  - "wiki/concepts/SmartApproval.md"
  - "wiki/summaries/2026-05-06_openclaw-vs-hermes-summary.md"
---

# 【万字】OpenClaw vs Hermes：一文深入拆解两大 Agent 框架

> 作者：叶小钗
> 来源：微信公众号
> 原文链接：https://mp.weixin.qq.com/s/wPuKUlajb6IaIL3uH7gSAA

## 核心定位差异

- **OpenClaw**（TypeScript）：回答"怎么让 Agent 安全可靠地执行任务"→ 多层审批、安全审计、沙箱隔离、严格插件边界
- **Hermes Agent**（Python, Nous Research）：回答"Agent 怎么才能越来越强？"→ 闭环学习：经验 → 技能 → 改进 → 知识持久化

## 学习闭环

Hermes 的核心设计理念是**闭环学习**，通过三层提示词引导 Agent 自己判断要不要把经验存下来：
1. 第一层告诉 Agent 什么时候该创建技能
2. 第二层列出五条创建条件和三条更新条件
3. 第三层在 Agent 使用技能时督促它持续改进

**技能沉淀倾向**：
- 成功完成复杂任务（5+ tool calls）
- 中途遇到错误但最终找到可行路径
- 用户纠正了做法
- 识别出可复用的 non-trivial workflow

用渐进式披露（skills_list 只返回名称和描述）解决技能过多问题，但团队场景下（几百个技能）可能不够用。

## 记忆系统对比

### OpenClaw 记忆
- 把记忆当作**特殊插件**，同一时间只激活一个记忆插件
- 设计关注：记忆怎么接进系统才不会破坏稳定性
- 可替换性强，但灵活性不足

### Hermes 记忆（三层）

**1. 内置记忆**：
- `MEMORY.md`（2200字符）：环境信息、项目约定、经验记录
- `USER.md`（1375字符）：用户偏好、沟通习惯、内容风格
- 注意：system prompt 拿到的是会话启动时的**快照**，当前对话写入的内容要下次会话才可见
- 写入时有安全扫描（提示注入、不可见 Unicode 等）

**2. 外部记忆（MemoryProvider）**：
- 可插拔设计，已接 8 种实现：Honcho、Hindsight、Mem0、Byterover、Holographic、OpenViking、RetainDB、Supermemory
- 围绕"推理过程"设计接口（非围绕"数据存储"）
- 关键 hook：prefetch、sync_turn、on_pre_compress、on_delegation、on_memory_write、on_session_end

**3. 会话搜索**：
- 不把历史对话塞进上下文，而是通过 `session_search` 工具按需查询
- 流程：FTS5 全文搜索 → 按会话分组 → 辅助模型做摘要 → 返回针对性摘要
- 比 OpenClaw 多一层 LLM 压缩，上下文更干净但多一次模型调用

## 上下文压缩

| 维度 | OpenClaw | Hermes |
|------|----------|--------|
| 压缩位置 | 从最老轮次（头部）开始压 | 保护两端、压缩中间 |
| 压缩方式 | LLM 提取摘要，保留完整归档和快照 | 辅助模型生成结构化摘要，迭代更新 |
| 可回溯性 | 强（原始消息归档到 archive） | 中（迭代摘要，跨多次压缩保持连续性） |

## 技能系统对比

| 维度 | OpenClaw | Hermes |
|------|----------|--------|
| 创建方式 | 人工/开发者编写 | Agent 自动提取 + 人工可编辑 |
| 更新机制 | 人工维护 | Agent 自主判断是否更新 |
| 存储形式 | 结构化定义（SKILL.md） | Markdown 文件 |
| 设计出发点 | 标准化、可控 | 自我进化、持续学习 |
| 生态 | 官方和团队内部维护，ClawHub | 社区 40+ 技能包，agentskills.io |

**核心差异**：OpenClaw 的 Skills 是"人教 Agent 怎么做"，Hermes 的 Skills 是"Agent 自己总结怎么做"。

## 执行环境

- **Hermes**：6 种后端（Daytona、Modal 等无服务器），统一 `BaseEnvironment` 抽象，切换改配置即可
- **OpenClaw**：3 种后端（本地 Docker、SSH、远程沙箱），安全优先，多层审批 + 审计系统

## 安全模型

- **OpenClaw**：默认安全，高权限需显式声明。10+ 安全模块，SECURITY.md + 漏洞响应流程
- **Hermes**：智能审批（辅助模型判断风险级别），低风险自动通过，高风险需用户确认。引入辅助模型判断的信任问题

## 国内生态

- **Hermes**：飞书、钉钉、企业微信原生 Gateway 支持；个人微信通过腾讯 iLink Bot API（官方通道）；models.dev 集成 4000+ 模型（含智谱、月之暗面、MiniMax）
- **OpenClaw**：25+ 渠道（含飞书和 QQ 机器人扩展），缺钉钉、企业微信和个人微信原生支持；Provider Plugin 路线，每个提供商一个插件

## 研究能力

- **Hermes**：内置 RL 训练工具链（rl_cli.py、batch_runner.py、trajectory_compressor.py），可训练下一代工具调用模型
- **OpenClaw**：纯产品，无训练能力

## 选择建议

- 安全合规硬要求 → OpenClaw
- Agent 自学习改进 → Hermes
- 消息平台覆盖广 → OpenClaw（25+ 渠道）
- 飞书/钉钉/企业微信主力 → Hermes（原生支持）
- RL 训练研究 → Hermes
- 低成本 24 小时运行 → Hermes（无服务器后端）
- TypeScript 技术栈 → OpenClaw，Python → Hermes
- 已有 `hermes claw migrate` 命令支持从 OpenClaw 一键迁移

## 作者观点

> 从整体设计来说，Hermes 非常迎合小白用户，而 OpenClaw 想要做生产级别的平台。现阶段用 OpenClaw/Hermes 的场景依旧偏玩耍。基于此，Hermes 可能会好点，但做工程研究还是要看 OpenClaw。
