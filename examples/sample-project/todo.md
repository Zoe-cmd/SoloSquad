# Todo List: TaskFlow 项目任务清单

<!--
Document: Project Task List
Version: 1.0.0
Author: Project Manager
Created: 2026-07-11
Updated: 2026-07-11
-->

## 项目统计

| 指标 | 数值 |
|------|------|
| 总任务数 | 35 |
| 已完成 | 14 |
| 进行中 | 0 |
| 待开始 | 21 |
| 完成率 | 40% |
| 预计完成日期 | 2026-09-15 |

---

## Phase 0: 项目初始化 (已完成)

| ID | 任务 | 负责人 | 优先级 | 截止日期 | 状态 | 依赖 |
|----|------|--------|--------|----------|------|------|
| T-001 | 项目规划与技术选型 | Project Manager | P0 | 2026-07-01 | COMPLETED | - |
| T-002 | 初始化 Git 仓库与 Monorepo 结构 | Tech Lead | P0 | 2026-07-01 | COMPLETED | T-001 |
| T-003 | 配置 CI/CD 流水线 (GitHub Actions) | DevOps Engineer | P0 | 2026-07-02 | COMPLETED | T-002 |
| T-004 | 配置开发环境 (Docker Compose, pnpm) | DevOps Engineer | P0 | 2026-07-02 | COMPLETED | T-002 |
| T-005 | 建立代码规范 (ESLint, Prettier, Commitlint) | Tech Lead | P1 | 2026-07-03 | COMPLETED | T-002 |

**输出:**
- `docs/项目计划.md`, `docs/决策日志.md`
- Monorepo 结构 (apps/api, apps/web, packages/shared)
- `.github/workflows/ci.yml`
- `.eslintrc.js`, `.prettierrc`, `commitlint.config.js`

---

## Phase 1: 需求与设计 (已完成)

| ID | 任务 | 负责人 | 优先级 | 截止日期 | 状态 | 依赖 |
|----|------|--------|--------|----------|------|------|
| T-006 | 编写 PRD (V1.0 核心功能 + V2.0 智能排期) | Product Manager | P0 | 2026-07-05 | COMPLETED | T-001 |
| T-007 | 设计 UI/UX 原型 (Figma) | UI/UX Designer | P0 | 2026-07-08 | COMPLETED | T-006 |
| T-008 | 设计系统架构 | Solution Architect | P0 | 2026-07-06 | COMPLETED | T-006 |
| T-009 | 设计数据库 Schema | Database Engineer | P0 | 2026-07-07 | COMPLETED | T-008 |
| T-010 | 编写 API 规范 | Backend Engineer | P0 | 2026-07-08 | COMPLETED | T-008, T-009 |
| T-011 | 编写测试计划 | QA Engineer | P1 | 2026-07-09 | COMPLETED | T-006, T-010 |

**输出:**
- `docs/产品需求文档.md`, `docs/架构设计文档.md`, `docs/数据库设计文档.md`
- `docs/API规范文档.md`, `docs/测试计划.md`
- Figma 设计稿

---

## Phase 2: 基础设施与后端 (进行中)

