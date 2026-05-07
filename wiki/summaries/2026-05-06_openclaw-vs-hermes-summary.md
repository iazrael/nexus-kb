---
id: "wiki-summaries-openclaw-vs-hermes"
title: "OpenClaw vs Hermes Agent 框架对比摘要"
type: "summary"
created_at: "2026-05-06"
derived_from:
  - "raw/articles/2026-05-06T132500+0800_openclaw-vs-hermes-agent-framework.md"
tags:
  - "summary"
  - "openclaw"
  - "hermes"
  - "agent"
  - "comparison"
status: "active"
---

# OpenClaw vs Hermes Agent 框架对比摘要

## 一句话总结
叶小钗从工程角度万字拆解两大 Agent 框架的核心设计差异：OpenClaw（TypeScript）追求安全稳定可生产化，Hermes（Python/Nous Research）追求灵活自进化越用越强。

## 关键要点

1. **设计哲学分歧**：OpenClaw 回答"怎么让 Agent 安全可靠执行任务"，Hermes 回答"Agent 怎么才能越来越强"
2. **学习闭环**（Hermes 核心特性）：三层提示词引导 Agent 自行判断何时提取技能，无硬编码自动触发，完全依赖 LLM 判断力
3. **记忆系统**：OpenClaw 单插件槽位可替换；Hermes 三层架构（内置 MEMORY/USER.md + 8种外部 MemoryProvider + 会话搜索），围绕推理过程设计 hook
4. **技能系统**：OpenClaw 人工编写标准化 Skill；Hermes Agent 自动提取 + 人工可编辑，渐进式披露防膨胀
5. **安全模型**：OpenClaw 默认安全 + 10+ 模块；Hermes 智能审批（辅助模型风险评估），引入新的信任问题
6. **国内生态**：Hermes 飞书/钉钉/企微/个人微信原生支持 + 4000+ 模型；OpenClaw 25+ 渠道但缺钉钉/企微/微信
7. **研究能力**：Hermes 内置 RL 训练工具链；OpenClaw 纯产品定位
8. **作者结论**：现阶段都在玩，Hermes 更迎合小白，OpenClaw 做工程研究更合适

## 来源说明
> 原文来自微信公众号「叶小钗」，2026年发布，万字深度对比文章，含源码级分析。
