# AI coding工具分享：从Claude Code开始

## 前言

帮助大家高效使用AI编码工具，减少重复编码，提升开发效率与代码质量
* 管中窥豹：通过一个工具的使用，学习AI Agent工具的使用思想
* 分享主题：以Claude Code为主线学习AI Coding工具的使用（能代表AI coding从"代码补全"走向"agent化开发"的变化）
* 欢迎随时提问，交流实际使用经验

---

## 一、常见AI coding工具

### 1.1 工具大致分三类

| 类型 | 代表工具 | 适合场景 |
|------|---------|---------|
| CLI / Agent型 | Claude Code、Codex CLI、OpenClaw、Gemini CLI、opencode cli、qwen cli | 终端工作流、任务执行、自动化 |
| IDE插件型 | GitHub Copilot、Claude Code插件 | IDE上做增强，接入方便 |
| AI原生IDE | Cursor、Trae、Qoder | 快速原型、新手上手 |

### 1.2 工具选择

Agent工具的主要能力：

* 编码质量和复杂逻辑能力
* 对项目上下文的理解深度
* 读文件、改文件、跑命令、调试、调用外部工具的能力
* 权限模型是否清晰，安全性问题
* 扩展能力：skills、hooks、MCP、subagent、memory是否完善

### 1.3 选择推荐

* **Claude Code**：主力编码agent，适合复杂编码、调试、重构、脚本
* **Codex**：补充型工具，和CC互补使用
* **Trae/Cursor**：IDE入口，图形界面更友好
* **OpenClaw**：更适合做外层调度、消息入口、智能助手，而非主编码器
---

## 二、Claude Code介绍与快速上手

Claude Code不只是"补全代码"，而是一个完整的coding agent（业界第一个具有agent能力的coding助手）。

和传统代码补全不同：**读代码 → 理解上下文 → 修改文件 → 执行命令 → 调试问题 → 拆分任务 → 串联工具**。

它的能力上限来自背后的 **Harness 能力**，而不只是模型本身。AI协作方式的交互重心需要从早期的"提示词技巧"转为"工作流和工程化使用方式"。

### 2.1 安装与接入
0. 环境要求：Node.js（版本要求：v20+，越新越好，避免低版本兼容性问题），提前官网安装node.js
1. 安装CLI：`npm install -g @anthropic-ai/claude-code`
2. 配置API（两种方式）：
   * `settings.json` 文件配置（单厂商）
   * 环境变量配置（多厂商切换）
   * 需要注意，api和url是兼容Anthropic的格式（业界通用的还有Open AI）
3. 启动验证：`claude -version` 确认安装成功
4. 输入简单指令测试，如"写一个Python求和函数"

### 2.2 环境变量配置

* 直接修改settings.json文件：
  ```json
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "智谱API Key",
    "ANTHROPIC_BASE_URL": "https://open.bigmodel.cn/api/anthropic",
    "API_TIMEOUT_MS": "3000000",
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "glm-4.7",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "glm-5.1",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "glm-5-turbo"
  },
  ```

* 使用CC-Switch切换厂商
* 每次都在cmd中直接写环境变量（适用于多套api key并行处理的场景）


### 2.3 斜杠命令和操作命令

#### 常用斜杠命令

| 命令 | 说明 |
|------|------|
| `/init` | 生成CLAUDE.md文件，建议自己看一遍之后编写和优化（可以让AI改） |
| `/help` | 查看帮助信息 |
| `/compact` | 压缩当前对话上下文，释放token空间（长对话时很有用） |
| `/clear` | 清空当前对话历史，重新开始 |
| `/resume` | 恢复上一次中断的会话 |
| `/rewind` | 撤回上一次操作，回退到之前的状态 |
| `/doctor` | 诊断Claude Code环境问题（安装、权限、网络等） |
| `/status` | 查看当前配置和连接状态 |
| `/memory` | 管理Claude Code的持久化记忆文件 |
| `/mcp` | 查看、管理已配置的MCP服务器 |
| `/debug` | 开启调试模式，查看详细的工具调用日志 |
| `/config` | 查看/修改当前配置 |
| `/cost` | 查看当前会话的token消耗和费用统计 |

