# Sample Project: TaskFlow - 企业级团队任务管理平台

<!--
Document: README
Version: 1.0.0
Author: Project Team
Created: 2026-07-11
Updated: 2026-07-11
-->

## 项目概述

TaskFlow 是一个面向中小型团队（5-50 人）的企业级任务管理平台，旨在帮助团队高效地创建、分配、追踪和完成任务，替代传统的电子表格或简单 Todo 工具方案。

### 核心解决的问题

| 问题 | 现状 | TaskFlow 解决方案 |
|------|------|-------------------|
| 任务分配混乱 | 口头分配、群聊记录，容易遗漏 | 结构化任务创建与指派，责任人清晰 |
| 进度不透明 | 不知道团队成员在做什么 | 看板视图、实时状态更新 |
| 排期困难 | 手动估算，经常延期 | AI 智能排期建议，自动识别瓶颈 |
| 信息分散 | 任务、评论、文件分散在各处 | 一站式任务协作，评论/附件集中管理 |
| 缺乏数据洞察 | 无法量化团队效率 | 数据看板、个人效率分析 |

### 核心功能

- **用户认证**：邮箱注册/登录，JWT 认证，RBAC 权限控制（admin / manager / member）
- **项目管理**：创建、编辑、归档项目，设置可见性（public / private）
- **任务管理**：创建、分配、状态流转（TODO → IN_PROGRESS → REVIEW → DONE），优先级（urgent / high / medium / low）
- **团队协作**：任务评论、附件上传、实时通知（站内通知 + 邮件通知）
- **AI 智能助手**：任务摘要自动生成、智能排期建议、工作量分析
- **数据看板**：项目统计、个人效率分析、团队燃尽图

---

## 技术栈

### 前端

| 技术 | 版本 | 用途 |
|------|------|------|
| React | 18.x | UI 框架 |
| TypeScript | 5.x | 类型安全 |
| Zustand | 4.x | 状态管理 |
| React Router | 6.x | 客户端路由 |
| TanStack Query | 5.x | 服务端状态管理 |
| Tailwind CSS | 3.x | 原子化 CSS 框架 |
| Radix UI | 1.x | 无障碍 UI 原语 |
| React Hook Form | 7.x | 表单管理 |
| Zod | 3.x | 运行时校验 |
| Vitest | 1.x | 单元测试 |
| Playwright | 1.x | E2E 测试 |

### 后端

| 技术 | 版本 | 用途 |
|------|------|------|
| Node.js | 20.x LTS | 运行时 |
| NestJS | 10.x | 企业级后端框架 |
| TypeScript | 5.x | 类型安全 |
| TypeORM | 0.3.x | ORM 框架 |
| PostgreSQL | 16 | 主数据库 |
| Redis | 7 | 缓存 + 会话 + 消息队列 |
| BullMQ | 5.x | 任务队列（基于 Redis） |
| OpenAI API | GPT-4o | AI 智能排期 |
| SendGrid | - | 邮件发送 |
| AWS S3 | - | 文件存储 |
| Passport.js | 0.7.x | 认证中间件 |
| Swagger | 7.x | API 文档 |

### DevOps

| 技术 | 版本 | 用途 |
|------|------|------|
| Docker | 26.x | 容器化 |
| Kubernetes | 1.29 | 容器编排 |
| GitHub Actions | - | CI/CD |
| Prometheus | 2.x | 监控 |
| Grafana | 10.x | 可视化看板 |
| ArgoCD | 2.x | GitOps 部署 |
| Sentry | - | 错误监控 |

---

## 快速开始

### 环境要求

| 工具 | 最低版本 | 说明 |
|------|----------|------|
| Node.js | 20.11.0 LTS | 运行时环境 |
| pnpm | 9.x | 包管理器（推荐） |
| Docker | 26.x | 本地开发数据库和缓存 |
| Docker Compose | 2.x | 编排本地服务 |
| Git | 2.40+ | 版本控制 |

### 安装步骤

