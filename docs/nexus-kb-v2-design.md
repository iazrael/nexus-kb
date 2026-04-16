# Nexus-KB v2.0 设计文档：跨平台知识库方案

## 版本定位

| 版本 | 定位 | 核心特征 |
|------|------|----------|
| **v1.x** | Claude Code 原生知识库 | 人类主动，会话内执行斜杠命令 |
| **v2.0** | 跨平台知识库 | 让 OpenClaw 作为"知识工作者代理人"，实现静默后台自动化 |

## 核心理念

**Nexus-KB 不是传统软件项目，而是一套"给 AI 阅读的 SOP（标准作业程序）"。**

所有操作都是通过自然语言指令驱动，AI 自己调用工具执行。无需写脚本，无需适配器架构，只需为每个平台编写对应格式的指令文档。

```yaml
传统思维（过度工程化）:
  core/engine.py → 编译逻辑
  adapters/openclaw/skill.md → 调用 engine

AI-Native 思维（正确）:
  .claude/commands/nexus/compile.md → "Claude，按这三步编译"
  openclaw/skills/nexus-compile.md → "OpenClaw，按这三步编译"
```

## 目录结构

```
nexus-kb/
├── .claude/commands/nexus/     # Claude Code 格式
│   ├── ingest.md               # 摄入新资料
│   ├── compile.md              # 编译知识库
│   ├── ask.md                  # 查询知识库
│   ├── assimilate.md           # 吸收问答
│   └── health.md               # 健康检查
│
├── openclaw/skills/            # OpenClaw 格式（待创建）
│   ├── nexus-ingest/SKILL.md
│   ├── nexus-compile/SKILL.md
│   ├── nexus-ask/SKILL.md
│   ├── nexus-assimilate/SKILL.md
│   └── nexus-health/SKILL.md
│
└── shared/                     # 共享数据
    ├── raw/                    # 原始资料
    ├── wiki/                   # 编译后的知识
    └── outputs/                # 临时输出
```

## 五大核心 Skill 设计

### 1. nexus-ingest：摄入新资料

**功能**：将外部资料（URL、文件）转换为 Markdown 并存入 `raw/` 目录。

**Claude Code 命令**：`/nexus:ingest <source>`

**OpenClaw Skill 模板**：

```yaml
---
name: nexus-ingest
description: >
  摄入新资料到 nexus-kb 知识库，支持 URL、本地文件或直接粘贴文本。
  自动转换为 Markdown 格式，保存到 raw/articles/ 并更新索引。
  Use when: 用户说"摄入这个""把这个存到知识库"。
  NOT for: 编译或查询已有内容（用 nexus-compile 或 nexus-ask）。
---

## When to Run

- 用户主动提供 URL、文件路径或文本内容
- 用户说"摄入""保存到知识库""存到 raw/"等关键词

## Workflow

1. 识别输入类型（URL / 文件路径 / 文本内容）
2. 根据类型选择转换方式：
   - URL：使用网页抓取工具（playwright-cli、r.jina.ai 等）
   - 文件：读取并转换为 Markdown
   - 文本：直接使用
3. 生成标准 frontmatter：
   ```yaml
   ---
   id: "raw-articles-{date}-{slug}"
   title: "[标题]"
   type: "article"
   created_at: "YYYY-MM-DDTHH:MM:SS+TZ"
   source_url: "[来源URL 或 null]"
   tags: ["tag1", "tag2"]
   status: "pending"
   compiled_to: []
   ---
   ```
4. 保存到 `raw/articles/{timestamp}_{title}.md`
5. 更新 `wiki/index.md` 标记新资料待编译

## Output Format

✅ 已摄入：{标题}
📄 保存位置：`raw/articles/{filename}.md`
📋 待编译：运行 `/nexus:compile` 或等待自动编译
```

---

### 2. nexus-compile：编译知识库

**功能**：将 `raw/` 中待编译的资料通过"三步编译法"转化为结构化的 `wiki/` 条目。

**Claude Code 命令**：`/nexus:compile`

**OpenClaw Skill 模板**：

