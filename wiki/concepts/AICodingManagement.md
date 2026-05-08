---
id: "wiki-concepts-AICodingManagement"
title: "AI Coding 管理"
type: "concept"
created_at: "2026-05-08"
updated_at: "2026-05-08"
derived_from:
  - "raw/articles/2026-05-08T114648+0800_用Agent评测思路管理AI Coding —— 31万行代码AI重构的实践.md"
tags:
  - "ai-coding"
  - "agent-evaluation"
  - "alignment"
  - "refactoring"
status: "active"
related_concepts: ["PrePRMechanism"]
---

# AI Coding 管理

## 定义

当90%+代码由AI生成时，用Agent评测方法论约束AI产出、防止系统腐化的工程治理实践。核心理念："人人对齐→人机对齐"。

## 核心要点

1. **人人对齐优先**：先让团队形成统一共识（分层原则、建模方式、依赖边界），再配置AI Rule。团队共识是AI约束的前提，顺序错了AI Rule只是一纸空文。

2. **人机对齐落地**：将共识固化为always级别的AI Rule，约束大模型生成结果。前置到预CR环节，提交前完成基础规范校验。

3. **经验价值重定义**：AI把"能看全"的门槛打到几乎为零。经验价值从"能看全"转移到"能判断什么重要"——这才是人不可替代的部分。

4. **技术债渐进式消化**：把技术债拆解为业务需求的"顺带动作"，借着迭代渐进式消化。31万行代码在业务交付中被消化，零和博弈变成双赢。

## 边界条件

- 需要已有Agent评测思维基础，或先建立评测文化
- 适用于高压迭代场景（月均16需求），低频场景可能不值得
- AI Rule质量直接影响效果，需要持续维护

## 来源说明

> 详见 frontmatter.derived_from