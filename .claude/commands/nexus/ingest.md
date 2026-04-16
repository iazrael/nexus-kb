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
格式：`YYYY-MM-DD_描述性标题.md`
- 日期：当前日期
- 标题：从内容中提取，去除特殊字符，截取前 50 字符

## 步骤 3：保存原始文件
- 判断内容类型（article/paper/note）
- 保存到对应子目录：
  - 文章类 → `raw/articles/`
  - 论文类 → `raw/papers/`
  - 笔记类 → `raw/notes/`

## 步骤 4：更新索引
在 `wiki/index.md` 中：
- 检查是否存在"待编译"章节
- 如果没有，创建 `## 待编译` 章节
- 添加：`- [YYYY-MM-DD] [文件名](raw/相对路径) - 待编译`

## 步骤 5：记录日志
在 `wiki/log.md` 中追加：
```markdown
## YYYY-MM-DD
- [摄入] 新增：`raw/路径/文件名`
```

## 步骤 6：输出结果
```
✅ 已摄入：[文件名]
📁 位置：raw/[类型]/[文件名]
📝 待编译：运行 /nexus:compile 将其编译为知识条目
```

## 注意事项
- 不要修改已有的 raw/ 文件
- 如果文件已存在，询问用户是否覆盖
- 保持原始内容的完整性，不要删减