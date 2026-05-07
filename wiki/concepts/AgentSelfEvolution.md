---
id: "wiki-concepts-AgentSelfEvolution"
title: "Agent 自进化机制"
type: "concept"
created_at: "2026-05-06"
updated_at: "2026-05-06"
derived_from:
  - "raw/articles/2026-05-06T132500+0800_openclaw-vs-hermes-agent-framework.md"
tags:
  - "agent"
  - "self-evolution"
  - "skills"
  - "learning-loop"
  - "hermes"
status: "active"
related_concepts: ["OpenClawSkill", "AgentMemorySystem"]
---

# Agent 自进化机制

## 定义
Agent 通过任务执行经验自动提取、沉淀和改进技能的闭环机制，核心循环：经验 → 技能 → 改进 → 知识持久化。

## 核心要点

1. **自进化的本质是数据飞轮**：与 AI 客服的"交互数据积累 → 经验提炼 → 知识沉淀 → 效果提升"同构，知识库是最早的自进化形态
   > "基本的架构一定不会发生变化，能变的就只有工作方法（Workflow），而承载 Workflow 的模块就是 Skills"

2. **Hermes 的实现：三层提示词引导**（无硬编码自动触发逻辑）
   - 第一层：告诉 Agent 什么时候该创建技能
   - 第二层：列出五条创建条件 + 三条更新条件
   - 第三层：使用技能时督促持续改进
   > "完全依赖 prompt 是否靠谱？肯定不会每一次都正确，但设计者认为大模型的指令跟随能力足够强"

3. **技能沉淀触发条件**：
   - 成功完成复杂任务（5+ tool calls）
   - 中途遇错但最终找到可行路径
   - 用户纠正了做法（外部反馈）
   - 识别出可复用的 non-trivial workflow

4. **渐进式披露解决技能膨胀**：`skills_list` 只返回名称和描述，不全量塞入 system prompt。个人场景（几十个）OK，团队场景（几百个）可能需要更智能的检索机制

5. **与传统工程的差异**：传统做法是独立后台流水线（任务完成→记录轨迹→筛选案例→提取模式→更新Skill），Hermes 把这个决策权交给了 Agent 本身

## 边界条件

### 不是什么
- 不是模型权重的自动更新（架构不变，变的是工作方法）
- 不是保证每次都正确的自动化 pipeline（依赖 LLM 判断力）
- 不是替代人工审核（Agent 可能跑偏，人仍需介入）

### 反模式
- 技能无节制增长导致 system prompt 膨胀
- 过度信任 Agent 的技能提取判断（可能沉淀低质量技能）
- 在生产环境盲目启用自进化（缺乏安全审计的技能可能有风险）

### 待验证
- 技能累积到数百时，渐进式披露是否仍然有效
- 三层提示词在不同模型上的稳定性差异
