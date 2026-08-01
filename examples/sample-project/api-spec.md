# API Specification: TaskFlow

<!--
Document: API Specification
Version: 1.0.0
Author: Backend Engineer
Created: 2026-07-11
Updated: 2026-07-11
Status: Approved
-->

## 1. 概述

| 属性 | 值 |
|------|------|
| 服务名称 | TaskFlow API |
| 版本 | v1 |
| 基础 URL | `https://api.taskflow.com/v1` |
| 协议 | HTTPS (TLS 1.3) |
| 数据格式 | JSON |
| 字符编码 | UTF-8 |
| 认证方式 | Bearer JWT (RS256) |
| API 文档 | `https://api.taskflow.com/v1/docs` (Swagger UI) |

### 1.1 版本策略

API 版本通过 URL 路径前缀控制 (`/v1/`, `/v2/`)。主版本号变更表示不兼容的 API 修改。当前版本生命周期：

| 版本 | 状态 | 下线日期 |
|------|------|----------|
| v1 | Current | - |

---

## 2. 认证

### 2.1 认证流程

```
1. 用户注册 → POST /auth/register → 返回验证邮件
2. 验证邮箱 → GET /auth/verify-email?token=xxx → 账户激活
3. 用户登录 → POST /auth/login → 返回 access_token + refresh_token
4. 后续请求 → Authorization: Bearer {access_token}
5. Token 过期 → POST /auth/refresh → 返回新的 access_token
```

### 2.2 Token 规范

| 属性 | access_token | refresh_token |
|------|-------------|---------------|
| 有效期 | 15 分钟 | 7 天 |
| 存储位置 | 内存 (前端) | httpOnly Cookie |
| 算法 | RS256 | RS256 |
| 载荷 | user_id, role, iat, exp | user_id, token_id, iat, exp |

### 2.3 认证接口

#### POST /auth/register

注册新用户。

**请求体:**
```json
{
  "email": "john@example.com",
  "password": "SecurePass123!",
  "displayName": "John Doe"
}
```

**响应 (201 Created):**
```json
{
  "data": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "type": "user",
    "attributes": {
      "email": "john@example.com",
      "displayName": "John Doe",
      "status": "pending_verification"
    }
  },
  "meta": {
    "requestId": "req-abc-123"
  }
}
```

**错误响应:**
| 状态码 | 错误码 | 说明 |
|--------|--------|------|
| 409 | EMAIL_ALREADY_EXISTS | 邮箱已被注册 |
| 400 | VALIDATION_ERROR | 密码不符合强度要求 |

#### POST /auth/login

用户登录。

**请求体:**
```json
{
  "email": "john@example.com",
  "password": "SecurePass123!"
}
```

**响应 (200 OK):**
```json
{
  "data": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "type": "auth",
    "attributes": {
      "accessToken": "eyJhbGciOiJSUzI1NiIs...",
      "refreshToken": "eyJhbGciOiJSUzI1NiIs...",
      "expiresIn": 900,
      "user": {
        "id": "550e8400-e29b-41d4-a716-446655440000",
        "email": "john@example.com",
        "displayName": "John Doe",
        "role": "member",
        "avatarUrl": null
      }
    }
  },
  "meta": {
    "requestId": "req-abc-123"
  }
}
```

**错误响应:**
| 状态码 | 错误码 | 说明 |
|--------|--------|------|
| 401 | INVALID_CREDENTIALS | 邮箱或密码错误 |
| 423 | ACCOUNT_LOCKED | 账户已锁定（登录失败 5 次），30 分钟后重试 |

---

## 3. 通用规范

### 3.1 通用响应格式

**成功响应:**
```json
{
  "data": {
    "id": "task-123",
    "type": "task",
    "attributes": {
      "title": "Task title",
      "status": "todo"
    }
  },
  "meta": {
    "requestId": "req-abc-123"
  }
}
```

**分页响应:**
```json
{
  "data": [
    {
      "id": "task-123",
      "type": "task",
      "attributes": {
        "title": "Task title"
      }
    }
  ],
  "meta": {
    "requestId": "req-abc-123",
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 156,
      "totalPages": 8
    }
  }
}
```

