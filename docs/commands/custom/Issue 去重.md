# /dedupe

> Issue 去重工具，自动查找并合并重复的 Issue

## 功能说明

`/dedupe` 命令用于识别仓库中的重复 Issue，并将重复 Issue 的评论迁移到主 Issue，然后关闭重复 Issue。

## 使用方式

```bash
/dedupe
```

## 执行流程

### 1. 分析当前 Issue

提取当前 Issue 的关键信息：
- 标题关键词
- 问题描述
- 错误信息
- 标签

### 2. 搜索相似 Issue

使用 GitHub 搜索 API 查找相似 Issue：

```bash
# 搜索标题相似的 Issue
gh search issues "<标题关键词>" --repo <owner>/<repo>

# 搜索内容相似的 Issue
gh search issues "<错误信息>" --repo <owner>/<repo>
```

### 3. 相似度判断

基于以下因素判断是否重复：

| 因素 | 权重 |
|------|------|
| 标题相似度 | 40% |
| 内容相似度 | 30% |
| 标签匹配 | 15% |
| 创建时间 | 15% |

**阈值**: 相似度 ≥ 80% 判定为重复

### 4. 迁移评论

将重复 Issue 的评论迁移到主 Issue：

```bash
# 获取重复 Issue 的评论
gh api /repos/<owner>/<repo>/issues/<number>/comments

# 在主 Issue 中添加引用评论
gh issue comment <主 Issue 编号> \
  --body "来自 #<重复 Issue 编号> 的评论：<内容>"
```

### 5. 关闭重复 Issue

```bash
gh issue close <重复 Issue 编号> \
  --comment "此 Issue 是 #<主 Issue 编号> 的重复，已合并讨论。"
```

## 输出示例

```
🔍 分析当前 Issue #42: "登录页面无法加载"...

📋 搜索相似 Issue...
✓ 找到 3 个相似 Issue:
  - #38: "登录页面空白" (相似度：85%)
  - #35: "无法访问登录界面" (相似度：72%)
  - #29: "登录页加载失败" (相似度：91%)

📊 识别重复 Issue...
✓ 确认 #38 和 #29 为重复 Issue

📥 迁移评论...
✓ 从 #38 迁移 5 条评论
✓ 从 #29 迁移 3 条评论

🔒 关闭重复 Issue...
✓ 关闭 #38
✓ 关闭 #29

✅ 去重完成！主 Issue: #42
```

## 前置条件

1. **GitHub CLI 已安装**
2. **已登录 GitHub**: `gh auth login`
3. **仓库访问权限**

## 配置选项

可在命令中指定以下参数：

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `--threshold <值>` | 相似度阈值 (0-100) | 80 |
| `--dry-run` | 仅显示，不执行操作 | false |
| `--migrate-comments` | 是否迁移评论 | true |

### 示例

```bash
# 仅检查，不执行操作
/dedupe --dry-run

# 设置更高的相似度阈值
/dedupe --threshold 90

# 不迁移评论
/dedupe --migrate-comments false
```

## 注意事项

### 保护高热度 Issue

以下 Issue 不会被自动关闭：
- 评论数 > 10
- 参与人数 > 5
- 有 `important` 标签

### 评论迁移限制

- 仅迁移文本评论
- 不迁移图片/附件
- 保留原评论作者信息

## 故障排查

### 问题：找不到相似 Issue

**可能原因**:
1. 标题/描述太短
2. 使用了特殊术语

**解决方案**:
- 补充 Issue 描述
- 添加更多关键词

### 问题：评论迁移失败

**可能原因**:
1. 权限不足
2. 评论数过多

**解决方案**:
- 检查仓库权限
- 手动迁移重要评论

## 相关命令

| 命令 | 说明 |
|------|------|
| `/triage-issue` | Issue 分类 |
| `/commit-push-pr` | Git 工作流 |

## 相关文档

- [Issue 生命周期管理](../../scripts/issue-lifecycle.ts.md)
- [GitHub CLI 使用](../../github/README.md)
