---
id: "raw-articles-2026-04-16-openclaw-skill-guide"
title: "OpenClaw 自己写 Skill 教程（2026）：SKILL.md 结构 + 实战示例"
type: "article"
created_at: "2026-04-16T14:02:50+0800"
published_at: "2026-03-11"
source_url: "https://tbbbk.com/openclaw-create-custom-skill-guide-2026/"
source_author: null
source_platform: "blog"
tags:
  - "openclaw"
  - "skill"
  - "tutorial"
  - "ai-automation"
  - "markdown"
status: "pending"
compiled_to: []
---

# OpenClaw 自己写 Skill 完整教程（2026）：从零开始，10 分钟做出第一个技能包

11 MAR 2026  阅读时间 10 分钟  折腾日记

ClawHub 没有你想要的 Skill？自己写一个。Skill 的本质是一个说明文档，不需要写代码，会 Markdown 就够了。这篇从建文件夹开始，带你做出第一个能实际跑起来的 Skill。

之前写了一篇 ClawHub 精选 Skill 推荐，但是有个问题：「ClawHub 上没有我想要的功能，能自己写一个吗？」

能。而且比你想的简单很多。

Skill 的本质就是一个文件夹，里面放一个 SKILL.md，告诉 OpenClaw「遇到什么情况、按什么步骤干活」。不需要写代码，不需要懂 API，会写 Markdown 就够了。复杂一点的 Skill 可以带脚本，但最小可用版本就是一个纯文本文件。

这篇从头到尾走一遍：Skill 是什么、文件结构怎么组织、SKILL.md 怎么写、怎么调试、写好之后怎么发布到 ClawHub 让别人也能用。我自己在用的 trend-scout Skill 就是这么做出来的，文章里会拿它当真实例子。

## Skill 是什么？不是插件，是「说明书」

很多人第一反应觉得 Skill 是类似浏览器插件那种东西——安装完有个运行时在后台跑。实际上不是。

Skill 更像是给 AI 的一份操作说明书。你在 SKILL.md 里写清楚：什么时候用这个技能、用的时候按什么步骤走、输出什么格式。OpenClaw 的 AI 读到这份说明，就知道遇到对应情况该怎么处理。

举个具体的例子。我给博客做了一个 trend-scout Skill，作用是定期扫描 V2EX、Hacker News、GitHub Trending、少数派、小众软件，找出适合写成文章的热门话题。每次触发的时候，AI 会读 SKILL.md 里的流程，拉各个平台的 API，过滤掉不相关的内容，最后整理成一份选题建议发给我。

这个 Skill 没有「安装后台服务」这回事。它就是一个文件夹，里面有说明文档和几个辅助脚本。OpenClaw 启动的时候自动扫描 skills 目录，把找到的 Skill 注册进来，之后 AI 就知道有这个能力了。

## 文件结构：最小只需要一个文件

一个最简单的 Skill 长这样：

```
~/.openclaw/workspace/skills/
└── my-skill/
    └── SKILL.md        ← 这一个文件就够了
```

复杂一点，带脚本和参考资料：

```
~/.openclaw/workspace/skills/
└── trend-scout/
    ├── SKILL.md              ← 核心：技能说明和工作流程
    ├── scripts/
    │   └── scan-trends.sh    ← 辅助脚本（可选）
    └── references/
        └── sources.md        ← 参考资料（可选）
```

放哪里？默认位置是 ~/.openclaw/workspace/skills/。OpenClaw 启动时会自动扫这个目录下的所有子文件夹，找 SKILL.md 并注册。不需要手动配置，放进去就生效。

## SKILL.md 怎么写：三个部分

一个标准的 SKILL.md 分三个部分：frontmatter、触发说明、工作流程。

### 第一部分：frontmatter（必须有）

```yaml
---
name: trend-scout
description: >
  扫描 V2EX、Hacker News、GitHub Trending、少数派、小众软件，
  发现适合博客的选题方向。
  Use when: 用户要找新选题，或每 2 天定期触发。
  NOT for: 分析已有流量数据（那个用 GSC/Bing 工具直接查）。
---
```

frontmatter 里最关键的是 description。OpenClaw 在决定「要不要用这个 Skill」的时候，主要靠这段描述判断。写清楚两件事：适合什么场景用，以及不适合什么场景。后者经常被忽略，但很重要——AI 不知道边界的话，会在不该用的时候用。

### 第二部分：触发条件

```markdown
## When to Run

- 每 2 天通过 heartbeat 或 cron 触发
- 用户主动问「有什么可以写的选题」
- 现有关键词数据感觉重复、没新意的时候
```

