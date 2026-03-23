# /commit-push-pr

> 一键完成代码提交、推送到远程、创建 Pull Request

## 功能说明

`/commit-push-pr` 是一个 Git 工作流自动化命令，将常见的 Git 操作整合为一步执行，提高开发效率。

## 使用方式

```bash
/commit-push-pr
```

无需参数，命令会自动完成所有步骤。

## 执行流程

### 1. 分支检查与创建

```
检查当前分支
├── 如果在 main/master 上
│   └── 创建新的功能分支（格式：feature/<描述>-<时间戳>）
└── 如果已在功能分支上
    └── 继续使用当前分支
```

### 2. 代码暂存

```bash
git add -A
```

暂存所有更改的文件。

### 3. 生成提交信息

基于当前更改的内容，自动生成符合 [Conventional Commits](https://www.conventionalcommits.org/) 规范的提交信息。

**提交类型自动识别**:
| 类型 | 识别条件 |
|------|----------|
| `feat` | 新增功能代码 |
| `fix` | 修复 Bug 的代码 |
| `docs` | 仅修改文档 |
| `refactor` | 重构代码（非功能变更） |
| `test` | 仅添加/修改测试 |
| `chore` | 构建/配置/依赖更新 |

### 4. 提交代码

```bash
git commit -m "<type>(<scope>): <subject>"
```

### 5. 推送到远程

```bash
git push -u origin <branch-name>
```

### 6. 创建 Pull Request

使用 GitHub CLI 创建 PR：

```bash
gh pr create \
  --title "<PR 标题>" \
  --body "<PR 描述>" \
  --base main
```

## 输出示例

```
📋 检查分支状态...
✓ 当前在功能分支：feature/user-auth

📝 分析代码更改...
✓ 检测到新增功能：用户认证模块

✍️ 生成提交信息...
✓ feat(auth): 新增用户登录功能

📤 提交代码...
✓ 提交成功：abc1234

🚀 推送到远程...
✓ 推送到 origin/feature/user-auth

📋 创建 Pull Request...
✓ PR 已创建：https://github.com/user/repo/pull/42
```

## 前置条件

1. **Git 已安装并配置**
2. **GitHub CLI (gh) 已安装**
3. **已登录 GitHub**: `gh auth login`
4. **远程仓库已配置**: `git remote -v`

## 注意事项

### 分支命名

如果在 main 分支上运行，会自动创建功能分支：
```
feature/<日期>-<简短描述>
```

### 提交信息格式

遵循 Conventional Commits 规范：
```
<type>(<scope>): <subject>

<body>

<footer>
```

### PR 标题和描述

- **标题**: 与提交信息一致
- **描述**: 包含更改详情、测试说明、相关 Issue

## 故障排查

### 问题：命令执行失败

**可能原因**:
1. 未安装 GitHub CLI
2. 未登录 GitHub
3. Git 仓库未初始化

**解决方案**:
```bash
# 安装 GitHub CLI
brew install gh  # macOS
sudo apt install gh  # Linux

# 登录 GitHub
gh auth login

# 检查 Git 状态
git status
```

### 问题：PR 创建失败

**可能原因**:
1. 远程仓库不存在
2. 权限不足
3. 分支已存在

**解决方案**:
```bash
# 检查远程仓库
git remote -v

# 检查分支
git branch -a

# 手动创建 PR
gh pr create
```

## 相关命令

| 命令 | 说明 |
|------|------|
| `/dedupe` | Issue 去重 |
| `/triage-issue` | Issue 分类 |
| `git commit` | 手动提交 |
| `gh pr create` | 手动创建 PR |

## 相关文档

- [Conventional Commits 规范](../PROJECT.md#提交规范)
- [GitHub CLI 使用](../github/README.md#github-cli-集成)
- [Git 工作流最佳实践](../guides/best-practices.md#git-工作流自动化)
