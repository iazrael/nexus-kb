# AI Agent 时间感知与主动智能研究资料汇总

> 采集时间: 2026-05-19
> 关键词: time-aware agent, proactive agent, autonomous, heartbeat, cron, chatty agent

---

## 一、核心发现

### 1. LLM 天生不具备时间感知能力

ICLR 2026 Workshop 论文 "Can LLMs Perceive Time?" (Garikaparthi, 2026) 实证研究表明：
- LLM 对自身任务耗时的预估偏差 4-7 倍（p < 0.001）
- 在需要对任务时长排序的测试中，GPT-5 在反直觉配对上只有 18% 正确率
- 即使任务完成后，回顾性估计仍然偏离实际一个数量级
- 核心结论：**LLM 的时间调度必须依赖外部信号，不能指望模型自身**

### 2. ProactiveAgent 开源框架

GitHub: leomariga/ProactiveAgent
核心循环: **wake → decide → respond → sleep**

三大组件：
1. **Decision Engine** ("Should I Respond?") - 评估对话上下文、时间间隔、话题紧迫性
2. **Message Generation** ("Respond") - 基于历史和时序生成回复
3. **Sleep Calculator** ("How Long Should I Wait?") - 决定下次唤醒时间

特点：
- 所有组件可替换（decision engine、sleep calculator 均可自定义）
- 支持 AI-based 和 rule-based 两种决策引擎
- 支持回调监控 agent 行为
- 实现了"话痨"agent：casual teenager 角色可自动发消息

### 3. Heartbeat + Cron + Memory 三层架构

来源: n1n.ai 博客 (2026-04-02)

**Layer 1: Heartbeat（心跳机制）**
- 每 5-15 分钟唤醒 agent
- 唤醒后执行检查清单：收件箱、目标文件、上下文文件
- 决定是否有事要做，没事就返回 HEARTBEAT_OK 继续休眠

**Layer 2: Cron（定时任务）**
- 时间驱动的确定性触发器
- 适合周期性高优任务：每日报告、数据同步等

**Layer 3: Memory（记忆系统）**
- 多层存储确保跨会话上下文持久化
- 短期 + 长期 + 向量检索

### 4. Proactive Agent 学术论文 (ICLR 2025)

"Proactive Agent: Shifting LLM Agents from Reactive Responses to Active Assistance"

核心框架：
- 通过观察用户行为和环境状态，预测用户可能需要的任务
- 构建 ProactiveBench 数据集，训练奖励模型评估"主动性"
- 微调后显著提升 LLM agent 的主动任务发起能力
- 关键创新：**agent 不等待指令，而是通过环境感知主动提议任务**

### 5. OpenClaw Proactive Agent Skill (ClawHub)

148.6k 下载量，729 stars
核心模式：
- WAL Protocol（Write-Ahead Log）
- Working Buffer
- Autonomous Crons vs Prompted Crons
- Curiosity Loop / Pattern Recognition Loop / Outcome Tracking Loop

### 6. 知乎：主动式语音 Agent 设计思考

主动式 Agent 在语音场景下的关键特性：
- 根据自主思考结果驱动目标完成
- 不依赖用户指令，主动做出决策
- 语音场景对主动性要求更高（因为用户可能不方便打字）

---

## 二、技术方案对比

| 方案 | 机制 | 时间感知 | 适用场景 | 复杂度 |
|------|------|----------|----------|--------|
| ProactiveAgent | wake-decide-respond-sleep | ✅ 动态 sleep | 聊天陪伴、话痨 agent | 低 |
| Heartbeat + Cron | 周期唤醒 + 定时任务 | ✅ 外部注入 | 监控、自动化运维 | 中 |
| ProactiveBench (学术) | 环境感知 + 任务预测 | ⚠️ 需训练数据 | 办公助手、任务管理 | 高 |
| OpenClaw Proactive Skill | WAL + Cron + Memory | ✅ 多层架构 | 个人 AI 助手 | 中 |
| 纯 Cron + LLM | 固定时间触发 | ⚠️ 静态 | 定时报送、提醒 | 低 |

---

## 三、实现"话痨 Agent"的最佳实践

### 核心要素
1. **外部时间注入**: 系统每次调用 LLM 时注入当前时间戳
2. **沉默检测**: 记录最后一条用户消息时间，超阈值触发主动对话
3. **上下文连贯**: 维护对话历史，主动消息与上文自然衔接
4. **频率控制**: cooldown + 每小时上限 + 相关性评分，避免骚扰
5. **个性化决策**: 基于用户画像和历史互动模式调整主动频率

### 参考代码架构
```python
class ChattyAgent:
    def __init__(self):
        self.last_user_message_time = None
        self.cooldown_minutes = 30
        self.max_hourly_proactive = 3
    
    def on_user_message(self, msg):
        self.last_user_message_time = datetime.now()
    
    def heartbeat(self):
        if self.last_user_message_time is None:
            return  # 从未互动
        
        silence_duration = datetime.now() - self.last_user_message_time
        
        if silence_duration > timedelta(minutes=self.cooldown_minutes):
            # 询问 LLM 是否应该主动说话
            should_speak = self.decision_engine.evaluate(
                context=self.conversation_history,
                silence_minutes=silence_duration.total_seconds() / 60,
                current_time=datetime.now()
            )
            if should_speak:
                message = self.generate_proactive_message()
                self.send(message)
```

---

## 四、关键参考链接

- ProactiveAgent: https://github.com/leomariga/ProactiveAgent
- ICLR 2026 论文 (LLM 时间感知): https://openreview.net/forum?id=x8nL1qVPD0
- ICLR 2025 论文 (主动式 Agent): https://arxiv.org/html/2410.12361v3
- Heartbeat+Cron+Memory: https://explore.n1n.ai/blog/building-autonomous-ai-agents-heartbeat-cron-memory-2026-04-02
- OpenClaw Proactive Skill: https://llmbase.ai/openclaw/proactive-agent/
- 主动式语音Agent: https://zhuanlan.zhihu.com/p/1918777324586922519
