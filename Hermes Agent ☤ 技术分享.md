# Hermes Agent ☤ 技术分享

> **Nous Research 出品 · MIT 开源 · 2026**
> 受众：技术团队内部 · 预计时长：60 min

---

## 目录

1. [它是什么？](#1-它是什么)
2. [为什么会有它？](#2-为什么会有它)
3. [它能做什么？](#3-它能做什么)
4. [它和同类工具有什么不同？](#4-它和同类工具有什么不同)
5. [整体架构](#5-整体架构)
6. [核心亮点深入](#6-核心亮点深入)
   - 6.1 [闭环学习系统：Agent 如何自己写"外挂"](#61-闭环学习系统agent-如何自己写外挂)
   - 6.2 [多 Agent 协作：4 种模式](#62-多-agent-协作4-种模式)
   - 6.3 ["有缰绳"：自由奔跑但不失控](#63-有缰绳自由奔跑但不失控)
7. [快速上手](#7-快速上手)

---

## 1. 它是什么？

**Hermes Agent 是一个完整的、可自托管的 AI Agent ，是Harness Engineering概念的第一次产品化**

用一句话说：它是一个你可以部署在自己服务器上、接入任意 LLM、通过 Telegram/微信/命令行等任意渠道使用的 AI 助手框架——而且它会随着使用不断变得更聪明。

它不是：

- ❌ 一个 SDK 或库（不是 LangChain 那种"你来搭积木"的框架）
- ❌ 一个绑定特定模型的产品（不是只能用 Claude 或 GPT）
- ❌ 一个只能在本地跑的工具（不是只能在你笔记本上用）

它是：

- ✅ 一个**开箱即用的 Agent **，安装后直接可用
- ✅ **模型无关**：接入 200+ 模型，一行命令切换，零代码改动
- ✅ **平台无关**：同一个 Agent 同时服务 CLI、Telegram、飞书、Discord……
- ✅ **会自我进化**：从每次对话中提炼经验，写成技能，下次直接复用

```
你在 Telegram 发消息 → Hermes 在云服务器上执行任务
                     → 任务完成后把结果发回给你
                     → 顺便把这次学到的东西写成技能存起来
```

---

## 2. 为什么会有它？

### 2.1 现有 Agent 工具的三个痛点

**痛点一：每次对话都是白板**

用过 ChatGPT 或 Claude 的人都有这个体验：今天教会它你的代码风格偏好，明天开新对话又得重新教一遍。大多数 Agent 框架也是如此——没有跨会话的记忆，没有从经验中学习的能力。

**痛点二：绑定特定模型/平台**

很多工具深度绑定 OpenAI 或 Anthropic，换个模型要改大量代码。或者只能在命令行用，不能在手机上通过 Telegram 控制。

**痛点三：只能在本地跑**

大多数 Agent 工具假设你就坐在电脑前。但如果你想让 Agent 在服务器上跑一个耗时几小时的任务，同时你在外面用手机查看进度呢？

### 2.2 Hermes 的答案

| 痛点          | Hermes 的解法                            |
| ------------- | ---------------------------------------- |
| 每次都是白板  | 闭环学习系统：技能自动创建 + 跨会话记忆  |
| 模型/平台绑定 | 插件化模型提供商 + 15+ 消息平台网关      |
| 只能本地跑    | 6 种终端后端（本地/Docker/SSH/云服务器） |

### 2.3 它从哪里来？

Hermes 的前身是 **OpenClaw**，一个简单的 CLI Agent 包装器。随着功能需求越来越多，团队决定从头重写，目标是：

> **"一个真正能在生产环境跑、会自我进化、不绑定任何模型或平台的 运行时 Agent "**

现在 Hermes 由 [Nous Research](https://nousresearch.com)（顶级开源模型研究机构）维护，MIT 开源，拥有约 17,000 个测试用例。

---

## 3. 它能做什么？

### 3.1 基础能力

安装后，`hermes` 命令就是一个功能完整的 AI 助手：

```bash
hermes   # 启动，开始对话
```

内置工具包括：

- 🌐 **网络**：搜索、抓取网页、浏览器自动化
- 💻 **终端**：执行命令、管理进程、操作文件
- 👁️ **视觉**：分析图片、生成图片
- 🎙️ **语音**：语音转文字、文字转语音
- 🔧 **代码**：执行 Python 脚本、调用工具

### 3.2 多平台接入

一个 `hermes gateway start` 命令，Agent 同时在线于：

```
Telegram · Discord · Slack · WhatsApp · Signal
飞书 · 钉钉 · 企业微信 · 微信 · QQ Bot
Email · SMS · Matrix · Mattermost · Home Assistant
```

你在 Telegram 发的消息和在命令行发的消息，Agent 看到的是同一个会话历史。

### 3.3 定时自动化

```bash
# 用自然语言设置定时任务
hermes cron add "每天早上 9 点总结昨天的 GitHub PR 动态，发到 Telegram"
hermes cron add "每周一生成上周工作报告"
hermes cron add "每小时检查服务器状态，异常时立即通知"
```

### 3.4 多 Agent 并行

```
"帮我同时测试这三个模块的性能"
→ Agent 自动拆分成 3 个子 Agent 并行执行
→ 汇总结果返回给你
```

### 3.5 自我进化（最核心的能力）

```
你：帮我分析这个 Python 项目的架构
Agent：[执行分析，给出结果]
[对话结束后，后台悄悄运行]
Agent 自己：这次分析用了一套不错的方法，我把它写成技能存起来
→ 创建 ~/.hermes/skills/python-architecture/SKILL.md
下次遇到类似任务，直接加载这个技能，不用重新摸索
```

---

## 4. 它和同类工具有什么不同？

### 4.1 和 ChatGPT / Claude.ai 的区别

|        | ChatGPT / Claude.ai         | Hermes                  |
| ------ | --------------------------- | ----------------------- |
| 部署   | 云端，Anthropic/OpenAI 控制 | 自托管，你控制          |
| 数据   | 发给第三方服务器            | 留在你自己的机器上      |
| 模型   | 固定（GPT/Claude）          | 任意模型，随时切换      |
| 记忆   | 有限，平台控制              | 完整跨会话记忆，你控制  |
| 自动化 | 手动触发                    | 内置 Cron，无人值守运行 |

### 4.2 和 Claude Code 的区别

Claude Code 是 Anthropic 为代码编辑场景打造的官方工具，TypeScript 全栈，IDE 集成极深。

Hermes 的侧重点不同：

|              | Claude Code             | Hermes                     |
| ------------ | ----------------------- | -------------------------- |
| **核心场景** | 代码编辑、IDE 集成      | 通用 Agent，多平台运行     |
| **模型**     | 主要 Claude 系列        | 200+ 模型，完全无绑定      |
| **平台**     | CLI + VS Code/JetBrains | CLI + 15+ 消息平台         |
| **自我进化** | 基础 Skills             | 完整闭环（创建→改进→归档） |
| **多 Agent** | 实验性                  | 4 种模式，生产就绪         |
| **开源协议** | 部分开源                | MIT 完全开源               |

### 4.3 和 LangChain / AutoGen 的区别

LangChain 是一个**框架库**，你用它来搭建自己的 Agent。Hermes 是一个**完整运行时**，你配置和扩展它。

```
LangChain：给你砖头和水泥，你来盖房子
Hermes：给你一栋已经建好的房子，你来装修和扩展
```

如果你的目标是"快速拥有一个可用的 Agent"，Hermes 更合适。
如果你的目标是"从零构建一个高度定制的 Agent 系统"，LangChain 更合适。

---

---

## 5. 整体架构

### 5.1 五层模型

```
┌──────────────────────────────────────────────────────────────┐
│  入口层                                                       │
│  hermes CLI/TUI · gateway（消息平台）· api_server · cron     │
├──────────────────────────────────────────────────────────────┤
│  会话管理层                                                   │
│  AIAgent · 预算控制 · 上下文压缩 · 记忆注入 · 提示构建       │
├──────────────────────────────────────────────────────────────┤
│  工具编排层                                                   │
│  工具分发 · 并行执行 · 危险命令守卫 · 插件钩子               │
├──────────────────────────────────────────────────────────────┤
│  工具注册层                                                   │
│  tools/registry.py（零依赖）· tools/*.py（自动发现）         │
├──────────────────────────────────────────────────────────────┤
│  模型提供商层                                                 │
│  plugins/model-providers/（28+ 提供商，插件化）              │
└──────────────────────────────────────────────────────────────┘
```

### 5.2 一个请求的完整生命周期

以"用户在 Telegram 发消息"为例：

```
1. 用户发消息 "帮我搜索最新的 Python 3.13 特性"
        ↓
2. Gateway（gateway/run.py）接收消息
   → 检查用户是否已配对（安全验证）
   → 加载会话历史
        ↓
3. AIAgent.run_conversation() 开始
   → 构建系统提示（注入技能、记忆、环境信息）
   → 调用 LLM API
        ↓
4. LLM 返回工具调用：web_search("Python 3.13 new features")
        ↓
5. model_tools.handle_function_call() 执行工具
   → 检查危险命令（这个是安全的，直接执行）
   → 调用 web_search 工具
   → 返回搜索结果
        ↓
6. LLM 收到结果，生成最终回答
        ↓
7. Gateway 把回答发回 Telegram
        ↓
8. [后台，用户不感知]
   Background Review Fork 启动
   → 分析这次对话有没有值得保存的技能或记忆
   → 如果有，写入 ~/.hermes/skills/ 或 MEMORY.md
```

### 5.3 工具自动发现机制

这是 Hermes 工程设计中最优雅的部分之一：

```python
# 添加一个新工具，只需创建一个文件：
# tools/my_tool.py

from tools.registry import registry

registry.register(
    name="my_tool",
    schema={...},
    handler=lambda args, **kw: my_tool(args),
)

# 就这样。不需要修改任何其他文件。
# model_tools.py 在启动时会自动扫描 tools/ 目录，
# import 每个文件，触发 register() 调用。
```

**依赖链单向流动**，任何工具文件都可以独立测试，不会产生循环依赖：

```
tools/registry.py（零依赖）
      ↑ 被导入
tools/*.py（各自注册）
      ↑ 触发发现
model_tools.py（编排层）
      ↑ 被使用
run_agent.py / cli.py（入口）
```

---

## 6. 核心亮点深入

### 6.1 闭环学习系统：Agent 如何自己写"外挂"

这是 Hermes 最核心的差异化能力，也是它被称为"自我进化 Agent"的原因。

#### 问题：为什么大多数 Agent 不会进化？

传统 Agent 的工作方式：

```
对话 1：用户教 Agent 如何处理 Python 依赖冲突
对话 2：用户又得重新教一遍
对话 3：还是得重新教
...
```

每次对话都是白板，经验无法积累。

#### Hermes 的解法：Background Review Fork

每次对话结束后，Hermes 会在后台悄悄做一件事：

```
主对话结束（用户立即收到回复，零延迟）
        ↓
[后台 daemon 线程启动，用户不感知]
        ↓
fork 一个新的 Agent 实例
→ 给它看刚才的完整对话记录
→ 问它："这次对话有没有值得保存的技能？"
        ↓
Review Agent 分析对话
→ 发现了一个处理 Python 依赖冲突的好方法
→ 调用 skill_manage(action="create") 写入技能文件
        ↓
~/.hermes/skills/python-deps/SKILL.md 创建完成
```

下次遇到类似问题，这个技能会被自动加载，Agent 直接复用经验。

#### 技能的"达尔文演化"

技能不是一次性写入的，它会随使用不断进化：

```
第 1 次：创建技能 "Python 依赖冲突处理"
第 3 次：用户说"你漏了检查虚拟环境这一步"
         → Review Fork 自动 patch 技能，补上这一步
第 10 次：技能已经包含了 10 次对话积累的经验
```

Review Agent 的 Prompt 明确要求优先 **patch 已有技能**，而不是无限新建：

```
优先级顺序：
1. 更新当前对话中已加载的技能（最优先）
2. 找到已有的同类技能并扩展它
3. 在已有技能下添加支持文件
4. 新建技能（最后手段）
```

#### Curator：技能的"自动清洁工"

技能库会不会越来越臃肿？Hermes 有一个后台维护系统 **Curator** 来解决这个问题。

Curator 是一个定期运行的独立 Agent（默认每 7 天，Agent 空闲 2 小时后启动），它会：

```
扫描所有 Agent 创建的技能
        ↓
30 天未使用 → 标记为 stale（过时）
90 天未使用 → 自动归档到 .archive/（永不删除，可恢复）
发现重复技能 → 合并
发现错误内容 → 修复
```

**关键设计**：Curator **永远不删除**，只归档。用户随时可以 `hermes curator restore` 恢复任何归档的技能。

#### 跨会话记忆：FTS5 全文搜索

所有会话历史存储在本地 SQLite 数据库，支持全文搜索：

```bash
# 在 CLI 中搜索历史
/session_search "Python 依赖冲突"
→ 找到 3 个月前那次解决方案的完整上下文
```

技术细节：使用两张 FTS5 虚拟表：

- 标准表：英文分词，适合英文搜索
- Trigram 表：3 字节滑动窗口分词，**专门解决中文搜索**（中文没有空格，普通分词器无法处理）

---

### 6.2 多 Agent 协作：4 种模式

当一个任务太复杂，单个 Agent 处理不过来时，Hermes 提供 4 种协作模式：

#### 模式对比

| 模式              | 类比                           | 适用场景                 |
| ----------------- | ------------------------------ | ------------------------ |
| `delegate_task`   | 临时雇一个助手，等他完成再继续 | 需要隔离上下文的子任务   |
| Kanban 看板       | 公司的项目管理系统             | 跨进程/长期/多人协作任务 |
| MoA 多模型投票    | 开会让多个专家各自发表意见     | 极难的推理/分析问题      |
| Background Review | 下班后自己复盘写总结           | 技能/记忆的自动沉淀      |

#### delegate_task：同步子 Agent

```python
# 单任务：父 Agent 等子 Agent 完成
delegate_task(goal="分析这份日志文件，找出所有异常")

# 批量并行：同时跑 3 个子 Agent
delegate_task(tasks=[
    {"goal": "测试模块 A"},
    {"goal": "测试模块 B"},
    {"goal": "测试模块 C"},
])
# 最多 3 个并发（可配置），父 Agent 等所有子 Agent 完成后汇总
```

子 Agent 的隔离设计：

- 有独立的上下文（看不到父 Agent 的对话历史）
- 有独立的终端会话（文件操作互不干扰）
- 默认**不能**再次委派（防止无限递归）
- 默认**不能**打断用户（不能调用 clarify 工具）

#### Kanban：工业级持久化工作队列

这是 Hermes 最接近"分布式任务队列"的设计，对标 Celery/RabbitMQ。

```bash
hermes kanban init my-project          # 创建看板
hermes kanban create "实现用户登录"    # 创建任务
hermes kanban assign task-001 coder    # 分配给 coder profile
hermes gateway start                   # 启动 Dispatcher
```

**和 delegate_task 的核心区别**：

```
delegate_task：进程崩溃 → 任务丢失
Kanban：进程崩溃 → 重启后任务自动恢复（SQLite 持久化）
```

Dispatcher 的工业级机制：

- 每 60 秒扫描一次，认领 ready 状态的任务
- Worker 定期发送心跳，超时未心跳 → 任务被重新认领
- 连续失败 2 次 → 自动 Block，防止无限重试

#### MoA：多模型投票

```
用户提问（极难的问题）
        ↓
4 个顶级模型并行生成答案（Claude / Gemini / GPT / DeepSeek）
        ↓
聚合模型综合 4 个答案，生成最终高质量回答
```

注意：这不是"子 Agent"，而是多个模型对同一个问题各自独立回答，然后由一个模型综合。适合需要多角度验证的复杂推理。

---

### 6.3 "有缰绳"：自由奔跑但不失控

AI Agent 落地企业最大的障碍是**不可控**：无限循环、乱删数据、绕过安全检查……

Hermes 的名字来自希腊神话信使之神，手持双蛇缠绕的权杖（☤ Caduceus）。"有缰绳"是 Hermes 的核心设计哲学：**Agent 越强大，控制机制就越重要**。

#### 7 道缰绳

**缰绳 1：预算上限 + 优雅收尾**

```
Agent 默认最多执行 90 次工具调用
预算耗尽时，不是粗暴截断，而是：
→ 触发一次特殊的 "Grace Call"
→ 系统提示注入："你的预算已耗尽，请总结已完成的工作并停止"
→ Agent 优雅地给出总结，然后停止
```

**缰绳 2：随时可中断**

```
用户发送新消息 或 /stop 命令
→ 主 Agent 立即停止
→ 所有正在运行的子 Agent 也被取消
→ 不会留下"孤儿进程"
```

**缰绳 3：危险命令审批**

```python
# tools/approval.py 中定义了两级模式：

# 硬封锁（永远拒绝，无论任何设置）：
- sudo rm -rf /
- mkfs（格式化文件系统）
- dd if=... of=/dev/...（写入原始设备）
# ... 12 个模式

# 危险模式（默认需要用户审批）：
- rm -rf（递归删除）
- DROP TABLE（删除数据库表）
- chmod -R 777（危险权限）
# ... 47 个模式
```

用户可以选择：

- 每次审批（默认）
- 永久允许某个命令（加入白名单）
- YOLO 模式（跳过所有审批，适合自动化场景）

**缰绳 4：工具循环守卫**

Agent 有时会陷入循环：同一个工具调用失败了，但它不断重试。Hermes 会检测这种情况：

```
同样的工具调用失败 2 次 → 警告 Agent 改变策略
同样的工具调用失败 5 次 → 阻断，强制 Agent 换思路
同一个工具失败 8 次 → 硬停止这个工具路径
```

**缰绳 5：Cron 任务 3 分钟硬中断**

定时任务中的 Agent 如果失控循环，最多运行 3 分钟就会被强制终止，防止占用调度器。

**缰绳 6：子 Agent 危险命令自动拒绝**

子 Agent 运行在后台线程，无法弹出审批对话框。默认策略：**危险命令自动拒绝**，并记录日志。（可配置为自动允许，适合完全自动化的批处理场景）

**缰绳 7：YOLO 模式冻结（最精妙的安全设计）**

```python
# tools/approval.py
# 在模块 import 时冻结，而不是每次调用时读取
_YOLO_MODE_FROZEN = is_truthy_value(os.getenv("HERMES_YOLO_MODE", ""))
```

为什么要在 import 时冻结？如果每次调用都读取环境变量，一个恶意的技能文件可以在运行时执行 `os.environ["HERMES_YOLO_MODE"] = "1"` 来绕过所有审批。冻结后，这个攻击路径被彻底关闭。

#### 供应链安全：litellm 事件的教训

2026 年 5 月，Mini Shai-Hulud 蠕虫事件（通过恶意 PyPI 包传播）后，Hermes 建立了严格的依赖锁定策略：

```toml
# pyproject.toml — 所有依赖精确锁定
"openai==2.24.0"
"httpx[socks]==0.28.1"
# GitHub Actions — 锁定到 commit SHA，而不是版本标签
uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683  # v4
# 版本标签可以被重新指向，SHA 不能
```

---

## 7. 快速上手

### 安装

```bash
# Linux / macOS / WSL2
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
source ~/.zshrc
hermes
```

### 接入阿里百炼（Qwen 系列）

```bash
# 方式一：环境变量（已写入 ~/.zshrc）
export DASHSCOPE_API_KEY="sk-xxxx"

# 方式二：配置文件
echo "DASHSCOPE_API_KEY=sk-xxxx" >> ~/.hermes/.env

# 启动后选择模型
hermes model   # 选择 qwen 提供商 → qwen-max 或 qwen-plus
```

### 常用命令

```bash
# 启动
hermes                    # 交互式 CLI
hermes --tui              # 现代 TUI 界面

# 配置
hermes model              # 切换模型
hermes tools              # 配置工具集
hermes setup              # 全量配置向导

# 多平台
hermes gateway start      # 启动消息网关（Telegram/Discord 等）

# 会话内命令
/skills                   # 查看已安装技能
/memory                   # 查看记忆内容
/new                      # 开始新会话
/compress                 # 手动压缩上下文
/model qwen:qwen-max      # 临时切换模型
```

### Demo：看 Agent 自己创建技能

```bash
# 1. 执行一个有一定复杂度的任务
hermes
> 帮我分析一下这个目录下的 Python 项目结构，给出改进建议

# 2. 对话结束后等几秒，会看到类似这样的提示：
# 💾 Self-improvement review: Created skill 'python-project-analysis'

# 3. 查看 Agent 自己写的技能文档
cat ~/.hermes/skills/python-project-analysis/SKILL.md
```

---

## 附录：核心文件速查

| 文件                         | 一句话说明                    |
| ---------------------------- | ----------------------------- |
| `run_agent.py`               | Agent 的大脑，核心对话循环    |
| `cli.py`                     | 命令行界面                    |
| `model_tools.py`             | 工具编排，连接 Agent 和工具   |
| `toolsets.py`                | 定义哪些工具组合在一起        |
| `hermes_state.py`            | 会话历史存储（SQLite + FTS5） |
| `agent/background_review.py` | 后台技能/记忆回顾             |
| `agent/curator.py`           | 技能生命周期管理              |
| `tools/approval.py`          | 危险命令审批系统              |
| `tools/delegate_tool.py`     | 子 Agent 委派                 |
| `tools/kanban_tools.py`      | Kanban 多 Agent 协作          |
| `gateway/run.py`             | 消息网关（15+ 平台）          |
| `tools/registry.py`          | 工具注册表（零依赖）          |

---

*基于 hermes-agent main 分支（2026-05）· MIT License · [GitHub](https://github.com/NousResearch/hermes-agent)*
