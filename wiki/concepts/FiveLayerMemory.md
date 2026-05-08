---
id: "wiki-concepts-FiveLayerMemory"
title: "5层记忆架构"
type: "concept"
created_at: "2026-05-08"
updated_at: "2026-05-08"
derived_from:
  - "raw/articles/2026-05-08T114801+0800_Building Autonomous AI Agents with Heartbeat Cron and Memory.md"
tags:
  - "memory"
  - "autonomous-agent"
  - "architecture"
  - "persistence"
status: "active"
related_concepts: ["AgentMemorySystem", "HeartbeatMechanism"]
---

# 5层记忆架构

## 定义

解决LLM"金鱼效应"（session结束就遗忘）的多层存储系统，确保Agent跨会话保留上下文和知识。

## 核心要点

1. **层级设计**：
   - L1: Session Memory（volatile）
   - L2: CONTEXT.md（当前状态）
   - L3: Daily Notes（今日日志）
   - L4: MEMORY.md（长期知识）
   - L5: Memory Service/RAG（向量检索）

2. **即时持久化规则**：自主系统中"I'll write it later"是禁止的，完成子任务后必须立即写入L2或L3。

3. **上下文窗口管理**：定期将Daily Notes摘要并移入向量DB，保持活跃上下文小而快。

4. **与其他概念关联**：Heartbeat唤醒时读取L2、L4；Agent自进化依赖L5存储历史经验。

## 边界条件

- 需要设计好各层同步策略，避免一致性问题
- L5向量DB需要额外基础设施投入

## 来源说明

> 详见 frontmatter.derived_from