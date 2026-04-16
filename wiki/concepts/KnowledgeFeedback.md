---
id: "wiki-concepts-KnowledgeFeedback"
title: "知识反哺"
type: "concept"
created_at: "2026-04-15"
updated_at: "2026-04-16"
derived_from:
  - "raw/articles/2026-04-15_Karpathy-LLM-Wiki完整拆解与实操路径.md"
  - "raw/articles/2026-04-16T103320+0800_llm-wiki.md"
tags:
  - "knowledge-growth"
  - "query"
status: "active"
related_concepts:
  - "LLMWiki"
---

# 知识反哺

## 定义
将 LLM 查询回答中有价值的内容保存回 wiki，形成新的知识条目，使每次查询都能为知识库"加料"而非消失在聊天记录中。

## 核心要点
1. **价值留存**：好的回答可以反哺 wiki，如对比分析、综合结论等有价值的内容存回 wiki 变成新页面
2. **主动式扩展**：用户的每次提问都在给知识库"加料"，知识库会随着使用自动增长，而非静态不变
3. **与 RAG 的对比**：RAG 的答案随用随弃，不积累；知识反哺让答案成为新的知识资产

## 来源
- [raw/articles/2026-04-15_Karpathy-LLM-Wiki完整拆解与实操路径.md](../../raw/articles/2026-04-15_Karpathy-LLM-Wiki完整拆解与实操路径.md)

## 关联概念
- [LLM Wiki](LLMWiki.md)

## 边界条件
- **质量控制**：需要人工判断哪些回答值得保存，避免低质量内容污染知识库
- **重复风险**：可能产生冗余或重复的条目
- **命名和分类**：反哺内容的命名和分类需要清晰规范

## 变更记录
- 2026-04-15：从 2026-04-15_Karpathy-LLM-Wiki完整拆解与实操路径.md 编译