```bash
# 1. 克隆仓库
git clone https://github.com/your-org/taskflow.git
cd taskflow

# 2. 安装依赖
pnpm install

# 3. 启动本地基础设施（PostgreSQL + Redis）
docker compose -f docker-compose.dev.yml up -d

# 4. 复制环境变量配置
cp .env.example .env
# 编辑 .env 文件，填入必要的配置（数据库连接、OpenAI API Key 等）

# 5. 运行数据库迁移
pnpm run migration:run

# 6. 启动开发服务器
pnpm run dev
# 前端: http://localhost:3000
# 后端: http://localhost:4000
# API 文档: http://localhost:4000/api/docs
```

### 运行测试

```bash
# 单元测试
pnpm run test

# 集成测试
pnpm run test:e2e

# 覆盖率报告
pnpm run test:cov
```

### 生产构建

```bash
# 构建前端
pnpm run build:web

# 构建后端
pnpm run build:api

# 构建 Docker 镜像
docker build -t taskflow-api:latest -f Dockerfile.api .
docker build -t taskflow-web:latest -f Dockerfile.web .
```

---

## 项目结构

```
taskflow/
├── .github/
│   └── workflows/              # GitHub Actions CI/CD 流水线
│       ├── ci.yml              # 持续集成
│       ├── deploy-staging.yml  # 部署到预发布环境
│       └── deploy-prod.yml     # 部署到生产环境
├── apps/
│   ├── api/                    # NestJS 后端应用
│   │   ├── src/
│   │   │   ├── modules/
│   │   │   │   ├── auth/       # 认证模块
│   │   │   │   ├── users/      # 用户模块
│   │   │   │   ├── projects/   # 项目模块
│   │   │   │   ├── tasks/      # 任务模块
│   │   │   │   ├── comments/   # 评论模块
│   │   │   │   ├── attachments/# 附件模块
│   │   │   │   ├── notifications/ # 通知模块
│   │   │   │   ├── dashboard/  # 数据看板模块
│   │   │   │   └── ai/         # AI 助手模块
│   │   │   ├── common/         # 公共模块（拦截器、守卫、过滤器）
│   │   │   ├── config/         # 配置模块
│   │   │   ├── database/       # 数据库迁移和种子数据
│   │   │   └── main.ts         # 入口文件
│   │   ├── test/               # 测试文件
│   │   └── Dockerfile
│   └── web/                    # React 前端应用
│       ├── src/
│       │   ├── components/     # 通用组件
│       │   ├── pages/          # 页面组件
│       │   ├── hooks/          # 自定义 Hooks
│       │   ├── stores/         # Zustand 状态
│       │   ├── services/       # API 服务层
│       │   ├── types/          # TypeScript 类型定义
│       │   └── utils/          # 工具函数
│       └── Dockerfile
├── packages/
│   └── shared/                 # 前后端共享类型和工具
│       ├── types/              # 共享 DTO 类型
│       └── constants/          # 共享常量
├── docs/                       # 项目文档
│   ├── prd.md                  # 产品需求文档
│   ├── architecture.md         # 架构设计文档
│   ├── api-spec.md             # API 规范文档
│   ├── database-schema.md      # 数据库 Schema 文档
│   ├── test-plan.md            # 测试计划文档
│   ├── deployment-plan.md      # 部署计划文档
│   ├── decision-log.md         # 决策日志文档
│   └── todo.md                 # 任务清单
├── docker-compose.dev.yml      # 本地开发环境
├── docker-compose.yml          # 生产环境
├── pnpm-workspace.yaml         # pnpm 工作区配置
├── turbo.json                  # Turborepo 配置
└── package.json                # 根包配置
```

---

## 开发规范

### 分支策略

本项目采用 **Trunk-Based Development** 分支策略：

| 分支 | 用途 | 保护规则 |
|------|------|----------|
| `main` | 生产就绪代码 | 需要 PR 审查 + CI 通过 |
| `develop` | 开发集成分支 | 需要 PR 审查 + CI 通过 |
| `feature/*` | 功能开发分支 | 从 `develop` 拉出，合并回 `develop` |
| `hotfix/*` | 紧急修复分支 | 从 `main` 拉出，合并回 `main` 和 `develop` |
| `release/*` | 发布准备分支 | 从 `develop` 拉出，合并回 `main` |

