# SoloSquad — 单人格 AI 全栈开发团队

> **一个人，就是一个研发团队。**
>
> 13 个 AI Agent 角色在同一个会话中自动接力，完成从需求分析到代码部署的完整软件项目。

---

## 这是什么？

**SoloSquad** 是一套 AI 软件工程团队模拟器，让你一个人就能拥有完整的研发团队。

你只需要与项目经理沟通需求，然后全自动流水线就会启动：产品经理写 PRD、架构师设计系统、数据库工程师建模、后端/前端/AI 工程师编码、测试/安全/代码评审工程师把关、运维工程师部署——**全部在一个会话中自动完成**。

```
你: 我想做一个任务管理系统
项目经理: 了解需求、技术栈、架构...
         ↓ 你确认后，全自动流水线启动
技术负责人 → 产品经理 → UI/UX设计师 → 系统架构师 → 数据库工程师
→ 后端工程师 → 前端工程师 → AI工程师 → 测试工程师 → 安全工程师
→ 代码评审工程师 → 运维工程师 → 项目验收
         ↓
✅ 项目交付！你只需要做决策和 Review
```

## 核心特性

| 特性 | 说明 |
|------|------|
| **全自动流水线** | 单会话自动推进所有角色，无需反复创建新对话 |
| **实时可见** | 所有角色工作实时输出，随时掌握进度 |
| **可中断可干预** | 随时打字中断，与项目经理沟通后说「继续」恢复 |
| **上下文安全** | 每角色完成写检查点文件，溢出后可软重启恢复 |
| **文档驱动** | Markdown 是唯一真实来源，不依赖聊天上下文 |
| **目录隔离** | 每个工程师只改自己的代码目录，越界即停 |
| **缺陷自动修复** | QA/安全发现问题后自动触发修复回路，修复后继续 |

## 快速开始

### 在 opencode 中使用

```bash
# 克隆仓库
git clone git@github.com:Zoe-cmd/SoloSquad.git
cd SoloSquad

# 在 opencode 中启动项目
# 在对话中告诉 AI：
# "请按照 SKILL.md 中的全自动流水线模式，帮我启动一个项目：我想做一个..."
```

### 在 TRAE / Claude Code / Cursor 中使用

本仓库是纯 Markdown 驱动，任何支持文件读取的 AI 工具都能用。详细导入指南见 [docs/使用指南.md](docs/使用指南.md)（待完善）。

## 项目结构

```
SoloSquad/
├── SKILL.md                     ← 入口文件（核心规则 + 角色体系）
├── workflow.md                  ← 工作流程定义
├── workflow-supplement.md       ← 补充流程（异常处理、变更管理）
├── VERSIONING.md                ← 版本管理协议
│
├── roles/                       ← 14 个 AI Agent 技能手册
│   ├── orchestrator.md          ← 调度指南（全自动流水线调度器）
│   ├── project-manager.md       ← 项目经理
│   ├── product-manager.md       ← 产品经理
│   ├── ui-ux-designer.md        ← UI/UX 设计师
│   ├── solution-architect.md    ← 系统架构师
│   ├── tech-lead.md             ← 技术负责人
│   ├── database-engineer.md     ← 数据库工程师
│   ├── backend-engineer.md      ← 后端工程师
│   ├── frontend-engineer.md     ← 前端工程师
│   ├── ai-engineer.md           ← AI 工程师
│   ├── qa-engineer.md           ← 测试工程师
│   ├── security-engineer.md     ← 安全工程师
│   ├── code-reviewer.md         ← 代码评审工程师
│   └── devops-engineer.md       ← 运维工程师
│
├── shared/                      ← 14 本共享规范
│   ├── coding-standard.md
│   ├── api-standard.md
│   ├── database-standard.md
│   ├── git-standard.md
│   ├── testing-standard.md
│   ├── deployment-standard.md
│   ├── review-standard.md
│   ├── documentation-standard.md
│   ├── decision-log-standard.md
│   ├── task-standard.md
│   ├── context-standard.md
│   ├── memory-standard.md
│   ├── handoff-standard.md
│   └── worklog-standard.md
│
├── templates/                    ← 12 个文档模板
├── examples/                     ← 示例项目
└── README.md                     ← 本文件
```

## 14 个角色一览

