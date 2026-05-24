---
title: cwc-makers：Cardputer 硬件套件的一键 onboarding
feishu_url: "https://fivwvysqdz.feishu.cn/wiki/YPn7waCjQibi23kWLTmcYKGtn1b"
last_synced: "2026-05-25T00:01:29+08:00"
---

# cwc-makers：让 Claude 帮你把一台空白 Cardputer 变成可用的口袋电脑

[Claude Code](https://claude.com/claude-code) 上的硬件 onboarding 插件，配合 Anthropic 的 [Code-with-Claude Makers](https://claude.com/cwc-makers) 项目使用。一条 `/maker-setup` 命令搞定：克隆官方仓库、刷 UIFlow 固件、装好 Claude Buddy app bundle。

## 它解决什么问题

Anthropic 在 Code-with-Claude 大会上给参会者发了一台 [M5Stack Cardputer-Adv](https://m5stack.com/products/m5stack-cardputer-kit-w-m5stamps3)（基于 ESP32-S3 的口袋小键盘电脑）。要把它用起来，你得：

1. 找到 [`moremas/build-with-claude`](https://github.com/moremas/build-with-claude) 这个仓库
2. 装 Python + `esptool` + `pyserial`
3. 把设备插进 USB-C，按住背面的 download 按钮触发 bootloader
4. 跑一串 `esptool` 命令刷 UIFlow 2.0 固件
5. 跑另一串脚本把 MicroPython app bundle 推到 `/flash/`
6. 弄清楚 BLE pairing 怎么连 Claude Desktop 的 Hardware Buddy

这套流程对硬件熟手是 30 分钟，对纯软件工程师可能搞一晚上还连不上。cwc-makers 把整个过程做成 [Claude Code](https://claude.com/claude-code) 里的一条命令——插上设备打 `/maker-setup`，剩下的它自己做。

## 目录结构与组件

```
cwc-makers/
├── .claude-plugin/plugin.json
├── commands/
│   └── maker-setup.md             # /maker-setup 入口命令
├── skills/
│   ├── m5-onboard/
│   │   └── SKILL.md               # 完整 provisioning playbook
│   └── cardputer-buddy/
│       └── SKILL.md               # 装完后的迭代工作流
└── README.md
```

三个组件分工：

- **`/maker-setup`（slash command）** —— 入口。克隆 `build-with-claude` 到当前工作目录，然后调起 `m5-onboard` skill 走 onboarding 流程
- **`m5-onboard`（Skill，技能）** —— 完整 provisioning 流程：USB 检测、识别型号、刷 UIFlow 2.0、把 Claude Buddy + Snake + Hello 三个 app 推到设备。封装了 `onboard/scripts/onboard.py --apps buddy` 的调用方式（重点是要后台跑 + tee 日志，不然 2-3 分钟刷固件的过程会被 Claude Code 的 Bash 工具误判为挂死）
- **`cardputer-buddy`（Skill）** —— onboarding 完成后的开发循环。怎么往 `/flash/apps/` 里加新 app、怎么 push 单文件、怎么 tail 设备串口日志、怎么跑 REPL 一次性命令

两个 skill 的内容是从 [`moremas/build-with-claude`](https://github.com/moremas/build-with-claude) 上游 vendor（内嵌）过来的，不需要往 `~/.claude/skills/` 软链——Claude 直接拿到设备域知识在上下文里用。

`/maker-setup` 在 SKILL.md 里特意写了 fallback：如果系统没装 `git`，就用 `curl` + `tar` 下载 GitHub tarball；如果 `python` 缺包，`onboard.py` 会自动 `pip install esptool`。目标是让纯小白也能从插上 USB 到玩起来不踩坑。

## 核心命令拆解

只有一个用户命令 `/maker-setup`。它做的事按顺序是：

1. **拉仓库**：`git clone moremas/build-with-claude`，或在没 git 时用 curl+tar 下 tarball。重跑只会重下（~500KB），不做版本对账
2. **调起 `m5-onboard` skill**：进入 `build-with-claude/onboard/` 跑 `python3 scripts/onboard.py --apps buddy`，并通过 background + tee 模式实时把进度回传给用户
3. **处理那一步手动操作**：刷固件之前要按住设备背面的 download 按钮再插 USB。命令负责在正确时机弹出 prompt（提示词）让用户做这一步
4. **装完后给提示**：告诉用户怎么在设备上启动 Claude Buddy，怎么跟 Claude Desktop 的 Hardware Buddy 通过 BLE pair

两个 skill 也支持直接调用：

- 直接打 `/m5-onboard`——重新 onboarding 一台设备（比如想重置）
- 直接打 `/cardputer-buddy`——onboarding 完成后写 / 调试新 app

`m5-onboard` 默认假设是 Cardputer-Adv（`--variant cardputer-adv`），因为这是 cwc-makers 项目实际发的设备。如果用户说"我有一台 Cardputer"（不带 Adv），skill 里写明要追问——两款共用外形但固件不同，刷错会 bootloop。其他型号（Core2、CoreS3、Basic、Fire）需要显式传 `--variant`。

## 使用示例

完整的从零到能玩 Snake：

```bash
# 1. 把 Cardputer-Adv 用 USB-C 线插进电脑
# 2. 在 Claude Code 里：
/maker-setup

# Claude 会：
# - clone build-with-claude/
# - 调 m5-onboard skill
# - 提示你"按住设备背面的 BOOT 按钮再松开 RST"
# - 后台跑 onboard.py，2-3 分钟内 tail 日志给你看
# - 装完后告诉你：在设备上选 claude_buddy，然后在 Claude Desktop 打开 Hardware Buddy 配对

# 3. 之后想加个新 app
"帮我加一个 magic 8-ball app，按一个键随机出回答"

# cardputer-buddy skill 自动触发，Claude 会：
# - 参考 hello_cardputer.py 写 apps/magic_8ball.py
# - 跑 onboard/scripts/install_apps.py --port <PORT> --src buddy 推过去
# - 不需要重刷固件，下次开机菜单里就多了 magic_8ball
```

迭代开发时还能用 `buddy/scripts/` 下的工具：`push.py` 推单文件、`tail_serial.py` 看日志、`repl_run.py` 跑 REPL 一次性命令——这些都写在 `cardputer-buddy` 的 SKILL.md 里。

## 已知限制和注意事项

- **针对 cwc-makers 这一批设备优化**。`m5-onboard` 默认按 Cardputer-Adv（ESP32-S3、原生 USB）走。普通 Cardputer 或 M5Stack 的其他 Core 系列需要手动传 `--variant`，没传错型号会 bootloop。skill 里强制追问，但用户回答"对就是 Cardputer"很容易踩
- **强依赖 Python 3.10+**。Windows 上如果 Python 没装好或者 PATH 没设对，第一步就挂。skill 里写了 Windows PowerShell 的 fallback 命令但用户体验仍然差
- **`onboard.py` 必须后台跑**。SKILL.md 里专门花了一大段讲为什么不能用 Claude Code 的 Bash 工具前台调用——脚本要跑 2-3 分钟，前台调用会让 Claude 等不到输出以为挂了，提前 KillShell。必须用 `run_in_background: true` + tee 日志 + Monitor。这是 LLM 调硬件长任务的通用坑，但插件用户感知不到时仍可能踩
- **设备识别有死角**。`detect.py` 在刷 UIFlow 之前没办法区分 Cardputer 和 Cardputer-Adv（同样的 VID、native USB-JTAG），完全靠用户描述。skill 的处理是直接问用户，但用户可能不知道自己手里是哪一款
- **没有 OTA 更新机制**。重跑 `/maker-setup` 只是重下 `build-with-claude/`（~500KB），不做"上游有新版本所以推新固件"这种事。要更新固件必须手动重新走一遍流程
- **不是通用 ESP32 烧写工具**。这是给 Code-with-Claude Makers 项目的 onboarding 插件，不是给任意 ESP32 开发板用的。要做通用 ESP32 工作流，直接用 esptool / Thonny / UIFlow Desktop 反而更合适

---

> 本章来自《Claude 插件官方指南》开源版 · 作者「递归客」  
> 在线阅读完整书系：[inferloop.dev](https://inferloop.dev)  
> 源码仓库：[github.com/diguike/book-claude-plugins](https://github.com/diguike/book-claude-plugins)
