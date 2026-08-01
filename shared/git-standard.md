# Git Standard - 统一 Git 规范

## 概述

本文档定义了所有 AI Agent 在使用 Git 进行版本控制时必须遵守的统一规范。涵盖分支管理、提交信息、代码合并、标签管理等。

## 适用范围

- 本规范适用于所有使用 Git 进行版本控制的项目
- 所有 Agent 在进行代码提交时必须遵守本规范

## 语言规范

本规范文档及所有相关文档、代码注释、提交信息均使用简体中文编写。Commit Message 中的 type 和 scope 使用英文，描述性内容使用中文。技术术语（如 Git、Commit、Branch、Merge、Rebase、PR、CI/CD 等）保留英文原文。

## 分支策略

### 分支模型

采用 Trunk-Based Development 结合 Feature Branch 的混合策略：

```
main (production)
  │
  ├── develop (integration)
  │     │
  │     ├── feature/xxx (feature branches)
  │     ├── fix/xxx (bug fix branches)
  │     ├── hotfix/xxx (hotfix branches)
  │     └── refactor/xxx (refactor branches)
  │
  └── release/x.y.z (release branches)
```

### 分支定义

| 分支 | 用途 | 来源 | 目标 | 命名 |
|------|------|------|------|------|
| main | 生产环境代码 | - | - | `main` |
| develop | 开发集成 | main | main | `develop` |
| feature | 功能开发 | develop | develop | `feature/{description}` |
| fix | Bug 修复 | develop | develop | `fix/{description}` |
| hotfix | 紧急修复 | main | main + develop | `hotfix/{description}` |
| release | 发布准备 | develop | main | `release/{version}` |
| refactor | 代码重构 | develop | develop | `refactor/{description}` |
| chore | 杂项任务 | develop | develop | `chore/{description}` |
| docs | 文档更新 | develop | develop | `docs/{description}` |
| test | 测试相关 | develop | develop | `test/{description}` |

### 分支命名规范

```
{type}/{description}

type: feature, fix, hotfix, release, refactor, chore, docs, test
description: 简短描述，使用 kebab-case
```

示例：
```
feature/user-authentication
feature/order-management
fix/login-error-handling
fix/pagination-bug
hotfix/security-patch-20260711
release/1.2.0
refactor/database-connection-pool
chore/update-dependencies
docs/api-documentation
test/add-integration-tests
```

### 分支生命周期

```
feature/xxx:
  创建: git checkout -b feature/xxx develop
  合并: git checkout develop && git merge --no-ff feature/xxx
  删除: git branch -d feature/xxx

hotfix/xxx:
  创建: git checkout -b hotfix/xxx main
  合并到 main: git checkout main && git merge --no-ff hotfix/xxx
  合并到 develop: git checkout develop && git merge --no-ff hotfix/xxx
  删除: git branch -d hotfix/xxx

release/x.y.z:
  创建: git checkout -b release/x.y.z develop
  合并到 main: git checkout main && git merge --no-ff release/x.y.z
  合并到 develop: git checkout develop && git merge --no-ff release/x.y.z
  删除: git branch -d release/x.y.z
```

## Commit 规范

### Commit Message 格式

采用 Conventional Commits 规范：

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Type 类型

| Type | 说明 | 示例 |
|------|------|------|
| feat | 新功能 | `feat(user): add user login` |
| fix | Bug 修复 | `fix(order): fix order total calculation` |
| docs | 文档更新 | `docs(api): update API documentation` |
| style | 代码格式（不影响逻辑） | `style(global): format code with prettier` |
| refactor | 代码重构 | `refactor(user): extract validation logic` |
| perf | 性能优化 | `perf(query): optimize user search query` |
| test | 测试相关 | `test(user): add user service unit tests` |
| chore | 构建/工具变更 | `chore(deps): update dependencies` |
| ci | CI/CD 变更 | `ci(github): add lint step to workflow` |
| revert | 回滚 | `revert: revert commit abc123` |

### Scope 范围

Scope 应使用模块名或功能名：

