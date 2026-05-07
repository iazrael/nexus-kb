---
id: "wiki-concepts-LCMMemory"
title: "LCM Memory"
type: "concept"
created_at: "2026-04-20"
updated_at: "2026-04-20"
derived_from:
  - "raw/articles/2026-04-16T230800+0800_lossless-claw-lcm-guide.md"
tags:
  - "openclaw"
  - "memory"
  - "embedding"
  - "compression"
status: "active"
related_concepts: ["AgentMemorySystem"]
---

# LCM Memory

## 定义
Lossless-claw 插件的核心记忆机制，通过向量压缩将旧对话递归摘要，实现近乎无限的记忆保持。

## 核心要点
1. **向量压缩机制**：将文字转换为向量，旧记忆递归摘要成"金字塔"结构
   > "旧记忆被摘要总结，变得越来越简练，就像一个记忆金字塔"
2. **四个核心参数**：EMBEDDING_MODEL、FRESH_TAIL_COUNT、INCREMENTAL_MAX_DEPTH、CONTEXT_THRESHOLD
   > 推荐配置：nomic-embed-text + 32 轮不压缩 + 无限递归 + 75% 阈值
3. **自动化 DAG 压缩**：无需额外 Agent，后台自动清理保持流畅响应
   > "自动化 DAG 压缩，始终保持流畅的响应，无需额外Agent"

## 边界条件
- **依赖本地模型**：nomic-embed-text 需本地运行，追求极致性能可换 OpenAI API
- **上下文仍有上限**：压缩减少占用，但 Token 限制仍存在
- **新对话不压缩**：FRESH_TAIL_COUNT 内的对话保持原始，占用较多

## 来源说明
> 详见 frontmatter.derived_from