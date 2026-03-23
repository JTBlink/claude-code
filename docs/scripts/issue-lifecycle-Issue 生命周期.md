# issue-lifecycle.ts - Issue 生命周期管理脚本

> 自动化管理 Issue 从创建到关闭的完整生命周期

## 功能说明

`issue-lifecycle.ts` 脚本用于自动化管理 Issue 的整个生命周期，包括：

1. **新 Issue 处理**: 添加欢迎评论和初始标签
2. **状态更新**: 根据活动更新 Issue 状态
3. **通知提醒**: 提醒维护者处理
4. **关闭处理**: Issue 关闭后的后续处理

## 使用方式

```bash
# 基本运行
npx tsx scripts/issue-lifecycle.ts

# 指定事件类型
npx tsx scripts/issue-lifecycle.ts --event opened
npx tsx scripts/issue-lifecycle.ts --event closed
npx tsx scripts/issue-lifecycle.ts --event labeled

# 模拟运行
npx tsx scripts/issue-lifecycle.ts --dry-run

# 详细日志
npx tsx scripts/issue-lifecycle.ts --verbose
```

## 参数说明

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `--event` | string | all | 事件类型：opened, closed, labeled |
| `--issue` | number | - | 指定 Issue 编号 |
| `--dry-run` | boolean | false | 模拟运行 |
| `--verbose` | boolean | false | 详细日志 |

## 工作流程

### 1. 新 Issue 处理 (opened)

```typescript
// 添加欢迎评论
await gh.issue.comment(issue.number, `
👋 感谢提交 Issue！

我们已收到您的问题，并会尽快处理。
如需补充信息，请直接在此评论。
`);

// 添加初始标签
await gh.issue.addLabel(issue.number, 'needs-triage');
```

### 2. Issue 标签更新 (labeled)

```typescript
// 根据标签触发不同处理
if (label === 'bug') {
  await handleBugIssue(issue);
} else if (label === 'enhancement') {
  await handleFeatureRequest(issue);
} else if (label === 'documentation') {
  await handleDocsIssue(issue);
}
```

### 3. Issue 关闭 (closed)

```typescript
// 添加关闭评论
await gh.issue.comment(issue.number, `
✅ 此 Issue 已关闭。

如果问题仍然存在，请重新打开或创建新 Issue。
`);

// 移除进行中标签
await gh.issue.removeLabel(issue.number, 'in-progress');
```

## 输出示例

```
🔄 处理 Issue #42...

📋 事件类型：opened
✓ 添加欢迎评论
✓ 添加标签：needs-triage
✓ 分配给：@maintainer-on-call

📊 Issue 状态更新完成
  - 评论数：1
  - 标签数：1
  - 分配者：1

✅ 处理完成！
```

## 配置选项

### 欢迎消息配置

在项目根目录创建 `.issue-lifecycle.json`:

```json
{
  "welcome": {
    "enabled": true,
    "message": "👋 感谢提交 Issue！我们会尽快处理。",
    "labels": ["needs-triage"]
  },
  "triage": {
    "bug": {
      "labels": ["bug", "priority:medium"],
      "assign": true
    },
    "enhancement": {
      "labels": ["enhancement"],
      "assign": false
    },
    "documentation": {
      "labels": ["documentation"],
      "assign": true
    }
  },
  "close": {
    "message": "✅ 此 Issue 已关闭。",
    "removeLabels": ["in-progress", "needs-triage"]
  }
}
```

### GitHub Actions 配置

```yaml
# .github/workflows/issue-lifecycle.yml
name: Issue Lifecycle Management

on:
  issues:
    types: [opened, closed, labeled, assigned]

jobs:
  lifecycle:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm install tsx
      
      - name: Run lifecycle script
        run: npx tsx scripts/issue-lifecycle.ts
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          ISSUE_NUMBER: ${{ github.event.issue.number }}
          EVENT_TYPE: ${{ github.event.action }}
```

## 事件处理详情

### opened 事件

**触发时机**: 新 Issue 创建时

**处理动作**:
1. 添加欢迎评论
2. 添加 `needs-triage` 标签
3. 通知维护者

### labeled 事件

**触发时机**: Issue 添加标签时

**处理动作**:
1. 根据标签类型添加额外标签
2. 分配给相应维护者
3. 更新 Issue 状态

### closed 事件

**触发时机**: Issue 关闭时

**处理动作**:
1. 添加关闭评论
2. 移除进行中标签
3. 记录关闭原因

### assigned 事件

**触发时机**: Issue 分配给维护者时

**处理动作**:
1. 添加分配评论
2. 更新状态标签

## 标签自动化

### 自动添加标签

| 条件 | 自动添加标签 |
|------|-------------|
| 包含错误信息 | `bug` |
| 包含"希望"、"建议" | `enhancement` |
| 包含"文档"、"README" | `documentation` |
| 首次提交者 | `first-time-contributor` |

### 自动移除标签

| 条件 | 自动移除标签 |
|------|-------------|
| Issue 关闭 | `in-progress`, `needs-triage` |
| Issue 重新打开 | `stale` |

## 注意事项

### 速率限制

GitHub API 有速率限制，建议：

1. 使用 GitHub Actions 运行
2. 批量操作时添加延迟
3. 使用 `--dry-run` 先测试

### 权限要求

需要以下权限：

- `issues:read` - 读取 Issue
- `issues:write` - 编辑 Issue、添加评论
- `labels:read` - 读取标签
- `labels:write` - 添加/移除标签

### 重复评论

脚本会检查是否已添加过相同评论，避免重复。

## 故障排查

### 问题：脚本无响应

**可能原因**:
1. API 速率限制
2. 网络问题

**解决方案**:
```bash
# 检查 API 状态
gh api /rate_limit

# 使用详细日志
npx tsx scripts/issue-lifecycle.ts --verbose
```

### 问题：标签添加失败

**可能原因**:
1. 标签不存在
2. 权限不足

**解决方案**:
```bash
# 检查标签
gh label list

# 创建标签
gh label create needs-triage --color ffffff
```

## 相关脚本

| 脚本 | 说明 |
|------|------|
| [sweep.ts](./sweep.ts.md) | Issue 过期清理 |
| [lifecycle-comment.ts](./lifecycle-comment.ts.md) | 生命周期评论 |

## 相关文档

- [GitHub 集成](../github/README.md)
- [Issue 模板](../github/README.md#issue-模板)

---

*最后更新：2026 年 3 月*
