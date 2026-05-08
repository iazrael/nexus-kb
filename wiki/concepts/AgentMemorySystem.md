---
id: "wiki-concepts-AgentMemorySystem"
title: "Agent 记忆系统架构"
type: "concept"
created_at: "2026-05-06"
updated_at: "2026-05-08"
derived_from:
  - "raw/articles/2026-05-06T132500+0800_openclaw-vs-hermes-agent-framework.md"
tags:
  - "agent"
  - "memory"
  - "hermes"
  - "openclaw"
  - "architecture"
  - "five-layer"
status: "active"
related_concepts: ["LCMMemory", "AgentSelfEvolution", "FiveLayerMemory"]
---

# Agent 记忆系统架构

## 定义
Agent 系统中负责存储、检索和利用历史信息的设计模式。两种代表性架构：OpenClaw 的"插件槽位"式 vs Hermes 的"三层可编排"式。

## 核心要点

1. **OpenClaw：记忆即插件**
   - 同一时间只激活一个记忆插件，可替换但约束强
   - 设计关注点：记忆怎么接进系统才不破坏稳定性
   - 实现：lossless-claw（DAG 压缩 + SQLite 持久化）
   > "OpenClaw 没认为自己的记忆系统适应于所有团队，做了最简实现，要的就是可替换"

2. **Hermes 三层记忆架构**：
   - **内置记忆**：MEMORY.md（2200字符，环境/约定/经验）+ USER.md（1375字符，用户画像）
     - 关键限制：system prompt 拿到的是会话启动时的快照，当前写入要下次会话才可见
     - 写入时有安全扫描（提示注入、不可见 Unicode 等）
   - **外部记忆（MemoryProvider）**：8种可插拔实现（Honcho、Mem0、RetainDB 等）
     - 接口围绕"推理过程"设计（prefetch/sync_turn/on_pre_compress 等 hook）
   - **会话搜索**：FTS5 全文搜索 → 按会话分组 → 辅助模型摘要 → 返回针对性结果
     - 比 OpenClaw 多一层 LLM 压缩：上下文更干净但多一次模型调用

3. **MemoryProvider 的推理生命周期 hook**：
   - `prefetch`：推理前预取相关记忆
   - `sync_turn`：每轮对话后同步新交互
   - `on_pre_compress`：压缩前摘取关键信息防误伤
   - `on_delegation`：子 Agent 完成后观察学习
   - `on_session_end`：会话结束做长期记忆整理

4. **设计哲学差异**：
   - OpenClaw：记忆是"如何接入才稳定"→ 控制优先
   - Hermes：记忆是"如何让 Agent 越来越懂你"→ 灵活优先

## 边界条件

### 不是什么
- 不是数据库选型问题（核心差异在于读写时机和编排方式）
- 不是简单的 RAG 检索（涉及推理过程中的主动注入和预取策略）

### 反模式
- 把所有历史对话无差别塞入上下文（token 浪费 + 噪音）
- 忽视 MemoryProvider 的 hook 时机（遗漏 on_pre_compress 导致关键信息被压缩丢失）

### 待验证
- 8 种 MemoryProvider 在实际场景中的效果差异
- 会话搜索中辅助模型摘要的信息保真度
