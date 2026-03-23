# Claude Code 项目文档

> 本文档中心提供 Claude Code 项目的完整使用说明，包括安装配置、命令参考、插件开发、最佳实践等。

## 📚 文档导航

### 快速入门

| 文档 | 说明 |
|------|------|
| [PROJECT.md](./PROJECT.md) | 项目总览，功能特性、技术架构、配置说明 |
| [快速入门](./guides/快速入门.md) | 安装步骤、首次配置、基本使用 |
| [命令参考](./commands/命令参考.md) | 内置命令、自定义命令、插件命令大全 |
| [常见问题](./PROJECT.md#常见问题) | FAQ 与故障排查 |

### 核心功能

| 文档 | 说明 |
|------|------|
| [插件系统](./plugins/插件概览.md) | 插件概览、安装使用、开发指南 |
| [Hooks 系统](./PROJECT.md#hooks-系统) | 事件拦截器配置与开发 |
| [配置说明](./PROJECT.md#配置说明) | 权限控制、沙箱配置、模型选择 |
| [GitHub 集成](./github/GitHub 集成.md) | Actions、@claude 提及、CLI 封装 |

### 开发指南

| 文档 | 说明 |
|------|------|
| [插件开发](./plugins/plugin-dev/插件开发工具包.md) | 插件开发工具包、7 阶段创建流程 |
| [脚本工具](./scripts/脚本工具.md) | 自动化脚本使用说明 |
| [开发容器](./devcontainer/开发容器配置.md) | Dev Container 配置与使用 |
| [最佳实践](./guides/最佳实践.md) | 高效使用技巧与规范 |

---

## 📁 文档结构

```
docs/
├── README.md                    # 本文档导航页
├── PROJECT.md                   # 项目总览文档
│
├── guides/                      # 使用指南
│   ├── 快速入门.md
│   ├── 最佳实践.md
│   └── 故障排查.md
│
├── commands/                    # 命令参考
│   ├── 命令参考.md              # 命令索引
│   └── custom/                  # 自定义命令
│       ├── 提交推送 PR.md
│       ├── Issue 去重.md
│       └── Issue 分类.md
│
├── plugins/                     # 插件文档
│   ├── 插件概览.md
│   ├── code-review/
│   │   └── 代码审查插件.md
│   ├── feature-dev/
│   │   └── 功能开发插件.md
│   ├── hookify/
│   │   └── Hookify 插件.md
│   ├── plugin-dev/
│   │   └── 插件开发工具包.md
│   ├── pr-review-toolkit/
│   │   └── PR 审查工具插件.md
│   ├── ralph-wiggum/
│   │   └── 自主迭代循环插件.md
│   └── security-guidance/
│       └── 安全指导插件.md
│
├── scripts/                     # 脚本工具
│   ├── 脚本工具.md
│   ├── sweep-Issue 清理.md
│   ├── issue-lifecycle-Issue 生命周期.md
│   └── auto-close-重复 Issue 关闭.md
│
├── github/                      # GitHub 配置
│   └── GitHub 集成.md
│
├── devcontainer/                # 开发容器
│   └── 开发容器配置.md
│
└── examples/                    # 示例配置
    └── 配置示例.md
```

---

## 🚀 快速链接

### 安装与配置
- [安装指南](./PROJECT.md#安装和使用方法) - macOS/Linux/Windows 安装方法
- [快速开始](./PROJECT.md#快速开始) - 首次使用步骤
- [配置说明](./PROJECT.md#配置说明) - 权限、沙箱、模型配置

### 常用命令
- `/feature-dev` - 7 阶段功能开发工作流
- `/code-review` - 自动化代码审查
- `/commit-push-pr` - 提交、推送、创建 PR
- `/hookify` - 创建自定义 Hooks
- `/plugin-dev:create-plugin` - 创建新插件

### 插件开发
- [插件结构](./plugins/plugin-dev/skills/plugin-structure/README.md)
- [Hook 开发](./plugins/plugin-dev/skills/hook-development/README.md)
- [Agent 开发](./plugins/plugin-dev/skills/agent-development/README.md)
- [Skill 开发](./plugins/plugin-dev/skills/skill-development/README.md)
- [MCP 集成](./plugins/plugin-dev/skills/mcp-integration/README.md)

### GitHub 自动化
- [Issue 生命周期管理](./scripts/issue-lifecycle-Issue 生命周期.md)
- [自动关闭重复 Issue](./scripts/auto-close-重复 Issue 关闭.md)
- [Sweep 自动管理](./scripts/sweep-Issue 清理.md)

---

## 📖 推荐阅读顺序

### 新手用户
1. [项目总览](./PROJECT.md) - 了解 Claude Code 能做什么
2. [快速入门](./guides/快速入门.md) - 安装并运行第一次
3. [命令参考](./commands/命令参考.md) - 学习可用命令
4. [最佳实践](./guides/最佳实践.md) - 提升使用效率

### 插件开发者
1. [插件系统概览](./plugins/插件概览.md) - 了解插件架构
2. [插件开发工具包](./plugins/plugin-dev/插件开发工具包.md) - 使用开发工具
3. [插件结构说明](./plugins/plugin-dev/skills/plugin-structure/README.md) - 学习组件模式
4. [Hook 开发指南](./plugins/plugin-dev/skills/hook-development/README.md) - 创建事件拦截器

### 高级用户
1. [Hooks 系统](./PROJECT.md#hooks-系统) - 自定义行为拦截
2. [配置详解](./PROJECT.md#配置说明) - 精细控制权限
3. [GitHub 集成](./github/GitHub 集成.md) - 自动化工作流
4. [故障排查](./guides/故障排查.md) - 解决常见问题

---

## 🔗 外部资源

| 资源 | 链接 |
|------|------|
| 官方文档 | https://code.claude.com/docs |
| Discord 社区 | https://anthropic.com/discord |
| GitHub 仓库 | https://github.com/anthropics/claude-code |
| NPM 包 | https://www.npmjs.com/package/@anthropic-ai/claude-code |

---

## 📝 贡献文档

欢迎贡献文档！可以通过以下方式帮助改进：

1. **报告问题**: 发现文档错误或不清晰，使用 `/bug` 命令或提交 Issue
2. **补充示例**: 为命令、插件添加更多使用示例
3. **翻译文档**: 帮助翻译为非英语语言
4. **完善细节**: 为缺少文档的文件添加说明

### 文档规范

- 使用 Markdown 格式
- 标题使用中文
- 代码示例保持可执行
- 包含必要的参数说明和返回值说明
- 复杂流程配合步骤说明

---

*最后更新：2026 年 3 月*