| ID | 任务 | 负责人 | 优先级 | 截止日期 | 状态 | 依赖 |
|----|------|--------|--------|----------|------|------|
| T-012 | 搭建 NestJS 项目骨架 + 模块划分 | Backend Lead | P0 | 2026-07-10 | COMPLETED | T-008 |
| T-013 | 实现数据库迁移脚本 + 种子数据 | Database Engineer | P0 | 2026-07-12 | COMPLETED | T-009 |
| T-014 | 实现用户认证模块 (注册/登录/JWT) | Backend Engineer A | P0 | 2026-07-15 | COMPLETED | T-012, T-013 |
| T-015 | 实现用户模块 (CRUD + 角色管理) | Backend Engineer A | P0 | 2026-07-17 | PENDING | T-014 |
| T-016 | 实现项目管理模块 (CRUD + 成员管理) | Backend Engineer B | P0 | 2026-07-19 | PENDING | T-014 |
| T-017 | 实现任务管理模块 (CRUD + 状态流转 + 搜索) | Backend Engineer B | P0 | 2026-07-22 | PENDING | T-016 |
| T-018 | 实现评论模块 (CRUD + @提及) | Backend Engineer A | P1 | 2026-07-24 | PENDING | T-017 |
| T-019 | 实现附件模块 (上传/下载 + S3 集成) | Backend Engineer A | P1 | 2026-07-25 | PENDING | T-017 |
| T-020 | 实现通知模块 (站内通知 + 邮件通知) | Backend Engineer B | P1 | 2026-07-26 | PENDING | T-017 |
| T-021 | 实现数据看板模块 (统计 + 燃尽图) | Backend Engineer B | P1 | 2026-07-29 | PENDING | T-017 |
| T-022 | 实现 AI 排期模块 (OpenAI 集成 + 降级) | Backend Engineer A | P0 | 2026-08-05 | PENDING | T-017 |
| T-023 | 实现 AI 摘要模块 (任务摘要生成) | Backend Engineer A | P2 | 2026-08-07 | PENDING | T-017 |
| T-024 | 编写后端单元测试 (覆盖率 >= 80%) | ALL Backend | P0 | 2026-08-10 | PENDING | T-014~T-023 |
| T-025 | 编写后端集成测试 (关键路径 100%) | ALL Backend | P0 | 2026-08-12 | PENDING | T-024 |

---

## Phase 3: 前端开发 (待开始)

| ID | 任务 | 负责人 | 优先级 | 截止日期 | 状态 | 依赖 |
|----|------|--------|--------|----------|------|------|
| T-026 | 搭建 React 项目骨架 + 路由 + 状态管理 | Frontend Lead | P0 | 2026-07-15 | PENDING | T-002 |
| T-027 | 实现登录/注册页面 | Frontend Engineer A | P0 | 2026-07-18 | PENDING | T-026, T-014 |
| T-028 | 实现项目列表 + 创建/编辑页面 | Frontend Engineer B | P0 | 2026-07-22 | PENDING | T-026, T-016 |
| T-029 | 实现任务看板页面 (Kanban) | Frontend Engineer A | P0 | 2026-07-29 | PENDING | T-026, T-017 |
| T-030 | 实现任务详情页 (含评论 + 附件) | Frontend Engineer B | P0 | 2026-08-02 | PENDING | T-026, T-017, T-018, T-019 |
| T-031 | 实现数据看板页面 (统计图表) | Frontend Engineer A | P1 | 2026-08-07 | PENDING | T-026, T-021 |
| T-032 | 实现智能排期页面 (甘特图 + 工作负载) | Frontend Engineer B | P0 | 2026-08-12 | PENDING | T-026, T-022 |
| T-033 | 实现通知中心 | Frontend Engineer A | P1 | 2026-08-14 | PENDING | T-026, T-020 |
| T-034 | 编写前端单元测试 (覆盖率 >= 80%) | ALL Frontend | P0 | 2026-08-20 | PENDING | T-027~T-033 |

---

## Phase 4: 质量保证 (待开始)

| ID | 任务 | 负责人 | 优先级 | 截止日期 | 状态 | 依赖 |
|----|------|--------|--------|----------|------|------|
| T-035 | 执行 E2E 测试 (核心流程 100%) | QA Engineer | P0 | 2026-08-25 | PENDING | T-034, T-025 |
| T-036 | 执行性能测试 (k6 压测) | DevOps Engineer | P1 | 2026-08-27 | PENDING | T-035 |
| T-037 | 执行安全审计 (OWASP ZAP) | Security Engineer | P1 | 2026-08-28 | PENDING | T-035 |
| T-038 | 代码审查 (全量 PR Review) | Tech Lead | P0 | 2026-08-30 | PENDING | T-035 |
| T-039 | 数据迁移验证 (种子数据) | Database Engineer | P1 | 2026-08-30 | PENDING | T-013 |
| T-040 | UI/UX 验收 (与 Figma 对比) | UI/UX Designer | P1 | 2026-08-31 | PENDING | T-035 |

---

## Phase 5: 部署与运维 (待开始)

