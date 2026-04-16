---
description: 将 raw/ 中的未处理资料编译为 wiki/ 知识条目
---

请执行以下步骤编译知识库：

## 步骤 1：扫描待编译文件

1. **优先**读取 `wiki/index.md` 的"待编译"章节，获取待编译文件列表
2. 如果"待编译"章节为空，则扫描 `raw/` 目录，找出所有 `status: "pending"` 的文件
3. 解析每个文件的 frontmatter，提取 `id`、`title`、`tags` 等元数据
4. 排除 `raw/.gitkeep` 等非内容文件
5. 如果没有待编译文件，输出：`✅ 知识库已是最新，无需编译` 并退出

## 步骤 2：对每个文件执行编译流程

### 2.1 摘要（Summarize）
- 为每个原始文件生成简明摘要
- 保存到 `wiki/summaries/[原始文件名]-summary.md`

### 2.2 浓缩（Condense）
- 提取不超过 3 条核心结论
- 每条结论附上关键证据（原文引用）
- 去除冗余和次要信息

### 2.3 质疑（Critique）
分析并记录：
- 结论依赖的前提假设是什么？
- 数据的可靠性和时效性如何？
- 边界条件：在什么情况下不适用？
- 潜在的反例或争议点？

### 2.4 对标（Compare）
- 扫描现有 `wiki/concepts/` 下的所有条目
- 寻找与当前内容相关的概念
- 判断关系：
  - 新概念 → 创建新文件
  - 补充已有概念 → 更新现有文件
  - 矛盾已有概念 → 在健康报告中标记

## 步骤 3：创建/更新知识条目

### 新概念条目格式（保存到 `wiki/concepts/`）：

使用 **YAML frontmatter + Markdown 正文** 结构：

```markdown
---
id: "wiki-concepts-[概念名]"
title: "[概念名称]"
type: "concept"
created_at: "[当前日期]"
updated_at: "[当前日期]"
derived_from:
  - "raw/articles/[原始文件名].md"
tags:
  - "[提取标签1]"
  - "[提取标签2]"
status: "active"
related_concepts: []
---

# [概念名称]

## 定义
[一句话定义，从核心结论中提炼]

## 核心要点
1. [要点1 + 证据]
2. [要点2 + 证据]
3. [要点3 + 证据]

## 边界条件
[质疑步骤中识别的边界]

## 来源说明
> 详见 frontmatter.derived_from
```

### 新摘要格式（保存到 `wiki/summaries/`）：

```markdown
---
id: "wiki-summaries-[原始文件名]"
title: "[原始文件标题] 摘要"
type: "summary"
created_at: "[当前日期]"
derived_from:
  - "raw/articles/[原始文件名].md"
tags:
  - "summary"
  - "[原始文件标签]"
status: "active"
---

# [原始文件标题] 摘要

## 一句话总结
[3-5句话概括全文主旨]

## 关键要点
1. [要点1]
2. [要点2]
3. [要点3]
```

### 补充已有概念：
- 读取现有文件的 frontmatter
- 在 `derived_from` 数组中追加新来源路径
- 更新 `updated_at` 字段为当前日期
- 在 `tags` 中追加新标签（如有）
- 在正文"核心要点"中追加新要点（标注来源）

## 步骤 3.5：建立双向链接

更新原始文件的 frontmatter：

1. 读取对应的 `raw/` 文件
2. 在 frontmatter 的 `compiled_to` 数组中追加：
   - 新生成的 wiki 文件路径（相对路径）
3. 将 `status` 从 `"pending"` 更新为 `"compiled"`
4. 保存更新后的 `raw/` 文件

**注意**：虽然 `raw/` 是"只读"，但更新 frontmatter 的状态和关联字段是允许的，这不会改变原始正文内容。

## 步骤 4：更新索引

在 `wiki/index.md` 中：
- 如果是新概念，添加到 `## 概念` 章节
  - 格式：`- [显示名称](concepts/概念名.md)`
- 添加摘要到 `## 摘要` 章节
  - 格式：`- [显示名称](summaries/文件名-summary.md)`
- 从"待编译"章节移除已处理的文件

## 步骤 5：记录日志

在 `wiki/log.md` 中追加：
```markdown
## [当前日期]
- [编译] 从 `raw/路径` 生成摘要：`wiki/summaries/文件名-summary.md`
  - 概念 ID：wiki-summaries-xxx
- [编译] 从 `raw/路径` 新增概念：`wiki/concepts/概念名.md`
  - 概念 ID：wiki-concepts-xxx
- [编译] 更新概念：`wiki/concepts/已有概念.md`（补充 XXX）
- [双向链接] 更新 `raw/路径` 的 compiled_to 和 status
```

## 步骤 6：输出编译报告

```
📊 编译完成
- 处理文件数：N
- 生成摘要：N
- 新增概念：M
- 更新概念：K
- 建立双向链接：N 条
- 发现矛盾：C（运行 /nexus:health 查看详情）
```

## 步骤 7：清理（可选）

将中间处理文件写入 `outputs/tmp/compile_[日期]/` 用于调试

## 注意事项

- 保持原子性：一个文件只讲一个概念
- 宁缺毋滥：如果内容质量低，不编译，在日志中标记"跳过：原因"
- 所有 wiki 文件必须包含 YAML frontmatter
- 关联概念使用标准 Markdown 链接：`[显示名称](concepts/概念名.md)`
- 确保 `wiki/summaries/` 目录存在