**错误响应:**
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation failed",
    "details": [
      { "field": "title", "message": "Title is required" },
      { "field": "priority", "message": "Priority must be one of: urgent, high, medium, low" }
    ]
  },
  "meta": {
    "requestId": "req-abc-123"
  }
}
```

### 3.2 通用错误码

| HTTP 状态码 | 错误码 | 说明 |
|-------------|--------|------|
| 400 | VALIDATION_ERROR | 请求参数校验失败 |
| 401 | UNAUTHORIZED | 未认证或 Token 无效 |
| 401 | TOKEN_EXPIRED | Token 已过期 |
| 403 | FORBIDDEN | 无权限访问该资源 |
| 404 | NOT_FOUND | 资源不存在 |
| 409 | CONFLICT | 资源冲突（如重复创建） |
| 413 | FILE_TOO_LARGE | 上传文件超过大小限制 |
| 429 | RATE_LIMIT_EXCEEDED | 请求频率超过限制 |
| 500 | INTERNAL_ERROR | 服务器内部错误 |
| 503 | SERVICE_UNAVAILABLE | 服务暂时不可用 |

### 3.3 分页、排序、筛选

**通用查询参数:**

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `page` | integer | 1 | 页码，从 1 开始 |
| `limit` | integer | 20 | 每页数量，最大 100 |
| `sort` | string | `-created_at` | 排序字段，`-` 前缀表示降序 |
| `search` | string | - | 全文搜索关键词 |
| `status` | string | - | 按状态筛选 |
| `priority` | string | - | 按优先级筛选 |
| `assigneeId` | string | - | 按指派人筛选 |

**示例:**
```
GET /projects/abc/tasks?page=1&limit=20&sort=-priority&status=todo&assigneeId=xyz
```

---

## 4. 任务管理 API

### 4.1 创建任务

```
POST /projects/{projectId}/tasks
```

**认证:** 需要 (项目成员)

**请求体:**
```json
{
  "title": "Implement user login page",
  "description": "Create a responsive login page with email/password fields, form validation, and error handling. Refer to Figma design v2.3.",
  "priority": "high",
  "assigneeId": "550e8400-e29b-41d4-a716-446655440001",
  "dueDate": "2026-07-25T00:00:00Z",
  "estimatedHours": 8
}
```

**请求体字段说明:**

| 字段 | 类型 | 必填 | 约束 | 说明 |
|------|------|------|------|------|
| title | string | 是 | 1-500 字符 | 任务标题 |
| description | string | 否 | 最大 10000 字符 | 任务描述，支持 Markdown |
| priority | enum | 否 | urgent/high/medium/low | 优先级，默认 medium |
| assigneeId | UUID | 否 | 必须是项目成员 | 指派人 ID |
| dueDate | ISO 8601 | 否 | 必须晚于当前时间 | 截止日期 |
| estimatedHours | number | 否 | 0.5-160 | 预估工时（小时） |
| parentTaskId | UUID | 否 | 必须是同项目任务 | 父任务 ID（子任务） |

**响应 (201 Created):**
```json
{
  "code": 0,
  "message": "Task created",
  "data": {
    "id": "660e8400-e29b-41d4-a716-446655440010",
    "projectId": "770e8400-e29b-41d4-a716-446655440020",
    "title": "Implement user login page",
    "description": "Create a responsive login page with email/password fields...",
    "status": "todo",
    "priority": "high",
    "assigneeId": "550e8400-e29b-41d4-a716-446655440001",
    "creatorId": "550e8400-e29b-41d4-a716-446655440000",
    "dueDate": "2026-07-25T00:00:00Z",
    "estimatedHours": 8,
    "createdAt": "2026-07-11T10:30:00Z",
    "updatedAt": "2026-07-11T10:30:00Z"
  }
}
```

**错误码:**
| 状态码 | 错误码 | 说明 |
|--------|--------|------|
| 400 | VALIDATION_ERROR | 字段校验失败 |
| 403 | NOT_PROJECT_MEMBER | 当前用户不是项目成员 |
| 404 | PROJECT_NOT_FOUND | 项目不存在 |

---

### 4.2 获取任务列表

```
GET /projects/{projectId}/tasks
```

**认证:** 需要 (项目成员)

**查询参数:**

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `page` | integer | 1 | 页码 |
| `limit` | integer | 20 | 每页数量，最大 100 |
| `sort` | string | `-created_at` | 排序字段: `created_at`, `priority`, `due_date`, `status` |
| `status` | string | - | 按状态筛选: `todo`, `in_progress`, `review`, `done` |
| `priority` | string | - | 按优先级筛选: `urgent`, `high`, `medium`, `low` |
| `assigneeId` | string | - | 按指派人 UUID 筛选 |
| `search` | string | - | 全文搜索任务标题和描述 |

**示例请求:**
```
GET /projects/770e8400.../tasks?page=1&limit=20&status=todo&priority=high&sort=-due_date
```

**响应 (200 OK):**
```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "items": [
      {
        "id": "660e8400-e29b-41d4-a716-446655440010",
        "title": "Implement user login page",
        "status": "todo",
        "priority": "high",
        "assignee": {
          "id": "550e8400-e29b-41d4-a716-446655440001",
          "displayName": "Jane Smith",
          "avatarUrl": "https://cdn.taskflow.com/avatars/jane.jpg"
        },
        "dueDate": "2026-07-25T00:00:00Z",
        "commentCount": 3,
        "createdAt": "2026-07-11T10:30:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 45,
      "totalPages": 3
    }
  }
}
```

---

### 4.3 获取任务详情

```
GET /tasks/{taskId}
```

**认证:** 需要 (项目成员)

**路径参数:**

| 参数 | 类型 | 说明 |
|------|------|------|
| taskId | UUID | 任务 ID |

**响应 (200 OK):**
```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "id": "660e8400-e29b-41d4-a716-446655440010",
    "projectId": "770e8400-e29b-41d4-a716-446655440020",
    "title": "Implement user login page",
    "description": "Create a responsive login page with email/password fields...",
    "status": "in_progress",
    "priority": "high",
    "assignee": {
      "id": "550e8400-e29b-41d4-a716-446655440001",
      "displayName": "Jane Smith",
      "avatarUrl": "https://cdn.taskflow.com/avatars/jane.jpg"
    },
    "creator": {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "displayName": "John Doe",
      "avatarUrl": null
    },
    "parentTaskId": null,
    "dueDate": "2026-07-25T00:00:00Z",
    "estimatedHours": 8,
    "actualHours": 3.5,
    "attachments": [
      {
        "id": "880e8400-e29b-41d4-a716-446655440030",
        "fileName": "login-design.png",
        "fileSize": 245760,
        "url": "https://s3.amazonaws.com/taskflow/attachments/880e...png",
        "uploadedAt": "2026-07-11T11:00:00Z"
      }
    ],
    "comments": [
      {
        "id": "990e8400-e29b-41d4-a716-446655440040",
        "content": "I've started working on this. The Figma link is updated.",
        "author": {
          "id": "550e8400-e29b-41d4-a716-446655440001",
          "displayName": "Jane Smith"
        },
        "createdAt": "2026-07-11T11:30:00Z"
      }
    ],
    "createdAt": "2026-07-11T10:30:00Z",
    "updatedAt": "2026-07-11T11:30:00Z"
  }
}
```

**错误码:**
| 状态码 | 错误码 | 说明 |
|--------|--------|------|
| 404 | TASK_NOT_FOUND | 任务不存在 |
| 403 | NOT_PROJECT_MEMBER | 无权查看该任务 |

---

### 4.4 更新任务

```
PATCH /tasks/{taskId}
```

**认证:** 需要 (项目成员)

**请求体 (部分更新):**
```json
{
  "status": "in_progress",
  "priority": "urgent",
  "assigneeId": "550e8400-e29b-41d4-a716-446655440002",
  "dueDate": "2026-07-28T00:00:00Z",
  "actualHours": 3.5
}
```

**状态流转规则:**

| 当前状态 | 允许的目标状态 |
|----------|---------------|
| `todo` | `in_progress`, `done` (跳过 review) |
| `in_progress` | `review`, `todo` (退回) |
| `review` | `done`, `in_progress` (退回修改) |
| `done` | 不可变更（需有 admin 权限才能重新打开） |

**响应 (200 OK):**
```json
{
  "code": 0,
  "message": "Task updated",
  "data": {
    "id": "660e8400-e29b-41d4-a716-446655440010",
    "status": "in_progress",
    "priority": "urgent",
    "assigneeId": "550e8400-e29b-41d4-a716-446655440002",
    "dueDate": "2026-07-28T00:00:00Z",
    "actualHours": 3.5,
    "updatedAt": "2026-07-11T14:00:00Z"
  }
}
```

**错误码:**
| 状态码 | 错误码 | 说明 |
|--------|--------|------|
| 400 | INVALID_STATUS_TRANSITION | 不合法的状态流转 |
| 400 | VALIDATION_ERROR | 字段校验失败 |
| 404 | TASK_NOT_FOUND | 任务不存在 |

---

### 4.5 删除任务

```
DELETE /tasks/{taskId}
```

**认证:** 需要 (项目管理员或任务创建者)

**响应 (204 No Content):**
```
(空响应体)
```

**错误码:**
| 状态码 | 错误码 | 说明 |
|--------|--------|------|
| 403 | FORBIDDEN | 非管理员且非创建者 |
| 404 | TASK_NOT_FOUND | 任务不存在 |

> 删除为软删除（设置 deleted_at），数据保留 30 天后自动清理。

---

### 4.6 分配任务

```
PATCH /tasks/{taskId}/assign
```

**认证:** 需要 (项目管理员)

**请求体:**
```json
{
  "assigneeId": "550e8400-e29b-41d4-a716-446655440003"
}
```

**响应 (200 OK):**
```json
{
  "code": 0,
  "message": "Task assigned",
  "data": {
    "taskId": "660e8400-e29b-41d4-a716-446655440010",
    "assigneeId": "550e8400-e29b-41d4-a716-446655440003",
    "assigneeName": "Alice Wang",
    "updatedAt": "2026-07-11T15:00:00Z"
  }
}
```

**错误码:**
| 状态码 | 错误码 | 说明 |
|--------|--------|------|
| 400 | USER_NOT_PROJECT_MEMBER | 被指派人不是项目成员 |
| 404 | TASK_NOT_FOUND | 任务不存在 |
| 403 | FORBIDDEN | 非项目管理员 |

---

### 4.7 添加评论

```
POST /tasks/{taskId}/comments
```

**认证:** 需要 (项目成员)

**请求体:**
```json
{
  "content": "The login page is ready for review. @John please check the responsive design."
}
```

**响应 (201 Created):**
```json
{
  "code": 0,
  "message": "Comment added",
  "data": {
    "id": "990e8400-e29b-41d4-a716-446655440041",
    "taskId": "660e8400-e29b-41d4-a716-446655440010",
    "content": "The login page is ready for review. @John please check the responsive design.",
    "author": {
      "id": "550e8400-e29b-41d4-a716-446655440001",
      "displayName": "Jane Smith"
    },
    "mentions": ["550e8400-e29b-41d4-a716-446655440000"],
    "createdAt": "2026-07-11T16:00:00Z"
  }
}
```

**业务规则:**
- 评论内容最大 5000 字符
- 支持 `@username` 提及（自动解析为 user ID）
- 被提及的用户会收到通知

---

### 4.8 获取项目统计

```
GET /projects/{projectId}/statistics
```

**认证:** 需要 (项目成员)

**查询参数:**

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `period` | string | `week` | 统计周期: `week` / `month` / `quarter` |

**响应 (200 OK):**
```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "totalTasks": 128,
    "completedTasks": 85,
    "completionRate": 66.4,
    "statusDistribution": {
      "todo": 20,
      "in_progress": 15,
      "review": 8,
      "done": 85
    },
    "priorityDistribution": {
      "urgent": 5,
      "high": 18,
      "medium": 60,
      "low": 45
    },
    "memberWorkload": [
      {
        "userId": "550e8400-e29b-41d4-a716-446655440001",
        "displayName": "Jane Smith",
        "assignedTasks": 12,
        "completedTasks": 8,
        "completionRate": 66.7,
        "avgCompletionDays": 3.2
      },
      {
        "userId": "550e8400-e29b-41d4-a716-446655440002",
        "displayName": "Bob Lee",
        "assignedTasks": 15,
        "completedTasks": 13,
        "completionRate": 86.7,
        "avgCompletionDays": 2.1
      }
    ],
    "burndownData": [
      { "date": "2026-07-05", "remaining": 50, "ideal": 50 },
      { "date": "2026-07-06", "remaining": 48, "ideal": 45 },
      { "date": "2026-07-07", "remaining": 43, "ideal": 40 },
      { "date": "2026-07-08", "remaining": 40, "ideal": 35 },
      { "date": "2026-07-09", "remaining": 35, "ideal": 30 },
      { "date": "2026-07-10", "remaining": 30, "ideal": 25 },
      { "date": "2026-07-11", "remaining": 28, "ideal": 20 }
    ]
  }
}
```

---

### 4.9 AI 智能排期建议

```
POST /projects/{projectId}/scheduling/suggest
```

**认证:** 需要 (项目管理员或 manager)

**请求体:**
```json
{
  "preferences": {
    "workingDays": ["monday", "tuesday", "wednesday", "thursday", "friday"],
    "dailyMaxHours": 8,
    "targetEndDate": "2026-08-15T00:00:00Z"
  }
}
```

**响应 (200 OK):**
```json
{
  "code": 0,
  "message": "Scheduling suggestion generated",
  "data": {
    "planId": "aa0e8400-e29b-41d4-a716-446655440050",
    "planName": "Sprint 3 - Auto Schedule",
    "summary": "共分配 25 个任务，预计 2 周完成。张三负载最高达 85%，建议将 2 个任务分配给李四。",
    "risks": [
      {
        "level": "high",
        "taskId": "660e8400-e29b-41d4-a716-446655440015",
        "title": "Database migration",
        "reason": "预估工时 16h，但距离截止日期仅剩 3 个工作日"
      },
      {
        "level": "medium",
        "taskId": "660e8400-e29b-41d4-a716-446655440018",
        "title": "API documentation",
        "reason": "依赖任务 'API design review' 尚未完成"
      }
    ],
    "assignments": [
      {
        "taskId": "660e8400-e29b-41d4-a716-446655440010",
        "title": "Implement user login page",
        "assigneeId": "550e8400-e29b-41d4-a716-446655440001",
        "assigneeName": "Jane Smith",
        "suggestedStartDate": "2026-07-14",
        "suggestedDueDate": "2026-07-18",
        "priority": "high"
      }
    ],
    "memberWorkload": [
      {
        "userId": "550e8400-e29b-41d4-a716-446655440001",
        "displayName": "Jane Smith",
        "totalHours": 34,
        "utilization": 85
      }
    ],
    "generatedAt": "2026-07-11T18:00:00Z"
  }
}
```

**降级行为:** 当 OpenAI API 不可用时，自动降级为基于规则的排期算法（按优先级排序 + 简单负载均衡），响应中 `generatedBy` 字段标记为 `rule-based`。

**错误码:**
| 状态码 | 错误码 | 说明 |
|--------|--------|------|
| 403 | FORBIDDEN | 非项目管理员 |
| 429 | RATE_LIMIT_EXCEEDED | AI 排期频率超限（每分钟最多 5 次） |
| 503 | AI_SERVICE_DEGRADED | AI 服务降级为规则排期 |

---

### 4.10 批量操作任务

```
POST /projects/{projectId}/tasks/batch
```

**认证:** 需要 (项目管理员)

**请求体:**
```json
{
  "operation": "update_status",
  "taskIds": [
    "660e8400-e29b-41d4-a716-446655440010",
    "660e8400-e29b-41d4-a716-446655440011",
    "660e8400-e29b-41d4-a716-446655440012"
  ],
  "payload": {
    "status": "done"
  }
}
```

**支持的操作类型:**

| 操作 | 说明 | payload 字段 |
|------|------|-------------|
| `update_status` | 批量更新状态 | `status` |
| `update_priority` | 批量更新优先级 | `priority` |
| `assign` | 批量分配 | `assigneeId` |
| `delete` | 批量删除（软删除） | 无 |

**响应 (200 OK):**
```json
{
  "code": 0,
  "message": "Batch operation completed",
  "data": {
    "operation": "update_status",
    "totalTasks": 3,
    "successCount": 3,
    "failedCount": 0,
    "failures": []
  }
}
```

**部分失败响应:**
```json
{
  "code": 0,
  "message": "Batch operation completed with errors",
  "data": {
    "operation": "update_status",
    "totalTasks": 3,
    "successCount": 2,
    "failedCount": 1,
    "failures": [
      {
        "taskId": "660e8400-e29b-41d4-a716-446655440012",
        "reason": "INVALID_STATUS_TRANSITION",
        "message": "Task is already in 'done' status"
      }
    ]
  }
}
```

---

## 5. 速率限制

| 端点类型 | 限制 | 时间窗口 | 说明 |
|----------|------|----------|------|
| 认证接口 | 10 次 | 1 分钟 | `/auth/login`, `/auth/register` |
| AI 接口 | 5 次 | 1 分钟 | `/scheduling/suggest`, `/tasks/summary` |
| 通用 CRUD | 100 次 | 1 分钟 | 其他所有接口 |
| 文件上传 | 20 次 | 1 分钟 | 附件上传 |

限流响应头:
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 87
X-RateLimit-Reset: 1623456789
```

---

## 6. Webhook 事件

TaskFlow 支持 Webhook 推送以下事件到用户配置的 URL：

| 事件 | 触发时机 | 载荷 |
|------|----------|------|
| `task.created` | 新任务创建 | Task 对象 |
| `task.updated` | 任务状态/优先级/指派人变更 | Task 对象 + 变更字段 |
| `task.deleted` | 任务删除 | taskId |
| `task.comment_added` | 新评论添加 | Comment 对象 |
| `project.member_added` | 新成员加入项目 | userId, projectId |
| `scheduling.plan_generated` | 排期方案生成 | Plan 对象 |

---

**版本: 1.0.0 | 作者: Backend Engineer | 日期: 2026-07-11**