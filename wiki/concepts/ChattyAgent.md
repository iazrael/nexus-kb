---
id: "wiki-concepts-ChattyAgent"
title: "话痨 Agent"
type: "concept"
created_at: "2026-05-08"
updated_at: "2026-05-08"
derived_from:
  - "raw/articles/2026-05-19-time-aware-proactive-agent-research.md"
tags:
  - "proactive"
  - "chatty"
  - "social-agent"
  - "time-aware"
status: "active"
related_concepts: ["HeartbeatMechanism"]
---

# 话痨 Agent

## 定义

能够主动发起对话的Agent，不是等用户输入才响应，而是根据沉默时长、话题相关性等判断是否应该主动说话。

## 核心要点

1. **核心要素**：
   - 外部时间注入（LLM天生无时间感知）
   - 沉默检测（记录最后用户消息时间）
   - 频率控制（cooldown + 每小时上限 + 相关性评分）
   - 个性化决策（基于用户画像调整主动频率）

2. **wake-decide-respond-sleep 循环**：唤醒→决策→生成→休眠（动态sleep）。

3. **决策引擎类型**：AI-based（LLM评估）或Rule-based（阈值判断）。

4. **适用场景**：聊天陪伴、主动提醒、社交类Agent。

## 边界条件

- 频率控制参数需要根据具体场景调优
- 过度主动可能被视为骚扰

## 来源说明

> 详见 frontmatter.derived_from