---
title: 插件速查索引
feishu_url: "https://fivwvysqdz.feishu.cn/wiki/FXRewezr6irYsjksQeQcSD9Hnne"
last_synced: "2026-05-25T00:00:42+08:00"
---

# 插件速查索引

本附录分两部分：

1. **第一部分**：本书覆盖的 52 个插件，按字母顺序，标注所在章节，方便按名字查找。
2. **第二部分**：官方 marketplace 当前收录的 150+ 个**企业级远程插件**目录（按领域分类）。这些插件在仓库里以 SHA pin 或 URL 形式存在，本书不展开讲，但你可以一眼扫到自己需要的服务。

> 数据来自 `anthropics/claude-plugins-official` 仓库的 `.claude-plugin/marketplace.json`，截至 2026 年 5 月 24 日共 203 个可安装插件（35 个 Anthropic 官方 + 15 个 in-repo 外部 + 153 个远程第三方）。最新清单见仓库主页：<https://github.com/anthropics/claude-plugins-official>。

## 第一部分：本书覆盖的 52 个插件

| 插件名 | 所在章节 | 一句话描述 |
|--------|---------|-----------|
| `agent-sdk-dev` | 第 1 章·开发工具 | Agent SDK 项目脚手架和合规检查 |
| `asana` | 第 6 章·外部服务 | Asana 项目管理集成（远程 SSE MCP） |
| `clangd-lsp` | 第 5 章·LSP 集成 | C/C++ 语言服务器 |
| `claude-code-setup` | 第 1 章·开发工具 | 分析项目并推荐 Claude Code 自动化配置 |
| `claude-md-management` | 第 1 章·开发工具 | CLAUDE.md 质量审计和会话经验提取 |
| `code-modernization` | 第 1 章·开发工具 | 遗留系统现代化工作流（assess→map→extract-rules→brief→reimagine/transform→harden） |
| `code-review` | 第 2 章·代码质量 | 多 Agent 协作的自动化 PR 审查 |
| `code-simplifier` | 第 2 章·代码质量 | 自动代码简化和重构 |
| `commit-commands` | 第 1 章·开发工具 | Git 提交、推送、创建 PR 的快捷命令 |
| `context7` | 第 6 章·外部服务 | 第三方库文档实时查询（免费，无需 key） |
| `csharp-lsp` | 第 5 章·LSP 集成 | C# 语言服务器 |
| `cwc-makers` | 第 1 章·开发工具 | M5Stack Cardputer 硬件套件一键 onboarding |
| `discord` | 第 6 章·外部服务 | Discord 消息通道（本地 channel 模式） |
| `example-plugin` | 第 1 章·开发工具 | 插件系统的官方脚手架示例 |
| `explanatory-output-style` | 第 3 章·输出风格 | 让 Claude 在回答中附加教学性解释 |
| `fakechat` | 第 6 章·外部服务 | 本地 Web UI 消息通道（开发调试用） |
| `feature-dev` | 第 1 章·开发工具 | 七阶段结构化功能开发工作流 |
| `firebase` | 第 6 章·外部服务 | Firebase 后端服务集成（本地 stdio） |
| `frontend-design` | 第 4 章·专业领域 | 前端 UI/UX 设计指导 |
| `github` | 第 6 章·外部服务 | GitHub 仓库、Issue、PR 集成（远程 HTTP MCP） |
| `gitlab` | 第 6 章·外部服务 | GitLab 项目、Issue、MR 集成（远程 HTTP MCP） |
| `gopls-lsp` | 第 5 章·LSP 集成 | Go 语言服务器 |
| `greptile` | 第 6 章·外部服务 | AI 代码审查集成（需要 API key） |
| `hookify` | 第 1 章·开发工具 | 用自然语言定义 Claude Code 行为拦截规则 |
| `imessage` | 第 6 章·外部服务 | iMessage 消息通道（仅 macOS） |
| `jdtls-lsp` | 第 5 章·LSP 集成 | Java 语言服务器 |
| `kotlin-lsp` | 第 5 章·LSP 集成 | Kotlin 语言服务器 |
| `laravel-boost` | 第 6 章·外部服务 | Laravel 框架开发工具集（需在 Laravel 项目内） |
| `learning-output-style` | 第 3 章·输出风格 | 交互式学习模式，关键决策点让用户动手 |
| `linear` | 第 6 章·外部服务 | Linear 项目管理集成（远程 HTTP MCP） |
| `lua-lsp` | 第 5 章·LSP 集成 | Lua 语言服务器 |
| `math-olympiad` | 第 4 章·专业领域 | 竞赛数学求解器（对抗验证机制） |
| `mcp-server-dev` | 第 1 章·开发工具 | MCP 服务器开发指导（选型、设计、部署） |
| `mcp-tunnels` | 第 1 章·开发工具 | 用 MCP tunnel 把私网 MCP 服务暴露给 Claude（Docker Compose quickstart） |
| `php-lsp` | 第 5 章·LSP 集成 | PHP 语言服务器 |
| `playground` | 第 1 章·开发工具 | 生成单文件交互式 HTML 探索工具 |
| `playwright` | 第 6 章·外部服务 | Playwright 浏览器自动化（本地 stdio） |
| `plugin-dev` | 第 1 章·开发工具 | 插件开发全套工具箱（7 skill + 3 agent + 1 command） |
| `pr-review-toolkit` | 第 2 章·代码质量 | 6 个专项 Agent 的模块化 PR 审查 |
| `pyright-lsp` | 第 5 章·LSP 集成 | Python 语言服务器 |
| `ralph-loop` | 第 1 章·开发工具 | Stop hook 实现会话内自动迭代循环 |
| `ruby-lsp` | 第 5 章·LSP 集成 | Ruby 语言服务器 |
| `rust-analyzer-lsp` | 第 5 章·LSP 集成 | Rust 语言服务器 |
| `security-guidance` | 第 2 章·代码质量 | 编辑时安全风险实时检测（PreToolUse hook） |
| `serena` | 第 6 章·外部服务 | 语义级代码分析（基于 LSP，第三方） |
| `session-report` | 第 1 章·开发工具 | 会话 token 用量和缓存效率报告 |
| `skill-creator` | 第 1 章·开发工具 | 工程化 skill 创建、测试、评估 |
| `supabase` | 第 6 章·外部服务 | Supabase 后端服务集成（远程 HTTP MCP） |
| `swift-lsp` | 第 5 章·LSP 集成 | Swift 语言服务器 |
| `telegram` | 第 6 章·外部服务 | Telegram 消息通道（本地 channel 模式） |
| `terraform` | 第 6 章·外部服务 | Terraform IaC 集成（Docker 容器运行） |
| `typescript-lsp` | 第 5 章·LSP 集成 | TypeScript/JavaScript 语言服务器 |

