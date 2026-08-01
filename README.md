# TraeSkill — AI 软件工程团队技能仓库

> **一个人，就是一个团队。**
>
> 13 个 AI Agent 角色，接力式完成从需求分析到部署上线的完整软件研发流程。

---

## 这是什么？

一套**企业级 AI Agent 技能仓库**，专为中文开发者打造。用纯 Markdown 驱动 13 个专业 AI 角色，按企业级 SOP 接力完成软件开发。

```
项目经理 → 技术负责人 → 产品经理 → UI/UX设计师 → 系统架构师 → 数据库工程师
→ 后端工程师 → 前端工程师 → AI工程师 → 测试工程师 → 安全工程师
→ 代码评审工程师 → 运维工程师 → 项目验收
```

**你只需要做决策，AI 负责执行。**

---

## 核心特性

### 单角色单对话

**这是最重要的设计决策。** 每个角色在独立对话中工作，完成后产出交接文档并停止。不会出现一个对话串联多个角色、角色越界、上下文溢出的问题。

```
对话 1: 「启动项目」   → 项目经理工作 → 交接 → 停止
对话 2: 「继续」      → 技术负责人工作 → 交接 → 停止
对话 3: 「继续」      → 产品经理工作 → 交接 → 停止
对话 4: 「继续」      → UI/UX 设计师工作 → 交接 → 停止
...
```

| 好处 | 说明 |
|------|------|
| 角色不越界 | 一个对话只有一个角色，天然防止产品经理去做 UI 设计 |
| 上下文不溢出 | 每次只加载一个角色的 Skill + 规范 + 输入文档 |
| 方便管理 | 每个角色完成后你可以审查产出，满意了再启动下一个 |
| 可回溯 | 每个对话对应一个角色的完整工作，便于定位问题 |

### 文档驱动开发

Markdown 是唯一真实来源（Source of Truth）。所有 Agent 从文档读取信息、分析、实现、更新文档、交接。不依赖聊天上下文。

### 人在回路（HITL）

关键决策点（PRD 审批、架构评审、安全审计、上线确认）必须暂停等待人类确认。

### 文件清单管控

AI Agent 只能创建规范中列明的文件，不得自行发明文件名。交接文档统一存放在 `docs/交接/` 子目录。杜绝 AI 随意生成 `xxx-explanation.md`、`change-request-xxx.md` 等垃圾文件。

---

## 仓库结构

