# hookify 插件

> 简易创建自定义 Hooks，防止不良行为

## 功能说明

`hookify` 插件提供了一套简单的系统来创建和管理自定义 Hooks，通过分析对话模式或明确指令来阻止 Claude Code 的不良行为。

## 安装方式

```bash
# 插件已包含在项目中
# 直接在会话中使用 /hookify 命令
```

## 命令参考

### `/hookify <规则>`

创建新的 Hook 规则。

**使用方式**:
```bash
# 创建警告规则
/hookify 警告我使用 rm -rf 命令

# 创建阻止规则
/hookify 阻止我使用 console.log

# 创建文件修改规则
/hookify 当我修改 .env 文件时提醒我
```

**规则类型**:
| 类型 | 说明 |
|------|------|
| `警告我...` | 触发时发出警告，但允许继续 |
| `阻止我...` | 触发时阻止操作 |
| `当我...时提醒我` | 特定条件下提醒 |

---

### `/hookify:list`

列出所有已创建的规则。

**输出示例**:
```
📋 Hookify 规则列表

1. block-dangerous-rm
   事件：bash
   模式：rm\s+-rf
   动作：block
   状态：✅ 启用

2. warn-console-log
   事件：file
   模式：console\.log\(
   动作：warn
   状态：✅ 启用

共 2 条规则
```

---

### `/hookify:configure`

配置现有规则。

**配置选项**:
- 启用/禁用规则
- 修改匹配模式
- 更改触发动作
- 删除规则

---

### `/hookify:help`

显示帮助信息和使用示例。

---

## Agent

### conversation-analyzer

**功能**: 分析对话识别问题行为

**触发条件**: 用户描述不良行为时

**处理流程**:
1. 分析用户描述
2. 识别行为模式
3. 生成 Hook 规则
4. 创建规则文件

**示例对话**:
```
用户：我总是在提交代码时忘记移除 console.log

@conversation-analyzer 帮我创建一个规则来提醒我

→ 自动创建 warn-console-log 规则
```

---

## Skill

### writing-rules

**功能**: Hook 规则语法指导

**规则文件格式**:

```markdown
---
name: <规则名称>
enabled: <true|false>
event: <事件类型>
pattern: <正则模式>
action: <动作类型>
---

<警告/阻止消息>
```

**字段说明**:

| 字段 | 说明 | 示例 |
|------|------|------|
| `name` | 规则唯一标识 | `block-dangerous-rm` |
| `enabled` | 是否启用 | `true` |
| `event` | 触发事件 | `bash`, `file`, `prompt` |
| `pattern` | 正则匹配模式 | `rm\s+-rf` |
| `action` | 触发动作 | `block`, `warn`, `notify` |

---

## 规则示例

### Bash 命令拦截

```markdown
---
name: block-rm-rf
enabled: true
event: bash
pattern: rm\s+-rf\s+/
action: block
---

⚠️ **危险命令！**

`rm -rf /` 会删除系统文件，此命令已被阻止。
```

### 文件修改监控

```markdown
---
name: warn-env-change
enabled: true
event: file
pattern: \.env$
action: warn
---

🔐 **环境文件修改！**

请确认：
1. 不包含真实密钥
2. 已添加到 .gitignore
3. 已更新 .env.example
```

### 提示词预处理

```markdown
---
name: block-insecure-code
enabled: true
event: prompt
pattern: 帮我写一个.*SQL.*拼接
action: block
---

🚫 **不安全代码请求！**

我无法帮助编写 SQL 拼接代码，这存在安全风险。

请使用参数化查询：
```python
cursor.execute("SELECT * FROM users WHERE id = ?", (user_id,))
```
```

### 工具调用前验证

```markdown
---
name: verify-git-push
enabled: true
event: PreToolUse
pattern: git\s+push
action: warn
---

📤 **Git Push 确认**

请确认：
1. 代码已审查
2. 测试已通过
3. 提交信息规范
```

---

## 事件类型

### bash

监控 Bash 命令执行。

**匹配对象**: 命令字符串

**示例**:
```yaml
event: bash
pattern: sudo\s+rm
```

### file

监控文件读写操作。

**匹配对象**: 文件路径或内容

**示例**:
```yaml
event: file
pattern: \.env$  # 匹配文件路径
```

### prompt

监控用户输入。

**匹配对象**: 提示词文本

**示例**:
```yaml
event: prompt
pattern: 如何.*SQL.*注入
```

### PreToolUse

工具调用前触发。

**匹配对象**: 工具名称和输入

**示例**:
```yaml
event: PreToolUse
matcher: Write
pattern: .*
```

### PostToolUse

工具调用后触发。

**匹配对象**: 工具输出

**示例**:
```yaml
event: PostToolUse
matcher: Bash
pattern: error  # 检测错误输出
```

### SessionStart

会话开始时触发。

**用途**: 注入上下文、设置

**示例**:
```yaml
event: SessionStart
pattern: .*
```

