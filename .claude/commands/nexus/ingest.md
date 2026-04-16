---
description: 摄入原始资料到知识库
arguments:
  - name: source
    description: URL、文件路径或直接粘贴的文本
    required: true
---

请执行以下步骤摄入资料到知识库：

## 步骤 1：获取内容
- 如果 {source} 是 URL：使用网页抓取工具（`playwright-cli` 技能或其他浏览器工具）获取内容，转换为 Markdown
- 如果 {source} 是文件路径：读取本地文件
- 如果 {source} 是文本：直接使用

## 步骤 2：生成文件名
格式：`YYYY-MM-DDTHHMMSS+TZ_描述性标题.md`
- 时间戳：当前时间（ISO 8601 格式，如 `2026-04-16T103320+0800`）
- 标题：从内容中提取，去除特殊字符，截取前 50 字符，转为 slug 格式

## 步骤 3：判断内容类型
根据内容特征判断：
- 文章类（网页、博客、教程） → `raw/articles/`
- 论文类（arxiv、学术文献） → `raw/papers/`
- 笔记类（会议记录、个人笔记） → `raw/notes/`

## 步骤 3.5：生成 Frontmatter

为摄入的文件生成完整的 YAML frontmatter：

### 必填字段提取规则

| 字段 | 来源 | 提取方法 |
|------|------|----------|
| `id` | 系统生成 | 格式：`raw-{type}-{date}-{slug}`（如 `raw-articles-2026-04-16-llm-wiki`） |
| `title` | 内容解析 | 从页面标题或 URL 提取 |
| `type` | 步骤 3 | 根据保存目录确定：`article`/`paper`/`note` |
| `created_at` | 系统生成 | 当前时间戳（ISO 8601，如 `2026-04-16T10:33:20+0800`） |
| `source_url` | 输入参数 | 用户提供的 URL，本地文件用 `null` |
| `tags` | 内容解析 | 从内容关键词/分类提取 2-5 个 |
| `status` | 固定值 | `"pending"` |

### 可选字段提取规则

| 字段 | 来源 | 提取方法 |
|------|------|----------|
| `source_author` | 内容解析 | 查找作者署名（如 "by X"、"author: X"） |
| `published_at` | 内容解析 | 查找原文发布日期 |
| `source_platform` | URL 推断 | 从域名推断：`github-gist`/`arxiv`/`medium`/`substack` 等 |
| `domain` | 内容推断 | 领域分类（如 `ai`、`software`、`research`） |

### Frontmatter 模板

```yaml
---
id: "[系统生成]"
title: "[提取标题]"
type: "[article/paper/note]"
created_at: "[当前时间戳]"
published_at: "[提取日期或 null]"
source_url: "[用户输入或 null]"
source_author: "[提取作者或 null]"
source_platform: "[推断平台或 null]"
tags:
  - "[提取标签1]"
  - "[提取标签2]"
  - "[提取标签3]"
status: "pending"
compiled_to: []
---
```

## 步骤 3.6：验证 Frontmatter

检查生成的 frontmatter：
1. **ID 唯一性**：扫描 `raw/` 目录确保 `id` 不冲突
2. **标签数量**：确保 `tags` 至少包含 1 个有效标签
3. **时间格式**：确保 `created_at` 符合 ISO 8601 格式
4. **必填字段**：确保所有必填字段不为空

如果验证失败，提示用户并等待确认后继续。

## 步骤 4：保存原始文件

将内容与 frontmatter 组合，保存到对应目录：
- 文件路径：`raw/{type}/{filename}`
- 文件结构：frontmatter + 正文内容

## 步骤 5：更新索引

在 `wiki/index.md` 中：
- 检查是否存在"待编译"章节
- 如果没有，创建 `## 待编译` 章节
- 添加：`- [YYYY-MM-DD] [标题](raw/{type}/{filename}) - 待编译`

## 步骤 6：记录日志

在 `wiki/log.md` 中追加：
```markdown
## [当前日期]
- [摄入] 新增：`raw/{type}/{filename}`
  - ID：[生成的 ID]
  - 来源：[source_url 或本地文件]
  - 标签：[提取的标签列表]
```

## 步骤 7：输出结果

```
✅ 已摄入：[文件名]
📁 位置：raw/[类型]/[文件名]
🆔 ID：[生成的 ID]
🏷️ 标签：[提取的标签列表]
📝 待编译：运行 /nexus:compile 将其编译为知识条目
```

## 注意事项

- 不要修改已有的 raw/ 文件正文内容
- 如果文件已存在，询问用户是否覆盖
- 保持原始内容的完整性，不要删减
- frontmatter 中的 `compiled_to` 数组在编译后会自动填充