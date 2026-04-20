---
id: "wiki-summaries-2026-04-16-openclaw-skill-guide"
title: "OpenClaw Skill 教程摘要"
type: "summary"
created_at: "2026-04-20"
derived_from:
  - "raw/articles/2026-04-16T140250+0800_openclaw-create-custom-skill-guide-2026.md"
tags:
  - "summary"
  - "openclaw"
  - "skill"
  - "tutorial"
status: "active"
---

# OpenClaw Skill 教程摘要

## 一句话总结
Skill 是 OpenClaw 的能力扩展机制，本质是一个 SKILL.md 说明文件，定义触发条件和工作流程，AI 按此执行，无需写代码。

## 关键要点
1. **Skill 本质是说明书**：不是插件，是告诉 AI "何时用、怎么用" 的 Markdown 文件
2. **最小只需 SKILL.md**：frontmatter（name/description）+ When to Run + Workflow 三部分
3. **description 决定触发**：写清 Use when 和 NOT for，否则 AI 不知道边界