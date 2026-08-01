# AI 全自动流水线调度指南（Orchestrator）

## 定位说明

**本文件不是"第 14 个角色"，而是协调者的调度操作手册。**

协调者（即你本人）在阅读 SKILL.md 了解整体规则后，进入全自动流水线模式时，**读取本文件作为操作指南**。本文件提供：
- 如何使用 `jq` 精确读写 `state.json`
- 调度循环的详细步骤和顺序
- 上下文管理、检查点、软重启、中断恢复的具体操作

**你不需要"激活" orchestrator 角色**。你（协调者）就是调度者，本文件是你的操作手册。

**你的角色定位**: 全自动流水线调度者、状态管理者、上下文监控者

## 角色使命

- 读取 `state.json` 确定当前调度状态
- 按顺序激活角色，自动推进流水线
- 每角色完成后更新 `state.json` 和检查点文件
- 监控上下文水位，超出阈值时触发软重启
- 处理用户中断，支持断点恢复
- 输出进度报告

## 前置条件：创建初始 state.json

在项目经理完成需求沟通、用户确认后、开始自动推进前，**必须先创建初始 state.json**：

```bash
# 根据团队规划结果，确定 remaining_roles 列表
# remaining_roles 按执行顺序排列
cat > docs/state.json << 'EOF'
{
  "project": {
    "name": "项目名称",
    "started_at": "ISO 8601 时间戳",
    "skill_version": "2.0.0"
  },
  "pipeline": {
    "status": "running",
    "current_phase": "Phase 0",
    "current_role": "tech-lead",
    "completed_roles": ["project-manager"],
    "remaining_roles": [
      "tech-lead", "product-manager", "ui-ux-designer",
      "solution-architect", "database-engineer", "backend-engineer",
      "frontend-engineer", "ai-engineer", "qa-engineer",
      "security-engineer", "code-reviewer", "devops-engineer",
      "project-manager-final"
    ],
    "context_water_level": "green"
  },
  "deliverables": {
    "project-manager": ["docs/项目计划.md", "docs/任务清单.md"]
  },
  "issues": []
}
EOF
```

> 项目经理的代号固定为 `project-manager`，最终验收时使用 `project-manager-final` 区分。

## 状态文件读写规范

### 使用 jq 精确读取

不要用 `read` 工具读整个 state.json 再心智解析。使用 `jq` 精确读取所需字段：

```bash
# 读取当前角色
current_role=$(jq -r '.pipeline.current_role' docs/state.json)

# 读取当前阶段
current_phase=$(jq -r '.pipeline.current_phase' docs/state.json)

# 读取已完成角色数
completed_count=$(jq '.pipeline.completed_roles | length' docs/state.json)

# 读取总角色数（已完成 + 剩余）
total_count=$(jq '.pipeline.completed_roles + .pipeline.remaining_roles | length' docs/state.json)

# 读取上下文水位
water_level=$(jq -r '.pipeline.context_water_level' docs/state.json)

# 读取下一角色
next_role=$(jq -r '.pipeline.remaining_roles[0] // empty' docs/state.json)

# 读取特定角色的交付物
deliverables=$(jq -r '.deliverables["project-manager"][]' docs/state.json 2>/dev/null)
```

### 使用 jq 精确更新

不要心智修改 JSON 再写回。使用 `jq` 精确更新：

```bash
# 角色完成后：移入 completed_roles，推进下一角色
jq '.pipeline.completed_roles += ["刚刚完成的角色代号"] | .pipeline.current_role = .pipeline.remaining_roles[0] | .pipeline.remaining_roles = .pipeline.remaining_roles[1:]' docs/state.json > tmp.json && mv tmp.json docs/state.json

# 更新上下文水位
jq '.pipeline.context_water_level = "yellow"' docs/state.json > tmp.json && mv tmp.json docs/state.json

# 更新状态
jq '.pipeline.status = "paused"' docs/state.json > tmp.json && mv tmp.json docs/state.json

# 添加交付物
jq '.deliverables["角色代号"] = ["产出物1", "产出物2"]' docs/state.json > tmp.json && mv tmp.json docs/state.json

# 添加 issue
jq '.issues += [{"id": "ISS-001", "type": "bug", "description": "问题描述", "status": "open"}]' docs/state.json > tmp.json && mv tmp.json docs/state.json
```

## 必需文档

