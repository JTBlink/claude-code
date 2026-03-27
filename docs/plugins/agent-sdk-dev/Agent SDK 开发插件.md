# Agent SDK 开发插件

> Claude Agent SDK 应用开发的完整工具包，支持 Python 和 TypeScript。

## 概述

Agent SDK 开发插件简化了构建 Agent SDK 应用的整个生命周期，从初始脚手架到最佳实践验证。它帮助你快速启动新项目，确保应用遵循官方文档模式。

## 组件

### 命令: `/new-sdk-app`

交互式创建新的 Claude Agent SDK 应用。

**功能**:
- 询问项目相关问题（语言、名称、Agent 类型、起点）
- 检查并安装最新 SDK 版本
- 创建所有必要的项目文件和配置
- 设置环境文件（.env.example、.gitignore）
- 提供适合你用例的工作示例
- 运行类型检查（TypeScript）或语法验证（Python）
- 自动使用适当的验证 Agent 验证设置

**使用方式**:
```bash
/new-sdk-app my-project-name

# 或交互式创建
/new-sdk-app
```

**交互式问题**:
1. 语言选择（TypeScript 或 Python）
2. 项目名称（如未提供）
3. Agent 类型（coding、business、custom）
4. 起点模式（minimal、basic 或特定示例）
5. 工具偏好（npm/yarn/pnpm 或 pip/poetry）

**示例**:
```bash
/new-sdk-app customer-support-agent
# → 创建新的 Agent SDK 项目
# → 设置 TypeScript 或 Python 环境
# → 安装最新 SDK 版本
# → 自动验证设置
```

---

### Agent: `agent-sdk-verifier-py`

全面验证 Python Agent SDK 应用的设置和最佳实践。

**验证检查**:
- SDK 安装和版本
- Python 环境设置（requirements.txt、pyproject.toml）
- 正确的 SDK 使用和模式
- Agent 初始化和配置
- 环境和安全（.env、API keys）
- 错误处理和功能
- 文档完整性

**使用时机**:
- 创建新的 Python SDK 项目后
- 修改现有 Python SDK 应用后
- 部署 Python SDK 应用前

**使用方式**:
```
"验证我的 Python Agent SDK 应用"
"检查我的 SDK 应用是否遵循最佳实践"
```

**输出报告包含**:
- 整体状态（PASS / PASS WITH WARNINGS / FAIL）
- 阻止功能的关键问题
- 非最佳模式的警告
- 通过的检查列表
- 带有 SDK 文档引用的具体建议

---

### Agent: `agent-sdk-verifier-ts`

全面验证 TypeScript Agent SDK 应用的设置和最佳实践。

**验证检查**:
- SDK 安装和版本
- TypeScript 配置（tsconfig.json）
- 正确的 SDK 使用和模式
- 类型安全和导入
- Agent 初始化和配置
- 环境和安全（.env、API keys）
- 错误处理和功能
- 文档完整性

**使用时机**:
- 创建新的 TypeScript SDK 项目后
- 修改现有 TypeScript SDK 应用后
- 部署 TypeScript SDK 应用前

**使用方式**:
```
"验证我的 TypeScript Agent SDK 应用"
"检查我的 SDK 应用是否遵循最佳实践"
```

---

## 典型工作流程

```
1. 创建新项目
   /new-sdk-app code-reviewer-agent

2. 回答交互式问题
   语言: TypeScript
   Agent 类型: Coding agent (code review)
   起点: Basic agent with common features

3. 自动验证
   命令自动运行 agent-sdk-verifier-ts 确保正确设置

4. 开始开发
   echo "ANTHROPIC_API_KEY=your_key_here" > .env
   npm start

5. 修改后验证
   "验证我的 SDK 应用"
```

## 最佳实践

- **始终使用最新 SDK 版本**: `/new-sdk-app` 会检查并安装最新版本
- **部署前验证**: 在部署到生产环境前运行验证 Agent
- **保护 API 密钥安全**: 永远不要提交 `.env` 文件或硬编码 API 密钥
- **遵循 SDK 文档**: 验证 Agent 会检查是否符合官方模式
- **TypeScript 项目类型检查**: 定期运行 `npx tsc --noEmit`
- **测试你的 Agent**: 为 Agent 功能创建测试用例

## 故障排除

### TypeScript 项目类型错误

**问题**: 创建后 TypeScript 项目有类型错误

**解决方案**:
- `/new-sdk-app` 命令会自动运行类型检查
- 如果错误持续，检查是否使用最新 SDK 版本
- 验证 `tsconfig.json` 符合 SDK 要求

### Python 导入错误

**问题**: 无法从 `claude_agent_sdk` 导入

**解决方案**:
- 确保已安装依赖: `pip install -r requirements.txt`
- 如果使用虚拟环境，请激活它
- 检查 SDK 是否已安装: `pip show claude-agent-sdk`

### 验证失败带警告

**问题**: 验证 Agent 报告警告

**解决方案**:
- 查看报告中的具体警告
- 检查提供的 SDK 文档引用
- 警告不会阻止功能，但表明有改进空间

## 相关资源

- [Agent SDK 概述](https://docs.claude.com/en/api/agent-sdk/overview)
- [TypeScript SDK 参考](https://docs.claude.com/en/api/agent-sdk/typescript)
- [Python SDK 参考](https://docs.claude.com/en/api/agent-sdk/python)
- [Agent SDK 示例](https://docs.claude.com/en/api/agent-sdk/examples)