#### 快捷键

| 快捷键 | 说明 |
|--------|------|
| `Shift+Tab` | 切换回复模式（普通/自动确认） |
| `Ctrl+Enter` | 换行（PowerShell中为`Shift+Enter`，不建议用Powerhell） |
| `Ctrl+C` | 取消当前输入（有选中内容时为复制），误触可按`↑`恢复 |
| `Ctrl+K` | 删除当前行光标后的内容 |
| `@+文件名` | 快速引用项目中的文件作为上下文 |

#### 启动方式

| 方式 | 说明 |
|------|------|
| `claude` | 普通模式启动，每次操作需手动确认权限 |
| `claude --dangerously-skip-permissions` | 跳过所有权限确认（长时间auto任务时使用，需谨慎） |
| `claude -p "指令"` | 非交互模式，直接执行一条指令后退出 |
| `claude --resume` | 启动时自动恢复上一次会话 |



---

## 三、AI coding经验分享

### 3.1 AI coding的原则

模型已经足够聪明，你只需要：
1. 把**需求拆解清楚**，具体到你最后需要的是什么
2. **约束明确**，明确模型不能干的事情（例如不能删除本地文档）
3. **验收标准明确**，设定规则和目标
4. 控制Spec和Plan文档的长度，不要一次性给太多输入

更有效的方式是先把**意图写清楚**：目标、边界、技术栈、输入输出、约束、验收标准。先把Spec写得清晰明确，比纯vibe coding更有效（这个没有达到我的要求；这个不好，再试试）。

### 3.2  Vibe coding方法论

Vibe coding不是"凭感觉让AI乱写"，而是**用自然语言驱动、用中间产物约束、用验收标准收敛**。

需求分析、架构设计、任务规划、代码生成、资源生成、代码集成6个阶段

核心要点：
* 意图明确——先把"我要什么"说清楚 
* 需求拆解 → 方案草图 → 任务清单 → 验收标准 **（真正有效的Vibe Coding不是即兴生成，而是流程化生成）**
* 约束越清楚，成功率越高 **（依赖结构化中间产物）**
* 技术栈、目录结构、接口协议越明确越好 
* Vibe只是交互模式变了，**工程化才是落地关键**
* 程序员的角色在变：不再只是"逐行写代码"，更偏向"问题拆解、指令设计、结果验收"

**过渡到Spec coding（Specification 规格说明）**
Vibe coding → TDD → SDD

例如，我们可以放弃碎片化的提示词交互，而使用结构化的文档来规定：
-   项目目录结构
-   构建命令
-   测试流程
-   代码规范
-   PR 提交流程

---

### 3.3 Skills体系：把经验变成可复用执行

Skills本质是把prompt、规范、流程、工具调用方式做成可复用资产。

Skills 举例：

| 分类 | Skill 名称 | 说明 |
|------|-----------|------|
| 编码 | `code-reviewer` | 编程技能 |
| 编码 | `coding-standards` | 各语言最佳实践 |
| 编码 | `tdd-workflow` | 测试驱动开发方法论 |
| 数据 | `clickhouse-io` | ClickHouse 分析、查询与数据工程 |
| 后端 | `backend-patterns` | API、数据库、缓存设计模式 |
| 前端 | `frontend-patterns` | React、Next.js 开发模式 |
| 前端 | `frontend-slides` | HTML 幻灯片与 PPTX 转网页工作流 |
| 内容 | `article-writing` | 长文本写作，保留指定风格、避免通用 AI 腔调 |
| 内容 | `content-engine` | 多平台社交内容创作与复用工作流 |
| 商业 | `market-research` | 带来源引用的市场、竞品与投资方研究 |
| 商业 | `investor-materials` | 融资路演 PPT、单页摘要、备忘录与财务模型 |
| 商业 | `investor-outreach` | 定制化融资触达与跟进 |
| 元技能 | `continuous-learning` | 从会话中自动提取模式（长文本指南） |
| 元技能 | `continuous-learning-v2` | 基于本能的学习，附带置信度评分 |
| 元技能 | `iterative-retrieval` | 为子智能体渐进式优化上下文 |
| 元技能 | `strategic-compact` | 手动上下文精简建议（长文本指南） |