| Scope | 说明 |
|-------|------|
| user | 用户模块 |
| order | 订单模块 |
| product | 产品模块 |
| auth | 认证模块 |
| payment | 支付模块 |
| api | API 层 |
| db | 数据库 |
| ui | 用户界面 |
| config | 配置 |
| deps | 依赖 |
| global | 全局变更 |

### Subject 规则

- 使用祈使语气（"add" 而非 "added" 或 "adds"）
- 首字母小写
- 不以句号结尾
- 不超过 50 字符
- 简洁明了地描述变更内容

### Body 规则

- 解释变更的原因（Why）和内容（What）
- 每行不超过 72 字符
- 与 subject 之间空一行
- 可选，如果 subject 已经足够清晰

### Footer 规则

- 引用 Issue 编号
- 标注 Breaking Changes
- 与 body 之间空一行

### Commit 示例

```
feat(user): add email verification flow

Implement email verification during user registration:
- Send verification email with unique token
- Add verification endpoint
- Update user status after verification
- Add token expiration (24 hours)

Closes #123
```

```
fix(order): prevent duplicate order creation

Add idempotency key check to prevent creating duplicate orders
when the same request is sent multiple times.

The issue was caused by network retries creating multiple orders
for the same user action.

Fixes #456
```

```
feat(api)!: change user endpoint response format

BREAKING CHANGE: The user endpoint now returns a different response
format. The `data` field is now wrapped in a `user` object.

Before:
{ "data": { "id": "123", "name": "John" } }

After:
{ "user": { "id": "123", "name": "John" } }

Migration guide: Update all API consumers to use the new response format.
```

### Commit 频率

- 每个 Commit 应包含一个逻辑变更
- 避免巨型 Commit（超过 500 行变更）
- 每天至少 Commit 一次
- 工作结束前必须 Commit 并 Push

## 合并策略

### 合并方式

| 方式 | 使用场景 | 命令 |
|------|----------|------|
| --no-ff | feature → develop | `git merge --no-ff feature/xxx` |
| --squash | 多个小 commit → main | `git merge --squash feature/xxx` |
| --rebase | 同步 develop 到 feature | `git rebase develop` |

### 合并规则

- feature → develop: 使用 `--no-ff`，保留分支历史
- develop → main: 使用 `--no-ff`，创建 Merge Commit
- hotfix → main: 使用 `--no-ff`
- 同步上游: 使用 `rebase`，保持历史整洁
- 禁止直接推送到 main 和 develop
- 合并前必须通过 CI 检查
- 合并前必须通过 Code Review

### 冲突解决

```
1. 在当前分支执行 git rebase develop
2. 解决冲突
3. git add .
4. git rebase --continue
5. 重新运行测试
6. 推送更新
```

## 标签管理

### 版本标签

采用 Semantic Versioning：

```
v{major}.{minor}.{patch}

v1.0.0  - 初始版本
v1.1.0  - 新增功能
v1.1.1  - Bug 修复
v2.0.0  - 不兼容的变更
```

### 标签创建

```bash
# 创建标签
git tag -a v1.0.0 -m "Release v1.0.0: Initial release"

# 推送标签
git push origin v1.0.0

# 推送所有标签
git push origin --tags
```

### 标签命名

| 类型 | 格式 | 示例 |
|------|------|------|
| 正式版本 | `v{major}.{minor}.{patch}` | `v1.0.0` |
| 预发布版本 | `v{major}.{minor}.{patch}-{stage}.{num}` | `v1.0.0-alpha.1` |
| 发布候选 | `v{major}.{minor}.{patch}-rc.{num}` | `v1.0.0-rc.1` |

## 工作流

### 功能开发流程

```
1. 从 develop 创建 feature 分支
   git checkout -b feature/user-auth develop

2. 在 feature 分支上开发
   git add .
   git commit -m "feat(user): add login endpoint"

3. 定期同步 develop
   git fetch origin
   git rebase origin/develop

4. 推送 feature 分支
   git push origin feature/user-auth

5. 创建 Pull Request / Merge Request
    目标分支: develop

6. Code Review 通过后合并
   git checkout develop
   git merge --no-ff feature/user-auth

7. 删除 feature 分支
   git branch -d feature/user-auth
   git push origin --delete feature/user-auth
```