```yaml
---
name: nexus-compile
description: >
  编译 nexus-kb 知识库：将 raw/ 中 status=pending 的资料编译为 wiki/ 条目。
  使用三步编译法：浓缩 → 质疑 → 对标。
  Use when: 用户说"编译知识库"，或定时自动触发（每 30 分钟）。
  NOT for: 摄入新资料或查询已有内容（用 nexus-ingest 或 nexus-ask）。
---

## When to Run

- 用户主动说"编译知识库"
- Heartbeat 定时触发（建议每 30 分钟）
- Cron 定时触发（如每日深夜批量编译）

## Workflow

1. 扫描 `raw/articles/` 目录，找出 `status: pending` 的文件
2. 对每个文件执行三步编译法：

   **步骤 1：浓缩（提取核心概念）**
   - 识别 3-5 个核心概念
   - 提取每个概念的定义、核心要点、边界条件
   - 在 `wiki/concepts/` 创建独立文件（如 `RAG.md`、`Embeddings.md`）

   **步骤 2：质疑（识别边界和限制）**
   - 列出每个概念的"不是什么"
   - 识别适用场景和反模式
   - 记录不确定或需要验证的内容

   **步骤 3：对标（关联已有知识）**
   - 搜索 `wiki/` 中相关概念
   - 建立链接关系（使用 `[显示名称](相对路径)` 格式）
   - 识别知识缺口，记录待研究内容

3. 为生成的每个概念文件添加标准 frontmatter：
   ```yaml
   ---
   id: "wiki-concepts-{name}"
   title: "[概念名称]"
   type: "concept"
   created_at: "YYYY-MM-DD"
   updated_at: "YYYY-MM-DD"
   derived_from: ["raw/articles/xxx.md"]
   tags: ["tag1", "tag2"]
   status: "active"
   related_concepts: ["Concept1", "Concept2"]
   ---
   ```

4. 更新原文件的 `compiled_to` 字段，将 `status` 改为 `compiled`
5. 更新 `wiki/index.md` 添加新概念链接

## Filtering Criteria

优先编译：
- 高价值来源（学术论文、技术文档、深度分析）
- 与现有知识库关联度高的内容
- 用户明确标记重要的资料

延后编译：
- 低质量或重复内容
- 过时或已失效的链接

## Output Format

📊 编译完成：
✅ 处理文件：{N} 个
🆕 新增概念：{N} 个
🔗 建立关联：{N} 个
📁 保存位置：`wiki/concepts/`
```

---

### 3. nexus-ask：查询知识库

**功能**：基于用户问题，从 `wiki/` 中检索相关内容并生成回答。

**Claude Code 命令**：`/nexus:ask <question>`

**OpenClaw Skill 模板**：

```yaml
---
name: nexus-ask
description: >
  从 nexus-kb 知识库检索并回答问题。扫描 wiki/ 目录，找到相关概念文件，
  提取关键信息，生成带引用的回答。
  Use when: 用户提问涉及 nexus-kb 存储的知识，或自动触发于知识库相关会话。
  NOT for: 摄入新资料或编译知识库（用 nexus-ingest 或 nexus-compile）。
---

## When to Run

- 用户主动提问（如"什么是 RAG？""知识库怎么说 X？"）
- 会话中涉及知识库关键词时自动触发
- 用户说"查查知识库""看看 wiki"

## Workflow

1. 分析用户问题，提取 3-5 个关键词
2. 在 `wiki/` 目录中搜索：
   - 文件名匹配（如 `RAG.md`）
   - 文件内容搜索（使用 grep 或类似工具）
   - tags 字段匹配
3. 读取最相关的 3-5 个文件
4. 基于检索结果生成回答：
   - 总结核心概念
   - 提取关键要点
   - 标注引用来源（使用 `[来源](文件路径)` 格式）
5. 识别相关概念，建议进一步阅读

## Output Format

📚 基于 nexus-kb 的回答：

{核心回答内容}

**参考资料**：
- [{概念名称}]({相对路径})
- [{概念名称}]({相对路径})

**相关阅读**：
- [{相关概念}]({相对路径})
```

---

### 4. nexus-assimilate：吸收问答

**功能**：将有价值的问答对吸收到知识库，扩展 `wiki/` 内容。

**Claude Code 命令**：`/nexus:assimilate`

**OpenClaw Skill 模板**：

