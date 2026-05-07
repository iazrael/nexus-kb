---
id: "wiki-concepts-OpenClawSkill"
title: "OpenClaw Skill"
type: "concept"
created_at: "2026-04-20"
updated_at: "2026-04-20"
derived_from:
  - "raw/articles/2026-04-16T140250+0800_openclaw-create-custom-skill-guide-2026.md"
tags:
  - "openclaw"
  - "skill"
  - "ai-agent"
  - "markdown"
status: "active"
related_concepts: ["AgentSelfEvolution", "AgentMemorySystem"]
---

# OpenClaw Skill

## 定义
OpenClaw 的能力扩展机制，通过 SKILL.md 说明文件定义触发条件和工作流程，AI 按此执行，无需编写代码。

## 核心要点
1. **本质是说明书**：Skill 不是后台插件，是一份告诉 AI "何时用、怎么用" 的 Markdown 文档
   > "Skill 的本质就是一个文件夹，里面放一个 SKILL.md，告诉 OpenClaw「遇到什么情况、按什么步骤干活」"
2. **三部分结构**：frontmatter（name/description）+ When to Run + Workflow
   > "一个标准的 SKILL.md 分三个部分：frontmatter、触发说明、工作流程"
3. **description 决定触发边界**：必须写清 Use when 和 NOT for，防止误触发
   > "frontmatter 里最关键的是 description...写清楚两件事：适合什么场景用，以及不适合什么场景"

## 边界条件
- **不适合复杂逻辑**：Workflow 写得越具体越好，模糊指令如"拉数据"会导致 AI 自由发挥
- **需要重启生效**：改完 SKILL.md 必须运行 `openclaw gateway restart`
- **脚本需执行权限**：带 shell 脚本的 Skill 需 `chmod +x`

## 来源说明
> 详见 frontmatter.derived_from