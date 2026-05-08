---
id: "wiki-concepts-PrePRMechanism"
title: "Pre-PR 机制"
type: "concept"
created_at: "2026-05-08"
updated_at: "2026-05-08"
derived_from:
  - "raw/articles/2026-05-08T114648+0800_用Agent评测思路管理AI Coding —— 31万行代码AI重构的实践.md"
tags:
  - "code-review"
  - "ai-coding"
  - "workflow"
status: "active"
related_concepts: ["AICodingManagement"]
---

# Pre-PR 机制

## 定义

AI编码提速后，Code Review成为瓶颈的解决方案：提交前先用AI自查，过滤基础规范问题，让人工CR只聚焦业务语义。

## 核心要点

1. **问题背景**：AI压缩编码时间，压力系统性地向CR环节集中。如果CR效率不提升，AI Coding的提效红利会被CR瓶颈吞掉。

2. **执行流程**：
   - 提交前：RD必须先用AI审查代码多轮自查，修复所有AI能发现的问题
   - 提交后：提交标准PR文档（改动点、影响范围、需重点Review的业务逻辑）
   - Reviewer拿到"已过滤"的高质量代码，只需聚焦核心业务语义

3. **审查策略**：高阶模型审查低阶模型；不同厂商模型对抗互相审核。

4. **CR价值转变**：从"你写得对吗？"转变为"是否在正确约束下解决正确问题？"

## 边界条件

- 需要配置好AI审查工具和规范模板
- 适用AI编码占比高的团队

## 来源说明

> 详见 frontmatter.derived_from