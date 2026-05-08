# 知识库索引

> 本文件是知识库的主目录，记录所有编译后的知识条目。

## 概念
<!-- 概念条目列表：格式 - [标题](路径) | 一句话定义 -->
- [LLM Wiki](concepts/LLMWiki.md) | Karpathy 的 Markdown 知识库方法论，三层架构
- [Idea File](concepts/IdeaFile.md) | 知识分享载体，Markdown 文件承载想法
- [Memex](concepts/Memex.md) | Vannevar Bush 的知识关联设想原型
- [渐进式 Token 披露](concepts/ProgressiveTokenDisclosure.md) | 分级读取策略，避免不必要全文加载
- [知识反哺](concepts/KnowledgeFeedback.md) | 对话成果回流到知识库的机制
- [OpenClaw Skill](concepts/OpenClawSkill.md) | AI 能力扩展机制，SKILL.md 定义触发和流程
- [Agent 自进化机制](concepts/AgentSelfEvolution.md) | Hermes 闭环学习：经验→技能→改进→持久化
- [Agent 记忆系统架构](concepts/AgentMemorySystem.md) | OpenClaw 插件槽位 vs Hermes 三层可编排
- [智能审批安全模型](concepts/SmartApproval.md) | 默认安全 vs 辅助模型风险评估的两种范式
- [AI Coding 管理](concepts/AICodingManagement.md) | 90%+代码AI生成时的工程治理：人人对齐→人机对齐
- [Pre-PR 机制](concepts/PrePRMechanism.md) | 提交前AI自查，过滤基础问题，CR聚焦业务语义
- [Heartbeat 机制](concepts/HeartbeatMechanism.md) | 周期唤醒Agent感知环境，从reactive到proactive
- [5层记忆架构](concepts/FiveLayerMemory.md) | 解决LLM金鱼效应的多层存储系统
- [双系统 Agent 架构](concepts/DualSystemAgent.md) | 快思考+慢思考，语音场景主动性
- [话痨 Agent](concepts/ChattyAgent.md) | 主动发起对话，沉默检测+频率控制

## 摘要
<!-- 资料摘要列表：格式 - [标题](路径) | 一句话总结 -->
- [Karpathy LLM Wiki 完整拆解](summaries/2026-04-15_Karpathy-LLM-Wiki完整拆解与实操路径-summary.md) | 中文深度解读，含生产环境 6 条经验
- [LLM Wiki 方法论文档摘要](summaries/2026-04-16_llm-wiki-summary.md) | Karpathy 官方 Gist，三层架构与三大操作详解
- [Karpathy X 原帖摘要](summaries/2026-04-16_karpathy-thread-summary.md) | 概念首发帖，简述核心流程和工具链
- [OpenClaw Skill 教程摘要](summaries/2026-04-16_openclaw-skill-guide-summary.md) | Skill 是 SKILL.md 说明文件，定义触发条件和工作流程
- [OpenClaw vs Hermes 对比摘要](summaries/2026-05-06_openclaw-vs-hermes-summary.md) | 万字对比两大框架核心差异，含源码级分析
- [AI Coding 管理](summaries/2026-05-08_ai-coding-management-summary.md) | Agent评测方法论管理AI Coding，零排期重构
- [自主 Agent 架构](summaries/2026-05-08_heartbeat-cron-memory-summary.md) | Heartbeat+Cron+Memory三层架构，proactive转变
- [主动式语音 Agent](summaries/2026-05-08_proactive-voice-agent-summary.md) | 双系统架构，系统1快速反应+系统2深度推理
- [时间感知研究](summaries/2026-05-08_time-aware-proactive-agent-summary.md) | LLM无时间感知，外部注入+话痨Agent实践

## 对比分析
<!-- 对比文档列表 -->

## 操作指南
<!-- 教程文档列表 -->

## 可复用方案
<!-- 方案模板列表 -->

## 洞察
<!-- 衍生洞察列表 -->

- [AgentSelfEvolution](concepts/AgentSelfEvolution.md) ↔ [OpenClawSkill](concepts/OpenClawSkill.md)：自进化的产物就是 Skill
- [AgentMemorySystem](concepts/AgentMemorySystem.md) ↔ [LCMMemory](concepts/LCMMemory.md)：OpenClaw 记忆的 LCM 实现
- [AgentSelfEvolution](concepts/AgentSelfEvolution.md) ↔ [AgentMemorySystem](concepts/AgentMemorySystem.md)：进化依赖记忆，记忆支撑进化
- [HeartbeatMechanism](concepts/HeartbeatMechanism.md) ↔ [AgentSelfEvolution](concepts/AgentSelfEvolution.md)：周期唤醒是自进化的感知基础
- [FiveLayerMemory](concepts/FiveLayerMemory.md) ↔ [AgentMemorySystem](concepts/AgentMemorySystem.md)：5层是对OpenClaw/Hermes的扩展设计
- [DualSystemAgent](concepts/DualSystemAgent.md) ↔ [HeartbeatMechanism](concepts/HeartbeatMechanism.md)：系统1快速响应类似Heartbeat感知
- [ChattyAgent](concepts/ChattyAgent.md) ↔ [HeartbeatMechanism](concepts/HeartbeatMechanism.md)：话痨Agent是Heartbeat的社交应用

## 待编译
<!-- 新摄入但尚未编译的原始资料 -->
（无待编译文件）