| ID | 任务 | 负责人 | 优先级 | 截止日期 | 状态 | 依赖 |
|----|------|--------|--------|----------|------|------|
| T-041 | 配置 Kubernetes 资源文件 | DevOps Engineer | P0 | 2026-08-20 | PENDING | T-003 |
| T-042 | 配置 ArgoCD GitOps 部署 | DevOps Engineer | P0 | 2026-08-22 | PENDING | T-041 |
| T-043 | 配置监控告警 (Prometheus + Grafana) | DevOps Engineer | P1 | 2026-08-25 | PENDING | T-041 |
| T-044 | 部署到 staging 环境 | DevOps Engineer | P0 | 2026-09-01 | PENDING | T-038, T-042 |
| T-045 | staging 环境冒烟测试 | QA Engineer | P0 | 2026-09-02 | PENDING | T-044 |
| T-046 | 部署到 production 环境 | DevOps Engineer | P0 | 2026-09-05 | PENDING | T-045 |
| T-047 | production 环境验证 | ALL | P0 | 2026-09-05 | PENDING | T-046 |
| T-048 | 配置备份与恢复策略 | DevOps Engineer | P1 | 2026-09-06 | PENDING | T-046 |

---

## Phase 6: 上线后 (待开始)

| ID | 任务 | 负责人 | 优先级 | 截止日期 | 状态 | 依赖 |
|----|------|--------|--------|----------|------|------|
| T-049 | 用户反馈收集与分析 | Product Manager | P1 | 2026-09-12 | PENDING | T-047 |
| T-050 | 性能监控与优化 | DevOps Engineer | P1 | 2026-09-15 | PENDING | T-047 |
| T-051 | 编写项目总结文档 | Project Manager | P2 | 2026-09-20 | PENDING | T-047 |
| T-052 | 制定 V2.0 迭代计划 | Product Manager | P2 | 2026-09-25 | PENDING | T-049 |

---

## 任务依赖关系图

```
Phase 0: 项目初始化
  T-001 ──▶ T-002 ──▶ T-003
                   │
                   ├──▶ T-004
                   └──▶ T-005

Phase 1: 需求与设计
  T-001 ──▶ T-006 ──▶ T-007
            │
            ├──▶ T-008 ──▶ T-009
            │             │
            │             └──▶ T-010
            │
            └──▶ T-011

Phase 2: 基础设施与后端
  T-008 ──▶ T-012
  T-009 ──▶ T-013
  T-012, T-013 ──▶ T-014 ──▶ T-015
                             │
                             ├──▶ T-016 ──▶ T-017 ──▶ T-018
                             │                        │
                             │                        ├──▶ T-019
                             │                        ├──▶ T-020
                             │                        ├──▶ T-021
                             │                        ├──▶ T-022
                             │                        └──▶ T-023
                             │
                             └──▶ T-024 ──▶ T-025

Phase 3: 前端开发
  T-002 ──▶ T-026
  T-014 ──▶ T-027
  T-016 ──▶ T-028
  T-017 ──▶ T-029
  T-017, T-018, T-019 ──▶ T-030
  T-021 ──▶ T-031
  T-022 ──▶ T-032
  T-020 ──▶ T-033
  T-027~T-033 ──▶ T-034

Phase 4: 质量保证
  T-034, T-025 ──▶ T-035 ──▶ T-036
                             │
                             ├──▶ T-037
                             ├──▶ T-038
                             ├──▶ T-039
                             └──▶ T-040

Phase 5: 部署与运维
  T-003 ──▶ T-041 ──▶ T-042
                     │
                     ├──▶ T-043
                     └──▶ T-044 (wait T-038)
                              │
                              └──▶ T-045 ──▶ T-046 ──▶ T-047
                                                       │
                                                       └──▶ T-048

Phase 6: 上线后
  T-047 ──▶ T-049 ──▶ T-052
           │
           └──▶ T-050
           └──▶ T-051
```

---

## 风险与缓解

| 风险 | 影响 | 概率 | 缓解措施 |
|------|------|------|----------|
| OpenAI API 不稳定导致排期功能不可用 | 高 | 中 | 已实现降级方案（规则排期），不影响核心功能 |
| 后端开发进度延迟 | 中 | 低 | 前后端并行开发，Mock API 先行 |
| 性能测试不达标 | 中 | 中 | 提前进行性能基准测试，预留优化时间 |
| 人员变动 | 高 | 低 | 知识文档化，代码审查覆盖 |

---

**版本: 1.0.0 | 作者: Project Manager | 日期: 2026-07-11**