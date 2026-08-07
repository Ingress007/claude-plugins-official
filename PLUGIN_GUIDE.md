# Claude Code 插件库总览文档

> 本文档梳理 `claude-plugins-official` 市场仓库中**实际存放在本仓库内**的全部 **53 个插件**（`/plugins` 下 38 个 Anthropic 内部插件 + `/external_plugins` 下 15 个第三方插件）。
>
> 市场清单 `.claude-plugin/marketplace.json` 还引用了另外 227 个远程 Git 仓库的插件，本文不一一展开，可参阅市场清单原文。

## 目录

- [一、市场概览](#一市场概览)
- [二、安装方式](#二安装方式)
- [三、内部插件（Anthropic 维护）](#三内部插件anthropic-维护)
  - [3.1 语言服务器（LSP，共 12 个）](#31-语言服务器lsp共-12-个)
  - [3.2 开发工作流类](#32-开发工作流类)
  - [3.3 生产力 / 项目管理类](#33-生产力--项目管理类)
  - [3.4 安全类](#34-安全类)
  - [3.5 学习 / 输出风格类](#35-学习--输出风格类)
  - [3.6 数学类](#36-数学类)
  - [3.7 示例插件](#37-示例插件)
- [四、外部插件（第三方）](#四外部插件第三方)
  - [4.1 代码托管与协作平台](#41-代码托管与协作平台)
  - [4.2 消息通道桥接（Messaging Bridges）](#42-消息通道桥接messaging-bridges)
  - [4.3 代码理解与文档检索](#43-代码理解与文档检索)
  - [4.4 后端框架与基础设施](#44-后端框架与基础设施)
  - [4.5 测试与开发辅助](#45-测试与开发辅助)
  - [4.6 项目管理 SaaS](#46-项目管理-saas)
- [五、快速索引表](#五快速索引表)

---

## 一、市场概览

- **市场名称**：`claude-plugins-official`，由 Anthropic 维护。
- **仓库结构**：
  - `/plugins` — Anthropic 内部开发维护的插件（38 个）。
  - `/external_plugins` — 第三方伙伴 / 社区提交的插件（15 个）。
- **安全提示**：插件内可能包含任意 MCP 服务器、脚本或文件，Anthropic 不对其行为做担保。安装前请确认信任来源，并阅读各插件主页与 README。
- **插件名不可变**：marketplace.json 中的 `name` 字段为不可变 slug，发布后不可改名；如需改 UI 显示名请用 `displayName`，确实要改名须在顶层 `renames` 映射中登记以自动迁移。

## 二、安装方式

在 Claude Code 中执行：

```
/plugin install {plugin-name}@claude-plugins-official
```

或在 `/plugin > Discover` 中浏览。部分插件安装后需额外配置（OAuth、API Key、环境变量、`--channels` 启动参数等），详见下文各插件说明。

---

## 三、内部插件（Anthropic 维护）

### 3.1 语言服务器（LSP，共 12 个）

这一系列插件为 Claude Code 接入各语言的 Language Server，提供代码智能（定义跳转、引用查找、诊断、补全、重构等）。它们本身不含 slash 命令，安装后通过 LSP 协议增强 Claude 对应语言的代码理解能力。各自需要对应工具链预先安装。

| 插件名 | 语言 | 依赖 / 前置条件 |
| --- | --- | --- |
| `clangd-lsp` | C / C++ | 系统需安装 clangd |
| `csharp-lsp` | C# | 需 .NET SDK 6.0+ |
| `gopls-lsp` | Go | 需安装 gopls |
| `jdtls-lsp` | Java | 需 Java 17+（Eclipse JDT.LS） |
| `kotlin-lsp` | Kotlin | 需 Kotlin LSP CLI |
| `lua-lsp` | Lua | 需 lua-language-server |
| `php-lsp` | PHP | 需通过 npm 安装 Intelephense |
| `pyright-lsp` | Python | 需通过 npm/pip/pipx 安装 Pyright |
| `ruby-lsp` | Ruby | 需 Ruby 3.0+ 与 ruby-lsp gem |
| `rust-analyzer-lsp` | Rust | 需通过 rustup / 包管理器安装 rust-analyzer |
| `swift-lsp` | Swift | 需 Swift 工具链（SourceKit-LSP） |
| `typescript-lsp` | TypeScript / JavaScript | 需全局安装 typescript-language-server 与 typescript |

### 3.2 开发工作流类

| 插件名 | 类别 | 作用 | 提供的能力 |
| --- | --- | --- | --- |
| `agent-sdk-dev` | development | 面向 Claude Agent SDK（Python / TypeScript）的开发套件，覆盖从脚手架到按最佳实践校验的完整生命周期 | 命令 `/new-sdk-app`；agent `agent-sdk-verifier-py`、`agent-sdk-verifier-ts` |
| `code-modernization` | development | 针对遗留系统（COBOL、老 Java/C++/.NET、单体 Web 应用）的现代化改造，产出高管评估、交互式架构图、业务规则挖掘、现代化方案简报、新代码脚手架等 | 命令 `/modernize-preflight`、`/modernize-assess`、`/modernize-map`、`/modernize-extract-rules`、`/modernize-brief`、`/modernize-reimagine`、`/modernize-transform`、`/modernize-uplift`、`/modernize-harden`、`/modernize-status`；多个专家 agent（legacy-analyst、business-rules-extractor、architecture-critic、security-auditor、test-engineer 等） |
| `feature-dev` | development | 端到端的功能开发结构化工作流，含代码库探索、架构设计、质量评审 | 命令 `/feature-dev`；agent `code-explorer`、`code-architect`、`code-reviewer` |
| `frontend-design` | development | 生成有设计感、避免“AI 味”模板感的高质量前端界面代码 | skill `frontend-design` |
| `mcp-server-dev` | development | 指导设计与构建能与 Claude 协作的 MCP 服务器，涵盖远程 HTTP / MCPB / 本地等部署模型、工具设计模式、鉴权与交互式 MCP 应用 | skills `build-mcp-server`、`build-mcp-app`、`build-mcpb` |
| `mcp-tunnels` | development | 通过 Anthropic MCP tunnel 把内网私有 MCP 服务器安全暴露给 Claude | 命令 `/create-docker-mcp-tunnel`（一键驱动 Docker Compose：证书、代理、cloudflared、示例服务器） |
| `playground` | development | 生成自包含单文件交互式 HTML playground（带可视化控件、实时预览、复制按钮），含设计 playground、数据浏览器、概念图、文档评审等模板 | skill `playground` |
| `plugin-dev` | development | Claude Code 插件开发综合工具包，覆盖 hooks、MCP 集成、commands、agents、skills 与最佳实践 | 命令 `/plugin-dev:create-plugin`；skills `hook-development`、`mcp-integration`、`plugin-structure`、`plugin-settings`、`command-development`、`agent-development`、`skill-development` |
| `ralph-loop` | development | 实现“Ralph Wiggum 技术”的自引用迭代循环：让 Claude 反复处理同一任务、每次都能看到上一次结果，直到完成 | 命令 `/ralph-loop`、`/cancel-ralph`（Windows 需 Git for Windows） |
| `skill-creator` | development | 创建新 skill、改进已有 skill、运行 eval 测试与基准对比（含方差分析） | skill `skill-creator` |

### 3.3 生产力 / 项目管理类

| 插件名 | 作用 | 提供的能力 |
| --- | --- | --- |
| `claude-code-setup` | 分析代码库并给出量身定制的 Claude Code 自动化建议（hooks、skills、MCP、subagents）。只读，不改文件 | skill `claude-code-setup` |
| `claude-md-management` | 维护与改进 `CLAUDE.md`：审计质量、捕获 session 学习、保持项目记忆新鲜 | skill `claude-md-improver`；命令 `/revise-claude-md` |
| `code-review` | 多专家 agent + 置信度评分过滤误报的 PR 自动评审 | 命令 `/code-review`（需 Git 仓库 + GitHub CLI `gh` 已认证） |
| `code-simplifier` | 简化与精炼近期改动代码，提升清晰度、一致性与可维护性，同时保证功能不变 | agent `code-simplifier` |
| `commit-commands` | Git 提交工作流命令：提交、推送、建 PR、清理已删除分支 | 命令 `/commit`、`/commit-push-pr`、`/clean_gone`（`/commit-push-pr` 需 `gh`） |
| `cwc-makers` | 一键上手 Code-with-Claude Makers Cardputer 套件：克隆仓库、刷入 UIFlow 固件、安装 Claude Buddy app | 命令 `/maker-setup`；skills `/m5-onboard`、`/cardputer-buddy`（需 Python 3.10+ 与 M5Stack Cardputer-Adv 设备） |
| `hookify` | 通过分析对话模式或显式指令，轻松创建自定义 hook 来阻止不良行为；规则用简单 markdown 定义 | 命令 `/hookify`、`/hookify:list`、`/hookify:configure`、`/hookify:help` |
| `pr-review-toolkit` | 一组专注 PR 评审的专家 agent，覆盖评论、测试、错误处理、类型设计、代码质量、代码简化 | agents `comment-analyzer`、`pr-test-analyzer`、`silent-failure-hunter`、`type-design-analyzer`、`code-reviewer`、`code-simplifier` |
| `project-artifact` | 生成并发布“活的”项目状态页（概览、成功标准、工作流序列、下一步），作为可分享的 claude.ai artifact；刷新时重新采集实时状态、复用同一 URL、仅报告增量 | 命令 `/project-artifact`（需 Claude Code Artifact 工具与 `gh`） |
| `receipts` | 个人 Claude Code 影响力报告：你交付了什么、流向哪些项目、各项目占比。读取 `~/.claude/projects` 转写并以只读 git 统计；只上传计数与项目名，报告留在本地 | 命令 `/receipts` |
| `session-report` | 从本地转写生成可探索的 HTML 报告：token 用量、缓存效率、subagents、skills、最贵 prompt | skill `session-report` |

### 3.4 安全类

| 插件名 | 作用 | 提供的能力 |
| --- | --- | --- |
| `claude-security` | 在 Claude Code session 内部对自有代码做深度漏洞扫描（可选 effort 等级），每个发现都先被挑战验证后才上报，并把幸存发现转为定向补丁、由 agent 小组逐一验证后由你决定何时应用 | 命令 `/claude-security`（需 Python 3.9+、git checkout 用于扫描改动并给补丁） |
| `security-guidance` | 针对 Claude 生成代码的安全审查：编辑时基于模式的告警、Stop 时的 LLM diff 评审、提交前 agent 评审，覆盖注入、XSS、SSRF、硬编码密钥等 25+ 类漏洞 | Stop hook + LLM diff 评审 + agent 提交评审（需 Python 3.8+、Claude Code CLI ≥ v2.1.144） |

### 3.5 学习 / 输出风格类

| 插件名 | 作用 | 提供的能力 |
| --- | --- | --- |
| `explanatory-output-style` | 模拟已废弃的 Explanatory 输出风格，补充关于实现选择与代码库模式的教学性洞察 | SessionStart hook，安装后自动生效 |
| `learning-output-style` | 模拟未发布的 Learning 输出风格，在决策点要求你做有意义的代码贡献，形成交互式学习 | SessionStart hook，安装后自动生效 |

### 3.6 数学类

| 插件名 | 作用 | 提供的能力 |
| --- | --- | --- |
| `math-olympiad` | 解竞赛数学题（IMO、Putnam、USAMO），采用对抗式验证（用全新上下文验证者按特定失败模式攻击证明），宁可校准式弃答也不蒙混 | skill `math-olympiad` |

### 3.7 示例插件

| 插件名 | 作用 | 提供的能力 |
| --- | --- | --- |
| `example-plugin` | 完整演示 Claude Code 插件扩展点（commands、agents、skills、hooks、MCP），用作插件开发参考模板 | 命令 `/example-command`；skills `example-skill`、`example-command`；含 `.mcp.json` |

---

## 四、外部插件（第三方）

### 4.1 代码托管与协作平台

| 插件名 | 作者 | 作用 | 配置方式 |
| --- | --- | --- | --- |
| `github` | GitHub | 官方 GitHub MCP 服务器：管理仓库、issue、PR、代码评审、搜索等 GitHub 全 API | HTTP MCP，`https://api.githubcopilot.com/mcp/`，需环境变量 `GITHUB_PERSONAL_ACCESS_TOKEN` |
| `gitlab` | GitLab | GitLab DevOps 集成：仓库、merge request、CI/CD 流水线、issue、wiki | HTTP MCP，`https://gitlab.com/api/v4/mcp` |

### 4.2 消息通道桥接（Messaging Bridges）

这一组插件把即时通讯渠道接入 Claude Code，通过 MCP 服务器把消息转发给 Claude，并提供 reply / react / edit 等工具。均需以 `claude --channels plugin:<name>@claude-plugins-official` 方式启动 session，并配 access 控制策略（pairing / allowlist）。

| 插件名 | 渠道 | 关键点 | 工具 |
| --- | --- | --- | --- |
| `discord` | Discord | 需 [Bun](https://bun.sh)；需自建 Discord 应用与 bot、Message Content Intent；通过 `/discord:configure` 写入 token，`/discord:access` 管理配对与策略 | `reply`、`react`、`edit_message`、`fetch_messages`、`download_attachment` |
| `telegram` | Telegram | 需 Bun；通过 @BotFather 创建 bot 取 token，`/telegram:configure` 写入；Bot API 不提供历史与搜索，故无 `fetch_messages` | `reply`、`react`、`edit_message` |
| `imessage` | iMessage | **macOS 专属**；直接读 `~/Library/Messages/chat.db`、经 AppleScript 发送；需授予 Full Disk Access；自聊默认放行、其余需 allowlist；可设 `IMESSAGE_ALLOW_SMS`（默认关闭，因 SMS 发送方可伪造） | `reply`、`chat_messages`（完整原生历史） |
| `fakechat` | （本地） | 仅用于测试 channel 协议的本地 web 聊天，无 token、无访问控制、无第三方服务；启动后访问 `http://localhost:8787` | `reply`、`edit_message` |

### 4.3 代码理解与文档检索

| 插件名 | 作者 | 作用 | 配置方式 |
| --- | --- | --- | --- |
| `context7` | Upstash | Upstash Context7 MCP：拉取与版本相关的最新文档和代码示例直接进 LLM 上下文 | `npx -y @upstash/context7-mcp` |
| `greptile` | Greptile | 接入 Greptile AI 代码评审，查看 / 解决 PR 评审评论、触发评审、检索评论、管理自定义编码规则 | 需 Greptile 账号与 `GREPTILE_API_KEY` |
| `serena` | Oraios | 基于 LSP 的语义代码分析 MCP：代码理解、重构建议、代码库导航 | `uvx --from git+https://github.com/oraios/serena serena start-mcp-server` |

### 4.4 后端框架与基础设施

| 插件名 | 作者 | 作用 | 配置方式 |
| --- | --- | --- | --- |
| `firebase` | Google | Google Firebase MCP：管理 Firestore、Authentication、Cloud Functions、Hosting、Storage | `npx -y firebase-tools@latest mcp` |
| `terraform` | HashiCorp | Terraform MCP：与 Terraform 生态集成，支持 IaC 高级自动化与交互 | Docker 运行 `hashicorp/terraform-mcp-server:0.4.0`，需 `TFE_TOKEN` |
| `laravel-boost` | Laravel | Laravel 开发工具包 MCP：Artisan 命令、Eloquent 查询、路由、迁移、框架特定代码生成 | `php artisan boost:mcp`（须在 Laravel 项目内） |

### 4.5 测试与开发辅助

| 插件名 | 作者 | 作用 | 配置方式 |
| --- | --- | --- | --- |
| `playwright` | Microsoft | 浏览器自动化与端到端测试 MCP：导航页面、截图、填表、点击、自动化测试工作流 | `npx @playwright/mcp@latest` |

### 4.6 项目管理 SaaS

| 插件名 | 作者 | 作用 | 配置方式 |
| --- | --- | --- | --- |
| `asana` | Asana | 接入 Asana V2 MCP 服务器：创建/管理任务、搜索项目、更新分配、跟踪进度。需一次性自建 Asana OAuth app（V2 不支持 DCR） | 命令 `/asana-setup`；通过 `claude mcp add --transport http --client-id … --client-secret --callback-port 8080 asana https://mcp.asana.com/v2/mcp` 添加 |
| `linear` | Linear | Linear issue 跟踪：创建 issue、管理项目、更新状态、跨工作区搜索 | HTTP MCP，`https://mcp.linear.app/mcp` |

---

## 五、快速索引表

按用途速查（数字为本文档章节号）：

- 想让 Claude 更懂某种语言代码 → [3.1 LSP 系列](#31-语言服务器lsp共-12-个)
- 想做插件 / skill / MCP 开发 → `plugin-dev`、`skill-creator`、`mcp-server-dev`、`mcp-tunnels`、`agent-sdk-dev`、`example-plugin`（[3.2](#32-开发工作流类) / [3.7](#37-示例插件)）
- 想做功能开发 / 前端 / 遗留系统改造 → `feature-dev`、`frontend-design`、`code-modernization`、`ralph-loop`、`playground`（[3.2](#32-开发工作流类)）
- 想做代码评审 / 提交 / 简化 → `code-review`、`pr-review-toolkit`、`commit-commands`、`code-simplifier`（[3.3](#33-生产力--项目管理类)）
- 想优化 Claude 配置与项目记忆 → `claude-code-setup`、`claude-md-management`、`hookify`（[3.3](#33-生产力--项目管理类)）
- 想看用量 / 出报告 → `receipts`、`session-report`、`project-artifact`（[3.3](#33-生产力--项目管理类)）
- 想做安全扫描 → `claude-security`、`security-guidance`（[3.4](#34-安全类)）
- 想接 GitHub / GitLab → [4.1](#41-代码托管与协作平台)
- 想接 IM 渠道 → `discord`、`telegram`、`imessage`、`fakechat`（[4.2](#42-消息通道桥接messaging-bridges)）
- 想查最新文档 / 理解代码库 → `context7`、`greptile`、`serena`（[4.3](#43-代码理解与文档检索)）
- 想管理后端 / 基础设施 → `firebase`、`terraform`、`laravel-boost`（[4.4](#44-后端框架与基础设施)）
- 想做浏览器测试 → `playwright`（[4.5](#45-测试与开发辅助)）
- 想接任务管理 SaaS → `asana`、`linear`（[4.6](#46-项目管理-saas)）
- 想改输出风格 / 学数学 → `explanatory-output-style`、`learning-output-style`（[3.5](#35-学习--输出风格类)）、`math-olympiad`（[3.6](#36-数学类)）

> 各插件的权威说明以其目录下 `README.md` 与 `.claude-plugin/plugin.json` 为准。市场完整清单见 `.claude-plugin/marketplace.json`。