> 三个**新增**的官方插件（`code-modernization`、`cwc-makers`、`mcp-tunnels`）已并入第 1 章，对应章节为 1.14、1.15、1.16。

## 第二部分：企业级远程插件目录（按领域分类）

下列 150+ 个插件由第三方厂商维护、通过 SHA pin 或 URL 接入官方 marketplace。安装方式跟内置插件一样：

```bash
/plugin install <plugin-name>@claude-plugins-official
```

安装前**强烈建议**先去插件的 `homepage` / `repository` 看一眼来源是不是你信任的厂商。Anthropic 不对这些远程插件的代码质量和安全性做担保。

### Cloud / 平台 / 部署

| 插件名 | 一句话描述 |
|--------|-----------|
| `aws-agents` | 在 AWS 上构建、部署、运营 AI Agent（Amazon Bedrock AgentCore） |
| `aws-amplify` | AWS Amplify Gen 2 全栈应用脚手架（认证、数据、存储、部署） |
| `aws-core` | AWS 核心：IaC 编排、Lambda、容器、API Gateway 等 |
| `aws-data-analytics` | 数据湖与 ETL：S3 Tables、Glue、Athena |
| `aws-dev-toolkit` | AWS 开发工具箱（34 skill + 11 agent + 3 MCP server） |
| `aws-serverless` | AWS Serverless（SAM、Lambda、Step Functions）设计与调试 |
| `deploy-on-aws` | 给 AWS 应用做架构推荐、成本估算和 IaC 部署 |
| `databases-on-aws` | AWS 数据库选型与性能优化（RDS、DynamoDB、Aurora 等） |
| `azure` | Azure MCP server + 专家指导 |
| `azure-cosmos-db-assistant` | Cosmos DB 数据建模、查询优化、性能调优 |
| `cloudflare` | Cloudflare 开发者平台（Workers、Durable Objects、Agents SDK） |
| `vercel` | Vercel 部署平台集成（部署管理、build 状态、日志） |
| `netlify-skills` | Netlify 平台（functions、edge functions、blobs、数据库等） |
| `railway` | Railway 应用、数据库、基础设施部署 |
| `base44` | Base44 全栈应用开发与部署 |
| `wix` | Wix 站点与应用开发部署 |
| `wordpress.com` | WordPress.com 站点开发（Studio 本地预览 + 部署） |
| `fastly-agent-toolkit` | Fastly 开发工具与平台 skill |
| `expo` | React Native + Expo 全套开发部署 |