| 阶段 | 角色 | 核心产出 |
|------|------|----------|
| Phase 0 | 项目经理 | 项目计划、任务清单、决策日志 |
| Phase 0 | 技术负责人 | 技术选型决策 |
| Phase 1 | 产品经理 | PRD 产品需求文档 |
| Phase 1 | UI/UX 设计师 | 设计系统、用户流程、线框图 |
| Phase 1 | 系统架构师 | 架构设计文档 |
| Phase 2 | 数据库工程师 | 数据库 Schema、迁移计划 |
| Phase 2 | 后端工程师 | API 文档、后端代码 |
| Phase 3 | 前端工程师 | 前端页面、组件 |
| Phase 3 | AI 工程师 | AI 功能、RAG 管线 |
| Phase 4 | 测试工程师 | 测试计划、测试报告、缺陷报告 |
| Phase 4 | 安全工程师 | 安全审计报告 |
| Phase 4 | 代码评审工程师 | 代码审查报告、重构建议 |
| Phase 5 | 运维工程师 | 部署计划、CI/CD 配置 |
| Phase 5 | 项目经理（验收） | 项目总结、经验教训 |

## 调度机制

### 单会话全自动流水线

所有角色在同一个会话中自动推进，无需用户手动触发：

```
用户提出需求 → 项目经理（沟通需求） → 用户确认
    ↓
[自动推进] 技术负责人 → 产品经理 → UI/UX设计师 → 系统架构师
    ↓
[自动推进] 数据库工程师 → 后端工程师
    ↓
[自动推进] 前端工程师 → AI工程师（可并行）
    ↓
[自动推进] 测试工程师 → 安全工程师 → 代码评审工程师
    ↓
[自动推进] 运维工程师 → 项目经理（验收）
    ↓
✅ 项目全部完成
```

### 上下文管理

单会话跑全流程，上下文消耗会持续增长。SoloSquad 使用三层机制解决：

1. **检查点持久化**：每完成一个角色，将进度状态写入 `docs/state.json` 和 `docs/检查点/`
2. **水位监控**：绿/黄/橙/红四级，黄色压缩历史消息，橙色/红色建议软重启
3. **软重启**：在新对话中读取 `state.json` 从断点恢复

### 精准状态管理

使用 `jq` 精确读写 `state.json`，避免心智解析 JSON 出错：

```bash
# 读取当前角色
current_role=$(jq -r '.pipeline.current_role' docs/state.json)

# 读取进度
completed=$(jq '.pipeline.completed_roles | length' docs/state.json)
total=$(jq '.pipeline.completed_roles + .pipeline.remaining_roles | length' docs/state.json)
```

## 质量保障

| 门禁 | 检查内容 | 通过标准 |
|------|---------|---------|
| G1 | PRD 完整性 | 所有章节完整，无占位符 |
| G2 | 架构完整性 | 模块划分清晰，接口定义明确 |
| G3 | 代码审查 | 无 Critical/High 问题 |
| G4 | 测试通过 | 所有测试用例通过 |
| G5 | 安全审计 | 无 Critical 安全漏洞 |
| G6 | 上线确认 | 部署验证通过 |

## 使用场景

| 场景 | 说明 |
|------|------|
| **个人开发者做 Side Project** | 一个人花 2-3 天搞定全栈项目 |
| **创业团队快速验证 MVP** | 3 个人分别负责决策 + AI 执行 + Review，产出翻 3 倍 |
| **企业内部工具开发** | 运维同事自己用 AI 开发，不需要等开发部排期 |
| **学习全栈开发** | 观察每个角色的产出物，学习完整的软件开发流程 |

## 设计哲学

| 理念 | 说明 |
|------|------|
| 文档驱动 | Markdown 是唯一真实来源，所有 Agent 从文档读取、分析、更新、交接 |
| 全自动流水线 | 单会话全流程，自动推进，每角色完成通报进度 |
| 人在回路 | 用户可随时中断干预，关键决策由人类确认 |
| 目录隔离 | 每个工程师只改自己的代码目录，越界即停 |
| 文件清单管控 | AI 只能创建规范中列明的文件 |

## 常见问题

**Q: 一个会话跑完所有角色，上下文不会溢出吗？**

A: SoloSquad 采用「检查点持久化 + 软重启」机制。每完成一个角色，将进度写入 `state.json` 和检查点文件。上下文接近上限时，协调者会建议你开启新对话，输入「继续项目」即可从检查点恢复。

**Q: 一定要按 13 个角色的顺序走吗？**

A: 不需要。项目经理在 Phase 0 会根据项目需求进行团队规划，决定需要哪些角色。小项目可以跳过 UI 设计师、AI 工程师等非必需角色。

**Q: AI 生成的代码质量如何保证？**

A: 四层保障：(1) 每个角色 Skill 包含「常见错误」章节；(2) 14 本共享规范保证代码风格统一；(3) 代码评审工程师专门审查；(4) 你是最终审查者。

**Q: 可以用于商业项目吗？**

A: 可以。MIT 许可证，自由使用、修改、分发。

## License

MIT

---

> **"一个人就是一支军队。"**
>
> 不再等招人、不再等排期、不再等沟通。
> 打开你的 AI 工具，导入 SoloSquad，启动你的 AI 研发团队。
>
> **现在就试试。**