**核心文件**：

* `SKILL.md` — 编码规范（如缩进4空格、下划线命名）
* `reference/` — 接口文档、表结构，CC自动读取
* `scripts/` — 常用脚本，可快速调用

**特点**：渐进式加载，仅加载当前需要的Skills，降低Token消耗。

**对个人**：提效工具，减少重复解释（重复输出prompt）。

**对团队**：把"个人经验"变成"团队共识"。

例子：编写一个解读github repo的skill，如果每次都是把链接给AI，需要重复进行：仓库内容clone，主要内容读取和分析，拆解和讲解，以及有用内容总结。


### 3.4 有用的使用小技巧分享

* 多开终端，任务拆开并发处理（需要明确边界，不要修改同一个功能——可延伸到git开发，如果需要开发多个feature，可以在git里不动main分支的情况下新建feature1，feature2，然后并行开发，测试通过后合并到git main）

* 遇到bug、环境变量问题、skills加载失败、MCP启动失败——直接把报错和上下文丢给cc处理

* 不要追求一次生成可行的代码，脑中有"生成 → 检查 → 修正 → 再验收"的思维

* 如果需要长时间的auto方式，可以选择`claude --dangerously-skip-permissions` ，需谨慎决定

* spec 是对代码架构的理解；plan是驱动工具从无到有生成代码的指令；spec是写什么, 怎么写, 设计什么；plan是按照什么顺序写, 写到某阶段要做什么测试, 测试达到什么程度才能继续写。
  终点是spec描述的状态，plan是驱动到达终点的动力；Vibe coding的本质是自然语言到编程语言的投影工具。

* 在最初开发的时候，完全不确定方向，只知道一个大概和模糊的目标，可以先与llm做交互（网页版或者Agent工具里对话均可），形成一个大致的思路。或者就是使用Brainstorming skill（头脑风暴，推荐）

* 有的时候自己的思路不明确，无法做很有效的vibe coding，可以先写一个spec，然后让cc来生成，cc生成后，再根据cc生成的代码，来调整spec，然后再让cc生成，如此反复，直到生成自己满意的代码。

---

## 四、从Harness视角理解CC和OpenClaw

### 4.1 核心概念

Model只是"大脑"，**Harness才是把模型变成"能稳定做事的系统"的工程骨架**。

所以 Agent = Model + Harness。真正差异往往不在模型参数，而在harness的能力边界、权限设计、工作流设计和协同方式。

CC的Harness体系：Hooks、Skills、Subagent、MCP、Memory、Plan/Task

* **Hooks**：事件触发的自动化脚本，可以在特定动作（如文件写入、命令执行）前后自动运行自定义shell命令，实现自动格式化、lint检查、通知等
* **MCP（Model Context Protocol）**：让CC调用外部工具和数据源的协议，通过配置MCP Server可以扩展CC的能力边界（如访问数据库、调用API、操作浏览器等）

从宏观的角度看，Coding Agent 的运行依赖于三类上下文：
- 任务上下文（Task Context） ：当前任务的 prompt 与输入信息
- 项目上下文（Project Context） ：代码仓库结构与工程规则
- 环境上下文（Environment Context） ：工具、权限与执行环境
其中，环境上下文决定了 Agent 能够做什么，以及能够做到什么程度。

### 4.2 定位差异

cc——定位就是专门的coding agent。

openclaw——coding能力比不上cc，codex等，但是具有自主唤醒能力，具有更多权限，并且原生适配了一些社交通讯工具，更适合作为一个智能助手，可以通过配置好心跳和定时任务以及agent prompt让openclaw调用本地的claude code和codex完成开发工作。