```text
TraeSkill/
├── SKILL.md                     TRAE Skill 入口（YAML 元数据 + 协调指令 + 变更管理规则）
├── README.md                    你正在看的这个文件
├── workflow.md                  核心工作流定义（概述、角色、Phase、交接、状态）
├── workflow-supplement.md       补充流程（异常处理、变更管理、角色依赖、时间估算）
├── VERSIONING.md                版本管理协议（语义化版本 + 迁移矩阵）
│
├── roles/                       13 个 AI Agent 技能手册（每个 300+ 行）
│   ├── product-manager.md        产品经理 — 需求分析、PRD 撰写
│   ├── ui-ux-designer.md         UI/UX 设计师 — 交互设计、设计系统
│   ├── solution-architect.md     系统架构师 — 架构设计、技术选型
│   ├── database-engineer.md      数据库工程师 — 数据建模、Schema 设计
│   ├── backend-engineer.md       后端工程师 — API 开发、业务逻辑
│   ├── frontend-engineer.md      前端工程师 — 组件开发、页面实现
│   ├── ai-engineer.md            AI 工程师 — Prompt 工程、RAG 管线
│   ├── qa-engineer.md            测试工程师 — 测试策略、缺陷管理（含缺陷修复回路）
│   ├── security-engineer.md      安全工程师 — 安全审计、漏洞修复
│   ├── code-reviewer.md          代码评审工程师 — 代码审查、重构建议
│   ├── devops-engineer.md        运维工程师 — CI/CD、部署监控
│   ├── project-manager.md        项目经理 — 进度跟踪、风险管理
│   └── tech-lead.md              技术负责人 — 技术决策、架构评审
│
├── shared/                       14 本共享规范 — 按角色需要加载，非全部预读
│   ├── coding-standard.md        编码规范（TypeScript / Python / Java / Go）
│   ├── api-standard.md           REST API 设计规范
│   ├── database-standard.md      数据库设计规范
│   ├── git-standard.md           Git 分支与提交规范
│   ├── testing-standard.md       测试规范
│   ├── deployment-standard.md    部署规范
│   ├── review-standard.md        代码评审规范（含严重等级定义）
│   ├── documentation-standard.md 文档规范（含文件清单 + 禁止创建清单外文件）
│   ├── decision-log-standard.md  技术决策记录规范
│   ├── task-standard.md          任务管理规范
│   ├── context-standard.md       上下文管理规范
│   ├── memory-standard.md        记忆管理规范
│   ├── handoff-standard.md       Agent 交接规范（含缺陷修复回路）
│   └── worklog-standard.md       工作日志与防漂移规范
│
├── templates/                    12 个文档模板 — 拿来即用
│   ├── project-plan-template.md  项目计划文档模板
│   ├── prd-template.md           产品需求文档模板
│   ├── architecture-template.md  架构设计文档模板
│   ├── api-template.md           API 设计文档模板
│   ├── database-template.md      数据库设计文档模板
│   ├── test-template.md          测试计划文档模板
│   ├── deployment-template.md    部署计划文档模板
│   ├── decision-log-template.md  决策记录文档模板
│   ├── todo-template.md          任务清单模板
│   ├── team-plan-template.md     团队规划文档模板
│   ├── change-request-template.md 变更请求文档模板
│   └── changelog-template.md      变更总览文档模板
│
└── examples/                     完整示例项目 — 可直接参考
    └── sample-project/           TaskFlow 任务管理系统
```

### 项目使用时的 docs/ 目录结构

AI Agent 在实际项目中产出的文档统一存放在 `docs/` 目录：

```text
docs/
├── 产品需求文档.md              ← 产品经理产出
├── 架构设计文档.md              ← 系统架构师产出
├── 数据库设计文档.md            ← 数据库工程师产出
├── API规范文档.md               ← 后端工程师产出
├── 测试计划.md                  ← 测试工程师产出
├── ...                         ← 完整清单见 documentation-standard.md
├── 工作日志/                    ← 各角色工作日志统一存放
├── 交接/                        ← 所有交接文档统一存放
│   ├── 交接-产品经理-to-系统架构师.md
│   ├── 交接-系统架构师-to-数据库工程师.md
│   └── 缺陷修复交接-{BUG-ID/VULN-ID/REVIEW-ID}.md
└── 归档/                        ← 已废弃文档归档
```

> **禁止创建清单外文件**：AI Agent 只能创建 `documentation-standard.md` 中列出的文件。

---

## 7 条核心规则

| # | 规则 | 说明 |
|---|------|------|
| 1 | **文档驱动** | 所有信息从 Markdown 文档读取，不得依赖聊天上下文 |
| 2 | **人在回路** | 关键决策点必须暂停等待人类确认 |
| 3 | **单一职责** | 每个角色只做自己职责范围内的事 |
| 4 | **显式交接** | 每个角色完成后必须产出交接文档，下一个角色读取交接文档继续 |
| 5 | **单角色单对话** | 一个对话只激活一个角色，完成后停止，用户在新对话中启动下一个 |
| 6 | **目录隔离** | 每个工程师只改自身代码目录（src/frontend/、src/backend/ 等），越界即停 |
| 7 | **工作日志防漂移** | 长对话内维护工作日志，每完成子任务锚定身份，触发预警主动停 |

### 角色标准工作流

