---
id: "wiki-concepts-SmartApproval"
title: "智能审批安全模型"
type: "concept"
created_at: "2026-05-06"
updated_at: "2026-05-06"
derived_from:
  - "raw/articles/2026-05-06T132500+0800_openclaw-vs-hermes-agent-framework.md"
tags:
  - "agent"
  - "security"
  - "hermes"
  - "openclaw"
  - "approval"
status: "active"
related_concepts: []
---

# 智能审批安全模型

## 定义
Agent 执行高权限操作前的安全审查机制。两种范式：OpenClaw 的"默认安全 + 显式声明"vs Hermes 的"辅助模型风险评估 + 条件自动通过"。

## 核心要点

1. **OpenClaw：默认安全**
   - 10+ 安全模块，危险工具白名单
   - 高权限需显式声明（allow-once 单次有效）
   - SECURITY.md + 漏洞响应流程
   - 多层审批：用户确认、沙箱隔离、审计日志
   > "安全、稳定才是上生产的必须，10多个安全审计模块完全是工程实践的产物"

2. **Hermes：智能审批**
   - 用便宜辅助模型评估命令风险级别
   - 低风险自动通过，高风险需用户确认
   ```
   risk_level = cheap_model.evaluate(command)
   if risk_level == "low": return True  # 自动通过
   else: return ask_user(command)       # 需用户确认
   ```
   - 附加上下文注入防护（提示注入、不可见 Unicode、凭证外泄检测）

3. **核心权衡**：
   - 安全性 vs 便利性：OpenClaw 偏安全，Hermes 偏流畅
   - 辅助模型的信任问题：判断不准时引入新的安全漏洞
   - 源码中未见对辅助模型判断结果的二次检查机制

4. **上下文压缩的安全差异**：
   - OpenClaw：从最老轮次压缩，原始消息归档可回溯
   - Hermes：保护两端压缩中间，迭代摘要式，跨多次压缩保持连续性

## 边界条件

### 不是什么
- 不是完整的网络安全方案（仅覆盖 Agent 执行层）
- 不是权限管理替代品（是执行前的风险拦截）

### 反模式
- 盲目信任辅助模型的风险评估（模型可能被 prompt injection 绕过）
- 在生产环境禁用用户确认环节（即便辅助模型判定低风险）

### 待验证
- 辅助模型在对抗性输入下的鲁棒性
- 实际使用中"低风险自动通过"的误判率