| 工具 | 定位 | 核心能力 |
|------|------|---------|
| Claude Code | 专用编码agent | 写代码、改代码、调代码、跑任务 |
| OpenClaw | 智能助手/调度层 | 接收消息、定时触发、唤醒调度 |

Openclaw（以及国内大厂跟进的各种claw）在目前的AI agent领域属于单独的一类agent，本身的定位是帮助人打造一个智能中枢，需要花比较长的时间去让这个agent具备足够的skill，属于你的使用习惯，熟悉你的应用场景（使用有冷启动，类似于基建类游戏）。


OpenClaw 的一个 Agent 的 workspace 核心文件：

| 文件 | 作用 |
|------|------|
| `AGENTS.md` | Agent 的工作说明书与行为准则，划定核心职责、任务流程与行为边界 |
| `BOOTSTRAP.md` | 全新工作区的一次性初始化引导脚本，完成基础配置后需删除 |
| `SOUL.md` | Agent 的灵魂内核与顶层宪法，定义核心身份、人格、价值观与安全红线 |
| `MEMORY.md` | Agent 的长期记忆中枢，存储用户稳定偏好、重要决策等跨会话信息 |
| `HEARTBEAT.md` | Agent 的生物钟与主动任务引擎，负责周期任务、健康巡检与主动提醒 |
| `user.md` | 记录用户个人信息、职业背景、沟通偏好等完整档案，是 Agent “懂用户” 的核心依据 |
| `identity.md` | 定义 Agent 基础身份、运行环境配置、工具权限等，明确工作区的运行规则 |


### 4.3 高级协同方式

1. OpenClaw负责接收消息、定时触发或任务调度
2. 真正的编码子任务交给本地Claude Code或Codex
3. OpenClaw串联其他工具、执行测试、回传结果

**使用举例**：可以用OpenClaw作为一个智能中枢的总调度，调度不同的agent 执行不同的任务，调用数据采集agent搜集资讯，爬取数据，调用数据分析agent分析数据，调用CC/Codex写代码等等

---



## 五、资源推荐

### 5.1 小工具

| 工具 | 说明 | 链接 | Stars |
|------|------|------|-------|
| ccstatusline | Claude Code状态栏增强 | [github.com/sirmalloc/ccstatusline](https://github.com/sirmalloc/ccstatusline) | ~6.8k |
| starship | 通用shell prompt，美化终端 | [github.com/starship/starship](https://github.com/starship/starship) | ~55.9k |

### 5.2 推荐学习的开源仓库

| 仓库 | 说明 | 链接 | Stars |
|------|------|------|-------|
| everything-claude-code | harness/rules/skills/workflow集合 | [github.com/affaan-m/everything-claude-code](https://github.com/affaan-m/everything-claude-code) | ~143k |
| claude-howto | 系统化学习CC教程 | [github.com/luongnv89/claude-howto](https://github.com/luongnv89/claude-howto) | ~21.3k |
| superpowers | workflow型skills框架 | [github.com/obra/superpowers](https://github.com/obra/superpowers) | ~137k |

### 5.3 Skills站点

* SkillHub：
* https://www.skill-cn.com/    
* https://skillhub.tencent.com/（腾讯）
* ClawHub：https://claw-hub-bay.vercel.app/

### 5.4 Coding模型推荐

* **国外**：Claude 4.6 Opus > GPT-5.4 > Gemini 3.1 Pro（前端强，Coding弱）
* **国内**：GLM 5.1、Minimax 2.7、Qwen 3.5 Plus、Kimi 2.5、Doubao-seed-2.0、Step-3.1-flash、MimoV2-pro

---

## 六、工程实践思维

0. **自然语言的模糊性和执行的精确性，是阻碍长程工作的一大障碍（vibe coding→spec coding）**
1. **工具的差异不只是模型差异，更是harness和workflow的差异**
2. **真正决定效果的，不是工具和模型本身，而是如何使用Spec、Skills，并约束和验收你的项目**
3. **CC适合作为agentic coding的主线工具，但最好和其他工具协同使用，而不是唯一答案**

---
