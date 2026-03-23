# Claude Code GitHub 集成

> GitHub 平台深度集成，包括 Actions 自动化、@claude 提及和 CLI 工具

## 目录

- [GitHub Actions 集成](#github-actions-集成)
- [@claude 提及集成](#claude-提及集成)
- [GitHub CLI 集成](#github-cli-集成)
- [Issue 模板](#issue-模板)
- [工作流配置](#工作流配置)

---

## GitHub Actions 集成

### 官方 Action

Claude Code 提供官方 GitHub Action，可在 CI/CD 流程中使用：

```yaml
- name: Run Claude Code
  uses: anthropics/claude-code-action@v1
  with:
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    claude_args: "--model claude-sonnet-4-5-20250929"
```

### 使用示例

#### PR 自动审查

```yaml
# .github/workflows/claude-review.yml
name: Claude Code Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Run Claude Code Review
        uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          claude_args: "--model claude-sonnet-4-5-20250929"
          prompt: |
            请审查这个 PR：
            1. 检查代码是否符合项目规范
            2. 查找潜在的 Bug
            3. 提供改进建议
            
            相关文件：
            - CLAUDE.md
            - CONTRIBUTING.md
```

#### Issue 自动响应

```yaml
# .github/workflows/claude-issue.yml
name: Claude Code Issue Response

on:
  issues:
    types: [opened]

jobs:
  respond:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Run Claude Code
        uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            请分析这个新 Issue：
            1. 判断问题类型（Bug、功能请求、文档问题等）
            2. 添加适当的标签
            3. 提供初步回复
            
            Issue 标题：${{ github.event.issue.title }}
            Issue 内容：${{ github.event.issue.body }}
```

### 项目工作流

本项目包含以下 GitHub Actions 工作流：

| 工作流 | 说明 |
|--------|------|
| `claude.yml` | 主 CI 流程，运行 Claude Code 任务 |
| `claude-dedupe-issues.yml` | Issue 去重流程 |
| `claude-issue-triage.yml` | Issue 自动分类 |
| `auto-close-duplicates.yml` | 自动关闭重复 Issue |
| `issue-lifecycle-comment.yml` | Issue 生命周期评论 |
| `issue-opened-dispatch.yml` | Issue 打开时分发 |
| `lock-closed-issues.yml` | 关闭 Issue 锁定 |
| `sweep.yml` | Issue 过期清理 |

---

## @claude 提及集成

在 GitHub Issue 和 PR 中提及 `@claude` 可触发自动化处理。

### 使用方式

```markdown
@claude 请帮我分析这个问题...
```

### 触发场景

| 场景 | 触发动作 |
|------|----------|
| Issue 中提及 | 分析问题、添加标签、提供回复 |
| PR 中提及 | 代码审查、提供反馈 |
| 评论中提及 | 回答具体问题 |

### 配置方式

在仓库设置中启用：

1. 进入 **Settings > Actions > General**
2. 启用 **Allow GitHub Actions to create and approve pull requests**
3. 配置 Anthropic API Key

---

## GitHub CLI 集成

### gh.sh 封装脚本

项目提供 `scripts/gh.sh` 封装脚本，提供受限的 GitHub CLI 访问。

**使用方式**:

```bash
# 查看 Issue
./scripts/gh.sh issue view 42

# 列出 Issue
./scripts/gh.sh issue list --state open

# 搜索 Issue
./scripts/gh.sh search issues "bug report" --limit 10

# 列出标签
./scripts/gh.sh label list --limit 100

# 添加标签
./scripts/gh.sh label add 42 bug

# 关闭 Issue
./scripts/gh.sh issue close 42
```

### 安全限制

- 仅限安全的只读操作（部分）
- 限制批量操作数量
- 记录所有操作日志

### 完整文档

详见：[脚本工具](../scripts/脚本工具.md#ghsh)

---

## Issue 模板

项目提供以下 Issue 模板：

### Bug 报告

**文件**: `.github/ISSUE_TEMPLATE/bug_report.yml`

**用途**: 报告功能问题

**字段**:
- 问题描述
- 复现步骤
- 预期行为
- 实际行为
- 环境信息

### 功能请求

**文件**: `.github/ISSUE_TEMPLATE/feature_request.yml`

**用途**: 请求新功能

**字段**:
- 功能描述
- 使用场景
- 替代方案
- 额外信息

### 文档问题

**文件**: `.github/ISSUE_TEMPLATE/documentation.yml`

**用途**: 报告文档错误或不清晰

**字段**:
- 文档链接
- 问题描述
- 建议修改

### 模型行为

**文件**: `.github/ISSUE_TEMPLATE/model_behavior.yml`

**用途**: 报告 Claude 模型相关问题

**字段**:
- 问题描述
- 提示词
- 实际输出
- 预期输出

### 配置

**文件**: `.github/ISSUE_TEMPLATE/config.yml`

```yaml
blank_issues_enabled: false
contact_links:
  - name: Discord 社区
    url: https://anthropic.com/discord
    about: 在社区讨论问题
```

---

## 工作流配置

### 完整示例

```yaml
# .github/workflows/claude.yml
name: Claude Code Automation

on:
  issues:
    types: [opened, assigned]
  issue_comment:
    types: [created]
  pull_request:
    types: [opened, synchronize]

jobs:
  claude:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      
      - name: Run Claude Code
        uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          claude_args: "--model claude-sonnet-4-5-20250929"
```

### 环境变量

| 变量 | 说明 |
|------|------|
| `ANTHROPIC_API_KEY` | Anthropic API 密钥 |
| `GITHUB_TOKEN` | GitHub 自动令牌 |
| `CLAUDE_MODEL` | 使用的模型（可选） |

### 触发条件

```yaml
# Issue 相关
on:
  issues:
    types: [opened, closed, labeled, assigned, reopened]
  issue_comment:
    types: [created, edited]

# PR 相关
on:
  pull_request:
    types: [opened, synchronize, closed, labeled]
  pull_request_review:
    types: [submitted]

# 定时任务
on:
  schedule:
    - cron: '0 0 * * *'  # 每天 UTC 0 点
```

---

## Issue 生命周期管理

### 自动化流程

```
Issue 创建
    ↓
自动分类（添加标签、分配）
    ↓
处理中（维护者处理）
    ↓
解决/关闭
    ↓
锁定（可选）
```

### 相关脚本

| 脚本 | 说明 |
|------|------|
| [sweep.ts](../scripts/sweep-Issue 清理.md) | 过期 Issue 清理 |
| [issue-lifecycle.ts](../scripts/issue-lifecycle-Issue 生命周期.md) | 生命周期管理 |
| [auto-close-duplicates.ts](../scripts/auto-close-重复 Issue 关闭.md) | 重复 Issue 关闭 |

---

## 标签管理

### 标签体系

| 标签 | 颜色 | 说明 |
|------|------|------|
| `bug` | #d73a4a | 功能缺陷 |
| `enhancement` | #a2eeef | 功能改进 |
| `documentation` | #0075ca | 文档相关 |
| `model-behavior` | #fbca04 | 模型行为问题 |
| `priority:critical` | #b60205 | 严重问题 |
| `priority:high` | #d93f0b | 高优先级 |
| `priority:medium` | #fbca04 | 中优先级 |
| `priority:low` | #0e8a16 | 低优先级 |
| `stale` | #ffffff | 过期 |
| `needs-triage` | #c5def5 | 待分类 |

### 管理命令

```bash
# 列出所有标签
gh label list

# 创建标签
gh label create bug --color d73a4a

# 编辑标签
gh label edit bug --color ff0000

# 删除标签
gh label delete bug
```

---

## 故障排查

### 问题：Action 执行失败

**症状**:
```
Error: Anthropic API key is required
```

**解决方案**:
```yaml
# 在仓库设置中添加密钥
# Settings > Secrets and variables > Actions
# 添加 ANTHROPIC_API_KEY
```

### 问题：@claude 无响应

**可能原因**:
1. 未配置 Action
2. API Key 无效

**解决方案**:
```bash
# 检查 Action 配置
gh workflow list

# 检查密钥
gh secret list
```

### 问题：速率限制

**症状**:
```
API rate limit exceeded
```

**解决方案**:
- 使用 GitHub Actions 运行（更高限制）
- 减少运行频率
- 使用个人访问令牌

---

## 相关文档

- [脚本工具](../scripts/脚本工具.md)
- [Issue 模板配置](../.github/ISSUE_TEMPLATE/)
- [工作流配置](../.github/workflows/)

---

*最后更新：2026 年 3 月*