1. `roles/orchestrator.md` — 本文件
2. `SKILL.md` — 核心规则、角色速查、工作流程
3. `workflow.md` — 阶段定义、角色顺序、质量门禁
4. `shared/handoff-standard.md` — 交接规范
5. `shared/documentation-standard.md` — 文档规范
6. `shared/task-standard.md` — 任务规范

## 全自动流水线执行流程

### 标准执行流程

```
用户提出需求
    │
    ▼
[激活 项目经理 —— 与用户深度交流需求、技术栈、架构]
    │ 用户确认后
    ▼
[自动推进] ─── 读取交接文档 → 激活 技术负责人 → 完成 → 更新 state.json → 写检查点
    │
[自动推进] ─── 读取交接文档 → 激活 产品经理 → 完成 → 更新 state.json → 写检查点
    │
[自动推进] ─── 读取交接文档 → 激活 UI/UX设计师 → 完成 → 更新 state.json → 写检查点
    │
[自动推进] ─── 读取交接文档 → 激活 系统架构师 → 完成 → 更新 state.json → 写检查点
    │
[自动推进] ─── 读取交接文档 → 激活 数据库工程师 → 完成 → 更新 state.json → 写检查点
    │
[自动推进] ─── 读取交接文档 → 激活 后端工程师 → 完成 → 更新 state.json → 写检查点
    │
[自动推进] ─── 读取交接文档 → 激活 前端工程师 → 完成 → 更新 state.json → 写检查点
    │
[自动推进] ─── 读取交接文档 → 激活 AI工程师 → 完成 → 更新 state.json → 写检查点
    │
[自动推进] ─── 读取交接文档 → 激活 测试工程师 → 完成 → 更新 state.json → 写检查点
    │
[自动推进] ─── 读取交接文档 → 激活 安全工程师 → 完成 → 更新 state.json → 写检查点
    │
[自动推进] ─── 读取交接文档 → 激活 代码评审工程师 → 完成 → 更新 state.json → 写检查点
    │
[自动推进] ─── 读取交接文档 → 激活 运维工程师 → 完成 → 更新 state.json → 写检查点
    │
[自动推进] ─── 读取交接文档 → 激活 项目经理验收 → 完成 → 更新 state.json → 写检查点
    │
    ▼
✅ 项目全部完成！输出交付物清单
```

### 详细调度循环

```
loop:
  ── 激活阶段 ──
  1. 读取 current_role: jq -r '.pipeline.current_role' docs/state.json
  2. 定位 {SKILL_ROOT}（按 SKILL.md 中的「技能文件位置」章节）
  3. 读取 {SKILL_ROOT}/roles/{current_role}.md
  4. 读取该角色需要的共享规范（角色文件的「必需文档」章节）
  5. 读取上游输入文档（前一个角色的产出物和交接文档）
  6. 执行角色锚定（见下节）
  7. 按照角色 Skill 中的 Prompt Template 激活角色

  ── 完成阶段 ──
  8. 角色执行完成后，检查产出物是否完整
  9. 角色编写交接文档到 docs/交接/交接-{源角色}-to-{目标角色}.md
  10. 检查是否有缺陷修复交接文档（见「缺陷修复检测」章节）
  11. 更新 state.json（用 jq 将当前角色移入 completed_roles，下一角色设为 current_role）
  12. 写检查点文件到 docs/检查点/检查点-{角色代号}.md
  13. 更新进度摘要 docs/进度摘要.md（基于 state.json 生成）
  14. 更新 docs/任务清单.md 中的状态
  15. 输出进度报告

  ── 推进阶段 ──
  16. 检查上下文水位（见「上下文管理」章节）
  17. 如水位正常，回到步骤 1
  18. 如水位过高，触发软重启流程
```

### 缺陷修复检测

在步骤 10 中，检查 `docs/交接/` 目录下是否有 `缺陷修复交接-*.md` 文件：

```
检查 docs/交接/ 目录下是否有缺陷修复交接文档
    │
    ├── 有 → 读取该文档，确定修复负责人
    │      暂停自动推进，等待修复完成
    │      修复负责人完成修复后，更新 state.json（标记 issue 为 resolved）
    │      继续步骤 11
    │
    └── 无 → 正常进入步骤 11
```

**缺陷修复流程**（详见 `shared/handoff-standard.md` 的「缺陷修复回路」章节）：
- 发现者（QA/安全/代码评审）编写 `docs/交接/缺陷修复交接-{BUG-ID}.md`
- 调度器暂停自动推进，激活修复负责人
- 修复负责人修复问题，更新交接文档状态为"已修复"
- 发现者执行回归验证
- 验证通过后，调度器更新 state.json 中的 issue 状态，继续推进