### 紧急修复流程

```
1. 从 main 创建 hotfix 分支
   git checkout -b hotfix/security-fix main

2. 修复问题
   git add .
   git commit -m "fix(security): patch XSS vulnerability"

3. 合并到 main
   git checkout main
   git merge --no-ff hotfix/security-fix
   git tag -a v1.0.1 -m "Hotfix: security patch"
   git push origin main --tags

4. 合并到 develop
   git checkout develop
   git merge --no-ff hotfix/security-fix
   git push origin develop

5. 删除 hotfix 分支
   git branch -d hotfix/security-fix
```

### 发布流程

```
1. 从 develop 创建 release 分支
   git checkout -b release/1.1.0 develop

2. 在 release 分支上做最终调整
   - 更新版本号
   - 更新 CHANGELOG
   - 最终测试

3. 合并到 main
   git checkout main
   git merge --no-ff release/1.1.0
   git tag -a v1.1.0 -m "Release v1.1.0"
   git push origin main --tags

4. 合并回 develop
   git checkout develop
   git merge --no-ff release/1.1.0
   git push origin develop

5. 删除 release 分支
   git branch -d release/1.1.0
```

## .gitignore 规范

### 必须忽略的文件

```gitignore
# Dependencies
node_modules/
.pnp
.pnp.js

# Build outputs
dist/
build/
.next/
out/

# Environment files
.env
.env.local
.env.*.local

# IDE files
.vscode/
.idea/
*.swp
*.swo

# OS files
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Cache
.cache/
*.tsbuildinfo

# Test coverage
coverage/

# Temporary files
tmp/
temp/
*.tmp
```

### 不应忽略的文件

- `package-lock.json` / `yarn.lock` — 锁定依赖版本
- `.env.example` — 环境变量模板
- 构建配置 — Webpack、Vite、tsconfig 等

## Git Hooks

### 推荐 Hooks

| Hook | 用途 |
|------|------|
| pre-commit | Lint 检查、格式化检查 |
| commit-msg | Commit 信息格式检查 |
| pre-push | 运行测试 |
| post-merge | 安装依赖 |
| post-checkout | 安装依赖 |

### pre-commit 示例

```bash
#!/bin/bash
# .git/hooks/pre-commit

echo "Running pre-commit checks..."

# Run lint
npm run lint
if [ $? -ne 0 ]; then
  echo "Lint failed. Please fix the issues."
  exit 1
fi

# Run type check
npm run type-check
if [ $? -ne 0 ]; then
  echo "Type check failed. Please fix the issues."
  exit 1
fi

echo "All checks passed."
```

## 反模式

| 反模式 | 说明 | 正确做法 |
|--------|------|----------|
| 直接推送到 main | 绕过 Code Review | 通过 PR/MR 合并 |
| 巨型 Commit | 一次提交包含过多变更 | 拆分为多个小 Commit |
| 无意义的 Commit Message | "fix", "update", "wip" | 使用 Conventional Commits |
| 长期存在的分支 | 分支长期不合并 | 定期合并，小步快跑 |
| Force Push | 强制推送覆盖远程历史 | 禁止 force push 到共享分支 |
| 提交敏感信息 | 提交密码、密钥等 | 使用 .gitignore 和环境变量 |
| 提交生成文件 | 提交 dist、build 目录 | 加入 .gitignore |
| 提交依赖 | 提交 node_modules | 加入 .gitignore |

## 检查清单

### 提交前检查

- [ ] 代码通过 Lint 检查
- [ ] 代码通过类型检查
- [ ] 测试通过
- [ ] 无敏感信息
- [ ] Commit Message 符合规范
- [ ] 分支名称正确
- [ ] 分支已同步最新 develop

### 合并前检查

- [ ] PR/MR 已创建
- [ ] CI 检查通过
- [ ] Code Review 通过
- [ ] 无合并冲突
- [ ] 分支已同步最新目标分支

---

**本规范是所有 AI Agent 使用 Git 的基础标准。每个 Agent 在进行代码提交时必须严格遵守本规范。**