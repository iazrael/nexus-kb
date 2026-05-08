---
id: "wiki-concepts-HeartbeatMechanism"
title: "Heartbeat 机制"
type: "concept"
created_at: "2026-05-08"
updated_at: "2026-05-08"
derived_from:
  - "raw/articles/2026-05-08T114801+0800_Building Autonomous AI Agents with Heartbeat Cron and Memory.md"
  - "raw/articles/2026-05-19-time-aware-proactive-agent-research.md"
tags:
  - "autonomous-agent"
  - "proactive"
  - "time-aware"
  - "architecture"
status: "active"
related_concepts: ["AgentSelfEvolution", "FiveLayerMemory"]
---

# Heartbeat 机制

## 定义

自主Agent的核心感知层：周期性唤醒信号，强制Agent评估环境、决定是否有事要做。从reactive到proactive的关键转变。

## 核心要点

1. **必要性**：LLM天生reactive（等用户输入），企业级自主运行需要proactive转变。没有Heartbeat，Agent是"死的"直到用户说话。

2. **唤醒流程**：每5-15分钟唤醒，执行检查清单：
   - Check Inbox：是否有其他Agent消息或系统警报
   - Read GOALS.md：本周/本月主要目标
   - Read CONTEXT.md：15分钟前做什么、哪里中断
   - Decide：是否需要立即行动？有事执行，无事返回HEARTBEAT_OK休眠

3. **成本优化**：无事时返回HEARTBEAT_OK退出LLM调用，节省token。

4. **时间感知依赖**：ICLR 2026研究证明LLM天生不具备时间感知能力，Heartbeat是外部时间注入的关键机制。

## 边界条件

- 需要配置唤醒间隔和检查清单
- 需要GOALS.md、CONTEXT.md等状态文件支撑

## 来源说明

> 详见 frontmatter.derived_from