```yaml
---
name: nexus-assimilate
description: >
  将当前会话中的问答对吸收到 nexus-kb 知识库。
  提取问题、答案、上下文，分类后存入 wiki/ 对应子目录。
  Use when: 用户说"记住这个""存到知识库""吸收这个问题"。
  NOT for: 摄入外部资料或查询已有内容（用 nexus-ingest 或 nexus-ask）。
---

## When to Run

- 用户主动说"记住这个""存到知识库""吸收这个问题"
- 多渠道触发后统一处理（见下方"待沉淀队列"机制）

## Workflow

1. 捕获当前会话中的问答对：
   - 用户的问题
   - AI 的回答
   - 相关上下文

2. 评估价值，决定是否吸收：
   - 是否有独特见解？
   - 是否是通用知识？
   - 是否与现有知识重复？

3. 分类并决定存储位置：
   - 概念性内容 → `wiki/concepts/`
   - 操作步骤 → `wiki/recipes/`
   - 对比分析 → `wiki/compare/`
   - 洞见观点 → `wiki/insights/`
   - 教程指南 → `wiki/howto/`

4. 为吸收的内容生成 frontmatter：
   ```yaml
   ---
   id: "wiki-{category}-{slug}"
   title: "[标题]"
   type: "{category}"
   created_at: "YYYY-MM-DD"
   derived_from: ["conversation"]
   tags: ["assimilated"]
   source_session: "{会话标识}"
   ---
   ```

5. 保存到对应目录
6. 更新 `wiki/index.md` 添加新内容链接

## Filtering Criteria

值得吸收：
- 有独特见解或原创分析
- 解决了具体问题
- 提供了新视角或方法论

不值得吸收：
- 通用常识
- 重复已有知识
- 低质量或无意义对话

## Output Format

💡 已吸收到知识库：
📝 标题：{标题}
📂 分类：{category}
📄 位置：`wiki/{category}/{filename}.md`
🔗 已更新索引
```

---

### 5. nexus-health：健康检查

**功能**：扫描知识库，检测潜在问题并生成报告。

**Claude Code 命令**：`/nexus:health`

**OpenClaw Skill 模板**：

```yaml
---
name: nexus-health
description: >
  检查 nexus-kb 知识库健康状况。扫描 wiki/ 目录，检测矛盾、过时内容、
  孤岛文件、知识缺口，生成结构化健康报告。
  Use when: 用户说"健康检查""检查知识库"，或定时自动触发（每日凌晨）。
  NOT for: 摄入资料或编译知识库（用 nexus-ingest 或 nexus-compile）。
---

## When to Run

- 用户主动说"健康检查""检查知识库"
- Cron 定时触发（建议每日凌晨 2 点）
- 大量更新后手动触发

## Workflow

1. 扫描 `wiki/` 目录，读取所有概念文件

2. 检测四类问题：

   **矛盾检测**
   - 搜索对同一概念的不同描述
   - 识别冲突的定义或要点
   - 标记需要人工审查的内容

   **过时检测**
   - 检查 `created_at` 和 `updated_at` 时间戳
   - 识别超过 6 个月未更新的内容
   - 检查来源链接是否失效

   **孤岛检测**
   - 找出 `related_concepts` 为空的文件
   - 识别没有被其他文件引用的内容
   - 标记孤立的知识点

   **缺口检测**
   - 分析概念之间的关联
   - 识别经常被引用但不存在的内容
   - 记录待研究的知识空白

3. 生成结构化健康报告，保存到 `outputs/reports/health-{date}.md`

## Output Format

📊 Nexus-KB 健康报告
📅 生成时间：{timestamp}

## 问题统计
- 🔴 严重矛盾：{N} 处
- 🟡 过时内容：{N} 处
- 🟠 孤岛文件：{N} 处
- 🔵 知识缺口：{N} 处

## 详细报告
{具体问题列表}

## 建议行动
{修复建议}

📄 完整报告：`outputs/reports/health-{date}.md`
```

---

## OpenClaw 接入指南

### 前置条件

1. OpenClaw 已安装并运行
2. nexus-kb 目录可被 OpenClaw 访问
3. 确认路径配置（建议使用绝对路径）

### 步骤 1：创建 Skill 文件

将上述 5 个 Skill 的内容分别保存为：

```bash
~/.openclaw/workspace/skills/
├── nexus-ingest/
│   └── SKILL.md
├── nexus-compile/
│   └── SKILL.md
├── nexus-ask/
│   └── SKILL.md
├── nexus-assimilate/
│   └── SKILL.md
└── nexus-health/
│   └── SKILL.md
```

### 步骤 2：配置知识库路径

在 Skill 中替换路径占位符：

```yaml
# 将 {NEXUS_KB_PATH} 替换为实际路径
# 例如：/Users/azrael/nexus-kb
```

或在 OpenClaw 配置中设置环境变量：

```bash
export NEXUS_KB_PATH="/Users/azrael/nexus-kb"
```

### 步骤 3：重启 OpenClaw Gateway

```bash
openclaw gateway restart
```

### 步骤 4：配置定时任务

将以下配置发送给 OpenClaw，它会自动设置：

**Heartbeat 配置（自动编译）**：

```
添加 heartbeat 任务，名称：kb-auto-compile
间隔：每 30 分钟
提示：运行 nexus-compile skill，编译知识库
```

**Cron 配置（每日健康检查）**：

```
添加 cron 任务，名称：kb-daily-health
时间：每日凌晨 2 点
时区：Asia/Shanghai
提示：运行 nexus-health skill，将报告保存到 nexus-kb/outputs/reports/
```

