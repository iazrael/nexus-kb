# /nexus:evolve 命令实现计划

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-step.

**Goal:** 创建 `/nexus:evolve` 命令文件，用于分析对话上下文并生成命令优化建议报告

**Architecture:** 单个 Markdown 命令定义文件，包含检测规则、执行流程、报告格式模板。遵循 nexus 命名空间的现有风格（compile、health 等）。

**Tech Stack:** Markdown 命令定义 + YAML frontmatter + project memory MCP

---

## Task 1: 创建命令文件骨架

**Files:**
- Create: `.claude/commands/nexus/evolve.md`

**Step 1: 创建 frontmatter 和概述**

```markdown
---
description: 分析当前对话的工具调用和决策过程，生成命令优化建议报告
---

## 概述

`/nexus:evolve` 用于在执行其他 nexus 命令后，反思本次对话的效率和正确性问题，检测常见反模式，生成针对性的命令优化建议。

**触发时机**：手动执行，建议在 compile、health、assimilate 等复杂命令完成后调用。

**输出位置**：`outputs/reports/evolve_YYYY-MM-DD.md`

**核心目标**：
- 检测工具选择问题（是否使用合适的工具）
- 检测效率问题（冗余读取、步骤顺序）
- 检测格式规范问题（frontmatter、命名、链接）
- 检测命令正确性问题（步骤遗漏、顺序错误）
- 捕获用户纠正反馈并转化为命令改进
```

**Step 2: 添加五维度检测规则章节**

写入 1.1-1.5 五个检测表格（从设计文档复制）。

**Step 3: 添加执行流程章节**

写入步骤 1-5 的执行流程描述（从设计文档复制）。

**Step 4: 添加报告输出格式章节**

写入完整的报告 Markdown 模板（从设计文档复制）。

**Step 5: 添加注意事项章节**

写入 6 条注意事项（从设计文档复制）。

**Step 6: 验证文件结构**

确认文件包含：
- frontmatter
- 概述章节
- 五维度检测规则
- 执行流程
- 报告格式
- 注意事项

---

## Task 2: 手动测试命令

**Files:**
- Read: `.claude/commands/nexus/evolve.md`

**Step 1: 模拟执行场景**

回顾本次对话（compile 命令执行 + 后续反思优化），作为测试用例。

**Step 2: 对照检测规则**

手动检查本次对话是否符合 evolve 检测规则：
- 是否有工具选择问题？（用了 Bash 还是用专用工具）
- 是否有效率问题？（读了全文还是只读 frontmatter）
- 是否有用户反馈？（你纠正了我的行为）

**Step 3: 验证输出格式模板**

检查报告格式模板是否完整可用。

**Step 4: 验证与现有命令风格一致**

对比 compile.md、health.md 的结构，确认 evolve.md 风格一致。

---

## Task 3: 提交命令文件

**Files:**
- `.claude/commands/nexus/evolve.md`

**Step 1: 暂存文件**

```bash
git add .claude/commands/nexus/evolve.md
```

**Step 2: 提交**

```bash
git commit -m "feat: 添加 /nexus:evolve 命令，用于反思优化 nexus 命令集

- 检测五类问题：工具选择、效率、格式规范、命令正确性、用户反馈
- 生成优化建议报告到 outputs/reports/
- 更新项目记忆积累优化经验

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>"
```

**Step 3: 确认提交成功**

```bash
git log -1 --oneline
```

Expected: 显示新提交

---

## Task 4: 更新索引文件（可选）

**Files:**
- Modify: `wiki/index.md`

**Step 1: 添加命令到索引**

如果 wiki/index.md 有命令列表章节，添加 evolve 命令链接。

（当前 index.md 未包含命令列表，此步骤可跳过）

---

## Task 5: 记录日志

**Files:**
- Modify: `wiki/log.md`

**Step 1: 添加日志条目**

```markdown
## 2026-04-16
- [新增命令] `/nexus:evolve` — 分析对话上下文，生成命令优化建议报告
```

**Step 2: 提交日志更新**

```bash
git add wiki/log.md
git commit -m "kb: 记录 evolve 命令新增到日志

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>"
```

---

## 验收标准

- `.claude/commands/nexus/evolve.md` 文件存在且结构完整
- 与 compile.md、health.md 风格一致
- 检测规则覆盖五个维度
- 报告格式模板完整可用
- 已提交到 Git