### SessionEnd

会话结束时触发。

**用途**: 清理、总结

---

## 动作类型

### block

阻止操作执行。

```yaml
action: block
```

### warn

发出警告，但允许继续。

```yaml
action: warn
```

### notify

仅通知，不中断。

```yaml
action: notify
```

### modify

修改输入后继续。

```yaml
action: modify
```

---

## 使用场景

### 场景 1：阻止危险命令

```bash
/hookify 阻止我执行 rm -rf / 命令
```

**生成规则**:
```markdown
---
name: block-rm-rf-root
enabled: true
event: bash
pattern: rm\s+-rf\s+/$
action: block
---

⚠️ **系统级危险命令！** 此命令已被阻止。
```

---

### 场景 2：警告调试代码

```bash
/hookify 警告我在代码中使用 console.log
```

**生成规则**:
```markdown
---
name: warn-console-log
enabled: true
event: file
pattern: console\.log\(
action: warn
---

🐛 **调试代码检测！**

提交前请移除 console.log 语句。
```

---

### 场景 3：监控敏感文件

```bash
/hookify 当我修改 .env 文件时提醒我检查是否提交
```

**生成规则**:
```markdown
---
name: warn-env-commit
enabled: true
event: file
pattern: \.env$
action: warn
---

🔐 **环境文件提醒！**

请确认：
- .env 已添加到 .gitignore
- 不包含真实密钥
- 已创建 .env.example
```

---

### 场景 4：安全编码指导

```bash
/hookify 当我请求编写 SQL 代码时提醒使用参数化查询
```

**生成规则**:
```markdown
---
name: sql-parameterized-reminder
enabled: true
event: prompt
pattern: SQL.*查询|SELECT.*FROM
action: notify
---

💡 **SQL 安全提示**

请使用参数化查询防止 SQL 注入：
```python
# ✅ 安全
cursor.execute("SELECT * FROM users WHERE id = ?", (user_id,))

# ❌ 不安全
cursor.execute(f"SELECT * FROM users WHERE id = {user_id}")
```
```

---

## 规则管理

### 规则文件位置

```
.claude/
├── hookify.block-rm-rf.local.md
├── hookify.warn-console-log.local.md
└── hookify.warn-env-change.md
```

**命名约定**:
- `hookify.<规则名>.local.md` - 本地规则（不提交）
- `hookify.<规则名>.md` - 项目规则（提交）

---

### 启用/禁用规则

**方法 1**: 编辑规则文件
```markdown
---
name: my-rule
enabled: false  # 禁用规则
---
```

**方法 2**: 使用命令
```bash
/hookify:configure
# 选择规则 → 禁用
```

---

### 测试规则

```bash
# 模拟触发规则
echo "rm -rf /" | python3 hooks/test-rule.py hookify.block-rm-rf.local.md

# 查看规则匹配
/hookify:list --test "rm -rf /tmp"
```

---

## 最佳实践

### 规则设计原则

1. **精确匹配**: 模式不要过于宽泛
2. **明确消息**: 清楚说明问题和解决方案
3. **适度拦截**: 避免过多干扰工作流
4. **渐进采用**: 先警告后阻止

### 规则组织

```
.claude/
├── hookify.security/           # 安全相关规则
│   ├── block-dangerous-commands.local.md
│   └── warn-sensitive-files.md
├── hookify.quality/            # 代码质量规则
│   ├── warn-debug-code.local.md
│   └── require-type-annotations.md
└── hookify.workflow/           # 工作流规则
    └── remind-git-best-practices.md
```

### 团队共享规则

```bash
# 创建团队规则
cat > .claude/hookify.team-rules.md << 'EOF'
---
name: team-security-rules
enabled: true
event: bash
pattern: sudo\s+rm
action: block
---

🚫 **团队安全规则** 禁止使用 sudo rm
EOF

# 提交到版本控制
git add .claude/hookify.team-rules.md
git commit -m "chore: 添加团队安全规则"
```

---

## 故障排查

### 问题：规则不生效

**诊断步骤**:
```bash
# 1. 检查规则文件语法
cat .claude/hookify.*.md | head -20

# 2. 验证 Frontmatter 格式
# 确保 --- 包裹 YAML

# 3. 测试规则匹配
/hookify:list --test "测试文本"

# 4. 检查规则状态
/hookify:list
```

### 问题：误报过多

**解决方案**:
```markdown
# 调整匹配模式，使其更精确

# ❌ 过于宽泛
pattern: rm

# ✅ 精确匹配
pattern: rm\s+-rf\s+(/|~|\$HOME)
```

### 问题：性能影响

**解决方案**:
- 减少规则数量
- 优化正则表达式
- 使用更具体的 matcher

---

## 相关文档

- [Hooks 系统](../PROJECT.md#hooks-系统)
- [Security Guidance](./security-guidance/安全指导插件.md)
- [命令参考](../commands/命令参考.md)

---

*最后更新：2026 年 3 月*
