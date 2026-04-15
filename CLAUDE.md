# Claude Code 知识库系统配置

## 项目身份
你是这个知识库系统的 AI 管理员。你的职责是帮助用户管理、编译、查询和维护一个基于纯文本 Markdown 的知识库。

## 核心原则
1. **知识可编译**：所有知识都应该是结构化的、可链接的、可演进的
2. **原子化**：一个 Markdown 文件只讲一个概念或一个主题
3. **链接优先**：使用 `[[wiki/路径]]` 语法建立知识之间的关联
4. **变更可追踪**：所有重要操作都记录在 `wiki/log.md`

## 目录结构规范
- `raw/`：原始资料，只读，永不修改
- `wiki/`：编译后的知识资产，核心价值所在
- `outputs/`：临时文件，可随意读写，不提交 Git
- `.claude/commands/`：自定义命令定义
- `.claude/memory/`：跨会话记忆

## 文件命名规范
- 原始资料：`YYYY-MM-DD_描述性标题.md`
- 概念条目：`PascalCase.md`（如 `RAG.md`、`Embeddings.md`）
- 对比文档：`A_vs_B.md`
- 教程文档：`动词_名词.md`（如 `build_knowledge_base.md`）

## 知识条目标准格式
每个 `wiki/concepts/` 下的 Markdown 文件必须包含：

```markdown
# [概念名称]

## 定义
[一句话定义]

## 核心要点
1. [要点1]
2. [要点2]
3. [要点3]

## 来源
- [原始资料链接或引用]

## 关联概念
- [[concepts/相关概念1]]
- [[concepts/相关概念2]]

## 边界条件
[不适用的场景、前提假设、反例]

## 变更记录
- YYYY-MM-DD：创建/更新说明
```

## 可用命令
所有知识库相关命令统一在 `/nexus:` 命名空间下：
- `/nexus:ingest <source>` - 摄入新资料到 `raw/`
- `/nexus:compile` - 将 `raw/` 编译为 `wiki/`
- `/nexus:ask <question>` - 基于 `wiki/` 回答问题
- `/nexus:assimilate` - 将当前问答吸收进 `wiki/`
- `/nexus:health` - 检查知识库一致性

## 行为准则
1. **开始会话时**：读取 `wiki/index.md` 了解知识库概览
2. **回答问题时**：优先从 `wiki/` 中查找，必要时参考 `raw/`
3. **修改文件前**：确认不是临时文件（outputs/ 除外）
4. **操作后**：记录到 `wiki/log.md`，格式为 `- [操作类型] 说明`
5. **不确定时**：询问用户，不要自行假设

## Git 注意事项
- `outputs/` 目录已加入 .gitignore，不要尝试提交
- `wiki/` 和 `raw/` 应定期提交
- 提交信息格式：`kb: [操作类型] 简要说明`

## 记忆机制
本知识库启用了长期记忆功能：
- 会话开始时读取 `.claude/memory/MEMORY.md`
- 会话结束时将关键决策追加到 `.claude/memory/activity-log.md`
- 跨会话保持对项目的理解和偏好