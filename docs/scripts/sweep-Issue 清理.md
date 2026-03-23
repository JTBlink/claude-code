# sweep.ts - Issue 自动管理脚本

> 自动标记过期 Issue 并关闭长时间无活动的 Issue

## 功能说明

`sweep.ts` 脚本用于自动管理仓库中的 Issue 生命周期，主要功能包括：

1. **标记过期**: 长时间无活动的 Issue 标记为 `stale`
2. **自动关闭**: 过期 Issue 自动关闭
3. **热度保护**: 高热度 Issue 不自动关闭

## 使用方式

```bash
# 基本运行
npx tsx scripts/sweep.ts

# 模拟运行（不实际执行）
npx tsx scripts/sweep.ts --dry-run

# 自定义过期天数
npx tsx scripts/sweep.ts --stale-days 30 --close-days 90

# 详细日志
npx tsx scripts/sweep.ts --verbose
```

## 参数说明

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `--stale-days` | number | 30 | 标记为过期的天数 |
| `--close-days` | number | 90 | 自动关闭的天数 |
| `--min-heat` | number | 5 | 最小热度保护阈值 |
| `--dry-run` | boolean | false | 模拟运行，不实际执行 |
| `--verbose` | boolean | false | 详细日志输出 |

## 工作流程

### 1. 获取所有打开的 Issue

```typescript
// 获取所有打开的 Issue
const issues = await gh.issue.list({ state: 'open' });
```

### 2. 检查每个 Issue 的活动

```typescript
// 计算最后活动时间
const lastActivity = max([
  issue.createdAt,
  issue.updatedAt,
  ...comments.map(c => c.createdAt)
]);

// 计算天数差
const daysSinceActivity = (now - lastActivity) / (1000 * 60 * 60 * 24);
```

### 3. 判断是否过期

```typescript
// 检查是否过期
if (daysSinceActivity > staleDays) {
  // 标记为 stale
  await gh.issue.addLabel(issue.number, 'stale');
}
```

### 4. 判断是否关闭

```typescript
// 检查是否应该关闭
if (daysSinceActivity > closeDays) {
  // 检查热度保护
  if (issue.comments < minHeat) {
    await gh.issue.close(issue.number);
  }
}
```

## 输出示例

```
🔍 开始 Issue 清理...
📊 共检查 150 个打开的 Issue

📋 标记过期 Issue:
  - #42: 60 天无活动 → 标记 stale
  - #38: 45 天无活动 → 标记 stale
  - #35: 35 天无活动 → 标记 stale

🔒 关闭过期 Issue:
  - #29: 120 天无活动 → 已关闭
  - #25: 95 天无活动 → 已关闭

🛡️ 热度保护:
  - #10: 150 天无活动，但评论数 25 → 跳过

✅ 清理完成！
  - 标记过期：3
  - 关闭：2
  - 保护：1
```

## 配置选项

### 在 GitHub Actions 中使用

```yaml
# .github/workflows/sweep.yml
name: Issue Sweep

on:
  schedule:
    - cron: '0 0 * * *'  # 每天 UTC 0 点运行

jobs:
  sweep:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm install tsx
      
      - name: Run sweep
        run: npx tsx scripts/sweep.ts --stale-days 30 --close-days 90
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### 自定义配置

在项目根目录创建 `.sweep.json`:

```json
{
  "staleDays": 30,
  "closeDays": 90,
  "minHeat": 5,
  "exemptLabels": ["important", "bug", "security"],
  "message": {
    "stale": "此 Issue 已长时间无活动，将在 60 天后自动关闭。",
    "closed": "此 Issue 已过期，已自动关闭。如需重新打开请评论。"
  }
}
```

## 注意事项

### 保护规则

以下 Issue 不会被自动关闭：

1. **高热度 Issue**: 评论数 ≥ `minHeat`
2. **重要标签**: 包含 `important`, `bug`, `security` 等标签
3. **已分配 Issue**: 已分配给维护者的 Issue
4. **里程碑 Issue**: 关联到活跃里程碑的 Issue

### 速率限制

GitHub API 有速率限制：

| 认证方式 | 限制 |
|----------|------|
| 未认证 | 60 请求/小时 |
| 个人令牌 | 5000 请求/小时 |
| GitHub Actions | 更高限制 |

**建议**: 使用 GitHub Actions 运行，避免速率限制。

### 时区考虑

脚本使用 UTC 时间，注意与本地时间的转换。

## 故障排查

### 问题：脚本执行失败

**症状**:
```
Error: API rate limit exceeded
```

**解决方案**:
```bash
# 使用个人访问令牌
export GITHUB_TOKEN=ghp_xxx

# 或在 GitHub Actions 中运行
# 使用 GITHUB_TOKEN 环境变量
```

### 问题：Issue 未正确标记

**可能原因**:
1. 标签不存在
2. 权限不足

**解决方案**:
```bash
# 检查标签列表
gh label list

# 创建缺失标签
gh label create stale --color ffffff

# 检查权限
gh repo view
```

### 问题：脚本运行时间过长

**解决方案**:
```bash
# 限制处理的 Issue 数量
npx tsx scripts/sweep.ts --limit 100

# 增加分页
npx tsx scripts/sweep.ts --per-page 50
```

## 相关脚本

| 脚本 | 说明 |
|------|------|
| [issue-lifecycle.ts](./issue-lifecycle.ts.md) | Issue 生命周期管理 |
| [auto-close-duplicates.ts](./auto-close-duplicates.ts.md) | 自动关闭重复 Issue |

## 相关文档

- [Issue 生命周期管理](../github/README.md#issue-生命周期管理)
- [GitHub Actions 配置](../github/README.md#github-actions)

---

*最后更新：2026 年 3 月*
