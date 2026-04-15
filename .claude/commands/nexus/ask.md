---
description: 基于知识库回答问题
arguments:
  - name: question
    description: 用户的问题
    required: true
  - name: report
    description: 是否生成报告文件
    required: false
    default: false
---

请执行以下步骤回答问题：

## 步骤 1：检索相关知识
1. 读取 `wiki/index.md` 了解知识库结构
2. 从用户问题中提取关键词
3. 在 `wiki/concepts/`、`wiki/compare/`、`wiki/howto/` 中搜索相关文件
   - 确匹配：文件名包含关键词
   - 语义匹配：扫描文件内容，寻找相关段落
4. 如果相关概念不足，提示用户可能需要 `/nexus:ingest` 新资料

## 步骤 2：综合回答
- 优先使用 `wiki/` 中的编译知识
- 如果多个条目相关，综合它们的内容
- 标注信息来源：`[路径](相对路径)`
- 如果发现矛盾，明确指出并建议运行 `/nexus:health`

## 步骤 3：特殊模式处理

### 模式 A：生成报告（--report 或用户要求"生成报告"）
1. 将回答格式化为完整的 Markdown 报告
2. 保存到 `outputs/reports/[主题]_[日期].md`
3. 输出：`📄 报告已生成：outputs/reports/[文件名]`

### 模式 B：缓存问答（--cache）
1. 将问题和回答保存到 `outputs/cache/qa_cache.json`
2. 格式：`{"question": "问题", "answer": "回答", "timestamp": "日期", "source": ["wiki/路径"]}`

## 步骤 4：记录交互（可选）
如果用户后续执行 `/nexus:assimilate`，本次问答会被吸收到知识库

## 回答格式示例
```
根据知识库中的信息：

[回答内容]

---
📚 信息来源：
- [概念1](concepts/概念1.md)
- [概念2](concepts/概念2.md)

💡 相关操作：
- 发现新见解？运行 `/nexus:assimilate` 吸收到知识库
- 信息不足？运行 `/nexus:ingest [URL]` 添加新资料
```

## 注意事项
- 如果 `wiki/` 中没有相关信息，明确告知用户，不要编造
- 回答要简洁、结构化，便于后续吸收
- 缓存文件不要提交 Git（已在 .gitignore）