这部分用自然语言写就行，不需要代码。目的是让 AI 知道这个 Skill 的使用时机。

### 第三部分：工作流程（核心）

```markdown
## Workflow

1. 读 `references/sources.md` 了解信息源列表
2. 跑 `scripts/scan-trends.sh` 拉取 V2EX、HN、GitHub、少数派、小众软件的数据
3. 对于被 Cloudflare 拦截的来源（Linux.do、Reddit），用 web_search 代替：
   - `web_search "site:linux.do 热门"`
4. 对照博客受众过滤（见下方 Filtering 部分）
5. 输出排名选题列表

## Filtering Criteria

匹配条件：
- 受众：国内技术用户，关注 VPS/服务器、AI 工具、白嫖资源、支付工具
- 排除：企业 SaaS、纯代码教程、没有实操价值的新闻
- 优先：有免费版的工具、可自托管项目、需要中文教程的内容
```

工作流程写得越具体，AI 执行得越准。「拉数据、过滤、输出」这种三步骤写法太模糊，不如直接写「跑哪个脚本」「用什么 API」「遇到什么情况用什么 fallback」。

## 带脚本的 Skill：真实的 scan-trends.sh

纯文字说明有时候不够用，比如「批量拉 8 个平台的 API」这种事，每次让 AI 一条一条手写 curl 太慢，而且结果格式不统一。这时候写一个辅助脚本，让 AI 直接调用就干净多了。

我的 scan-trends.sh 干一件事：依次请求 V2EX、Hacker News、GitHub Trending、少数派、小众软件，把结果存成 JSON 文件放到 /tmp/trend-scout/，然后 AI 再读这些 JSON 来分析。

关键片段长这样：

```bash
#!/bin/bash
OUTPUT_DIR="${1:-/tmp/trend-scout}"
mkdir -p "$OUTPUT_DIR"

# V2EX 热门话题（有公开 API，不需要认证）
curl -s "https://www.v2ex.com/api/topics/hot.json" | python3 -c "
import json, sys
data = json.load(sys.stdin)
results = []
for t in data[:10]:
    results.append({
        'title': t.get('title',''),
        'node': t.get('node',{}).get('title',''),
        'replies': t.get('replies', 0)
    })
print(json.dumps(results, ensure_ascii=False, indent=2))
" > "$OUTPUT_DIR/v2ex.json"

# Hacker News（Firebase 实时数据库，稳定好用）
curl -s "https://hacker-news.firebaseio.com/v0/topstories.json" | python3 -c "
import json, sys, urllib.request
ids = json.load(sys.stdin)[:10]
results = []
for id in ids:
    resp = urllib.request.urlopen(f'https://hacker-news.firebaseio.com/v0/item/{id}.json')
    item = json.loads(resp.read())
    if item and item.get('type') == 'story':
        results.append({'title': item.get('title',''), 'score': item.get('score',0)})
print(json.dumps(results, ensure_ascii=False, indent=2))
" > "$OUTPUT_DIR/hn.json"
```

脚本放在 scripts/ 子目录下，在 SKILL.md 的 Workflow 里直接引用路径：

```markdown
## Workflow

2. 跑 `scripts/scan-trends.sh` 拉取数据
```

AI 看到这行，会用 exec 工具直接跑这个脚本，不需要你再解释参数。

一个小踩坑：脚本里如果用了 python3，要确认服务器上装了。我当时写完发现 GitHub Trending 那段解析 HTML 的代码跑不起来，排查了半天才发现是正则表达式里有个分组写错了。所以写完脚本先手动跑一遍确认输出正常，再写进 Skill。

## 从零开始做一个最简单的 Skill：daily-brief

光看 trend-scout 这种复杂的可能有点晕，我再做一个最小版本的演示，从建文件夹开始。

需求：每天早上自动生成一份简报，包含上海天气和 V2EX 热帖，推送到 Telegram。

第一步，建文件夹和文件：

```bash
mkdir -p ~/.openclaw/workspace/skills/daily-brief
touch ~/.openclaw/workspace/skills/daily-brief/SKILL.md
```

第二步，写 SKILL.md：

```markdown
---
name: daily-brief
description: >
  每天早上生成简报：上海天气 + V2EX 热帖前 5 条。
  Use when: 用户说"生成今日简报"，或 cron 在早上 8 点触发。
  NOT for: 详细的天气预报或深度新闻分析。
---

# Daily Brief

## When to Run

- 每天 8:00 AM 通过 cron 触发
- 用户主动说「给我今天的简报」「今天有什么热点」

## Workflow

1. 拉取上海天气：
   `curl "https://wttr.in/Shanghai?format=3"`
2. 拉取 V2EX 热帖前 5 条：
   `curl https://www.v2ex.com/api/topics/hot.json`
   取 title 和 node.title 字段
