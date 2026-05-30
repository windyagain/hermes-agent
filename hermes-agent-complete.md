# Hermes Agent ☤ 完整指南

> Nous Research 出品 · MIT 开源 · 2026
> 整合技术分享 + 橙皮书精华，面向技术团队内部

---

## 目录

1. [认识 Hermes Agent](#1-认识-hermes-agent)
2. [整体架构](#2-整体架构)
3. [核心亮点深入](#3-核心亮点深入)
4. [快速上手](#4-快速上手)

---

## 1. 认识 Hermes Agent

### 1.1 它是什么

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

### 1.2 为什么会有它

现有 Agent 工具有三个共同痛点：每次对话都是白板（经验无法跨会话积累）、深度绑定特定模型或平台（换模型要改大量代码）、只能在本地跑（无法在服务器后台长时间运行同时用手机查看进度）。

| 痛点 | Hermes 的解法 |
|---|---|
| 每次都是白板 | 闭环学习系统：技能自动创建 + 跨会话记忆 |
| 模型/平台绑定 | 插件化模型提供商 + 15+ 消息平台网关 |
| 只能本地跑 | 6 种终端后端（本地/Docker/SSH/云服务器） |

Hermes 由 [Nous Research](https://nousresearch.com) 维护，MIT 开源，约 17,000 个测试用例。

### 1.3 它能做什么

一个 `hermes` 命令启动，内置 40+ 工具覆盖网络（搜索、抓取、浏览器自动化）、终端（执行命令、操作文件）、视觉（图片分析/生成）、代码执行等场景。一个 `hermes gateway start` 命令，同时接入 15+ 消息平台：

```
Telegram · Discord · Slack · WhatsApp · Signal · 飞书 · 钉钉 · 企业微信 · 微信 · QQ Bot · Email · SMS
```

内置 Cron 调度器支持自然语言设置定时任务，多 Agent 并行可以把复杂任务拆分给多个子 Agent 同时执行。

### 1.4 一个简单的闭环系统

Hermes 的架构可以用一条线串起来：

```
学习循环 → 三层记忆 → Skill 系统 → 40+ 工具 → 多平台 Gateway
```

其中最核心的是**学习循环**，五个环节形成一个持续改进的飞轮：

```mermaid
flowchart LR
    A["策划记忆\n每轮对话后\n主动决定记什么"] --> B["创建 Skill\n复杂任务完成后\n自动提炼方法论"]
    B --> C["Skill 自改进\n使用中收到反馈\n自动 patch Skill 文件"]
    C --> D["FTS5 召回\n新对话开始前\n按需检索历史经验"]
    D --> E["用户建模\nHoncho 推理\n偏好和行为模式"]
    E --> A

    style A fill:#1a2a3e,stroke:#4a9eff,color:#e8e8f0
    style B fill:#1a2a1e,stroke:#4ade80,color:#e8e8f0
    style C fill:#2a1a1e,stroke:#fb923c,color:#e8e8f0
    style D fill:#2a1a3e,stroke:#7c5cbf,color:#e8e8f0
    style E fill:#1a2a2e,stroke:#FFD700,color:#e8e8f0
```

记忆提供了 Skill 创建的素材，Skill 在使用中积累反馈触发自改进，FTS5 让历史经验被精准召回，用户建模把这些碎片拼成一幅完整的画。**用得越多，每个环节都在变强，而且是同时变强。**

### 1.5 它和同类工具的关系

三个工具定位不同，不是竞争关系：Claude Code 做交互式编码（结对工程师）、OpenClaw 做配置即行为（写 SOUL.md 定制行为）、Hermes 做自主后台 + 自改进（7×24 自己跑自己学）。三者都采用 agentskills.io 标准，**Skill 可以互通**。

| 维度 | Hermes Agent | OpenClaw |
|---|---|---|
| **核心理念** | 自改进学习循环 | 配置即行为（SOUL.md） |
| **记忆** | 三层自改进（会话/持久/Skill） | 多层记忆，人工维护为主 |
| **Skill 维护** | Agent 自动创建 + 自改进 | 人工编写和维护 |
| **用户建模** | Honcho 辩证建模 | 基于 SOUL.md 配置 |
| **生态规模** | 40+ 内置工具 + MCP 6000+ | ClawHub 44000+ 社区 Skill |
| **部署方式** | 自托管（$5 VPS 起） | 官方托管/自托管 |

OpenClaw 生态更成熟，Hermes 学习能力更强。一句话区分：**OpenClaw 是你养出来的龙虾，Hermes 是自己会长大的龙虾。**

#### Harness Engineering：理解 Hermes 的钥匙

Hermes 是 **Harness Engineering** 概念的第一次产品化。核心洞察：AI 的瓶颈不是模型，是环境——同一个模型只调整"缰绳"配置，成绩可以从 Top 30 跳到 Top 5，模型一行没改。

Harness Engineering 把方法论拆成五个组件，Hermes 把它们全部内建：

| Harness 五组件 | 手动实现方式 | Hermes 内建系统 |
|---|---|---|
| **指令层** | 手写 CLAUDE.md / AGENTS.md | Skill 系统（自动创建 + 自改进） |
| **约束层** | 配置 hooks / linter / CI | Tool permissions + toolset 按需启用 |
| **反馈层** | 人工审查 / 评估者 | Agent 自改进学习循环 |
| **记忆层** | 手动维护 knowledge base | 三层记忆 + Honcho 用户建模 |
| **编排层** | 自己搭多 Agent pipeline | 子 Agent 委派 + cron 调度 |

如果你用过 Claude Code 的 CLAUDE.md + hooks + memory，你已经在手动实现 Harness 了。Hermes 把这套手动流程变成了自动运行的系统。

---

## 2. 整体架构

### 2.1 五层模型

```
┌──────────────────────────────────────────────────────────────┐
│  入口层                                                       │
│  hermes CLI/TUI · gateway（消息平台）· api_server · cron     │
├──────────────────────────────────────────────────────────────┤
│  会话管理层                                                   │
│  AIAgent · 预算控制 · 上下文压缩 · 记忆注入 · 提示构建       │
│  三层记忆（会话/持久/Skill）· Honcho 用户建模（可选外挂）    │
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



### 2.2 依赖链单向流动

```
tools/registry.py（零依赖）
      ↑ 被导入
tools/*.py（各自注册）
      ↑ 触发发现
model_tools.py（编排层）
      ↑ 被使用
run_agent.py / cli.py（入口）
```

添加一个新工具只需创建一个文件，`run_agent.py` 不需要改动任何一行。这是教科书级的开闭原则实践。

### 2.3 工具自动发现机制

```python
# tools/my_tool.py — 只需创建这一个文件
from tools.registry import registry

registry.register(
    name="my_tool",
    schema={...},
    handler=lambda args, **kw: my_tool(args),
)
# model_tools.py 在启动时自动扫描 tools/ 目录，import 每个文件，触发 register()
```

### 2.4 一个请求的完整生命周期

```
1. 用户发消息（Discord/Telegram/CLI）
        ↓
2. Gateway 接收 → 鉴权 → 加载会话历史
        ↓
3. AIAgent.run_conversation() 开始
   → 构建系统提示（注入技能列表、记忆、环境信息）
   → 调用 LLM API
        ↓
4. LLM 返回工具调用
        ↓
5. handle_function_call() 执行工具
   → 检查危险命令（47 个危险模式 + 12 个硬封锁）
   → 调用具体工具，返回结果
        ↓
6. LLM 收到结果，生成最终回答
        ↓
7. Gateway 把回答发回平台
        ↓
8. [后台，用户不感知，零延迟]
   Background Review Fork 启动（daemon 线程）
   → 分析对话有没有值得保存的技能或记忆
   → 写入 ~/.hermes/skills/ 或 MEMORY.md
```

### 2.5 三层插件系统

```
通用插件（~/.hermes/plugins/）
  → register(ctx) 注册工具、CLI 子命令、生命周期钩子
  → 插件 MUST NOT 修改核心文件（run_agent.py / gateway/run.py 等）

记忆提供商插件（plugins/memory/）
  → 实现 MemoryProvider ABC
  → 内置：honcho、mem0、supermemory、byterover 等 8 种

模型提供商插件（plugins/model-providers/）
  → 每个推理后端是一个插件，28+ 提供商
  → 用户插件覆盖内置插件（last-writer-wins）
```

---

## 3. 核心亮点深入

### 3.1 闭环学习系统：Agent 如何自己写"外挂"

#### 问题：为什么大多数 Agent 不会进化？

传统 Agent 的工作方式：
```
对话 1：用户教 Agent 如何处理 Python 依赖冲突
对话 2：用户又得重新教一遍
对话 3：还是得重新教
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

**为什么需要 Curator？**

学习循环会持续创建和更新技能，但没有清理机制的话，技能库会越来越臃肿：
- 三个月前解决某个一次性问题的技能，现在已经没用了
- 同一类任务被创建了多个高度重叠的技能
- 某个技能的内容随着工具版本升级已经过时

技能库越大，每次对话注入的技能列表越长，LLM 的注意力越分散，反而降低质量。Curator 就是为了解决这个问题——**让技能库保持精简和准确**。

Curator 是一个定期运行的独立 Agent（默认每 7 天，Agent 空闲 2 小时后启动）：

```
扫描所有 Agent 创建的技能
        ↓
30 天未使用 → 标记为 stale（过时）
90 天未使用 → 自动归档到 .archive/（永不删除，可恢复）
发现重复技能 → 合并
发现错误内容 → 修复
```

**关键设计**：Curator **永远不删除**，只归档。用户随时可以 `hermes curator restore` 恢复任何归档的技能。这保证了"清洁"操作是可逆的，不会因为误判而丢失有价值的经验。

---

### 3.2 记忆设计：三层架构 + FTS5 全文搜索

#### 核心问题：记忆不是存得多，是找得准

大多数 AI 工具的记忆方案是"全量加载"——把所有历史对话塞进上下文。这个方案有三个致命问题：

1. **上下文爆炸**：活跃用户每天聊几千字，一个月就是几万字，很快超出模型的上下文窗口
2. **注意力稀释**：内容越多，模型越难聚焦在真正相关的部分，回答质量反而下降
3. **噪音污染**：大量无关的历史对话混入上下文，干扰当前任务的判断

Hermes 的解法是**三层分离 + 按需检索**：不同类型的记忆用不同的存储和召回策略，每次只加载当前任务真正需要的部分。

#### 三层架构

```mermaid
flowchart LR
    subgraph L1["🗂 第一层：会话记忆"]
        A1["存储：state.db SQLite\n内容：所有对话 · 工具调用 · 工具结果\n注入：不自动注入\n召回：LLM 调用 session_search 按需检索"]
    end

    subgraph L2["🧠 第二层：持久记忆"]
        B1["存储：MEMORY.md + USER.md\n内容：编码偏好 · 项目习惯 · 工具链\n注入：全量注入 System Prompt\n更新：每轮对话后自动写入"]
    end

    subgraph L3["⚡ 第三层：Skill 记忆"]
        C1["存储：~/.hermes/skills/*.md\n内容：方法论 · 操作规范 · 工作流\n注入：索引注入 System Prompt\n召回：LLM 调用 skill_view 加载内容"]
    end

    L1 -. "按需检索" .-> SP["System Prompt\n最终注入 LLM"]
    L2 -- "全量注入" --> SP
    L3 -. "索引+按需" .-> SP

    style L1 fill:#0d1b2a,stroke:#4a9eff,color:#c8d8e8
    style L2 fill:#0d2a1a,stroke:#4ade80,color:#c8e8d0
    style L3 fill:#1a0d2a,stroke:#a78bfa,color:#d8c8e8
    style SP fill:#2a2a0d,stroke:#FFD700,color:#e8e8c0
```

**三层的注入策略完全不同：**

| 层 | 存储位置 | 注入方式 | 触发时机 |
|---|---|---|---|
| 会话记忆 | `state.db`（SQLite） | 不自动注入，工具调用按需检索 | LLM 主动调用 `session_search` |
| 持久记忆 | `MEMORY.md` / `USER.md` | 全量注入 System Prompt | 每次对话开始时 |
| Skill 记忆 | `~/.hermes/skills/*.md` | 索引注入，内容按需加载 | LLM 看到索引后主动调用 `skill_view` |

这个设计的关键在于：**持久记忆通常很小（几百行），全量注入没有成本；会话历史可能很大，必须按需检索；Skill 内容可能很长，只注入索引让 LLM 自己决定要不要加载。**

#### 记忆检索决策流程

```mermaid
flowchart TD
    MSG(["💬 新消息到达"]) --> SP["agent/prompt_builder.py\n构建 System Prompt"]

    SP --> SK["技能索引注入\n只有名称+描述，不含完整内容"]
    SP --> MEM["prefetch_all()\nagent/memory_manager.py"]
    MEM --> P1["持久记忆全量注入\nMEMORY.md + USER.md"]
    MEM --> P2["外部提供商语义召回\nhoncho / mem0 按相关性"]

    SK --> FINAL["📋 最终 System Prompt\n身份 + 技能索引 + 记忆块 + 环境信息"]
    P1 --> FINAL
    P2 --> FINAL

    FINAL --> LLM["🤖 LLM 推理"]

    LLM --> T1["skill_view(name)\n加载完整 SKILL.md"]
    LLM --> T2["session_search(query)\nFTS5 检索 state.db\n命中片段 + Bookend 上下文"]
    LLM --> T3["直接生成回答"]

    T1 --> ANS(["✅ 最终回答"])
    T2 --> ANS
    T3 --> ANS

    style MSG fill:#1a1a0d,stroke:#FFD700,color:#e8e8c0
    style FINAL fill:#0d1b2a,stroke:#4a9eff,color:#c8d8e8
    style LLM fill:#0d2a1a,stroke:#4ade80,color:#c8e8d0
    style T1 fill:#1a0d2a,stroke:#a78bfa,color:#d8c8e8
    style T2 fill:#1a0d2a,stroke:#a78bfa,color:#d8c8e8
    style ANS fill:#1a1a0d,stroke:#FFD700,color:#e8e8c0
```

#### FTS5 技术实现

FTS5 是 SQLite 内置的全文搜索扩展，不需要额外部署搜索服务。Hermes 建了**两张虚拟表**解决不同语言的搜索问题：

```sql
-- 标准表：unicode61 分词器，按空格/标点分词，适合英文
CREATE VIRTUAL TABLE messages_fts USING fts5(
    session_id UNINDEXED,
    content
);

-- Trigram 表：3字节滑动窗口分词，解决中文无空格分词问题
-- "你好世界" → ["你好世", "好世界", "世界"] 支持任意子串搜索
CREATE VIRTUAL TABLE messages_fts_trigram USING fts5(
    session_id UNINDEXED,
    content,
    tokenize='trigram'
);
```

**触发器自动同步**：每次 INSERT/UPDATE/DELETE messages 表，触发器同步更新两张 FTS 表，无需手动维护索引。

**索引内容**：`content || tool_name || tool_calls`，工具调用的名称和参数也被索引，可以搜索"上次用了什么工具"。

**Bookend 机制**：FTS5 命中的消息可能在一个很长的会话中间，`session_search` 工具会同时返回：
- 命中消息的上下文窗口（前后 N 条）
- 会话开头几条（了解背景）
- 会话结尾几条（了解结论）

一次搜索获得完整上下文，而不需要加载整个会话。这是 Hermes 能在不撑爆上下文的情况下实现跨会话记忆的核心机制。

#### 和 Claude Code 记忆系统的对比

Claude Code 也有记忆系统：CLAUDE.md 文件和 auto-memory。两者的设计哲学完全不同：

| 维度 | Claude Code | Hermes Agent |
|---|---|---|
| **记忆格式** | CLAUDE.md + auto-memory 文本文件 | SQLite 数据库 + FTS5 索引 + Skill 文件 |
| **写入方式** | CLAUDE.md 手动写，auto-memory 半自动 | 全自动写入，人可以随时覆盖 |
| **检索方式** | 启动时全量加载 CLAUDE.md | 按需 FTS5 全文检索 |
| **记忆粒度** | 项目级（每个项目一份 CLAUDE.md） | 全局级 + 项目级都有 |
| **用户建模** | 无（需要用户自己写偏好） | Honcho 自动推理用户画像 |
| **程序性记忆** | CLAUDE.md 中的指令 | 独立 Skill 文件，可自改进 |
| **跨项目共享** | `~/.claude/CLAUDE.md`（全局指令文件） | 所有记忆天然全局 |
| **存储上限** | CLAUDE.md 建议控制在几 KB | SQLite 理论上限很高 |

**两者的设计哲学不同**：Claude Code 的 CLAUDE.md 是人编写、AI 执行的模式，好处是人有完全的控制权，坏处是需要持续投入维护。Hermes 是 AI 自写、人审核的模式，好处是门槛低、自动化程度高，坏处是自动生成的内容不一定都准确。

**哪种更好取决于使用场景**：如果你是重度 Claude Code 用户，已经花了几周精心打磨 CLAUDE.md，那你手工编织的缰绳可能比 Hermes 自动生成的更精准。但如果你不想花时间维护配置文件，Hermes 的全自动方案确实省心不少。

---

### 3.3 多 Agent 协作：4 种模式

| 模式 | 类比 | 适用场景 |
|---|---|---|
| `delegate_task` | 临时雇一个助手，等他完成再继续 | 需要隔离上下文的子任务 |
| Kanban 看板 | 公司的项目管理系统 | 跨进程/长期/多人协作任务 |
| MoA 多模型投票 | 开会让多个专家各自发表意见 | 极难的推理/分析问题 |
| Background Review | 下班后自己复盘写总结 | 技能/记忆的自动沉淀 |

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

**delegate_task vs Kanban 的核心区别**：
```
delegate_task：进程崩溃 → 任务丢失
Kanban：进程崩溃 → 重启后任务自动恢复（SQLite 持久化）
```

Kanban Dispatcher 的工业级机制：每 60 秒扫描，Worker 定期心跳，超时未心跳 → 任务被重新认领，连续失败 2 次 → 自动 Block 防止无限重试。对标 Celery/RabbitMQ 的去中心化翻版。

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

### 3.4 "有缰绳"：7 道控制机制

AI Agent 落地企业最大的障碍是**不可控**：无限循环、乱删数据、绕过安全检查……

| # | 缰绳 | 代码位置 | 作用 |
|---|---|---|---|
| 1 | 预算上限 | `agent/iteration_budget.py` | 默认 90 次工具调用，防止无限循环 |
| 2 | Grace Call | `conversation_loop.py L782` | 预算耗尽后优雅收尾，不粗暴截断 |
| 3 | 随时可中断 | `tools/interrupt.py` | 父中断子也中断，不留孤儿进程 |
| 4 | 危险命令审批 | `tools/approval.py` | 12 个硬封锁 + 47 个危险模式 |
| 5 | 工具循环守卫 | `agent/tool_guardrails.py` | 检测重复失败/无进展的工具调用循环 |
| 6 | Cron 3 分钟硬中断 | `cron/scheduler.py` | 防止定时任务失控占用调度器 |
| 7 | YOLO 模式冻结 | `tools/approval.py` | 模块 import 时冻结，防止 skill 运行时绕过审批 |

**YOLO 模式冻结的精妙之处**：
```python
# 在模块 import 时冻结，而不是每次调用时读取
_YOLO_MODE_FROZEN = is_truthy_value(os.getenv("HERMES_YOLO_MODE", ""))
```
如果每次调用都读取环境变量，一个恶意 skill 可以在运行时 `os.environ["HERMES_YOLO_MODE"] = "1"` 来绕过所有审批。冻结后，这个攻击路径被彻底关闭。

---

## 4. 快速上手

### 安装

```bash
# Linux / macOS / WSL2
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
source ~/.zshrc
hermes
```

### 接入阿里百炼（Qwen 系列）

```bash
# ~/.hermes/.env
DASHSCOPE_API_KEY=sk-xxxx
DASHSCOPE_BASE_URL=https://dashscope.aliyuncs.com/compatible-mode/v1

# 启动后选择模型
hermes model   # 选择 qwen 提供商 → qwen3.7-max
```

### 常用命令

```bash
hermes                    # 交互式 CLI
hermes --tui              # 现代 TUI 界面
hermes model              # 切换模型
hermes tools              # 配置工具集
hermes gateway start      # 启动消息网关（Telegram/Discord 等）

# 会话内命令
/skills                   # 查看已安装技能
/memory                   # 查看记忆内容
/session_search "关键词"  # 搜索历史会话
/new                      # 开始新会话
/compress                 # 手动压缩上下文
```

### Demo：看 Agent 自己创建技能

```bash
hermes
> 帮我分析一下这个目录下的 Python 项目结构，给出改进建议

# 对话结束后等几秒，会看到：
# 💾 Self-improvement review: Created skill 'python-project-analysis'

cat ~/.hermes/skills/python-project-analysis/SKILL.md
```

---

## 附录：核心文件速查

| 文件 | 一句话说明 |
|---|---|
| `run_agent.py` | AIAgent 类，核心对话循环（~12k LOC） |
| `agent/conversation_loop.py` | 主循环实现，所有核心逻辑 |
| `model_tools.py` | 工具编排，连接 Agent 和工具 |
| `toolsets.py` | 定义哪些工具组合在一起 |
| `hermes_state.py` | 会话历史存储（SQLite + FTS5） |
| `agent/background_review.py` | 后台技能/记忆回顾（daemon 线程） |
| `agent/curator.py` | 技能生命周期管理 |
| `tools/approval.py` | 危险命令审批系统 |
| `tools/delegate_tool.py` | 子 Agent 委派 |
| `tools/kanban_tools.py` | Kanban 多 Agent 协作 |
| `gateway/run.py` | 消息网关（15+ 平台，~17k LOC） |
| `tools/registry.py` | 工具注册表（零依赖） |

---

*基于 hermes-agent main 分支（2026-05）· MIT License · [GitHub](https://github.com/NousResearch/hermes-agent)*