### 数据库 / 数据存储

| 插件名 | 一句话描述 |
|--------|-----------|
| `mongodb` | 官方 MongoDB 插件：MCP server + skill，连接数据库与数据探索 |
| `alloydb` | Google Cloud AlloyDB for PostgreSQL 连接与操作 |
| `cloud-sql-postgresql` | Google Cloud SQL for PostgreSQL 连接与操作 |
| `cockroachdb` | CockroachDB 集群直连操作 |
| `planetscale` | 鉴权后访问 PlanetScale 组织、数据库、分支 |
| `neon` | Neon Postgres 项目与数据库管理 |
| `prisma` | Prisma ORM：Postgres 管理、迁移、SQL 查询 |
| `convex-backend` | Convex 后端（响应式、类型安全） |
| `supabase` | Supabase MCP：数据库、Auth、Storage、Realtime |
| `dataverse` | Microsoft Dataverse（含 Dataverse MCP） |
| `redis-development` | Redis 开发最佳实践：数据结构、查询引擎、向量搜索、缓存 |
| `clickhouse` | 连接 ClickHouse Cloud 数据库 |
| `clickhouse-best-practices` | ClickHouse 28 条 schema 设计 / 查询优化 / 数据导入最佳实践 |
| `pinecone` | Pinecone 向量数据库集成 |
| `qdrant-skills` | Qdrant 向量搜索 skill（扩展、性能、查询） |
| `zilliz` | Zilliz Cloud（14 skill：集群生命周期、collection、查询） |
| `duckdb-skills` | DuckDB 数据文件读取与查询 |
| `sanity` | Sanity 内容平台 |
| `airtable` | Airtable：作为 Agent 的数据库与运营层 |

### 可观测性 / SRE / 性能

| 插件名 | 一句话描述 |
|--------|-----------|
| `datadog` | Datadog MCP server：查询日志、metric、trace |
| `sentry` | Sentry 错误监控集成 |
| `sentry-cli` | Sentry CLI 命令行交互 |
| `logfire` | Logfire 可观测性（FastAPI、httpx 等自动埋点） |
| `dash0` | OpenTelemetry 观测 Claude Code 会话（工具调用、LLM 调用、性能） |
| `posthog` | PostHog 分析、特性开关、实验、错误追踪 |
| `fullstory` | Fullstory 行为分析、session replay、客户体验 |
| `rootly` | 全生命周期事件管理：部署安全、事件响应、on-call |
| `pagerduty` | PagerDuty 风险评分 + 事件关联 |
| `sonarqube` | SonarQube 代码质量与安全（7000+ 规则） |
| `sonatype-guide` | Sonatype 供应链情报与依赖安全 |
| `codspeed` | CodSpeed 性能测试与基准对比 |

### 安全 / 合规

| 插件名 | 一句话描述 |
|--------|-----------|
| `42crunch-api-security-testing` | 42Crunch OpenAPI 自动安全审计 |
| `aikido` | Aikido 安全扫描（SAST、密钥、IaC 漏洞） |
| `auth0` | Auth0 认证脚手架（自动检测框架） |
| `workos` | WorkOS（AuthKit、SSO、Directory Sync、RBAC、Vault、审计日志） |
| `duende-skills` | Duende 开发（OAuth/OIDC、IdentityServer） |
| `semgrep` | Semgrep 实时安全漏洞检测 |
| `ai-plugins` | Endor Labs：依赖安全扫描与修复 |
| `jfrog` | JFrog Platform：Artifactory 仓库与安全发现 |
| `nightvision` | NightVision DAST + API 发现 |
| `vanta-mcp-plugin` | Vanta 安全合规平台 MCP |
| `zscaler` | Zscaler 云安全（ZPA、ZIA、ZDX、ZCC） |
| `crowdstrike-falcon-foundry` | CrowdStrike Falcon Foundry 网络安全应用开发 |

### AI / 数据工程 / Agent 框架

