---
title: code-modernization：遗留系统现代化工作流
feishu_url: "https://fivwvysqdz.feishu.cn/wiki/GcbYwP4IfidTztkAW8EcbA8ansc"
last_synced: "2026-05-25T00:01:29+08:00"
---

# code-modernization：把遗留代码搬到现代栈的结构化工作流

[Claude Code](https://claude.com/claude-code) 上的遗留系统现代化插件。它不是给你一把"一键迁移"的银弹，而是把一个真实的现代化项目应该走的七步做成 slash command，每步产出可以单独 review 的 artifact，强制你按顺序走完。

## 它解决什么问题

遗留现代化失败，多数时候不是因为目标技术选错，而是因为团队跳步：还没读懂代码就开始转译；还没把业务规则提取出来就开始重新设计架构；还没准备好测试网就直接上线。结果新系统行为漂移、客户投诉、回滚——然后被钉在"AI 迁移不靠谱"的耻辱柱上。

这个插件强制了一个顺序：

```
assess → map → extract-rules → brief → reimagine | transform → harden
```

前三步是 discovery（理解）。第四步是审批闸门。第五步是改造，分两条路：`reimagine`（绿地重写）或 `transform`（strangler-fig 模式，模块级替换）。最后一步给还在跑的遗留系统打安全补丁。

跟 [strangler-fig 模式](https://martinfowler.com/bliki/StranglerFigApplication.html)（Martin Fowler 总结的渐进替换模式）天然契合：`transform` 每次只搞一个模块，写 characterization tests 锁住行为，新代码通过测试才算等价，老代码在剩余模块替换完之前继续跑。

## 目录结构与组件

```
code-modernization/
├── .claude-plugin/plugin.json
├── commands/                       # 7 个 slash command
│   ├── modernize-assess.md
│   ├── modernize-map.md
│   ├── modernize-extract-rules.md
│   ├── modernize-brief.md
│   ├── modernize-reimagine.md
│   ├── modernize-transform.md
│   └── modernize-harden.md
├── agents/                         # 5 个 specialist subagent（子代理）
│   ├── legacy-analyst.md
│   ├── business-rules-extractor.md
│   ├── architecture-critic.md
│   ├── security-auditor.md
│   └── test-engineer.md
└── README.md
```

约定的工作目录布局：`legacy/<system>/` 放只读的遗留代码，`analysis/<system>/` 放 discovery 产物（ASSESSMENT.md、TOPOLOGY.html、BUSINESS_RULES.md、MODERNIZATION_BRIEF.md 等），`modernized/<system>/` 放新写的代码。如果遗留代码不在 `legacy/` 下，README 建议 `ln -s` 链一个软连接进去。

配套的 `.claude/settings.json` 权限模板里把 `Edit(legacy/**)` 写进 `deny`，从工具层就拦住"不小心改了老代码"这种事。

## 核心命令拆解

**`/modernize-assess <system-dir>`** —— 盘点遗留系统。语言分布、代码行数、复杂度、构建系统、集成点、技术债、安全态势、文档缺口，最后给一个 COCOMO 工作量估算。并行启动两个 `legacy-analyst` 和一个 `security-auditor` agent 做深读。优先用 [`scc`](https://github.com/boyter/scc) 或 [`cloc`](https://github.com/AlDanial/cloc) 统计；都没有就降级到 `find` + `wc`。带 `--portfolio <parent-dir>` 时扫一整个目录下的所有子系统，输出 sequencing 热力图 `analysis/portfolio.html`，给指导委员会排优先级用。

**`/modernize-map <system-dir>`** —— 画依赖和拓扑图。程序/模块调用图、数据血缘（程序 ↔ 数据存储）、入口点、孤立分支候选、一条带注释的关键业务路径。命令本身会写一个 one-off 解析脚本（可重跑），产物有 `topology.json`（机器可读）、`TOPOLOGY.html`（渲染后的 Mermaid + architect 观察）以及 `call-graph.mmd`、`data-lineage.mmd`、`critical-path.mmd` 三张独立的 Mermaid 图。

**`/modernize-extract-rules <system-dir> [module-pattern]`** —— 把藏在代码里的业务规则挖出来。并行启动三个 `business-rules-extractor` agent，分别看：计算（公式、税率、阈值）、校验/资格、状态机/生命周期。每条规则写成 Given/When/Then 形式的 Rule Card，带 `file:line` 引用和置信度。产物是 `BUSINESS_RULES.md` 和 `DATA_OBJECTS.md`——这是项目里最值钱的资产，老员工退休后只剩这些能交接。

**`/modernize-brief <system-dir> [target-stack]`** —— 把前面所有 artifact 合成一份 **Modernization Brief**：目标架构、strangler-fig 分阶段计划（每阶段的进入/退出标准）、行为契约、验证策略、未决问题、审批块。读不到 ASSESSMENT / TOPOLOGY / BUSINESS_RULES 任何一份就直接停——必须先跑完前三个命令。写完 `MODERNIZATION_BRIEF.md` 后进入 plan mode，作为 human-in-the-loop 闸门。

**`/modernize-reimagine <system-dir> <target-vision>`** —— 绿地重写。不是按结构搬运，是从提取出来的 intent 重新设计。先并行跑 `business-rules-extractor` + 两个 `legacy-analyst` + 一个 `architecture-critic` 做规范挖掘和对抗 review，再 scaffold 服务（带可执行验收测试）到 `modernized/<system>-reimagined/`，最后写一份 `CLAUDE.md` 给新系统做知识交接。两个 HITL（human-in-the-loop）检查点。

**`/modernize-transform <system-dir> <module> <target-stack>`** —— 单模块手术刀式重写，strangler-fig 的一片切片。先 plan（HITL 闸门），再让 `test-engineer` 写 characterization tests（捕捉遗留行为），再写目标实现到 `modernized/<system>/<module>/`，跑测试证明等价，最后写 `TRANSFORMATION_NOTES.md` 标注每处刻意偏离。`architecture-critic` 收尾 review。

**`/modernize-harden <system-dir>`** —— 安全加固。OWASP/CWE 扫描、依赖 CVE、密钥、注入。`security-auditor` 先出 `SECURITY_FINDINGS.md`（按 Critical/High/Medium/Low 排序），再生成 `security_remediation.patch`，**第二个 security-auditor 复审**这份 patch 后你才看到。命令**绝不直接改 legacy/**，patch 留给你自己 review + apply。专门为"迁移期间老系统还在生产跑"的场景设计。

五个 specialist agent 不只在 command 里被调用，也支持直接对 Claude 说"启动 architecture-critic review 一下这套设计"来单独用。

## 使用示例

一条典型的端到端流水线，对应一个叫 `billing` 的遗留系统：

```bash
# 1. 先做一次软链接，把真实代码挂到 legacy/billing
mkdir -p legacy && ln -s /path/to/your/legacy/billing legacy/billing

# 2. 盘点：建立第一印象
/modernize-assess billing

# 3. 画图：搞清楚谁调谁、谁读写什么
/modernize-map billing

# 4. 提规则：把业务逻辑从代码里捞出来
/modernize-extract-rules billing

# 5. 写 Brief：合成审批文档，进入 plan mode，等用户确认
/modernize-brief billing java-spring

# 6a. 走绿地重写：从 intent 重新设计
/modernize-reimagine billing "event-driven services on Java 21 / Spring Boot"

# 或 6b. 走 strangler-fig：一个模块一个模块换
/modernize-transform billing interest-calc java-spring

# 7. 给还在线上的老系统打安全补丁
/modernize-harden billing
```

每个命令都产出独立 artifact。中途随时可以停下来 review，然后接着跑——不需要从头来。

## 已知限制和注意事项

- **跑完一遍非常贵**。前三个 discovery 命令每个都会并行起 2-3 个 subagent 长时间读代码，一个中等规模（几十万行）的遗留系统跑完一轮，token（模型输入的最小单位）消耗很容易上百万。建议先在小范围（单个模块）试一遍流程跑通，再上整个系统
- **`legacy-analyst` 偏向 COBOL / 老 Java / 老 C++ / 经典 ASP**。对纯前端 JS/TS、Rust 这种现代栈的"遗留"项目，描述里写的"JOBOL 模式"识别就用不上了，但流程本身还是可以套
- **依赖项目结构约定**。命令硬编码了 `legacy/`、`analysis/`、`modernized/` 三个目录。如果你不想按这个布局走，要么 `ln -s` 兼容，要么改命令里的路径
- **`modernize-brief` 的"必须先跑完前三个"是硬卡点**。少跑一个就 hard fail。这是设计意图——避免基于不完整信息生成 Brief——但第一次用的时候容易踩
- **`modernize-transform` 的等价性证明取决于 characterization tests 的覆盖率**。`test-engineer` 写的测试只能覆盖它能识别出的行为；那些藏在罕见输入下的边角逻辑可能漏。重要模块上线前还需要灰度对照（dual-run）
- **`modernize-reimagine` 的两个 HITL 检查点不要跳过**。绿地重写比 transform 危险得多——架构选错了后面很难回滚。`architecture-critic` 的对抗 review 看似挑刺，但那是设计意图，越不舒服越说明它在干活

---

> 本章来自《Claude 插件官方指南》开源版 · 作者「递归客」  
> 在线阅读完整书系：[inferloop.dev](https://inferloop.dev)  
> 源码仓库：[github.com/diguike/book-claude-plugins](https://github.com/diguike/book-claude-plugins)