3. 整合输出以下格式，推送 Telegram

## Output Format

📅 {今天日期}
🌤 天气：{wttr.in 返回结果}

🔥 V2EX 今日热帖：
1. {标题}（{节点}）
2. ...（共 5 条）
```

第三步，重启 gateway 让 Skill 生效：

```bash
openclaw gateway restart
```

然后在 Telegram 里对 OpenClaw 说「给我今天的简报」，它就会按 Workflow 里的步骤跑，拉天气、拉热帖、整合成格式推给你。整个流程大概花了 10 分钟，没写一行逻辑代码。

如果想让它每天自动跑，加个 cron（cron 的详细用法可以看进阶配置教程）：

```bash
openclaw cron add \
  --name "早间简报" \
  --cron "0 8 * * *" \
  --tz "Asia/Shanghai" \
  --session main \
  --message "生成今日简报并推送"
```

## 调试：Skill 没触发怎么排查

写完发现 AI 不用这个 Skill，或者用错了，几个常见原因：

1. **description 写得太模糊**
   AI 判断要不要用 Skill，靠的是 frontmatter 里的 description。如果写的是「处理各种任务」这种宽泛的话，AI 不知道该不该用，通常选择不用。改成「当用户说 XX 时使用，不适用于 XX」这种明确格式。

2. **文件夹结构不对**
   SKILL.md 必须直接放在 skills 子目录下，不能再嵌套一层。正确结构是 skills/my-skill/SKILL.md，不是 skills/my-skill/src/SKILL.md。

3. **改完没重启**
   OpenClaw 在启动时扫描 skills 目录，改完 SKILL.md 需要重启 gateway 才能生效：
   ```bash
   openclaw gateway restart
   ```

4. **脚本没有执行权限**
   如果 Skill 带了 shell 脚本，要给执行权限：
   ```bash
   chmod +x ~/.openclaw/workspace/skills/my-skill/scripts/*.sh
   ```

调试的时候可以直接在 OpenClaw 对话里说「用 daily-brief Skill 生成简报」，强制指定 Skill，看看执行过程哪步出了问题。

## 发布到 ClawHub：让别人也能用

Skill 写好了，如果觉得对别人也有用，可以发布到 ClawHub。整个过程比想象的顺，用 clawhub CLI 工具几条命令搞定。

先装 CLI（如果没装过）：

```bash
npm install -g clawhub
```

在 Skill 文件夹里初始化发布配置：

```bash
cd ~/.openclaw/workspace/skills/daily-brief
clawhub init
```

它会问几个问题：Skill 名称、简介、版本号、分类。填完会生成一个 clawhub.json。

然后登录并发布：

```bash
clawhub login    # 用 GitHub 账号授权
clawhub publish  # 发布
```

发布成功后，别人就可以用 npx clawhub install daily-brief 安装你的 Skill 了。ClawHub 那边会做安全扫描（VirusTotal + OpenClaw 双重检测），过了之后会显示绿色安全标记。

发布之前建议检查一下 SKILL.md 里有没有硬编码的个人信息，比如 API Key、服务器 IP、个人路径——这些要替换成占位符或者从环境变量读取。

## 几个实用的写法技巧

踩坑后总结了几个写 SKILL.md 的经验：

1. **流程步骤写命令，不写意图。**「搜索相关资料」不如「用 web_search 搜索 '{关键词} site:github.com'」。前者 AI 会自由发挥，后者会按你说的做。

2. **加 NOT for。**在 description 里写清楚这个 Skill 不适合什么场景，防止 AI 在不该用的地方误触发。我的 trend-scout 里写了「NOT for: 分析已有流量数据」，就是因为最开始 AI 总是在我问流量数据的时候也跑这个 Skill。

3. **Output Format 单独写一节。**如果你要求固定格式输出（比如推送给 Telegram），把格式模板明确写出来，连 emoji 都可以指定，AI 会按格式来。

4. **辅助文件放 references/。**不要把大段参考内容（比如 100 行的信息源列表）直接堆在 SKILL.md 里，放到 references/ 子目录，在 Workflow 里引用路径。这样 SKILL.md 保持简洁，AI 读起来也快。

就这些。自己写 Skill 其实门槛很低，主要是把流程想清楚，写清楚，剩下的交给 AI 执行。有什么做出来比较有意思的 Skill 可以评论区聊聊，或者发到 ClawHub 上来。
