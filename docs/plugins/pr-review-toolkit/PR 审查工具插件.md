# pr-review-toolkit 插件

> PR 审查专家 Agent 集合，提供多维度代码审查

## 功能说明

`pr-review-toolkit` 包含 6 个 specialized agents，每个专注于 PR 审查的不同方面，可以单独运行或组合使用。

## 安装方式

```bash
# 插件已包含在项目中
# 直接在会话中使用 /pr-review-toolkit:review-pr 命令
```

## 命令参考

### `/pr-review-toolkit:review-pr [方面]`

启动 PR 审查流程，可选择审查方面。

**使用方式**:
```bash
# 全面审查
/pr-review-toolkit:review-pr --aspect all

# 只审查测试
/pr-review-toolkit:review-pr --aspect tests

# 只审查类型设计
/pr-review-toolkit:review-pr --aspect types

# 组合审查
/pr-review-toolkit:review-pr --aspect tests,errors,types
```

**审查方面**:
| 方面 | Agent | 说明 |
|------|-------|------|
| `comments` | `comment-analyzer` | 评论分析 |
| `tests` | `pr-test-analyzer` | 测试覆盖分析 |
| `errors` | `silent-failure-hunter` | 静默失败检测 |
| `types` | `type-design-analyzer` | 类型设计审查 |
| `code` | `code-reviewer` | 代码质量审查 |
| `simplify` | `code-simplifier` | 代码简化建议 |
| `all` | 所有 Agent | 全面审查 |

**选项**:
| 选项 | 说明 | 默认值 |
|------|------|--------|
| `--aspect <方面>` | 审查方面（逗号分隔） | all |
| `--pr <编号>` | 指定 PR 编号 | 当前 PR |
| `--comment` | 添加评论到 PR | false |
| `--verbose` | 详细输出 | false |

---

## Agents

### comment-analyzer

**功能**: 评论分析

**职责**:
- 分析 PR 评论质量
- 识别未解决的讨论
- 检查评论回复情况
- 评估沟通效果

**检查项**:
```markdown
## 评论分析

### 未解决讨论
- # 关于错误处理的讨论（3 条回复，未结论）

### 待确认
- @maintainer 需要确认 API 变更

### 良好实践
- ✅ 所有评论都有回复
- ✅ 建设性反馈
```

**使用方式**:
```bash
@comment-analyzer 分析这个 PR 的评论
```

---

### pr-test-analyzer

**功能**: 测试覆盖分析

**职责**:
- 检查测试覆盖率
- 识别未测试的代码路径
- 评估测试质量
- 建议额外测试

**检查项**:
```markdown
## 测试分析

### 覆盖率
- 新增代码：85% 覆盖
- 修改代码：92% 覆盖

### 缺失测试
- ❌ 边界条件：空输入
- ❌ 异常路径：网络错误

### 测试质量
- ✅ 断言清晰
- ⚠️ 部分测试过于复杂
```

**使用方式**:
```bash
@pr-test-analyzer 审查这个 PR 的测试
```

---

### silent-failure-hunter

**功能**: 静默失败检测

**职责**:
- 检测被吞掉的异常
- 识别缺失的错误处理
- 查找无日志的失败路径
- 评估错误传播

**检查项**:
```markdown
## 静默失败检测

### 🔴 严重
```python
# ❌ 吞掉异常
try:
    risky_operation()
except:
    pass
```

### 🟠 警告
```python
# ⚠️ 缺少日志
try:
    db.update()
except Exception:
    return None  # 没有记录错误
```

### 建议修复
```python
# ✅ 正确处理
try:
    risky_operation()
except SpecificError as e:
    logger.error(f"操作失败：{e}")
    raise
```
```

**使用方式**:
```bash
@silent-failure-hunter 查找静默失败
```

---

### type-design-analyzer

**功能**: 类型设计审查

**职责**:
- 检查类型注解完整性
- 评估类型设计质量
- 识别类型安全问题
- 建议类型改进

**检查项**:
```markdown
## 类型设计

### 缺失注解
- `def process(data)` → `def process(data: dict) -> Result`
- `def get_user(id)` → `def get_user(id: int) -> Optional[User]`

### 类型问题
- ⚠️ 过度使用 `Any`
- ⚠️ 联合类型过于宽泛

### 良好实践
- ✅ 使用 TypedDict 表示复杂对象
- ✅ 使用 Enum 代替字符串字面量
```

**使用方式**:
```bash
@type-design-analyzer 审查类型设计
```

---

### code-reviewer

**功能**: 代码质量审查

**职责**:
- 代码规范检查
- 代码异味检测
- 复杂度分析
- 改进建议

**检查项**:
```markdown
## 代码质量

### 规范遵循
- ✅ 命名规范
- ⚠️ 格式化不一致
- ❌ 函数体过大（120 行）

### 代码异味
- 🔄 重复代码（3 处）
- 📦 过度耦合

### 复杂度
- 圈复杂度：15（建议<10）
- 认知复杂度：25（建议<20）
```

**使用方式**:
```bash
@code-reviewer 审查代码质量
```

---

### code-simplifier

**功能**: 代码简化建议

**职责**:
- 识别过度复杂的代码
- 建议简化方案
- 提供重构示例
- 评估简化效果

**检查项**:
```markdown
## 简化建议

### 当前代码
```python
# ❌ 复杂嵌套
if condition1:
    if condition2:
        if condition3:
            return do_something()