| 插件名 | 一句话描述 |
|--------|-----------|
| `huggingface-skills` | Hugging Face 开源模型、数据集、Spaces |
| `datarobot-agent-skills` | DataRobot AI/ML 工作流（训练、部署、预测） |
| `fiftyone` | FiftyOne 数据集与计算机视觉模型评估 |
| `togetherai-skills` | Together AI 推理、训练、嵌入、音视频图像 |
| `pydantic-ai` | Pydantic AI 准确写法的最佳实践 |
| `atomic-agents` | Atomic Agents 框架完整开发工作流 |
| `agentforce-adlc` | Salesforce Agentforce 开发生命周期 |
| `mcp-apps` | MCP Apps SDK 应用开发 skill |
| `astronomer-data-agents` | Apache Airflow + Astronomer 数据工程 |
| `data` | Apache Airflow 数据工程（与 astronomer-data-agents 相似） |
| `data-agent-kit-starter-pack` | 数据工程师与数据库实践者专用 skill |
| `data-engineering` | 数仓探索、Pipeline 编写、Airflow 集成 |
| `datahub-skills` | DataHub 开发与交互（连接器、PR 审查、catalog） |
| `dominodatalab` | Domino Data Lab 全平台支持 |
| `remember` | 持续记忆：抽取、压缩、记录 Claude Code 会话 |

### 搜索 / 数据采集 / 销售情报

| 插件名 | 一句话描述 |
|--------|-----------|
| `exa` | Exa AI 网页搜索、深度调研、内容提取 |
| `firecrawl` | Firecrawl 网页抓取（输出 LLM-ready markdown） |
| `brightdata-plugin` | Bright Data 网页抓取 + Google 搜索 + 结构化数据 |
| `nimble` | Nimble 网络数据工具箱（搜索、提取、map、crawl） |
| `lumen` | 本地语义代码搜索（Go AST + embedding） |
| `sourcegraph` | Sourcegraph 跨代码库搜索与引用追踪 |
| `youdotcom-agent-skills` | You.com 搜索、调研、内容提取（带引用） |
| `microsoft-docs` | 官方 Microsoft 文档 / API 参考 / 代码示例 |
| `apollo` | Apollo.io 线索丰富、外联序列、销售分析 |
| `hunter` | Hunter.io 邮箱查找与验证 |
| `zoominfo` | ZoomInfo 公司与联系人搜索、线索丰富 |
| `bigdata-com` | Bigdata.com 金融研究、分析、情报 |
| `amplitude` | Amplitude 产品分析（埋点、机会发现、查询） |

### 协作 / 通讯 / 设计

| 插件名 | 一句话描述 |
|--------|-----------|
| `slack` | Slack workspace（消息搜索、频道、线程） |
| `notion` | Notion workspace（页面搜索、文档创建、数据库） |
| `atlassian` | Jira + Confluence（搜索 issue、创建 issue） |
| `forge-skills` | Atlassian Forge 应用脚手架与部署 |
| `intercom` | Intercom 客户支持对话搜索与分析 |
| `circleback` | Circleback 会议、邮件、日历对话上下文 |
| `miro` | Miro 白板访问（读上下文、画流程图） |
| `figma` | Figma 设计文件、组件信息、对象提取 |
| `mintlify` | Mintlify 文档站构建（非 markdown 转 markdown） |
| `postiz` | 社媒自动化 CLI（排程、媒体上传、集成） |
| `circle-skills` | Circle USDC 支付与跨链 |
| `mapbox` | Mapbox 地理 skill + MCP server |
| `amazon-location-service` | Amazon Location Service（地图、地理编码、路由） |

### 电商 / 支付 / 金融

| 插件名 | 一句话描述 |
|--------|-----------|
| `shopify` | Shopify 开发者工具（文档、GraphQL 校验） |
| `shopify-ai-toolkit` | Shopify AI Toolkit（18 个 skill） |
| `stripe` | Stripe 支付开发 |
| `mercadopago` | Mercado Pago 全产品集成（4 个 orchestration skill） |
| `sumup` | SumUp 终端 + 在线支付（Android、iOS、Web） |
| `revenuecat` | RevenueCat 项目、应用、产品、entitlement 配置 |
| `rc` | RevenueCat 简写名（与 `revenuecat` 同源） |
| `carta-cap-table` | Carta Cap Table（股权表、grant、SAFE、409A 估值） |
| `carta-crm` | Carta CRM（投资人、公司搜索与丰富） |
| `carta-investors` | Carta Investors（投资人数据、绩效基准、监管） |
| `apollo-skills` | Apollo GraphQL（Client、Server、Federation、Connectors） |
| `legalzoom` | LegalZoom 律师指导 + AI 法律文档审查 |

