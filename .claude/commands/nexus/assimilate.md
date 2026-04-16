---
description: 将当前问答吸收进知识库
---

请执行以下步骤吸收问答：

## 步骤 1：捕获对话上下文
1. 获取最近一次用户问题
2. 获取我（Claude）对该问题的回答
3. 记录回答中引用的 `wiki/` 条目（如果有）

## 步骤 2：自动分类

根据问题类型判断目标目录和 `type` 字段：

| 问题模式 | 分类 | type 值 | 目标目录 |
|---------|------|---------|---------|
| "什么是 X"/"解释 X" | 概念 | `concept` | `wiki/concepts/X.md` |
| "X 和 Y 的区别"/"对比" | 对比 | `compare` | `wiki/compare/X_vs_Y.md` |
| "如何做 X"/"步骤" | 教程 | `howto` | `wiki/howto/X.md` |
| "推荐 X 方案"/"最佳实践" | 方案 | `recipe` | `wiki/recipes/X.md` |
| 其他/无法分类 | 洞察 | `insight` | `wiki/insights/YYYY-MM-DD_主题.md` |

## 步骤 3：确定目标路径和 ID

### 文件命名规则：
- 概念类：PascalCase（如 `wiki/concepts/RAG.md`）
- 对比类：`X_vs_Y`（如 `wiki/compare/RAG_vs_FineTuning.md`）
- 教程类：动词_名词（如 `wiki/howto/BuildKnowledgeBase.md`）
- 方案类：描述性名称（如 `wiki/recipes/LintWorkflow.md`）
- 洞察类：日期_主题（如 `wiki/insights/2026-04-16_EnterpriseKB.md`）

### ID 生成规则：
格式：`wiki-{type}-{name}`
- 概念：`wiki-concepts-RAG`
- 对比：`wiki-compare-RAG-vs-FineTuning`
- 教程：`wiki-howto-BuildKnowledgeBase`
- 方案：`wiki-recipes-LintWorkflow`
- 洞察：`wiki-insights-2026-04-16-EnterpriseKB`

### 如果文件已存在：
- 追加到现有文件
- 更新 frontmatter 的 `updated_at` 字段
- 在 `derived_from` 中追加本次对话信息

## 步骤 4：创建/更新知识条目

### 新条目格式（使用 YAML frontmatter）：

```markdown
---
id: "wiki-{type}-{name}"
title: "[标题]"
type: "[concept/compare/howto/recipe/insight]"
created_at: "[当前日期]"
updated_at: "[当前日期]"
derived_from: []                               # 从问答吸收，无原始文件
tags:
  - "[分类标签]"
  - "[内容标签1]"
  - "[内容标签2]"
status: "active"
related_concepts:
  - "wiki/concepts/[已有概念1].md"
  - "wiki/concepts/[已有概念2].md"
---

# [标题]

## 来源
<!-- 来自问答吸收 -->
- 提问时间：[当前日期时间]
- 触发问题：[用户原问题]

## 核心内容
[Claude 的回答正文，或经过精简重构的版本]

## 边界条件
[如果回答中有提到，记录该结论不适用的场景]
```

### 更新已有条目：

1. 读取现有文件的 frontmatter
2. 更新 `updated_at` 为当前日期
3. 在 `tags` 中追加新标签（如有）
4. 在 `related_concepts` 中追加新关联（如有）
5. 在正文"核心内容"部分追加新内容（标注来源）

## 步骤 5：更新索引

在 `wiki/index.md` 中：
- 如果是新文件，根据分类添加到对应章节
- 格式：`- [显示名称](路径/文件名.md)`
- 如果是更新已有文件，无需修改索引

## 步骤 6：记录日志

在 `wiki/log.md` 中追加：
```markdown
## [当前日期]
- [吸收] 从问答新增：`wiki/{type}/{filename}.md`
  - ID：wiki-{type}-{name}
  - 分类：{type}
  - 关联概念：N 个
```

## 步骤 7：输出结果

```
✅ 已吸收为知识条目：`wiki/{type}/{filename}.md`

📊 统计：
- ID：wiki-{type}-{name}
- 分类：{type}
- 关联概念：N 个
- 变更类型：[新增/更新]

💡 提示：
- 运行 `/nexus:health` 检查与其他条目的一致性
- 运行 `/nexus:compile` 处理其他待编译资料
```

## 注意事项

- 只吸收高质量的、可复用的问答
- 如果问答内容已在 `wiki/` 中存在，提示用户并询问是否覆盖
- 个人偏好类问题不要吸收（如"你喜欢什么颜色"）
- 保持原子性：一个条目只讲一个主题
- 所有条目必须包含 YAML frontmatter
- `derived_from` 为空数组表示来自问答而非原始资料