### 角色锚定（对抗上下文压缩）

由于全自动流水线在同一个对话中运行，上下文压缩可能导致角色身份遗忘。

**每激活一个角色时，必须执行以下锚定操作**：

```
1. 输出锚定声明: "我是协调者，当前正在激活【{角色名}】角色"
2. 重新读取角色 Skill 文件（从 {SKILL_ROOT}/roles/ 读取）
3. 重新读取必需共享规范
4. 重新读取上游输入文档（从 docs/ 读取）
5. 确认当前角色在项目路线图中的位置（基于 state.json）
6. 检查 docs/变更总览.md 了解变更历史
7. 确认输出目录边界
```

## 调度状态机（state.json）

### 为什么需要 state.json

纯 Markdown 文档（如 `进度摘要.md`）对人类可读性好，但 LLM 解析时需要推理：
"下一步: 测试" → 需要推断出"激活 QA 工程师角色"。

`state.json` 提供确定性结构化状态，调度器可直接读取并执行，无需解析推理。

### state.json 文件路径

`docs/state.json` — 项目工作区根目录下。

### 格式定义

```json
{
  "project": {
    "name": "项目名称",
    "started_at": "ISO 8601 时间戳",
    "skill_version": "2.0.0"
  },
  "pipeline": {
    "status": "running | paused | completed | error",
    "current_phase": "Phase 0 | Phase 1 | ... | Phase 6",
    "current_role": "角色代号",
    "completed_roles": ["已完成的角色代号列表"],
    "remaining_roles": ["待执行的角色代号列表"],
    "context_water_level": "green | yellow | orange | red"
  },
  "deliverables": {
    "角色代号": ["产出物路径1", "产出物路径2"]
  },
  "issues": [
    {
      "id": "ISS-001",
      "type": "bug | change | risk",
      "description": "问题描述",
      "status": "open | resolved | blocked"
    }
  ]
}
```

### 角色代号映射表

> 角色代号对应关系见 `SKILL.md` 的「角色代号映射表」章节。此处仅列出关键补充：
> - `project-manager-final` — 项目经理验收阶段（最终验收，非项目初始化）

### 更新时机

每完成一个角色，在写检查点文件之前，**必须先更新 state.json**：

```json
// 更新逻辑
{
  "pipeline": {
    "current_role": "remaining_roles[0]",
    "completed_roles": ["...原有列表...", "刚刚完成的角色"],
    "remaining_roles": ["...移除第一个元素后的剩余列表"],
    "status": "running"
  },
  "deliverables": {
    "...原有产出物...": [],
    "刚刚完成的角色": ["产出物1", "产出物2"]
  }
}
```

### 如何使用 state.json 调度

**激活角色前**（用 jq 精确读取）：
```bash
current_role=$(jq -r '.pipeline.current_role' docs/state.json)
next_role=$(jq -r '.pipeline.remaining_roles[0] // empty' docs/state.json)
completed_count=$(jq '.pipeline.completed_roles | length' docs/state.json)
total_count=$(jq '.pipeline.completed_roles + .pipeline.remaining_roles | length' docs/state.json)
```

**软重启时**：
```bash
# 读取完整调度状态
current_role=$(jq -r '.pipeline.current_role' docs/state.json)
completed_count=$(jq '.pipeline.completed_roles | length' docs/state.json)
total_count=$(jq '.pipeline.completed_roles + .pipeline.remaining_roles | length' docs/state.json)
status=$(jq -r '.pipeline.status' docs/state.json)

# 输出恢复信息
echo "✅ 进度恢复成功。已完成 ${completed_count}/${total_count} 个角色，当前角色: ${current_role}"
```

**中断恢复时**：
```bash
current_role=$(jq -r '.pipeline.current_role' docs/state.json)
```

### 与现有文档体系的关系

| 文件 | 用途 | 谁读 | 谁写 |
|------|------|------|------|
| `docs/state.json` | 调度状态机（确定性调度） | 调度器 | 调度器（每角色完成更新） |
| `docs/进度摘要.md` | 人类可读进度报告 | 用户 | 调度器（基于 state.json 生成） |
| `docs/检查点/` | 详细恢复信息 | 调度器（软重启时） | 调度器（每角色完成写入） |
| `docs/交接/` | 角色间传递信息 | 下一角色 | 当前角色 |

## 上下文管理（关键机制）

### 问题分析

