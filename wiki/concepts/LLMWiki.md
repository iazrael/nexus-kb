---
id: "wiki-concepts-LLMWiki"
title: "LLM Wiki"
type: "concept"
created_at: "2026-04-15"
updated_at: "2026-04-16"
derived_from:
  - "raw/articles/2026-04-15_Karpathy-LLM-Wiki完整拆解与实操路径.md"
  - "raw/articles/2026-04-16T103320+0800_llm-wiki.md"
  - "raw/articles/2026-04-16T103544+0800_Thread by @karpathy.md"
tags:
  - "llm"
  - "knowledge-management"
  - "karpathy"
status: "active"
related_concepts:
  - "Memex"
  - "IdeaFile"
  - "ProgressiveTokenDisclosure"
  - "KnowledgeFeedback"
---

# LLM Wiki

## 定义
一种用 LLM 将原始资料"编译"成结构化知识库的方法论，通过三层架构实现知识的持久化和增量更新。核心区别：知识预先编译成持久化 wiki，而非每次查询临时检索（RAG）。

## 核心要点

### 架构设计
1. **三层架构**：
   - **Raw Sources**：原始资料目录（文章、论文、图片、数据），不可变——LLM 只读不写，是信息源头
   - **Wiki**：LLM 生成的 Markdown 文件集合（摘要页、实体页、概念页、对比分析、综述、索引），LLM 完全拥有这一层
   - **Schema**：配置文档（如 CLAUDE.md），定义 wiki 结构规范、命名约定、工作流程，让 LLM 从"通用聊天机器人"变成"专业 wiki 维护员"

### 三大核心操作
2. **Ingest（摄入）**：丢新资料到 raw/，LLM 读原文、写摘要页、更新索引、扫描所有相关页面做增量更新。单篇资料可能触发 10-15 页面更新。Karpathy 倾向一次处理一篇并全程参与
3. **Query（查询）**：LLM 先读索引找相关页面，再深入阅读综合回答。输出可以是 Markdown、对比表格、Marp 幻灯片、matplotlib 图表。关键洞察：好的回答可反哺 wiki 成为新页面
4. **Lint（健康检查）**：定期让 LLM 检查 wiki——找矛盾数据、过时结论、孤立页面、缺失的概念页面、可补充的数据缺口

### 索引策略
5. **为什么不需要 RAG**：约 100 篇文章、40 万字规模下，两个文件足够：
   - **index.md**：内容目录，每页一行（链接 + 一句话摘要 + 分类），LLM 查询时先读此文件
   - **log.md**：操作日志，按时间记录摄入/查询/检查，格式统一便于 grep 回溯
6. **扩展工具**：规模增长后可用 qmd（本地 Markdown 搜索引擎，BM25 + 向量混合搜索，有 CLI 和 MCP server）

### 适用场景
7. **个人**：目标追踪、健康记录、自我提升——日记、文章、播客笔记积累成结构化自我画像
8. **研究**：论文、报告、文章——数周/数月深度积累，形成演化中的论点
9. **书籍伴侣**：按章节摄入，构建角色页、主题页、情节线索页，类似粉丝 wiki（如 Tolkien Gateway）
10. **团队/企业**：Slack 线程、会议纪要、项目文档、客户通话——LLM 做没人愿意做的维护工作

## 来源
- [中文拆解文章](../../raw/articles/2026-04-15_Karpathy-LLM-Wiki完整拆解与实操路径.md)（含生产环境 6 条经验）
- [Karpathy Gist 方法论文档](../../raw/articles/2026-04-16T103320+0800_llm-wiki.md)（官方完整方法论）
- [Karpathy X 原帖](../../raw/articles/2026-04-16T103544+0800_Thread by @karpathy.md)（概念首发）

## 关联概念
- [Memex](Memex.md) — 1945 年的历史渊源
- [Idea File](IdeaFile.md) — 知识传播范式转移
- [渐进式 Token 披露](ProgressiveTokenDisclosure.md) — 生产环境优化策略
- [知识反哺](KnowledgeFeedback.md) — 查询价值留存机制

## 边界条件
- **规模上限**：Karpathy 实测约 100 篇文章、40 万字，更大规模需验证索引策略是否仍然有效
- **对 LLM 能力的要求**：默认使用顶级模型（Claude、GPT-4 级别），小模型效果可能打折扣
- **冷启动成本**：前 5-10 篇资料需要深度参与调校 schema 和 wiki 结构，不是"丢进去就能用"
- **数据类型限制**：目前主要针对文本和结构化资料，LLM 无法一次性读取 markdown 内嵌图片，需分步处理
- **企业场景复杂度**：数据安全、权限控制、多人协作冲突处理等问题未涉及
- **验证成本**：LLM 可在不引用来源的情况下做综合，严肃研究需抽查验证

## 变更记录
- 2026-04-15：从中文拆解文章编译
- 2026-04-16：补充 Gist 方法论文档和 X 原帖细节，完善架构、操作、索引策略说明