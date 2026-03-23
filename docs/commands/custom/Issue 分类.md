# /triage-issue

> Issue 自动分类和标记工具

## 功能说明

`/triage-issue` 命令自动分析 Issue 内容，判断问题类型，添加相应标签，并分配给合适的维护者。

## 使用方式

```bash
/triage-issue
```

## 执行流程

### 1. 分析 Issue 内容

提取 Issue 的关键信息：

```
Issue 内容分析
├── 标题分析
├── 描述分析
├── 代码片段检测
├── 错误信息提取
└── 情感分析（紧急程度）
```

### 2. 问题类型识别

基于内容判断 Issue 类型：

| 类型 | 识别特征 | 标签 |
|------|----------|------|
| **Bug** | 错误信息、异常堆栈、功能失效 | `bug` |
| **功能请求** | "希望"、"建议"、"添加" | `enhancement` |
| **文档问题** | "文档"、"说明"、"README" | `documentation` |
| **模型行为** | "Claude"、"回答"、"输出" | `model-behavior` |
| **问题咨询** | "如何"、"怎么"、"为什么" | `question` |

### 3. 添加标签

根据识别结果添加相应标签：

```bash
# 基础类型标签
gh issue edit <编号> --add-label "bug"

# 优先级标签（基于紧急程度）
gh issue edit <编号> --add-label "priority:high"

# 领域标签（基于内容）
gh issue edit <编号> --add-label "area:cli"
```

### 4. 分配维护者

基于 Issue 类型和领域分配：

| 类型/领域 | 分配给 |
|-----------|--------|
| Bug | 核心维护者轮值 |
| 功能请求 | 产品负责人 |
| 文档问题 | 文档维护者 |
| 模型行为 | ML 团队 |

### 5. 添加初步回复

自动添加欢迎和确认回复：

```markdown
👋 感谢提交 Issue！

我们已初步分析您的问题，并添加了相应标签。
团队成员将在 24-48 小时内回复。

如需补充信息，请直接在此评论。
```

## 输出示例

```
📋 分析 Issue #42: "登录时出现 500 错误"...

🏷️ 识别问题类型...
✓ Bug - 检测到错误信息和异常堆栈

📌 添加标签...
✓ bug (主要类型)
✓ priority:medium (基于紧急程度)
✓ area:backend (基于错误位置)

👤 分配维护者...
✓ 分配给：@maintainer-on-call

💬 添加初步回复...
✓ 已添加欢迎评论

✅ Issue 分类完成！
```

## 标签体系

### 类型标签

| 标签 | 说明 |
|------|------|
| `bug` | 功能缺陷 |
| `enhancement` | 功能改进/新增 |
| `documentation` | 文档相关 |
| `model-behavior` | 模型输出问题 |
| `question` | 使用咨询 |

### 优先级标签

| 标签 | 说明 | 响应时间 |
|------|------|----------|
| `priority:critical` | 严重问题 | 4 小时内 |
| `priority:high` | 高优先级 | 24 小时内 |
| `priority:medium` | 中优先级 | 48 小时内 |
| `priority:low` | 低优先级 | 1 周内 |

### 领域标签

| 标签 | 说明 |
|------|------|
| `area:cli` | 命令行界面 |
| `area:ide` | IDE 集成 |
| `area:github` | GitHub 集成 |
| `area:plugins` | 插件系统 |
| `area:security` | 安全问题 |

## 配置选项

可在命令中指定以下参数：

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `--auto-assign` | 是否自动分配 | true |
| `--add-comment` | 是否添加回复 | true |
| `--dry-run` | 仅显示，不执行 | false |

### 示例

```bash
# 仅添加标签，不分配
/triage-issue --auto-assign false

# 仅检查，不执行操作
/triage-issue --dry-run
```

## 前置条件

1. **GitHub CLI 已安装**
2. **已登录 GitHub**: `gh auth login`
3. **仓库写入权限**

## 注意事项

### 敏感 Issue 处理

以下 Issue 需要人工审查：
- 包含安全漏洞报告
- 包含敏感信息泄露
- 涉及法律合规问题

### 分配规则

- 每个维护者最多分配 5 个未处理 Issue
- 周末/节假日不自动分配
- 高优先级 Issue 优先分配

## 故障排查

### 问题：标签添加失败

**可能原因**:
1. 标签不存在
2. 权限不足

**解决方案**:
```bash
# 检查标签列表
gh label list

# 创建缺失标签
gh label create bug --color d73a4a
```

### 问题：无法分配维护者

**可能原因**:
1. 维护者列表为空
2. 用户名错误

**解决方案**:
- 检查维护者配置
- 手动分配：`gh issue edit <编号> --add-assignee <用户>`

## 相关命令

| 命令 | 说明 |
|------|------|
| `/dedupe` | Issue 去重 |
| `/commit-push-pr` | Git 工作流 |

## 相关文档

- [Issue 模板](../../github/README.md#issue-模板)
- [Issue 生命周期](../../scripts/issue-lifecycle.ts.md)
- [标签管理规范](../../github/README.md#标签管理)