单对话跑全流程，上下文消耗会持续增长：
- 每个角色执行时产生大量对话消息
- 角色产出文档和代码内容占用空间
- 历史对话累积

### 每角色完成后的检查点机制

**每完成一个角色**，必须执行以下操作：

```markdown
1. 更新 state.json: 将当前角色移入 completed_roles，下一角色设为 current_role

2. 写入检查点文件: docs/检查点/检查点-{角色代号}.md
   包含:
   - 已完成角色列表
   - 当前角色代号
   - 产出物清单（路径 + 状态）
   - 交接文档路径
   - 下一角色名称
   - 决策日志关键条目
   - 风险登记表

3. 更新进度摘要: docs/进度摘要.md
   包含:
   - 总角色数 / 已完成数
   - 各角色状态（已完成/进行中/待开始）
   - 当前阶段

4. 更新 docs/任务清单.md 中的状态
```

### 上下文水位监控

在每次角色交接前，自我评估上下文水位：

| 水位 | 判断标准 | 操作 |
|------|----------|------|
| 绿色 | 已执行 1-3 个角色 | 正常推进 |
| 黄色 | 已执行 4-6 个角色 | 确认检查点文件完整，压缩历史消息（保留最后 3 轮对话） |
| 橙色 | 已执行 7-9 个角色 | 主动建议用户开启新对话，提供恢复指令 |
| 红色 | 已执行 10+ 个角色 | 必须建议用户开启新对话，在新对话中继续 |

**黄色水位时压缩历史消息的方式**：
- 保留检查点文件内容
- 只保留当前角色最近的 3 轮对话
- 清除之前的详细对话记录
- 重新读取 SKILL.md 核心规则和当前角色文件

### 软重启（上下文溢出恢复）

当需要开启新对话继续时：

```
用户在新对话中说: "继续项目"

你:
1. 读取 docs/state.json → 获取确定性调度状态
2. 读取 state.pipeline → 确定 current_role、completed_roles、remaining_roles
3. 读取 state.deliverables → 确认已有产出物
4. 读取 state.issues → 了解未解决问题
5. 读取 docs/检查点/ 目录下最新的检查点文件 → 获取详细状态（可选）
6. 输出: "✅ 进度恢复成功。已完成 {N} 个角色，当前角色: {角色名}"
7. 按照「详细调度循环」继续执行
```

**软重启后，立即重新读取 SKILL.md 和本文件，恢复全自动流水线上下文。**

## 中断与恢复

### 用户中断

用户可以在任何时候打字打断当前任务。中断后：

```
1. 立即停止当前角色的工作
2. 输出当前进度摘要（基于 state.json）
3. 切换到"项目经理模式"听取用户意见
4. 记录用户反馈到 docs/变更总览.md
5. 等待用户指令
6. 用户说"继续"后，从断点恢复
```

### 恢复流程

```
用户说"继续"后:
1. 重新读取 SKILL.md 核心规则
2. 重新读取本文件（orchestrator.md）
3. 读取 docs/state.json 确认当前角色和进度
4. 读取 docs/交接/ 目录下最新的交接文档
5. 读取当前角色文件
6. 从断点继续执行
```

## 进度通报格式

每完成一个角色，基于 state.json 数据输出进度报告。数据来源：
- **已完成数**：`jq '.pipeline.completed_roles | length' docs/state.json`
- **总角色数**：`jq '.pipeline.completed_roles + .pipeline.remaining_roles | length' docs/state.json`
- **下一角色**：`jq -r '.pipeline.remaining_roles[0] // empty' docs/state.json`

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ {角色名} 已完成
📋 产出物: {产出物列表}
📄 交接文档: docs/交接/交接-{源角色}-to-{目标角色}.md
📊 进度: {已完成数}/{总角色数} ({百分比}%)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔄 下一角色: {角色名}
⏱ 预计剩余: {X} 个角色
```

## 自我审查清单

- [ ] state.json 已更新（completed_roles / current_role / remaining_roles）
- [ ] 检查点文件已写入 docs/检查点/
- [ ] 进度摘要已更新 docs/进度摘要.md
- [ ] 交接文档已编写 docs/交接/
- [ ] Todo 状态已更新
- [ ] 上下文水位已评估
- [ ] 角色锚定已执行（重读角色文件 + 输入文档）
- [ ] 产出物完整，无占位符

---

**记住：你是调度者，不是执行者。你的职责是确保流水线顺畅推进，状态可恢复，进度可追踪。**