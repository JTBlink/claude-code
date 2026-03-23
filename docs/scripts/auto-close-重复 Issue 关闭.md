# auto-close-duplicates.ts - 自动关闭重复 Issue 脚本

> 自动检测并关闭重复的 Issue，保持 Issue 追踪器整洁

## 功能说明

`auto-close-duplicates.ts` 脚本用于自动检测仓库中的重复 Issue，并执行以下操作：

1. **相似度分析**: 基于标题和内容计算相似度
2. **重复检测**: 识别相似度超过阈值的 Issue
3. **自动关闭**: 关闭重复 Issue
4. **添加引用**: 在重复 Issue 上添加指向主 Issue 的评论

## 使用方式

```bash
# 基本运行
npx tsx scripts/auto-close-duplicates.ts

# 指定 Issue 编号
npx tsx scripts/auto-close-duplicates.ts --issue 42

# 调整相似度阈值
npx tsx scripts/auto-close-duplicates.ts --threshold 85

# 模拟运行
npx tsx scripts/auto-close-duplicates.ts --dry-run

# 详细日志
npx tsx scripts/auto-close-duplicates.ts --verbose
```

## 参数说明

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `--issue` | number | - | 指定 Issue 编号 |
| `--threshold` | number | 80 | 相似度阈值 (0-100) |
| `--limit` | number | 50 | 最多检查的 Issue 数 |
| `--dry-run` | boolean | false | 模拟运行 |
| `--verbose` | boolean | false | 详细日志 |

## 工作流程

### 1. 获取 Issue 列表

```typescript
// 获取所有打开的 Issue
const issues = await gh.issue.list({ state: 'open' });
```

### 2. 计算相似度

```typescript
// 基于标题和内容计算相似度
function calculateSimilarity(issue1, issue2) {
  const title1 = issue1.title.toLowerCase();
  const title2 = issue2.title.toLowerCase();
  
  // 标题相似度 (40%)
  const titleSim = jaccardSimilarity(title1, title2);
  
  // 内容相似度 (30%)
  const bodySim = jaccardSimilarity(issue1.body, issue2.body);
  
  // 标签匹配度 (15%)
  const labelSim = labelMatchScore(issue1.labels, issue2.labels);
  
  // 时间接近度 (15%)
  const timeSim = timeProximity(issue1.createdAt, issue2.createdAt);
  
  return titleSim * 0.4 + bodySim * 0.3 + labelSim * 0.15 + timeSim * 0.15;
}
```

### 3. 识别重复 Issue

```typescript
// 找出相似度超过阈值的 Issue
const duplicates = issues.filter(issue => {
  const sim = calculateSimilarity(targetIssue, issue);
  return sim >= threshold && issue.number !== targetIssue.number;
});
```

### 4. 关闭重复 Issue

```typescript
// 关闭重复 Issue
for (const dup of duplicates) {
  await gh.issue.close(dup.number, {
    comment: `此 Issue 是 #${targetIssue.number} 的重复，已合并讨论。`
  });
}
```

## 输出示例

```
🔍 开始检测重复 Issue...
📊 共检查 150 个打开的 Issue

📋 检测到重复 Issue:
  - #38: "登录页面空白" (相似度：85%) → 重复于 #42
  - #29: "登录页加载失败" (相似度：91%) → 重复于 #42

🔒 关闭重复 Issue:
  ✓ #38 已关闭
  ✓ #29 已关闭

💬 添加评论:
  ✓ 在 #38 上添加引用评论
  ✓ 在 #29 上添加引用评论

✅ 检测完成！
  - 发现重复：2
  - 已关闭：2
```

## 相似度算法

### Jaccard 相似度

```typescript
function jaccardSimilarity(str1, str2) {
  const set1 = new Set(tokenize(str1));
  const set2 = new Set(tokenize(str2));
  
  const intersection = [...set1].filter(x => set2.has(x)).length;
  const union = new Set([...set1, ...set2]).size;
  
  return intersection / union;
}
```

### 标签匹配

```typescript
function labelMatchScore(labels1, labels2) {
  const common = labels1.filter(l => labels2.includes(l)).length;
  const total = Math.max(labels1.length, labels2.length);
  return common / total;
}
```

### 时间接近度

```typescript
function timeProximity(date1, date2) {
  const diff = Math.abs(date1 - date2) / (1000 * 60 * 60 * 24);
  return diff < 7 ? 1.0 : diff < 30 ? 0.5 : 0.1;
}
```

## 配置选项

### 自定义配置

在项目根目录创建 `.duplicate-checker.json`:

```json
{
  "threshold": 80,
  "weights": {
    "title": 0.4,
    "body": 0.3,
    "labels": 0.15,
    "time": 0.15
  },
  "exemptLabels": ["important", "security"],
  "minComments": 0,
  "message": "此 Issue 是 #{{mainIssue}} 的重复，已合并讨论。"
}
```

### GitHub Actions 配置

```yaml
# .github/workflows/auto-close-duplicates.yml
name: Auto Close Duplicates

on:
  issues:
    types: [opened, edited]

jobs:
  check-duplicates:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm install tsx
      
      - name: Check duplicates
        run: npx tsx scripts/auto-close-duplicates.ts --issue ${{ github.event.issue.number }}
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

## 注意事项

### 保护规则

以下 Issue 不会被自动关闭：

1. **重要标签**: 包含 `important`, `security` 等标签
2. **高评论数**: 评论数 > 10
3. **多参与者**: 参与人数 > 5
4. **已分配**: 已分配给维护者

### 阈值选择

| 阈值 | 效果 |
|------|------|
| 70-75 | 宽松，可能误判 |
| 80-85 | 平衡（推荐） |
| 90-95 | 严格，可能漏判 |

### 主 Issue 选择

当检测到重复时，选择主 Issue 的规则：

1. **最早创建** 的 Issue 为主 Issue
2. 如果创建时间相近，选择 **评论数更多** 的
3. 如果评论数相近，选择 **标签更多** 的

## 故障排查

### 问题：检测到过多重复

**可能原因**: 阈值过低

**解决方案**:
```bash
# 提高阈值
npx tsx scripts/auto-close-duplicates.ts --threshold 90
```

### 问题：未检测到明显重复

**可能原因**: 阈值过高或标题差异大

**解决方案**:
```bash
# 降低阈值
npx tsx scripts/auto-close-duplicates.ts --threshold 75

# 或手动标记
gh issue close 38 --comment "重复于 #42"
```

### 问题：脚本执行失败

**可能原因**: API 速率限制

**解决方案**:
```bash
# 使用 GitHub Actions 运行
# 或在本地使用个人访问令牌
export GITHUB_TOKEN=ghp_xxx
```

## 相关脚本

| 脚本 | 说明 |
|------|------|
| [backfill-duplicate-comments.ts](./backfill-duplicate-comments.ts.md) | 迁移重复 Issue 评论 |
| [comment-on-duplicates.sh](../README.md#comment-on-duplicatessh) | 添加评论到重复 Issue |

## 相关文档

- [Issue 去重命令](../commands/custom/dedupe.md)
- [GitHub 集成](../github/README.md)

---

*最后更新：2026 年 3 月*