```
1. 读取本角色 Skill 文件
2. 读取共享规范
3. 读取上游角色的交接文档和产出物
4. 分析需求
5. 设计方案
6. 执行实现
7. 自检 Review Checklist
8. 更新 Todo 状态
9. 编写交接文档（存放在 docs/交接/）
10. 输出交接摘要，告知用户下一角色名称
11. 停止。不得自动激活下一个角色
```

---

## 13 个角色一览

### 按阶段排列

| 阶段 | 角色 | Skill 文件 | 核心产出 |
|------|------|-----------|----------|
| Phase 0 | 项目经理 | `roles/project-manager.md` | 项目计划、任务清单、决策日志 |
| Phase 0 | 技术负责人 | `roles/tech-lead.md` | 技术选型决策 |
| Phase 1 | 产品经理 | `roles/product-manager.md` | PRD 产品需求文档 |
| Phase 1 | UI/UX 设计师 | `roles/ui-ux-designer.md` | 设计系统、用户流程、线框图 |
| Phase 1 | 系统架构师 | `roles/solution-architect.md` | 架构设计文档 |
| Phase 2 | 数据库工程师 | `roles/database-engineer.md` | 数据库 Schema、迁移计划 |
| Phase 2 | 后端工程师 | `roles/backend-engineer.md` | API 文档、后端代码 |
| Phase 3 | 前端工程师 | `roles/frontend-engineer.md` | 前端页面、组件 |
| Phase 3 | AI 工程师 | `roles/ai-engineer.md` | AI 功能、RAG 管线 |
| Phase 4 | 测试工程师 | `roles/qa-engineer.md` | 测试计划、测试报告、缺陷报告 |
| Phase 4 | 安全工程师 | `roles/security-engineer.md` | 安全审计报告 |
| Phase 4 | 代码评审工程师 | `roles/code-reviewer.md` | 代码审查报告、重构建议 |
| Phase 5 | 运维工程师 | `roles/devops-engineer.md` | 部署计划、CI/CD 配置 |
| Phase 5 | 项目经理 | `roles/project-manager.md` | 项目总结、经验教训 |

### 缺陷修复回路

当测试工程师、安全工程师或代码评审工程师发现 Critical/High 问题时，触发缺陷修复回路。流程、交接文档格式和 ID 命名规范详见 `shared/handoff-standard.md` 的「缺陷修复回路」章节。

---

## 开始使用

### 第 1 步：克隆仓库

```bash
git clone https://github.com/Zoe-cmd/TraeSkill.git
```

### 第 2 步：选择你的 AI 工具

本仓库是纯 Markdown 驱动，任何支持文件读取的 AI 工具都能用。以下是 7 个主流工具的导入方法。

---

## 各 AI 工具导入指南

### TRAE

TRAE 原生支持 Skill 系统，是最佳选择。

**导入**：设置 → 技能与命令 → 创建 → 上传 `SKILL.md` → 确认

**启动项目**：

```
我有一个项目想法：[描述你的项目]
请按照 SKILL.md 中的工作流程，从 Phase 0 开始推进。
```

**切换角色（新对话）**：

```
上一个角色已完成，请继续。
[或] 请激活技术负责人，读取 docs/交接/交接-项目经理-to-技术负责人.md
```

> TRAE 会根据 SKILL.md 的 `description` 自动判断是否触发本技能。你也可以手动调用：`用 software-team-simulator 技能帮我启动一个新项目`

### Claude Code

```bash
# 引用角色 Skill 文件 + 相关规范 + 模板
@roles/product-manager.md
@shared/documentation-standard.md
@templates/prd-template.md

请阅读以上文件，以产品经理身份分析需求：[描述需求]
```

角色完成后，**开新对话**引用下一个角色的文件。建议使用 Projects 功能将整个仓库作为项目上下文。

### Cursor

```bash
# .cursorrules
你是一个 AI 软件工程团队的成员。
请遵循 TraeSkill/roles/ 目录下的角色定义。
所有代码必须符合 TraeSkill/shared/ 目录下的编码规范。
关键决策必须等待人工确认（HITL）。
```