### 步骤 5：测试验证

在 OpenClaw 对话中测试：

```
用户：运行 nexus-compile
OpenClaw：[应该执行编译流程]

用户：什么是 RAG？
OpenClaw：[应该自动调用 nexus-ask 检索回答]
```

---

## 定时任务文字描述

### Heartbeat：自动编译

将以下内容发送给 OpenClaw：

```
请配置一个 heartbeat 任务：
- 名称：kb-auto-compile
- 间隔：每 30 分钟
- 提示内容：运行 nexus-compile skill，扫描 raw/articles/ 目录，
  将 status=pending 的文件编译为 wiki/ 条目
- 会话：main（或你的默认会话）
```

### Cron：每日健康检查

将以下内容发送给 OpenClaw：

```
请配置一个 cron 任务：
- 名称：kb-daily-health
- 时间：0 2 * * *（每日凌晨 2 点）
- 时区：Asia/Shanghai
- 提示内容：运行 nexus-health skill，检查知识库健康状况，
  将报告保存到 /Users/azrael/nexus-kb/outputs/reports/health-{日期}.md
- 会话：main（或你的默认会话）
```

### Heartbeat：待沉淀队列处理（可选）

如果你想启用多渠道知识沉淀：

```
请配置一个 heartbeat 任务：
- 名称：kb-pending-assimilate
- 间隔：每 5 分钟
- 提示内容：检查 nexus-kb/outputs/pending/ 目录，
  对待吸收的问答对运行 nexus-assimilate skill
- 会话：main（或你的默认会话）
```

---

## 设计原则

### 1. 纯文字指令驱动

所有操作都是通过自然语言描述，AI 自己调用工具。无需写脚本，无需复杂架构。

### 2. 平台格式分离

同一套逻辑，不同平台的不同格式：
- Claude Code：`.claude/commands/nexus/*.md`
- OpenClaw：`openclaw/skills/*/SKILL.md`
- 未来平台：对应格式的指令文件

### 3. 共享数据目录

`raw/`、`wiki/`、`outputs/` 三个目录被所有平台共享，确保数据一致性。

### 4. 职责自然分离

- **Claude Code**：需要人类判断的操作（初次编译、吸收验证）
- **OpenClaw**：自动化重复操作（定时编译、健康巡检）

无需复杂的并发控制，个人使用场景冲突概率极低。

---

## 验收标准

- [ ] OpenClaw 会话中能检索 `wiki/` 内容并回答问题
- [ ] OpenClaw 能定时执行编译，新摄入的资料自动编译
- [ ] OpenClaw 能定时执行健康检查，健康报告自动生成
- [ ] 五个核心操作均可用 OpenClaw Skill 触发
- [ ] Claude Code 侧的所有斜杠命令仍正常工作
- [ ] 双端共用同一套 `raw/`、`wiki/` 目录，互不冲突

---

## 后续扩展

### 支持更多平台

本设计的核心理念可以轻松扩展到其他 AI 平台：

- **Cursor**：创建 `.cursor/rules/nexus-*.md`
- **Continue**：创建 `~/.continue/rules/nexus-*.md`
- **Cline**：创建 `.clinerules-nexus-*.md`

本质都是"同一套自然语言流程，不同平台的不同格式"。

### Web UI 界面

未来可以考虑开发一个简单的 Web UI，让不熟悉命令行的用户也能：
- 查看知识库内容
- 触发编译和健康检查
- 查看健康报告

但这不是核心功能，知识库的本质仍然是"给 AI 阅读的 SOP"。

---

## 附录：v1.0 vs v2.0 对比

| 能力维度 | v1.0（Claude Code 原生） | v2.0（跨平台扩展） |
|----------|-------------------------|-------------------|
| 执行入口 | 仅 Claude Code 斜杠命令 | Claude Code + OpenClaw Skill + 自然语言 |
| 知识检索 | 手动 `/ask` | OpenClaw 自动检索 + 手动 `/ask` |
| 知识摄入 | 手动 `/ingest` | 手动 + OpenClaw Skill 触发 |
| 知识编译 | 手动 `/compile` | 手动 + OpenClaw 定时任务 |
| 知识吸收 | 手动 `/assimilate` | 手动 + 多渠道触发 |
| 健康检查 | 手动 `/health` | 手动 + OpenClaw 定时巡检 |
| 可用渠道 | Claude Code 对话界面 | Claude Code + Telegram/微信/Discord 等 |

---

**文档状态**：v2.0 设计草案（待评审）
**创建时间**：2026-04-16
**维护者**：Nexus-KB 项目
