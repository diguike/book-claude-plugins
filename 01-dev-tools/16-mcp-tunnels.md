---
title: mcp-tunnels：把私网 MCP server 通过 Anthropic tunnel 暴露给 Claude
feishu_url: "https://fivwvysqdz.feishu.cn/wiki/SxTdwyBZgigQl8kGxObcl3dcnTf"
last_synced: "2026-05-25T00:01:29+08:00"
---

# mcp-tunnels：让 Claude 调到你内网里的 MCP server，不开任何入向端口

[Claude Code](https://claude.com/claude-code) 插件，把私网里的 MCP（Model Context Protocol，模型上下文协议，<https://modelcontextprotocol.io>）server 通过 [Anthropic 的 MCP tunnel](https://platform.claude.com/docs/en/agents-and-tools/mcp-tunnels/overview)（research preview 阶段的功能）暴露给 Claude。出向连接、无需开放入向端口、无需在源 IP 白名单上加 Anthropic 的网段。一条 `/create-docker-mcp-tunnel` 走完 Docker Compose quickstart 全流程。

## 它解决什么问题

把 MCP server 接给 Claude 的传统做法有两种：

- **远程 streamable-HTTP**：你的 server 必须有公网入口、TLS 证书、OAuth 之类的认证。中小公司内部工具一个都没有
- **stdio / MCPB 本地进程**：只能给本机或者打包给单机用户，没法跨网络

中间地带：你有一个跑在内网（Kubernetes 集群里、办公室 NAS、homespace dev container）里的 MCP server，想让 Claude 能调，但**不想**给它开 ingress、不想申请公网域名、不想动公司的防火墙策略。

MCP tunnel 就是干这个的。模型类似 Cloudflare Tunnel / ngrok：cloudflared 容器跑在你的内网里，主动出向连接到 Anthropic 的 tunnel edge，Claude 的请求通过这个 outbound 连接反向打回来。整个过程没有任何 inbound 端口暴露。

> **Research preview** 阶段。Anthropic 明确写了 as-is、无 SLA、依赖 [Cloudflare](https://cloudflare.com) 作为 transport provider。生产敏感数据走之前先读一遍 [security model](https://platform.claude.com/docs/en/agents-and-tools/mcp-tunnels/security)。

## 目录结构与组件

```
mcp-tunnels/
├── .claude-plugin/plugin.json
├── commands/
│   └── create-docker-mcp-tunnel.md   # 唯一的 slash command
├── README.md
└── LICENSE
```

简洁到极致——一个 slash command、一份 README，没有 skill、没有 agent、没有 hook（钩子）。所有逻辑都在 `create-docker-mcp-tunnel.md` 的 prompt（提示词）里。命令本身的 `allowed-tools` 是 `[Bash, Read, Write, Edit, AskUserQuestion]`，配合 `AskUserQuestion` 在需要用户去 Console 操作时停下来等。

跑完一遍后，你的机器上会有一个容器栈：

| 容器 | 角色 |
|---|---|
| **mcp-proxy** | Anthropic 提供的代理容器。终结内层 TLS（用你自签的证书）、校验 upstream IP、按 hostname 路由 |
| **cloudflared** | tunnel agent。出向连接到 Anthropic tunnel edge，共享 mcp-proxy 的 network namespace |
| **hello-mcp**（可选） | 一个 FastMCP 样例 server。只在你还没自己的 MCP server 想先验证流程时启用 |

整体路径是：Claude → Anthropic tunnel edge → Cloudflare tunnel → cloudflared 容器 → localhost:8080 (mcp-proxy) → 你的 upstream MCP server。

## 核心命令拆解

`/create-docker-mcp-tunnel [deployment-dir]` 是唯一命令。默认部署目录 `./mcp-tunnel`。它按 10 步走 [official quickstart](https://platform.claude.com/docs/en/agents-and-tools/mcp-tunnels/quickstart)，关键节点：

**Step 0 — Preflight** —— 检查 `docker`、`docker compose`、`openssl` 1.1.1+ 是否可用，确认主机能出向访问 `api.anthropic.com:443` 和 tunnel edge（`198.41.192.0/19`、`2606:4700:a0::/44`）的 7844 TCP/UDP。缺什么就停下来报错。

**Step 1 — 在 Console 创建 tunnel** —— 用户操作。让用户去 [Claude Console](https://console.anthropic.com) → Manage → MCP tunnels → New tunnel，记下 Domain（形如 `abcd1234.tunnel.anthropic.com`）和 Token。**Token 不许粘进对话**——命令明确写了要让用户自己写进 `.env`，避免出现在 transcript 里。

**Step 2-3 — 部署目录 + 凭证文件** —— `mkdir -p $DIR/{config,data}`、写 `.env`、`chmod 600`、写 `.gitignore` 把 `.env` 和 `data/` 都排除。

**Step 4 — 生成 CA 和 server 证书** —— mcp-proxy 终结一个内层 TLS 握手，需要一份你掌控的 CA 签发的证书。命令用 `openssl req -x509 -addext` 的方式生成（不依赖 OpenSSL 3.0+ 的 `-copy_extensions`，保证 OpenSSL 1.1.x 还能用）。关键细节：`chmod 644 data/tls.key` 是强制要求——openssl 默认写 0600，但代理容器以非 root 用户跑，必须能读。

**Step 5 — Console 注册 CA** —— 用户操作。让用户把 `data/ca.crt` 内容粘到 Console 的 Certificates 页面。tunnel 状态从 Pending 翻到 Active 才能继续。如果用户在远程 dev container 上跑这一步，README 里专门有一段教 `scp` 怎么把 `ca.crt` 拉到能开浏览器的机器（不到 1KB 直接 `cat` 粘贴也行）。

**Step 6 — 选 upstream** —— 用 `AskUserQuestion` 问：用现成的 MCP server 还是用样例？现成的要求是 `scheme://host:port`（不能带 path——proxy 在 config 加载时就拒）、必须 resolve 到 RFC1918 私网（10/8、172.16/12、192.168/16），代理默认拒绝公网/loopback upstream 做 SSRF 防护。没现成的就 scaffold 一个 FastMCP 的 `hello-server`。

**Step 7-8 — 写 mcp-proxy.yaml + docker-compose.yaml** —— `routes` 是 subdomain → upstream URL 的扁平 map，**不是 list**（写成 list 是常见错误，README 的 troubleshooting 里专门列了）。compose 镜像按 sha256 digest pin 死。cloudflared 的 `--url http://localhost:8080` 在 manual flow 里是必须的——没有 ingress 规则推到服务端，少了这个 cloudflared 每个请求都 503。`network_mode: "service:mcp-proxy"` 让 cloudflared 共享 proxy 的 netns，所以它访问 `localhost:8080` 能直接打到 proxy。

**Step 9 — 启动 + 验证** —— `docker compose up -d`，然后 grep mcp-proxy 日志找 `route configured`、grep cloudflared 找四条 `Registered tunnel connection`。容器起来要几秒，命令里写了"empty 不要立刻判失败，多 grep 几次"。

**Step 10 — 从 Claude 调** —— 教用户怎么在 Managed Agents（Console）和 Messages API 里指向这个 tunnel。

`/create-docker-mcp-tunnel.md` 里还内置了一个 troubleshooting matrix，覆盖 TLS handshake 失败、`routes-must-be-a-map` 配置错、`tls.key` 权限错、config 改了不热加载（cloudflared 需要重启）、upstream IP 校验失败这几个高频坑，外加 token 轮换 / 证书续期的 ops 基础动作。

## 使用示例

最快的一条端到端流程，假设你有一个跑在内网 `wiki-mcp.internal:8080` 的 MCP server：

```bash
# 1. 在 Claude Code 里启动命令
/create-docker-mcp-tunnel ~/work/wiki-tunnel

# Claude 会带你走：
# - 跑 docker --version / openssl version 检查环境
# - 让你去 Console 创建 tunnel，回来告诉它 domain
# - 你自己把 token 粘进 .env（不进对话历史）
# - openssl 生成 ca.crt / tls.crt / tls.key
# - 让你把 ca.crt 粘到 Console，等 Active
# - 用 AskUserQuestion 问你 upstream，你答 http://wiki-mcp.internal:8080，subdomain 选 wiki
# - 写 config/mcp-proxy.yaml 和 docker-compose.yaml
# - docker compose up -d，grep 日志确认上线

# 2. 在 Console 里把这个 tunnel 挂到一个 Managed Agent，或者在 Messages API 里 reference 它

# 3. Claude 调用时走的 URL：
#    https://wiki.abcd1234.tunnel.anthropic.com/mcp
#    你的防火墙上没有任何入向端口被打开
```

只想先验证流程而不接真实 server？Step 6 选 sample server，会 scaffold 一个 FastMCP 的 `hello-server`，提供一个 `hello(name)` 工具。从 Claude 调它返回 "Hello, world!" 就说明整条链路通了。

## 已知限制和注意事项

- **Research preview**。Anthropic 写得很明白：无 SLA、无 uptime 承诺、依赖 Cloudflare 作为 transport。生产关键流量先评估再走
- **只覆盖 quickstart 路径**。这个插件做的是 manual-credentials、单机、Docker Compose 的最短路径。要 hardened 部署（non-root、read-only rootfs、cap_drop ALL）、Kubernetes（Helm chart）、或者 [Workload Identity Federation](https://platform.claude.com/docs/en/manage-claude/workload-identity-federation) 走程序化授权，得去看官方的 [Deploy with Docker Compose](https://platform.claude.com/docs/en/agents-and-tools/mcp-tunnels/deploy-compose) 和 [Deploy with Helm](https://platform.claude.com/docs/en/agents-and-tools/mcp-tunnels/deploy-helm)
- **必须有 Console 操作权限**。Step 1 和 Step 5 都需要用户登 Claude Console 操作。如果你只有 API（Application Programming Interface，应用编程接口）key 没有 Console 角色，整个流程跑不下去
- **upstream 必须是 RFC1918 私网**。代理默认拒绝公网和 loopback upstream（SSRF 防护）。要绕过得改 proxy 配置，但这正好是 mcp-tunnels 的安全模型，绕过等于自废武功
- **token 轮换不自动**。Console 上 rotate token 后，你得手动改 `.env` 然后 `docker compose up -d` 重起 cloudflared。同样 server 证书 90 天到期，命令产物里 `data/tls.crt` 不会自动续——README 的 ops 部分给了 openssl 续期命令但没做 cron 化
- **config 改了不热加载**。troubleshooting matrix 里专门写了这条：改 `mcp-proxy.yaml` 后必须 `docker compose restart mcp-proxy`。容易因为以为生效了而调试半天
- **cloudflared 的镜像是固定 digest pin**。这意味着 Anthropic 改了镜像之后你要重跑这个命令（或者手动改 `docker-compose.yaml`）才能拿到新版

---

> 本章来自《Claude 插件官方指南》开源版 · 作者「递归客」  
> 在线阅读完整书系：[inferloop.dev](https://inferloop.dev)  
> 源码仓库：[github.com/diguike/book-claude-plugins](https://github.com/diguike/book-claude-plugins)
