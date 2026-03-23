# Claude Code 项目文档

> 本文档提供 Claude Code 项目的全面概述，包括功能特性、技术架构、使用指南和扩展开发。

## 目录

- [项目简介](#项目简介)
- [主要功能特性](#主要功能特性)
- [技术栈说明](#技术栈说明)
- [目录结构说明](#目录结构说明)
- [安装和使用方法](#安装和使用方法)
- [配置说明](#配置说明)
- [插件系统](#插件系统)
- [Hooks 系统](#hooks-系统)
- [示例说明](#示例说明)
- [GitHub 集成](#github-集成)
- [贡献指南](#贡献指南)
- [常见问题](#常见问题)

---

## 项目简介

**Claude Code** 是 Anthropic 开发的智能编程助手，运行在终端环境中。它能够理解你的代码库，通过自然语言命令帮助开发者更高效地编写代码。

### 核心能力

- **代码理解**: 深度理解项目结构和代码库
- **任务执行**: 执行常规编程任务，如重构、调试、测试
- **Git 工作流**: 处理分支管理、提交、PR 创建等 Git 操作
- **多场景支持**: 支持终端、IDE 集成和 GitHub 协作

### 项目定位

Claude Code 不仅是一个聊天机器人，而是一个**代理式编程工具**（Agentic Coding Tool），能够：
- 自主执行多步骤任务
- 使用多种工具（文件读写、命令执行、网络搜索等）
- 理解上下文并做出智能决策
- 与开发者进行自然语言交互

---

## 主要功能特性

### 1. 智能代码助手

| 功能 | 描述 |
|------|------|
| 代码理解 | 分析代码库结构，理解现有模式和架构 |
| 代码生成 | 根据需求生成新代码，遵循项目规范 |
| 代码重构 | 改进现有代码结构，提升可维护性 |
| Bug 修复 | 定位并修复代码问题 |
| 测试编写 | 生成单元测试和集成测试 |

### 2. Git 工作流自动化

- **分支管理**: 自动创建功能分支
- **提交管理**: 生成规范的提交信息
- **PR 创建**: 自动创建 Pull Request
- **代码审查**: 自动化 PR 审查流程

### 3. 插件系统

支持扩展功能的插件架构，包括：
- **自定义命令**: 创建专属斜杠命令
- **专用 Agent**: 针对特定任务的专家 Agent
- **Hooks**: 事件驱动的行为拦截和修改
- **MCP 服务器**: 集成外部工具和服务

### 4. 多模型支持

支持多种 Claude 模型，可根据任务复杂度选择：
- **Claude Opus**: 复杂任务，最高质量
- **Claude Sonnet**: 平衡性能和成本
- **Claude Haiku**: 快速响应，简单任务

### 5. 沙箱安全

- **Bash 沙箱**: 限制命令执行权限
- **文件系统隔离**: 控制文件访问范围
- **网络策略**: 管理网络访问权限
- **权限规则**: 细粒度的工具调用控制

---

## 技术栈说明

### 运行时环境

| 组件 | 要求 | 说明 |
|------|------|------|
| Node.js | 18+ | 主要运行环境 |
| npm/npx | 最新稳定版 | 包管理 |
| 操作系统 | macOS / Linux / Windows | 跨平台支持 |

### 核心技术

- **TypeScript/JavaScript**: 主要开发语言
- **Node.js Runtime**: 服务端运行环境
- **Terminal UI**: 基于终端的用户界面
- **WebSocket**: 实时通信（IDE 集成）

### 依赖工具

| 工具 | 用途 |
|------|------|
| `git` | 版本控制集成 |
| `gh` (GitHub CLI) | GitHub 操作 |
| `ripgrep (rg)` | 快速文件搜索 |
| 各语言运行时 | Python、Rust 等（按需） |

### 架构组件

```
┌─────────────────────────────────────────────────────────┐
│                    Claude Code Core                      │
├─────────────────────────────────────────────────────────┤
│  Terminal UI  │  IDE Integration  │  GitHub Action      │
├─────────────────────────────────────────────────────────┤
│                   Agent Framework                        │
├─────────────────────────────────────────────────────────┤
│  Tool Layer: Read, Write, Bash, Grep, Glob, Web...      │
├─────────────────────────────────────────────────────────┤
│  Plugin System: Commands, Agents, Skills, Hooks, MCP   │
├─────────────────────────────────────────────────────────┤
│  Settings & Configuration Management                     │
└─────────────────────────────────────────────────────────┘
```

---

## 目录结构说明

```
claude-code/
├── .claude/                          # Claude 配置目录
│   └── commands/                     # 自定义命令
│       ├── commit-push-pr.md         # 提交推送 PR 命令
│       ├── dedupe.md                 # 去重命令
│       └── triage-issue.md           # Issue 分类命令
│
├── .claude-plugin/                   # 插件市场配置
│   └── marketplace.json              # 官方插件列表
│
├── .devcontainer/                    # 开发容器配置
│   ├── devcontainer.json             # 容器定义
│   └── Dockerfile                    # 容器镜像
│
├── .github/                          # GitHub 配置
│   ├── ISSUE_TEMPLATE/               # Issue 模板
│   │   ├── bug_report.yml
│   │   ├── feature_request.yml
│   │   └── ...
│   └── workflows/                    # GitHub Actions
│       ├── claude.yml                # Claude 自动化
│       ├── issue-lifecycle.yml       # Issue 生命周期
│       └── ...
│
├── examples/                         # 示例配置
│   ├── hooks/                        # Hooks 示例
│   │   └── bash_command_validator_example.py
│   └── settings/                     # 设置示例
│       ├── settings-lax.json         # 宽松配置
│       ├── settings-strict.json      # 严格配置
│       └── settings-bash-sandbox.json # 沙箱配置
│
├── plugins/                          # 官方插件集合
│   ├── agent-sdk-dev/                # Agent SDK 开发工具
│   ├── code-review/                  # 代码审查插件
│   ├── commit-commands/              # Git 提交命令
│   ├── feature-dev/                  # 功能开发工作流
│   ├── frontend-design/              # 前端设计插件
│   ├── hookify/                      # Hooks 创建工具
│   ├── plugin-dev/                   # 插件开发工具包
│   ├── pr-review-toolkit/            # PR 审查工具包
│   ├── security-guidance/            # 安全指导插件
│   └── ...                           # 更多插件
│
├── scripts/                          # 辅助脚本
│   ├── gh.sh                         # GitHub CLI 封装
│   ├── sweep.ts                      # Issue 自动管理
│   └── issue-lifecycle.ts            # Issue 生命周期管理
│
├── Script/                           # 平台特定脚本
│   └── run_devcontainer_claude_code.ps1
│
├── README.md                         # 项目说明
├── CHANGELOG.md                      # 变更日志
├── LICENSE.md                        # 许可协议
└── SECURITY.md                       # 安全政策
```

### 关键目录详解

#### `.claude/` - 用户配置
存储项目级别的 Claude 配置，包括自定义命令、技能和设置。

#### `plugins/` - 插件集合
包含官方维护的插件，每个插件都是独立的功能模块：
- **feature-dev**: 7 阶段功能开发工作流
- **hookify**: 简易 Hooks 创建工具
- **code-review**: 多 Agent 并行代码审查
- **pr-review-toolkit**: PR 审查专家 Agent 集合

#### `examples/` - 配置示例
提供各类配置模板，帮助快速上手：
- **settings**: 不同安全级别的设置示例
- **hooks**: Bash 命令验证等 Hooks 示例

---

## 安装和使用方法

### 安装方式

#### macOS / Linux（推荐）

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

#### Homebrew（macOS / Linux）

```bash
brew install --cask claude-code
```

#### Windows（推荐）

```powershell
irm https://claude.ai/install.ps1 | iex
```

#### WinGet（Windows）

```powershell
winget install Anthropic.ClaudeCode
```

#### NPM（已弃用）

```bash
npm install -g @anthropic-ai/claude-code
```

### 快速开始

1. **安装完成后**，导航到项目目录：
   ```bash
   cd /path/to/your/project
   ```

2. **启动 Claude Code**：
   ```bash
   claude
   ```

3. **开始对话**：
   ```
   > 帮我分析这个项目的结构
   > 创建一个用户认证功能
   > 修复这个 bug
   ```

### 常用命令

| 命令 | 描述 |
|------|------|
| `claude` | 启动交互式会话 |
| `claude --help` | 查看帮助 |
| `claude --version` | 查看版本 |
| `claude --model <model>` | 指定模型 |
| `claude --resume` | 恢复上次会话 |

### IDE 集成

Claude Code 支持以下 IDE 集成：
- **VS Code**: 通过官方扩展
- **Cursor**: 内置支持
- **JetBrains**: 通过终端集成

---

## 配置说明

### 配置文件层次

Claude Code 支持多级配置，优先级从低到高：

```
系统默认 < 用户全局 < 项目级 < 工作树级 < 命令行参数
```

### 配置文件位置

| 级别 | 位置 | 说明 |
|------|------|------|
| 全局 | `~/.claude/settings.json` | 用户级配置 |
| 项目 | `.claude/settings.json` | 项目级配置 |
| 本地 | `.claude/settings.local.json` | 本地覆盖（不提交） |
| 管理 | `managed-settings.json` | 企业强制策略 |

### 核心配置项

#### 权限控制

```json
{
  "permissions": {
    "disableBypassPermissionsMode": "disable",
    "ask": ["Bash"],
    "deny": ["WebSearch", "WebFetch"]
  }
}
```

#### 沙箱配置

```json
{
  "sandbox": {
    "enabled": true,
    "autoAllowBashIfSandboxed": false,
    "network": {
      "allowLocalBinding": false,
      "allowedDomains": ["api.example.com"]
    }
  }
}
```

#### 模型配置

```json
{
  "model": "claude-sonnet-4-5-20250929",
  "modelOverrides": {
    "opus": "arn:aws:bedrock:us-east-1:xxx:inference-profile/xxx"
  }
}
```

### 配置示例

#### 宽松配置 (`settings-lax.json`)

适用于个人开发，限制较少：
```json
{
  "permissions": {
    "disableBypassPermissionsMode": "disable"
  },
  "strictKnownMarketplaces": []
}
```

#### 严格配置 (`settings-strict.json`)

适用于企业环境，严格管控：
```json
{
  "permissions": {
    "disableBypassPermissionsMode": "disable",
    "ask": ["Bash"],
    "deny": ["WebSearch", "WebFetch"]
  },
  "allowManagedPermissionRulesOnly": true,
  "allowManagedHooksOnly": true,
  "sandbox": {
    "autoAllowBashIfSandboxed": false
  }
}
```

#### Bash 沙箱配置 (`settings-bash-sandbox.json`)

强制 Bash 命令在沙箱中运行：
```json
{
  "allowManagedPermissionRulesOnly": true,
  "sandbox": {
    "enabled": true,
    "autoAllowBashIfSandboxed": false,
    "allowUnsandboxedCommands": false
  }
}
```

---

## 插件系统

### 插件架构

Claude Code 插件系统支持以下扩展点：

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json          # 插件元数据
├── commands/                # 斜杠命令
├── agents/                  # 专用 Agent
├── skills/                  # Agent 技能
├── hooks/                   # 事件钩子
├── .mcp.json                # MCP 配置
└── README.md                # 插件文档
```

### 官方插件列表

| 插件 | 类别 | 描述 |
|------|------|------|
| **agent-sdk-dev** | 开发 | Claude Agent SDK 开发工具包 |
| **code-review** | 效率 | 多 Agent 并行代码审查 |
| **commit-commands** | 效率 | Git 提交工作流自动化 |
| **feature-dev** | 开发 | 7 阶段功能开发工作流 |
| **frontend-design** | 开发 | 高质量前端设计生成 |
| **hookify** | 效率 | 简易 Hooks 创建工具 |
| **plugin-dev** | 开发 | 插件开发完整工具包 |
| **pr-review-toolkit** | 效率 | PR 审查专家 Agent 集合 |
| **security-guidance** | 安全 | 代码安全提醒钩子 |
| **ralph-wiggum** | 开发 | 自主迭代循环开发 |

### 使用插件

#### 安装插件

```bash
# 从市场安装
/plugin install plugin-name

# 本地开发测试
claude --plugin-dir /path/to/plugin
```

#### 使用插件命令

```bash
# 功能开发
/feature-dev 添加用户登录功能

# 代码审查
/code-review

# PR 审查
/pr-review-toolkit:review-pr

# 提交代码
/commit
```

### 开发插件

参考 `plugins/plugin-dev` 插件，提供完整的插件开发指导：
- Hook 开发技能
- MCP 集成技能
- 命令开发技能
- Agent 开发技能
- 技能开发技能

---

## Hooks 系统

### 什么是 Hooks

Hooks 是事件驱动的拦截器，可以在特定事件发生前/后执行自定义逻辑。

### 支持的事件类型

| 事件 | 触发时机 | 用途 |
|------|----------|------|
| `PreToolUse` | 工具调用前 | 验证、修改工具输入 |
| `PostToolUse` | 工具调用后 | 处理工具输出 |
| `SessionStart` | 会话开始时 | 注入上下文、设置 |
| `SessionEnd` | 会话结束时 | 清理、总结 |
| `Stop` | Agent 想要停止时 | 检查完成条件 |
| `Prompt` | 用户提交提示时 | 预处理用户输入 |

### Hook 配置示例

#### Bash 命令验证器

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "python3 /path/to/validator.py"
          }
        ]
      }
    ]
  }
}
```

#### Python 验证器示例

```python
#!/usr/bin/env python3
import json
import re
import sys

_VALIDATION_RULES = [
    (r"^grep\b(?!.*\|)", "使用 'rg' 代替 'grep' 以获得更好性能"),
    (r"^find\s+\S+\s+-name\b", "使用 'rg --files' 代替 'find -name'"),
]

def main():
    input_data = json.load(sys.stdin)
    command = input_data.get("tool_input", {}).get("command", "")
    
    for pattern, message in _VALIDATION_RULES:
        if re.search(pattern, command):
            print(f"• {message}", file=sys.stderr)
            sys.exit(2)  # 阻止工具调用

if __name__ == "__main__":
    main()
```

### Hookify 插件

使用 `hookify` 插件可以简化 Hooks 创建：

```bash
# 创建规则
/hookify 警告我使用 rm -rf 命令

# 列出所有规则
/hookify:list

# 配置规则
/hookify:configure
```

规则文件格式（`.claude/hookify.warn-rm.local.md`）：

```markdown
---
name: block-dangerous-rm
enabled: true
event: bash
pattern: rm\s+-rf
action: block
---

⚠️ **危险命令检测！**

此命令可能删除重要文件，请确认路径正确。
```

---

## 示例说明

### 示例 1: 功能开发工作流

使用 `feature-dev` 插件进行结构化功能开发：

```bash
/feature-dev 添加 OAuth 用户认证
```

**7 阶段流程**：
1. **发现**: 澄清需求，理解要构建什么
2. **探索**: 分析现有代码库和模式
3. **提问**: 解决所有模糊点
4. **设计**: 提供多个架构方案
5. **实现**: 编写代码
6. **审查**: 质量检查和修复
7. **总结**: 记录完成内容

### 示例 2: 自动化代码审查

使用 `code-review` 插件审查 PR：

```bash
/code-review --comment
```

**审查流程**：
- 启动 4 个并行 Agent 独立审查
- 检查 CLAUDE.md 规范合规性
- 扫描明显 Bug
- 分析 Git 历史上下文
- 置信度评分（阈值 80）
- 过滤误报，输出高质量反馈

### 示例 3: Git 工作流自动化

使用内置命令自动化 Git 操作：

```bash
# 提交、推送、创建 PR 一步完成
/commit-push-pr
```

**执行内容**：
1. 创建新分支（如果在 main 上）
2. 生成规范的提交信息
3. 推送到远程
4. 创建 Pull Request

### 示例 4: Issue 自动管理

GitHub Actions 自动管理 Issue 生命周期：

```yaml
# .github/workflows/claude.yml
on:
  issues:
    types: [opened, assigned]
  issue_comment:
    types: [created]

jobs:
  claude:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

### 示例 5: 自定义 Hook

创建自定义 Bash 命令验证器：

```bash
# 使用 hookify 创建规则
/hookify 在 TypeScript 文件中不要使用 console.log
```

生成的规则文件：
```markdown
---
name: warn-console-log
enabled: true
event: file
pattern: console\.log\(
action: warn
---

🐛 **调试代码检测**

提交前请移除调试语句。
```

---

## GitHub 集成

### GitHub Actions 集成

Claude Code 提供官方 GitHub Action，可在 CI/CD 流程中使用：

```yaml
- name: Run Claude Code
  uses: anthropics/claude-code-action@v1
  with:
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    claude_args: "--model claude-sonnet-4-5-20250929"
```

### @claude 提及集成

在 GitHub Issue 和 PR 中提及 `@claude` 可触发自动化处理：

- **Issue 分类**: 自动标记和分配
- **PR 审查**: 自动化代码审查
- **问题回复**: 自动回答常见问题

### GitHub CLI 集成

项目包含 `scripts/gh.sh` 封装脚本，提供受限的 GitHub CLI 访问：

```bash
# 查看 Issue
./scripts/gh.sh issue view 123

# 列出 Issue
./scripts/gh.sh issue list --state open

# 搜索 Issue
./scripts/gh.sh search issues "bug report" --limit 10

# 列出标签
./scripts/gh.sh label list --limit 100
```

### Issue 生命周期管理

`sweep.ts` 脚本自动管理 Issue 状态：

- **标记过期**: 长时间无活动的 Issue 标记为 stale
- **自动关闭**: 过期 Issue 自动关闭
- **热度保护**: 高热度 Issue 不自动关闭

---

## 贡献指南

### 报告问题

使用以下方式报告问题：

1. **在 Claude Code 内**: 使用 `/bug` 命令
2. **GitHub Issue**: [提交 Issue](https://github.com/anthropics/claude-code/issues)

### Issue 模板

项目提供以下 Issue 模板：

| 模板 | 用途 |
|------|------|
| Bug 报告 | 报告功能问题 |
| 功能请求 | 请求新功能 |
| 文档问题 | 报告文档错误 |
| 模型行为 | 报告模型相关问题 |

### 开发环境

#### 使用 Dev Container

项目包含完整的 Dev Container 配置：

```bash
# 在 VS Code 中
# 命令面板 > Dev Containers: Reopen in Container
```

#### 本地开发

```bash
# 克隆仓库
git clone https://github.com/anthropics/claude-code.git

# 安装依赖
npm install

# 运行开发模式
npm run dev
```

### 代码规范

遵循项目的编码规范：

- **命名**: camelCase（JS/TS）或 snake_case（Python）
- **格式化**: 使用 Prettier
- **提交**: 遵循 Conventional Commits
- **测试**: 新功能必须包含测试

### 提交规范

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Type 列表**:
- `feat`: 新功能
- `fix`: Bug 修复
- `docs`: 文档更新
- `refactor`: 重构
- `test`: 测试相关
- `chore`: 构建/工具

---

## 常见问题

### Q: Claude Code 与 Claude 网页版有什么区别？

**A**: Claude Code 是专为编程场景设计的终端工具，具有：
- 代码库理解和文件操作能力
- Git 和开发工具集成
- 插件系统扩展功能
- 终端和 IDE 原生体验

### Q: 如何保护代码隐私？

**A**: Claude Code 的数据处理政策：
- 代码反馈用于产品改进，不用于模型训练
- 敏感数据有限保留期
- 企业用户可配置更严格的数据策略
- 沙箱模式限制数据访问范围

### Q: 如何自定义模型？

**A**: 使用以下方式切换模型：
```bash
# 命令行参数
claude --model claude-opus-4-6-20250929

# 会话内命令
/model claude-sonnet-4-5-20250929

# 配置文件
{
  "model": "claude-sonnet-4-5-20250929"
}
```

### Q: 插件不工作怎么办？

**A**: 排查步骤：
1. 确认插件已安装：`/plugin list`
2. 检查插件目录结构是否正确
3. 查看 `.claude-plugin/plugin.json` 元数据
4. 尝试重启 Claude Code
5. 查看日志输出：`claude --verbose`

### Q: 如何调试 Hooks？

**A**: 调试方法：
1. 启用详细模式：`claude --verbose`
2. 检查 Hook 文件语法（JSON/YAML）
3. 验证事件匹配器是否正确
4. 测试 Hook 脚本独立运行
5. 查看 Hook 执行日志

### Q: 沙箱模式有什么限制？

**A**: 沙箱限制包括：
- 文件系统访问范围受限
- 网络访问需要明确允许
- 某些系统命令被阻止
- 进程创建受限制

### Q: 如何在团队中共享配置？

**A**: 推荐方式：
1. 将 `.claude/settings.json` 提交到版本控制
2. 使用 `.claude/settings.local.json` 存储个人配置（不提交）
3. 企业用户可使用 `managed-settings.json` 统一策略

---

## 附录

### 相关资源

- **官方文档**: https://code.claude.com/docs
- **Discord 社区**: https://anthropic.com/discord
- **GitHub 仓库**: https://github.com/anthropics/claude-code
- **NPM 包**: https://www.npmjs.com/package/@anthropic-ai/claude-code

### 版本信息

- **当前版本**: 参见 [CHANGELOG.md](./CHANGELOG.md)
- **更新频率**: 每周多次更新
- **更新方式**: 自动更新或手动运行 `claude --update`

### 许可协议

© Anthropic PBC. 保留所有权利。
使用受 [商业服务条款](https://www.anthropic.com/legal/commercial-terms) 约束。

---

*本文档由 Claude Code 项目维护，最后更新时间：2026 年 3 月*