在 Cursor Chat 中用 `@file:roles/角色名.md` 引用。每个角色一个新对话。

### GitHub Copilot / Codex

```bash
# 在 Copilot Chat 中（VS Code）
请阅读 #file:roles/product-manager.md
你现在是一名产品经理，请按照文件中定义的工作流程分析我的需求。

# 使用 Codex CLI
cat roles/product-manager.md | codex -
```

建议创建 `.github/copilot-instructions.md` 包含核心规则。

### Gemini CLI

```bash
# 将 Skill 文件作为系统指令
gemini --system-instruction "$(cat roles/product-manager.md)"

# 或在对话中引用
gemini
> 请阅读 roles/product-manager.md，你是产品经理，分析需求：[描述]
```

Gemini 支持超长上下文，但仍然建议每个角色开新对话以避免角色越界。

### OpenHands

在 OpenHands 中为每个角色创建单独任务，通过文件系统传递上下文。天然适合文档驱动开发。

### 通用方法（任何 AI 工具）

1. **选择角色**：根据当前阶段，选择 `roles/角色名.md`
2. **喂给 AI**：Skill 文件 + 相关 `shared/` 规范 + `templates/` 模板 + 上游交接文档
3. **按流程执行**：AI 按照 Skill 中的 Prompt Template 工作
4. **完成后开新对话**：读取交接文档，激活下一个角色

| 你想做什么 | 读取什么 |
|-----------|---------|
| 启动某个角色 | `roles/角色名.md` |
| 了解编码规范 | `shared/coding-standard.md` |
| 参考文档模板 | `templates/xxx-template.md` |
| 了解整体流程 | `workflow.md` |
| 了解交接规范 | `shared/handoff-standard.md` |
| 了解文档清单 | `shared/documentation-standard.md` |

---

## 核心工作流

### 完整流程（7 个阶段）

```
Phase 0: 项目初始化
  项目经理 → 技术负责人

Phase 1: 需求与设计
  产品经理 → UI/UX设计师 → 系统架构师

Phase 2: 数据与后端
  数据库工程师 → 后端工程师

Phase 3: 前端与 AI
  前端工程师 → AI工程师

Phase 4: 质量保证
  测试工程师 → 安全工程师 → 代码评审工程师

Phase 5: 部署与运维
  运维工程师 → 项目经理验收

Phase 6: 持续维护（可选）
  技术负责人（技术债务管理、架构演进、性能优化、安全更新）
```

### 质量门禁

| 门禁 | 检查内容 | 通过标准 |
|------|---------|---------|
| G1 | PRD 评审 | Human Developer 审批通过 |
| G2 | 架构评审 | 技术负责人审批通过 |
| G3 | 代码审查 | 无 Critical/High 问题 |
| G4 | 测试通过 | 所有测试用例通过 |
| G5 | 安全审计 | 无 Critical 安全漏洞 |
| G6 | 上线确认 | Human Developer 审批通过 |

### 人在回路触发条件

| 触发条件 | 确认方式 |
|---------|---------|
| PRD 编写完成 | 等待 Human Developer 审批 |
| 架构设计完成 | 等待技术负责人评审 |
| 发现安全漏洞 | 列出漏洞，等待确认修复方案 |
| 上线前最终检查 | 等待 Human Developer 确认 |
| 遇到无法处理的异常 | 清晰描述问题，请求人工介入 |

---

## 使用场景

### 个人开发者做 Side Project

> "我想做一个 AI 记账小程序，但我是前端，后端不熟，数据库也不会设计。"

启动项目经理 → 写 PRD → 架构师设计 → 数据库工程师建模 → 后端 + 前端 → 测试 → 上线。一个人花 2-3 天搞定全栈项目。

### 创业团队快速验证 MVP

> "我们只有 3 个人，但要在一个月内出 MVP 去融资。"

3 个人分别负责：产品方向决策 + AI 执行 + 代码 Review。人力不变，产出翻 3 倍。

