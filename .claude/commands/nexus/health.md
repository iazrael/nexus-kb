---
description: 检查知识库一致性，生成健康报告
---

请执行以下步骤进行健康检查：

## 步骤 1：扫描知识库（基于 Frontmatter）

### 1.1 扫描 raw/ 目录
1. 遍历 `raw/articles/`、`raw/papers/`、`raw/notes/` 下的所有文件
2. 解析每个文件的 YAML frontmatter
3. 提取关键字段：`id`、`status`、`compiled_to`、`created_at`
4. 统计：
   - 待编译文件（`status: "pending"`）
   - 已编译文件（`status: "compiled"`）
   - 跳过文件（`status: "skipped"`）

### 1.2 扫描 wiki/ 目录
1. 遍历所有子目录（`concepts/`、`summaries/`、`compare/`、`howto/`、`recipes/`、`insights/`）
2. 解析每个文件的 YAML frontmatter
3. 提取关键字段：
   - `id`：唯一标识
   - `title`：标题
   - `type`：类型
   - `created_at`：创建日期
   - `updated_at`：最后更新日期
   - `derived_from`：来源列表
   - `related_concepts`：关联概念
   - `tags`：标签
   - `status`：条目状态
4. 构建知识图谱数据结构（JSON 格式）

## 步骤 2：检查六个维度

### 2.1 矛盾检测
- 从 frontmatter 提取 `derived_from` 列表
- 检查同一原始资料编译出的多个概念是否有冲突
- 对比同一概念在不同条目中的描述
- 检测逻辑冲突（如"A 优于 B"和"B 优于 A"同时出现）
- 标记位置：`[file1.id]` vs `[file2.id]`

### 2.2 过时检测（自动化）
- 从 frontmatter 提取 `updated_at` 字段
- 计算距离当前日期的天数
- 如果超过 **90 天**，自动标记为"可能过时"
- 检查 `derived_from` 对应的 raw/ 文件的 `created_at`
- 如果原始资料比概念条目更新，标记为"需要更新"

### 2.3 孤岛检测（增强版）
- 遍历所有文件的 `related_concepts` 字段
- 统计每个文件被引用的次数（反向索引）
- 构建引用计数表：
  ```json
  {
    "wiki-concepts-LLMWiki": { "references_count": 3, "referenced_by": ["file1", "file2"] },
    "wiki-concepts-NewConcept": { "references_count": 0, "referenced_by": [] }
  }
  ```
- 如果 `references_count = 0` 且不在 `index.md` 中，标记为"孤岛页面"

### 2.4 缺口检测
- 从所有文件的 `related_concepts` 提取引用路径
- 检查这些路径对应的文件是否存在
- 如果不存在，标记为"概念缺口"
- 统计缺口提及次数，优先处理高频缺口

### 2.5 Frontmatter 健康检查
检查所有文件的 frontmatter 完整性：

| 检查项 | 规则 | 问题类型 |
|--------|------|----------|
| 必填字段缺失 | `id`、`title`、`type`、`created_at` 任一为空 | 🔴 严重 |
| ID 格式错误 | 不符合 `wiki-{type}-{name}` 格式 | 🟡 中等 |
| ID 重复 | 多个文件使用相同 `id` | 🔴 严重 |
| 时间格式错误 | `created_at`/`updated_at` 不符合 ISO 8601 | 🟡 中等 |
| 标签缺失 | `tags` 数组为空或不存在 | 🟢 轻微 |
| 来源路径断裂 | `derived_from` 路径指向不存在文件 | 🟡 中等 |

### 2.6 编译状态检查
检查 raw/ 与 wiki/ 的双向链接：

| 检查项 | 规则 | 问题类型 |
|--------|------|----------|
| 编译遗漏 | `raw/` 文件 `status: "pending"` 超过 7 天 | 🟡 中等 |
| 链接断裂 | `raw/` 的 `compiled_to` 指向不存在的 wiki 文件 | 🔴 严重 |
| 反向链接缺失 | wiki 文件的 `derived_from` 未包含对应的 raw 文件 | 🔴 严重 |

## 步骤 3：计算健康评分