### Commit 规范

使用 **Conventional Commits** 格式：

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

| Type | 说明 |
|------|------|
| `feat` | 新功能 |
| `fix` | Bug 修复 |
| `docs` | 文档更新 |
| `style` | 代码格式（不影响功能） |
| `refactor` | 代码重构 |
| `perf` | 性能优化 |
| `test` | 测试相关 |
| `chore` | 构建/工具变更 |
| `ci` | CI/CD 变更 |

示例：
```
feat(tasks): add AI-powered scheduling suggestion

- Integrate OpenAI GPT-4o API for workload analysis
- Add task priority auto-adjustment based on deadlines
- Display scheduling suggestions on task detail page

Closes #234
```

### 代码审查清单

- [ ] 代码遵循项目 ESLint 和 Prettier 配置
- [ ] 新增功能有对应的单元测试
- [ ] API 变更已更新 Swagger 文档
- [ ] 数据库变更已创建迁移文件
- [ ] 敏感信息未硬编码（使用环境变量）
- [ ] 无 console.log 残留
- [ ] 性能无明显退化

---

## 贡献指南

### 开发流程

1. 从 `develop` 分支创建 `feature/xxx` 分支
2. 在本地开发并编写测试
3. 运行 `pnpm run lint && pnpm run test` 确保通过
4. 提交代码并推送
5. 创建 Pull Request 到 `develop` 分支
6. 等待 CI 通过和代码审查
7. 合并到 `develop`

### 本地开发环境变量

```env
# .env 文件示例
NODE_ENV=development

# 数据库
DATABASE_HOST=localhost
DATABASE_PORT=5432
DATABASE_USER=postgres
DATABASE_PASSWORD=postgres
DATABASE_NAME=taskflow

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379

# JWT
JWT_SECRET=your-secret-key-change-in-production
JWT_EXPIRATION=24h

# OpenAI
OPENAI_API_KEY=sk-xxxxx

# SendGrid
SENDGRID_API_KEY=SG.xxxxx

# AWS S3
AWS_ACCESS_KEY_ID=xxxxx
AWS_SECRET_ACCESS_KEY=xxxxx
AWS_S3_BUCKET=taskflow-dev
AWS_REGION=us-east-1
```

---

## 参考文档

| 文档 | 说明 |
|------|------|
| [PRD](./prd.md) | 产品需求文档 |
| [架构设计](./architecture.md) | 系统架构设计文档 |
| [API 规范](./api-spec.md) | REST API 接口规范 |
| [数据库 Schema](./database-schema.md) | 数据库表结构设计 |
| [测试计划](./test-plan.md) | 测试策略和用例 |
| [部署计划](./deployment-plan.md) | 部署架构和 CI/CD |
| [决策日志](./decision-log.md) | 技术决策记录 |
| [任务清单](./todo.md) | 项目任务跟踪 |

---

## 使用方式

本示例项目展示了如何在实际项目中使用本 Repository 中的 Skill 和模板：

1. **Product Manager** 参考 `prd.md` 编写 PRD
2. **UI/UX Designer** 参考 PRD 设计界面原型
3. **Solution Architect** 参考 `architecture.md` 设计系统架构
4. **Database Engineer** 参考 `database-schema.md` 设计数据库
5. **Backend Engineer** 参考 `api-spec.md` 开发 API
6. **QA Engineer** 参考 `test-plan.md` 编写测试用例
7. **DevOps Engineer** 参考 `deployment-plan.md` 配置 CI/CD
8. **所有角色** 参考 `decision-log.md` 记录技术决策
9. **Project Manager** 参考 `todo.md` 管理项目进度

---

**本示例项目是完整的参考实现，展示了如何将模板转换为实际可用的项目文档。**

**版本: 1.0.0 | 作者: Project Team | 日期: 2026-07-11**