return None
```

### 建议简化
```python
# ✅ 提前返回
if not condition1:
    return None
if not condition2:
    return None
if not condition3:
    return None
return do_something()
```

### 效果
- 嵌套深度：3 → 0
- 可读性：提升 40%
```

**使用方式**:
```bash
@code-simplifier 简化这段代码
```

---

## 使用示例

### 示例 1: 全面审查

```bash
/pr-review-toolkit:review-pr --aspect all --comment
```

**输出**:
```markdown
# PR 审查报告

**PR**: #42 - 添加用户认证功能
**审查方面**: all
**审查时间**: 2026-03-18 14:30

---

## 📝 评论分析

- 未解决讨论：2
- 待确认事项：1

## 🧪 测试分析

- 覆盖率：85%
- 缺失测试：边界条件、异常路径

## 🐛 静默失败

- 严重问题：1
- 警告：3

## 📐 类型设计

- 缺失注解：5 处
- 类型问题：2 处

## 📊 代码质量

- 规范问题：3
- 代码异味：2

## ✂️ 简化建议

- 可简化函数：4
```

---

### 示例 2: 只审查测试

```bash
/pr-review-toolkit:review-pr --aspect tests
```

**输出**:
```markdown
# 测试审查报告

## 覆盖率概览

| 文件 | 新增行 | 覆盖行 | 覆盖率 |
|------|--------|--------|--------|
| auth.py | 120 | 102 | 85% |
| models.py | 50 | 50 | 100% |

## 缺失测试

### 边界条件
- [ ] 空输入处理
- [ ] 超长输入处理

### 异常路径
- [ ] 网络超时
- [ ] 数据库连接失败

## 建议

1. 添加边界条件测试
2. 添加异常场景测试
3. 考虑属性基测试
```

---

### 示例 3: 静默失败检测

```bash
/pr-review-toolkit:review-pr --aspect errors
```

**输出**:
```markdown
# 静默失败检测报告

## 🔴 严重问题

### 1. 吞掉的异常
**位置**: `src/auth.py:45`

```python
try:
    verify_token(token)
except:
    return False  # 吞掉所有异常
```

**修复建议**:
```python
try:
    verify_token(token)
except InvalidTokenError as e:
    logger.warning(f"Token 无效：{e}")
    return False
except Exception as e:
    logger.error(f"验证失败：{e}")
    raise
```

## 🟠 警告

### 1. 缺少日志
**位置**: `src/auth.py:78`

```python
if not user:
    return None  # 没有记录为什么
```

---

## 统计

- 严重问题：1
- 警告：3
- 建议：5
```

---

## 配置选项

### 自定义配置

在项目根目录创建 `.pr-review-toolkit.json`:

```json
{
  "aspects": {
    "comments": {
      "enabled": true,
      "checkUnresolved": true,
      "checkTone": true
    },
    "tests": {
      "enabled": true,
      "minCoverage": 80,
      "checkBoundaryConditions": true
    },
    "errors": {
      "enabled": true,
      "checkBareExcept": true,
      "checkLogging": true
    },
    "types": {
      "enabled": true,
      "requireAnnotations": true,
      "allowAny": false
    },
    "code": {
      "enabled": true,
      "maxFunctionLength": 50,
      "maxComplexity": 10
    },
    "simplify": {
      "enabled": true,
      "suggestRefactoring": true
    }
  },
  "output": {
    "format": "markdown",
    "addComments": false,
    "verbose": false
  }
}
```

### GitHub Actions 配置

```yaml
# .github/workflows/pr-review.yml
name: PR Review Toolkit

on:
  pull_request:
    types: [opened, synchronize, review_requested]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Run PR Review Toolkit
        uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            /pr-review-toolkit:review-pr --aspect all --comment
```

---

## 最佳实践

### 审查时机

| 时机 | 推荐方面 |
|------|----------|
| PR 创建 | all |
| 代码变更后 | tests, errors |
| 发布前 | all |
| 紧急修复 | errors, code |

### 阈值选择

| 项目类型 | 测试覆盖率 | 类型要求 |
|----------|------------|----------|
| 生产代码 | ≥80% | 严格 |
| 测试代码 | ≥70% | 宽松 |
| 原型 | ≥50% | 可选 |

### 反馈优先级

1. **严重问题**: 安全漏洞、功能 Bug、静默失败
2. **主要问题**: 测试缺失、类型问题、代码异味
3. **建议**: 简化、风格、优化

---

## 故障排查

### 问题：审查时间过长

**解决方案**:
```bash
# 指定审查方面
/pr-review-toolkit:review-pr --aspect tests

# 限制审查文件
/pr-review-toolkit:review-pr --files src/auth/*.py
```

### 问题：评论添加失败

**解决方案**:
```bash
# 检查权限
gh repo view

# 手动添加评论
/pr-review-toolkit:review-pr > review.md
```

### 问题：误报过多

**解决方案**:
```bash
# 调整配置
# 编辑 .pr-review-toolkit.json
{
  "aspects": {
    "code": {
      "maxFunctionLength": 100  # 放宽限制
    }
  }
}
```

---

## 相关文档

- [code-review 插件](./code-review/README.md)
- [feature-dev 插件](./feature-dev/README.md)
- [最佳实践](../guides/best-practices.md#代码审查)

---

*最后更新：2026 年 3 月*
