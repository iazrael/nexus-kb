---
id: "wiki-concepts-DualSystemAgent"
title: "双系统 Agent 架构"
type: "concept"
created_at: "2026-05-08"
updated_at: "2026-05-08"
derived_from:
  - "raw/articles/2026-05-08T114833+0800_主动式语音Agent的设计思考.md"
tags:
  - "voice-agent"
  - "proactive"
  - "architecture"
  - "dual-system"
status: "active"
related_concepts: ["HeartbeatMechanism"]
---

# 双系统 Agent 架构

## 定义

借鉴《思考快与慢》的双系统理论，将Agent决策分为系统1（快速反应）和系统2（深度推理），实现语音场景下的主动性。

## 核心要点

1. **系统1（快思考）**：规则+状态机，无LLM依赖。处理情绪检测、EOU判断、沉默检测。毫秒级响应。

2. **系统2（慢思考）**：LLM驱动，深度推理。处理复杂意图理解、任务规划、长期记忆访问。在系统1争取到的时间内后台运行。

3. **协作机制**：系统1前台快速应答，系统2后台深度处理，结果通过系统1自然反馈。

4. **成本优化**：大部分高频简单响应由系统1处理，减少LLM调用。

## 边界条件

- 需要可靠的语音识别和情绪检测技术
- 系统1和系统2协作边界需要清晰定义

## 来源说明

> 详见 frontmatter.derived_from