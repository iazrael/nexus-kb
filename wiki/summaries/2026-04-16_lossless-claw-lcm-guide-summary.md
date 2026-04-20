---
id: "wiki-summaries-2026-04-16-lossless-claw-lcm-guide"
title: "Lossless-claw LCM 配置指南摘要"
type: "summary"
created_at: "2026-04-20"
derived_from:
  - "raw/articles/2026-04-16T230800+0800_lossless-claw-lcm-guide.md"
tags:
  - "summary"
  - "openclaw"
  - "lcm"
  - "memory"
status: "active"
---

# Lossless-claw LCM 配置指南摘要

## 一句话总结
LCM 插件通过向量压缩实现近乎无限的记忆保持，四个核心参数控制记忆翻译、保留深度、递归层级和触发阈值。

## 关键要点
1. **LCM_EMBEDDING_MODEL=nomic-embed-text**：本地轻量级模型，无需 API 密钥
2. **LCM_FRESH_TAIL_COUNT=32**：最近 32 轮对话不压缩，保证流畅性
3. **LCM_CONTEXT_THRESHOLD=0.75**：上下文占用达 75% 时自动压缩旧记忆