### DevOps / CI/CD / 测试

| 插件名 | 一句话描述 |
|--------|-----------|
| `chrome-devtools-mcp` | 控制 Chrome 浏览器（性能录制、DevTools） |
| `coderabbit` | CodeRabbit 外部代码审查（专用 AI agent） |
| `teamcity-cli` | TeamCity CI/CD CLI 交互 |
| `buildkite` | Buildkite pipeline、build、测试 skill |
| `desktop-commander` | MCP server：终端命令、进程管理、文件操作 |
| `qodo-skills` | Qodo 可复用 AI agent 能力库 |
| `postman` | Postman 全 API 生命周期（collection 同步、客户端代码生成） |
| `superpowers` | Superpowers：头脑风暴 + subagent 驱动开发 + 代码审查 |

### 企业 ERP / SAP

| 插件名 | 一句话描述 |
|--------|-----------|
| `cds-mcp` | SAP CAP（Cloud Application Programming Model）开发 |
| `sap-cds-mcp` | 同上（命名空间不同的版本） |
| `sap-fiori-mcp-server` | SAP Fiori 开发工具 MCP |
| `sap-mdk-server` | SAP Mobile Development Kit MCP |
| `ui5` | SAPUI5 / OpenUI5 项目创建与校验 |
| `ui5-typescript-conversion` | SAPUI5 / OpenUI5 JS→TS 转换 |
| `servicenow-sdk` | ServiceNow Fluent SDK 应用开发与部署 |
| `netsuite-suitecloud` | NetSuite SuiteCloud Development Framework |
| `snowflake-cortex-code` | Snowflake Cortex Code 自动路由 |
| `box` | Box 内容（文件搜索、文件夹组织、协作） |
| `qt-development-skills` | Qt C++/QML 代码审查与开发 |

### 创意 / 媒体 / 视觉

| 插件名 | 一句话描述 |
|--------|-----------|
| `adobe-for-creativity` | Adobe 创意 AI 工具（图像编辑、设计自动化） |
| `cloudinary` | Cloudinary 媒体资产管理与变换 |
| `runway-api` | Runway 视频/图像/音频批量生成 |
| `hyperframes` | HeyGen HyperFrames（HTML → 视频，GSAP 动画） |
| `save-to-spotify` | TTS 叙述 + 时间轴 + 封面，保存到 Spotify |
| `spotify-ads-api` | Spotify 广告活动管理（自然语言） |
| `liquid-lsp` | Shopify Liquid 模板 LSP |
| `liquid-skills` | Liquid 基础 + CSS/JS/HTML 编码规范 + WCAG 无障碍 |

### Web / API / 业务平台

| 插件名 | 一句话描述 |
|--------|-----------|
| `twilio-developer-kit` | Twilio API 程序化知识（哪个 API、什么时候用、怎么用） |
| `resend` | Resend 邮件 API 集成 |
| `zapier` | Zapier 8000+ 应用接入（发现、启用、执行） |
| `oracle-ai-data-platform-workbench-spark-connectors` | Oracle AI Data Platform Spark 连接器（18 个） |
| `quarkus-agent` | Quarkus 应用创建、管理、交互 MCP |
| `appwrite` | Appwrite SDK skill + MCP server + 部署 |
| `atlan` | Atlan 数据 catalog（搜索、探索、治理） |
| `outputai` | Output.ai 工作流开发（5 个专项 agent） |
| `pigment` | Pigment 业务数据分析与模型构建 |
| `zoom-plugin` | Zoom 集成（REST API、SDK） |
| `windsor-ai` | Windsor.ai 325+ 业务数据源（营销、销售） |

---

## 怎么用这份清单

- **想找某类工具**：直接定位到对应分类小节扫一遍
- **想看完整官方目录**：克隆仓库后看 `.claude-plugin/marketplace.json`
- **想看某个插件的源码**：远程插件的源码不在本仓库，看插件元数据里的 `repository` 字段，跳转到第三方仓库

仓库地址：<https://github.com/anthropics/claude-plugins-official>

---

> 本附录来自《Claude 插件官方指南》开源版 · 作者「递归客」  
> 在线阅读完整书系：[inferloop.dev](https://inferloop.dev)  
> 源码仓库：[github.com/diguike/book-claude-plugins](https://github.com/diguike/book-claude-plugins)