### 企业内部工具开发

> "运维部需要一个小工具，开发部排期到 3 个月后了。"

运维同事自己用 AI Agent 开发，开发部只做最终 Review。需求提出者 = 需求实现者。

---

## 设计哲学

| 理念 | 说明 |
|------|------|
| 文档驱动开发 (DDD) | Markdown 是唯一真实来源，所有 Agent 从文档读取、分析、更新、交接 |
| AI Agent First | 所有流程、规范、模板均面向 AI Agent 设计 |
| 人在回路 (HITL) | 关键决策由人类确认，AI 不可越权 |
| 多 Agent 协作 | 每个 Agent 担任单一角色，通过文档交接协作 |
| 单角色单对话 | 一个对话一个角色，完成后停止，避免越界和上下文溢出 |
| 文件清单管控 | AI 只能创建规范中列明的文件，杜绝随意生成垃圾文件 |
| SOLID / KISS / DRY / YAGNI | 遵循经典工程原则，避免过度设计 |

---

## 常见问题

**Q: 为什么每个角色要开新对话？一个对话做完不行吗？**

A: 不行。原因有三：(1) 单个角色的 Skill + 规范 + 输入文档已经很占上下文，串联多个角色必然溢出；(2) 一个对话多个角色会导致角色越界（产品经理去做 UI 设计）；(3) 你无法在每个角色完成后审查产出。单角色单对话是最优解。

**Q: 我一定要按 13 个角色的顺序走吗？**

A: 不需要。小项目可以跳过 UI 设计师、AI 工程师等非必需角色。`workflow.md` 中定义了哪些角色可以跳过。你也可以只用一个产品经理帮你写 PRD。

**Q: 这个仓库和 Cursor Rules / .cursorrules 有什么区别？**

A: Cursor Rules 是"给一个 AI 看的提示"。这个仓库是"给 13 个 AI 看的 SOP"。一个是便签，一个是一整套岗位手册 + 交接规范 + 质量门禁。

**Q: AI 生成的代码质量如何保证？**

A: 四层保障：(1) 每个角色 Skill 包含「常见错误」章节；(2) 13 本共享规范保证代码风格统一；(3) 代码评审工程师专门审查；(4) 你是最终审查者，所有关键决策由你确认。

**Q: 怎么自定义角色？**

A: 每个 Skill 文件是独立的，你可以修改、删除、添加。参考现有角色结构，遵循 `shared/` 中的规范，在 `workflow.md` 中定义新角色的位置即可。

**Q: 可以用于商业项目吗？**

A: 可以。MIT 许可证，自由使用、修改、分发。但 AI 生成的代码你需要自己审查质量。

---

## 仓库数据

| 指标 | 数值 |
|------|------|
| 总文件数 | 53 个 Markdown |
| 总行数 | 25,000+ |
| Skill 角色数 | 13 个（每个 300+ 行） |
| 共享规范数 | 14 本 |
| 文档模板数 | 12 个 |
| 示例项目 | 1 个完整项目（TaskFlow） |
| AI 工具支持 | 7 个主流工具 + 通用方法 |

---

## 重要说明

1. **这不是"一键生成"工具** — 你仍然需要做决策、Review 代码、调试问题。帮你省掉的是重复劳动和上下文切换，不是思考。
2. **AI 会犯错** — 永远要 Review AI 生成的代码。每本 Skill 手册都包含「常见错误」章节。
3. **单角色单对话** — 每个角色在独立对话中工作，完成后产出交接文档并停止。
4. **文件清单管控** — AI 只能创建规范中列明的文件，不得自行发明文件名。
5. **持续迭代** — 仓库本身也在不断进化。欢迎提 Issue 和 PR。

---

## License

MIT

---

> **"一个人就是一支军队。"**
>
> 不再等招人、不再等排期、不再等沟通。
> 打开你喜欢的 AI 工具，导入 Skill，启动你的 AI 研发团队。
>
> **现在就试试。**