采用扣分制公式：
```
健康评分 = 100 - (严重问题扣分 + 中等问题扣分 + 轻微问题扣分)

| 问题类型 | 扣分规则 | 权重说明 |
|---------|---------|---------|
| 🔴 严重 | 每条 -15分 | 最高优先级：矛盾、ID重复、链接断裂 |
| 🟡 中等 | 每条 -8分 | 中优先级：过时、缺口、格式错误 |
| 🟢 轻微 | 每条 -2分 | 低优先级：孤岛、标签缺失 |

最低分 = 0（不允许负分）

健康等级：
  - 90-100：优秀 🟢
  - 70-89：良好 🟡
  - 50-69：需维护 🟠
  - 0-49：严重 🔴
```

验收标准：评分 ≥ 80 为健康，< 80 认为有异常。

## 步骤 4：生成健康报告

保存到 `outputs/reports/health_YYYY-MM-DD.md`

报告格式：
```markdown
---
id: "outputs-reports-health-YYYY-MM-DD"
title: "知识库健康报告"
type: "report"
created_at: "YYYY-MM-DD"
check_type: "full"
---

# 知识库健康报告

生成时间：YYYY-MM-DD HH:MM

## 概览

| 指标 | 数值 |
|------|------|
| raw/ 总文件数 | N |
| raw/ 待编译 | M |
| wiki/ 总条目数 | K |
| wiki/ 概念数 | C |
| 健康评分 | [分数]/100 |

## 🔴 严重问题（优先级高）

| 问题类型 | 描述 | 涉及文件 | 建议操作 |
|---------|------|---------|---------|
| 矛盾 | [描述] | `[id1]`, `[id2]` | 手动审查并统一 |
| ID 重复 | [ID 值] | `[file1]`, `[file2]` | 重新分配 ID |
| 链接断裂 | [路径] | `[id]` | 修正 frontmatter |

## 🟡 中等问题（优先级中）

| 问题类型 | 描述 | 涉及文件 | 建议操作 |
|---------|------|---------|---------|
| 过时 | 超过 90 天未更新 | `[id]` | 检查新资料并更新 |
| 缺口 | [概念名] | 提及于 `[id]` | 创建新条目或修正链接 |
| 编译遗漏 | 超过 7 天待编译 | `[raw-id]` | 运行 /nexus:compile |

## 🟢 轻微问题（优先级低）

| 问题类型 | 描述 | 涉及文件 | 建议操作 |
|---------|------|---------|---------|
| 孤岛 | 无引用链接 | `[id]` | 添加到 index.md 或其他条目 |
| 标签缺失 | 无 tags 字段 | `[id]` | 补充分类标签 |

## 建议行动

1. [优先处理严重问题]
2. [更新过时的概念]
3. [补充缺口概念]
4. [运行 /nexus:compile 处理待编译文件]
```

## 步骤 5：输出摘要

```
🏥 健康检查完成

📊 报告位置：outputs/reports/health_YYYY-MM-DD.md

统计数据：
- raw/ 文件：N 个（待编译：M 个）
- wiki/ 条目：K 个（概念：C 个）

发现的问题：
- 🔴 严重：X 个
- 🟡 中等：Y 个
- 🟢 轻微：Z 个

健康评分：[分数]/100 [等级图标]

建议：[根据评分给出具体建议]
  - 90+：继续保持定期维护
  - 70-89：建议处理中等问题
  - 50-69：建议优先处理严重问题
  - <50：建议全面审查知识库
```

## 步骤 6：记录日志

在 `wiki/log.md` 中追加：
```markdown
## [当前日期]
- [健康检查] 生成报告：`outputs/reports/health_YYYY-MM-DD.md`
  - 健康评分：[分数]/100
  - 发现问题：严重 X、中等 Y、轻微 Z
```

## 注意事项

- 不要自动修复问题，只报告
- 孤岛页面不一定是问题（可能是新创建的）
- 定期（如每周）运行此命令维护知识库质量
- 所有检查基于 YAML frontmatter，确保文件格式正确
- 如果 frontmatter 解析失败，标记为"格式异常"并跳过该文件