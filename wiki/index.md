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

## 摘要
<!-- 资料摘要列表：格式 - [标题](路径) | 一句话总结 -->
- [Karpathy LLM Wiki 完整拆解](summaries/2026-04-15_Karpathy-LLM-Wiki完整拆解与实操路径-summary.md) | 中文深度解读，含生产环境 6 条经验
- [LLM Wiki 方法论文档摘要](summaries/2026-04-16_llm-wiki-summary.md) | Karpathy 官方 Gist，三层架构与三大操作详解
- [Karpathy X 原帖摘要](summaries/2026-04-16_karpathy-thread-summary.md) | 概念首发帖，简述核心流程和工具链
- [OpenClaw Skill 教程摘要](summaries/2026-04-16_openclaw-skill-guide-summary.md) | Skill 是 SKILL.md 说明文件，定义触发条件和工作流程
- [OpenClaw vs Hermes 对比摘要](summaries/2026-05-06_openclaw-vs-hermes-summary.md) | 万字对比两大框架核心差异，含源码级分析

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

## 待编译
<!-- 新摄入但尚未编译的原始资料 -->
（无待编译文件）