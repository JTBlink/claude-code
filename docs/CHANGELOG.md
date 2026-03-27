# 更新日志

## 2.1.85

- 为 MCP `headersHelper` 脚本添加了 `CLAUDE_CODE_MCP_SERVER_NAME` 和 `CLAUDE_CODE_MCP_SERVER_URL` 环境变量，允许一个辅助程序服务多个服务器
- 为钩子添加了条件 `if` 字段，使用权限规则语法（例如 `Bash(git *)`）过滤何时运行，减少进程生成开销
- 在计划任务（`/loop`、`CronCreate`）触发时在转录中添加了时间戳标记
- 粘贴图像时在 `[Image #N]` 占位符后添加了尾随空格
- 深度链接查询（`claude-cli://open?q=…`）现在支持最多 5,000 个字符，对于长预填提示显示"滚动查看"警告
- MCP OAuth 现在遵循 RFC 9728 受保护资源元数据发现来查找授权服务器
- 被组织策略（`managed-settings.json`）阻止的插件现在无法安装或启用，并在市场视图中隐藏
- PreToolUse 钩子现在可以通过返回 `updatedInput` 和 `permissionDecision: "allow"` 来满足 `AskUserQuestion`，启用通过自己的 UI 收集答案的无头集成
- OpenTelemetry tool_result 事件中的 `tool_parameters` 现在需要 `OTEL_LOG_TOOL_DETAILS=1` 才会显示
- 修复了对话增长过大导致压缩请求本身无法容纳时 `/compact` 因"context exceeded"失败
- 修复了当插件的安装位置与设置中声明的位置不同时 `/plugin enable` 和 `/plugin disable` 失败
- 修复了在非 git 存储库中 `--worktree` 在 `WorktreeCreate` 钩子运行之前以错误退出
- 修复了 `deniedMcpServers` 设置未阻止 claude.ai MCP 服务器
- 修复了 computer-use 工具中的 `switch_display` 在多显示器设置上返回"not available in this session"
- 修复了当 `OTEL_LOGS_EXPORTER`、`OTEL_METRICS_EXPORTER` 或 `OTEL_TRACES_EXPORTER` 设置为 `none` 时崩溃
- 修复了非原生构建中 diff 语法高亮不工作
- 修复了当存在刷新令牌时 MCP 步骤授权失败 — 通过 `403 insufficient_scope` 请求提升范围的服务器现在正确触发重新授权流程
- 修复了流式响应被中断时远程会话中的内存泄漏
- 修复了通过在重试时使用新的 TCP 连接来修复边缘连接中断期间的持续 ECONNRESET 错误
- 修复了运行某些斜杠命令后提示卡在队列中，上箭头无法检索它们
- 修复了 Python Agent SDK：通过 `--mcp-config` 传递的 `type:'sdk'` MCP 服务器在启动期间不再被丢弃
- 修复了通过 SSH 或在 VS Code 集成终端中运行时原始键序列出现在提示中
- 修复了权限解决后远程控制会话状态卡在"Requires Action"
- 修复了 shift+enter 和 meta+enter 被预先输入建议拦截而不是插入换行符
- 修复了流式传输期间向上滚动时陈旧内容渗透
- 修复了在 Ghostty、Kitty、WezTerm 和其他支持 Kitty 键盘协议的终端中退出后终端留在增强键盘模式 — 退出后 Ctrl+C 和 Ctrl+D 现在正常工作
- 改进了大型存储库中 @-mention 文件自动完成的性能
- 改进了 PowerShell 危险命令检测
- 通过用纯 TypeScript 实现替换 WASM yoga-layout 改进了大型转录的滚动性能
- 减少了大型会话触发压缩时的 UI 卡顿

## 2.1.84

- 为 Windows 添加了 PowerShell 工具作为选择加入预览。详情请访问 https://code.claude.com/docs/en/tools-reference#powershell-tool
- 添加了 `ANTHROPIC_DEFAULT_{OPUS,SONNET,HAIKU}_MODEL_SUPPORTS` 环境变量，用于覆盖第三方（Bedrock、Vertex、Foundry）固定默认模型的 effort/thinking 能力检测，以及 `_MODEL_NAME`/`_DESCRIPTION` 来自定义 `/model` 选择器标签
- 添加了 `CLAUDE_STREAM_IDLE_TIMEOUT_MS` 环境变量来配置流空闲看门狗阈值（默认 90s）
- 添加了通过 `TaskCreate` 创建任务时触发的 `TaskCreated` 钩子
- 为 `type: "http"` 添加了 `WorktreeCreate` 钩子支持 — 通过响应 JSON 中的 `hookSpecificOutput.worktreePath` 返回创建的 worktree 路径
- 添加了 `allowedChannelPlugins` 托管设置，供团队/企业管理员定义通道插件白名单
- 向 API 请求添加了 `x-client-request-id` 头用于调试超时
- 添加了空闲返回提示，在 75+ 分钟后返回时引导用户使用 `/clear`，减少陈旧会话上不必要的令牌重新缓存
- 深度链接（`claude-cli://`）现在在您首选的终端中打开，而不是检测列表中碰巧第一个的终端
- 规则和技能 `paths:` frontmatter 现在接受 YAML glob 列表
- MCP 工具描述和服务器指令现在上限为 2KB，以防止 OpenAPI 生成的服务器膨胀上下文
- 本地和通过 claude.ai 连接器配置的 MCP 服务器现在去重 — 本地配置优先
- 似乎卡在交互式提示上的后台 bash 任务现在在约 45 秒后显示通知
- ≥1M 的令牌计数现在显示为"1.5m"而不是"1512.6k"
- 启用 `ToolSearch` 时全局系统提示缓存现在工作，包括配置了 MCP 工具的用户
- 修复了语音按键说话：按住语音键不再向文本输入泄漏字符，转录现在在正确位置插入
- 修复了当页脚项目获得焦点时上/下箭头键无响应
- 修复了 `Ctrl+U`（kill-to-line-start）在多行输入的行边界处无效，所以重复 `Ctrl+U` 现在跨行清除
- 修复了空取消绑定默认和弦绑定（例如 `"ctrl+x ctrl+k": null`）仍然进入和弦等待模式而不是释放前缀键
- 修复了鼠标事件在转录搜索输入中插入字面"mouse"文本
- 修复了当外部会话使用 `--json-schema` 且子代理也指定模式时工作流子代理因 API 400 失败
- 修复了某些终端上用户消息气泡中某些 emoji 后面缺少背景颜色
- 修复了对于有 `Edit(.claude)` 允许规则的用户，"允许 Claude 为此会话编辑自己的设置"权限选项不持久
- 修复了为大型编辑文件生成附件片段时挂起
- 修复了服务器重连时 MCP 工具/资源缓存泄漏
- 修复了部分克隆存储库（Scalar/GVFS）触发大规模 blob 下载的启动性能问题
- 修复了原生终端光标未跟踪文本输入插入符，所以 IME 组合（CJK 输入）现在内联渲染，屏幕阅读器可以跟随输入位置
- 修复了 macOS 上由于瞬态钥匙串读取失败导致的虚假"Not logged in"错误
- 修复了冷启动竞争，核心工具可能在没有激活绕过的情况下延迟，导致 Edit/Write 因类型参数上的 InputValidationError 失败
- 改进了对 Windows 驱动器根目录（`C:\`、`C:\Windows` 等）危险删除的检测
- 通过并行运行 `setup()` 与斜杠命令和代理加载，交互式启动改进约 30ms
- 改进了带有 MCP 服务器的 `claude "prompt"` 启动 — REPL 现在立即渲染而不是阻塞直到所有服务器连接
- 改进了远程控制以在被阻止时显示具体原因而不是通用的"not yet enabled"消息
- 改进了 p90 提示缓存率
- 通过使消息窗口免疫压缩和分组更改，减少了长会话中的滚动到顶部重置
- 减少了动画工具进度滚动到视口上方时的终端闪烁
- 更改了 issue/PR 引用仅在写为 `owner/repo#123` 时成为可点击链接 — 裸 `#123` 不再自动链接
- 当前认证设置不可用的斜杠命令（`/voice`、`/mobile`、`/chrome`、`/upgrade` 等）现在隐藏而不是显示
- [VSCode] 添加了速率限制警告横幅，显示使用百分比和重置时间
- 统计截图（/stats 中的 Ctrl+S）现在在所有构建中工作，速度快 16 倍

## 2.1.83

- 在 `managed-settings.json` 旁边添加了 `managed-settings.d/` 放置目录，允许独立团队部署按字母顺序合并的独立策略片段
- 为响应式环境管理（例如 direnv）添加了 `CwdChanged` 和 `FileChanged` 钩子事件
- 添加了 `sandbox.failIfUnavailable` 设置，当启用沙盒但无法启动时以错误退出，而不是在非沙盒状态下运行
- 添加了 `disableDeepLinkRegistration` 设置以防止 `claude-cli://` 协议处理程序注册
- 添加了 `CLAUDE_CODE_SUBPROCESS_ENV_SCRUB=1` 以从子进程环境（Bash 工具、钩子、MCP stdio 服务器）中剥离 Anthropic 和云提供商凭据
- 添加了转录搜索 — 在转录模式（`Ctrl+O`）中按 `/` 搜索，`n`/`N` 遍历匹配项
- 添加了 `Ctrl+X Ctrl+E` 作为打开外部编辑器的别名（readline 原生绑定；`Ctrl+G` 仍然有效）
- 粘贴的图像现在在光标处插入 `[Image #N]` 芯片，以便您可以在提示中按位置引用它们
- 代理现在可以在 frontmatter 中声明 `initialPrompt` 以自动提交第一轮
- `chat:killAgents` 和 `chat:fastMode` 现在可以通过 `~/.claude/keybindings.json` 重新绑定
- 修复了退出后鼠标跟踪转义序列泄漏到 shell 提示
- 修复了 macOS 上退出时 Claude Code 挂起
- 修复了空闲几秒钟后屏幕闪烁空白
- 修复了差异很少的大文件差异时挂起 — 差异现在在 5 秒后超时并优雅回退
- 修复了启用语音输入时启动时的 1-8 秒 UI 冻结，由急切加载原生音频模块引起
- 修复了 Claude Code 在继续之前等待约 3s 获取 claude.ai MCP 配置的启动回归
- 修复了 `--mcp-config` CLI 标志绕过 `allowedMcpServers`/`deniedMcpServers` 托管策略执行
- 修复了 claude.ai MCP 连接器（Slack、Gmail 等）在单轮 `--print` 模式下不可用
- 修复了 Claude Code 退出时 `caffeinate` 进程未正确终止，阻止 Mac 睡眠
- 修复了 tab 接受 `!` 前缀命令建议时 bash 模式未激活
- 修复了导航建议后陈旧的斜杠命令选择显示错误的高亮命令
- 修复了 `/config` 菜单同时显示搜索光标和列表选择
- 修复了上下文压缩后后台子代理变得不可见，这可能导致生成重复的代理
- 修复了清理期间 git 或 API 调用挂起时后台代理任务卡在"running"状态
- 修复了升级后首次启动时 `--channels` 显示"Channels are not currently available"
- 修复了卸载的插件钩子继续触发直到下一个会话
- 修复了流式响应期间排队命令闪烁
- 修复了处理消息时提交斜杠命令作为文本发送给模型
- 修复了折叠的读取/搜索组在滚动离屏后完成时滚动跳跃
- 修复了模型开始或停止思考时滚动跳跃到顶部
- 修复了由于钩子进度/附件消息分叉 parentUuid 链导致恢复时 SDK 会话历史丢失
- 修复了在终端窗口外释放鼠标时复制选择未触发
- 修复了项目溢出时高度受限列表中出现幽灵字符
- 修复了 `Ctrl+B` 在空闲提示时干扰 readline backward-char — 它现在仅在前台任务可以后台化时触发
- 修复了工具结果文件从未清理，忽略 `cleanupPeriodDays` 设置
- 修复了释放语音按住说话后空格键被吞掉长达 3 秒
- 修复了在没有音频硬件的 Linux 上使用语音模式时 ALSA 库错误破坏终端 UI（Docker、headless、WSL1）
- 修复了 Termux/Android 上的语音模式 SoX 检测，其中生成 `which` 受内核限制
- 修复了远程控制会话在主动运行时在 Web 会话列表中显示为空闲
- 修复了页脚导航在配置驱动模式下选择不可见的远程控制药丸
- 修复了远程会话中工具使用 ID 无限累积的内存泄漏
- 通过与其他引导工作并行获取配置文件，改进了 Bedrock SDK 冷启动延迟
- 改进了大型会话上的 `--resume` 内存使用和启动延迟
- 改进了插件启动 — 命令、技能和代理现在从磁盘缓存加载无需重新获取
- 改进了远程控制会话标题：AI 生成的标题现在在第一条消息后几秒内出现
- 改进了 `WebFetch` 以标识为 `Claude-User`，以便站点操作员可以通过 `robots.txt` 识别和允许列表 Claude Code 流量
- 减少了大型页面的 `WebFetch` 峰值内存使用
- 将长会话中的滚动重置从每轮一次减少到每约 50 条消息一次
- 使用未经身份验证的 HTTP/SSE MCP 服务器更快的 `claude -p` 启动（节省约 600ms）
- Bash 幽灵文本建议现在立即包含刚提交的命令
- 增加了非流式回退令牌上限（21k → 64k）和超时（120s → 300s 本地），所以回退请求不太可能被截断
- 在任何响应之前中断提示现在自动恢复您的输入，以便您可以编辑和重新提交
- `/status` 现在在 Claude 响应时工作，而不是排队直到轮次完成
- 复制组织管理连接器的插件 MCP 服务器现在被抑制而不是运行第二个连接
- Linux：注册 `claude-cli://` 协议处理程序时尊重 `XDG_DATA_HOME`
- 将"停止所有后台代理"键绑定从 `Ctrl+F` 更改为 `Ctrl+X Ctrl+K` 以停止遮蔽 readline forward-char
- 弃用了 `TaskOutput` 工具，改用 `Read` 读取后台任务的输出文件路径
- 添加了 `CLAUDE_CODE_DISABLE_NONSTREAMING_FALLBACK` 环境变量以在流式传输失败时禁用非流式回退
- 插件选项（`manifest.userConfig`）现在可外部使用 — 插件可以在启用时提示配置，`sensitive: true` 值存储在钥匙串（macOS）或受保护的凭据文件（其他平台）
- Claude 现在可以引用剪贴板粘贴图像的磁盘路径进行文件操作
- `Ctrl+L` 现在清除屏幕并强制完全重绘 — 当 Cmd+K 使 UI 部分空白时使用此功能恢复。使用 `Ctrl+U` 或双击 Esc 清除提示输入。
- `--bare -p`（SDK 模式）到 API 请求速度提高约 14%
- 内存：`MEMORY.md` 索引现在在 25KB 和 200 行处截断
- 当 `--channels` 激活时禁用 `AskUserQuestion` 和计划模式工具
- 修复了在失败的工具调用期间排队粘贴图像时的 API 400 错误
- 修复了 SSE 连接在调用中途断开并耗尽重连尝试时 MCP 工具调用无限期挂起
- 修复了后台代理在第一条用户消息之前完成时远程控制会话标题显示原始 XML
- 修复了由于恢复的转录链中进度消息间隙导致容器重启后远程会话忘记对话历史
- 修复了远程会话在瞬态认证错误时需要重新登录而不是自动重试
- 修复了 Linux 上沙盒模式下 `rg ... | wc -l` 和类似管道命令挂起并返回 `0`
- 修复了 CJK IME 插入全角空格时语音输入按住说话未激活
- 修复了 worktree 名称包含正斜杠时 `--worktree` 静默挂起
- [VSCode] 后端 60 秒未响应时旋转器现在变红并显示"Not responding"
- [VSCode] 修复了通过 URL 或重启后重新打开会话时会话历史未正确加载
- [VSCode] 添加了双击 Esc（或 `/rewind`）打开键盘可导航的回退选择器
- [VSCode] 修复了会话缓存过期后"从这里分叉对话"和回退操作静默失败

## 2.1.82

- 添加了 `CLAUDE_CODE_OAUTH_CLAIMS` 环境变量，用于在 OAuth 登录期间注入自定义 JWT 声明（例如团队 ID、组织 ID）
- 添加了 `CLAUDE_CODE_OAUTH_REDIRECT_PORT` 环境变量来覆盖 OAuth 回调端口（默认 8787）
- 为 `--mcp-config` 添加了 HTTP URL 支持 — 通过 HTTP/HTTPS 获取远程 MCP 配置文件
- 添加了 `sandbox.excludedCommands` 设置来从沙盒中排除特定 bash 命令
- 添加了 `OTEL_LOG_TOOL_DETAILS=1` 以在 OpenTelemetry 跨度中包含工具参数和结果
- 添加了 `CronDelete` 工具来取消计划的 cron 作业
- 添加了 `mcpServerName` 字段到 MCP 工具调用，在工具使用块中显示服务器名称
- 改进了 `/remote-control` 会话列表性能，使用虚拟化延迟渲染
- 改进了 `/model` 选择器来显示 Bedrock 推理配置文件 ARN
- 改进了代理错误消息来显示具体的工具验证错误而不是通用的"Invalid tool call"
- 修复了 1M 上下文窗口会话中 `/compact` 挂起
- 修复了具有许多并行工具调用的会话中 `--resume` 截断历史
- 修复了使用带有 `baseUrl` 配置的 Bedrock/Vertex SDK 时 `CLAUDE_CODE_USE_MCP_BUS` 环境变量被忽略
- 修复了使用 `bypassPermissions: true` 时权限对话框仍然出现
- 修复了 MCP 服务器的 `cacheResources` 和 `cacheTools` 配置选项被忽略
- 修复了 `--json-schema` 在 Windows 上创建文件失败，原因是无效的文件 URI 处理
- 修复了 `.mcp.json` 中的相对路径不相对于项目目录解析
- 修复了 `$HOME` 和 `~` 在 `.mcp.json` 路径中不被展开
- 修复了语音模式在 macOS 上创建重复的录制文件
- 修复了配置 MCP 工具时 `--statusline` 脚本被重复调用
- 修复了 `/stats` 对话框中的令牌计数不匹配状态栏
- 修复了启用 `voiceEnabled: true` 时按键声音播放两次
- 修复了 `/clear` 不清除排队的消息
- 修复了当前分支与基础分支相同时 `/pr` 失败并显示"no commits to create PR"
- 修复了 git 工作目录有未暂存更改时 `/pr` 创建空 PR
- 修复了使用 Git Bash 时 `/pr` 在 Windows 上失败
- 修复了 `/pr` 在 worktree 中不工作
- 修复了启用 MCP 工具时 `/effort` 选择器显示空列表
- 修复了 `/model` 选择器在 Claude 响应时不工作
- 修复了远程控制会话中屏幕共享初始化期间的竞争条件
- 修复了流式传输期间显示陈旧状态的遥测事件
- 修复了 `--json` 输出中 `sessionId` 不一致
- 修复了 `--resume` 在最近删除的会话上失败并显示"Session not found"
- 修复了 `--worktree` 使用现有的 worktree 而不是创建新的
- 修复了 `claude auth status` 显示缓存的订阅信息而不是实时获取
- 修复了 `claude --version` 不显示 git commit SHA
- 修复了使用 `--output` 时 `claude -p` 退出代码不正确
- 修复了启动时 `managed-settings.json` 验证错误不显示
- 修复了 `allowedMcpServers` 设置不匹配部分服务器名称
- 修复了 hooks 配置中相对路径不相对于项目目录解析
- 修复了 `Stop` 钩子在工具使用期间触发两次
- 修复了 `SessionEnd` 钩子接收陈旧的会话数据
- 修复了 `PreToolUse` 钩子不接收工具输入参数
- 修复了 `PostToolUse` 钩子在工具失败时不触发
- 修复了后台代理失败时 `Notification` 钩子不触发
- 修复了通过 `--mcp-config` 配置时 MCP 服务器按字母顺序而不是配置顺序加载
- 修复了 MCP 服务器首次连接失败后不重试
- 修复了 MCP 工具描述在服务器断开连接后保留在内存中
- 修复了启动时 MCP 服务器连接超时太短（5s → 30s）
- 修复了 MCP 资源订阅在服务器重连后不恢复
- 修复了 MCP 提示在会话期间不刷新
- 修复了使用 `-p` 标志时 MCP 工具不可用
- 改进了 MCP 服务器连接错误消息以显示具体原因
- 改进了 MCP 工具调用性能，通过并行化多个服务器的工具发现
- 改进了 MCP 资源读取以处理大文件
- 改进了 MCP 提示执行以支持参数验证
- 改进了 MCP 服务器健康检查间隔（60s → 30s）
- [VSCode] 修复了 MCP 服务器状态指示器不更新
- [VSCode] 修复了 MCP 工具列表不刷新
- [VSCode] 修复了 MCP 资源浏览器不显示嵌套资源
- [VSCode] 修复了 MCP 提示参数输入不验证

## 2.1.81

- 为脚本化的 `-p` 调用添加了 `--bare` 标志 — 跳过钩子、LSP、插件同步和技能目录遍历；需要通过 `--settings` 提供 `ANTHROPIC_API_KEY` 或 `apiKeyHelper`（OAuth 和钥匙串认证已禁用）；自动记忆功能完全禁用
- 添加了 `--channels` 权限中继 — 声明权限能力的通道服务器可以将工具批准提示转发到您的手机
- 修复了多个并发 Claude Code 会话在一个会话刷新其 OAuth 令牌时需要重复重新认证的问题
- 修复了语音模式静默吞掉重试失败并显示误导性的"检查您的网络"消息而不是实际错误的问题
- 修复了服务器静默丢弃 WebSocket 连接时语音模式音频无法恢复的问题
- 修复了 `CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS` 未抑制结构化输出 beta 头，导致转发到 Vertex/Bedrock 的代理网关出现 400 错误
- 修复了没有配置其他托管设置的团队/企业组织的 `--channels` 绕过问题
- 修复了 Node.js 18 上的崩溃问题
- 修复了字符串中包含破折号的 Bash 命令出现不必要的权限提示
- 修复了当插件目录在会话中间被删除时插件钩子阻止提示提交的问题
- 修复了后台代理任务输出在轮询间隔之间完成任务时可能无限期挂起的竞争条件
- 恢复在 worktree 中的会话现在会切换回该 worktree
- 修复了在活动响应期间使用时 `/btw` 未包含粘贴文本的问题
- 修复了在 tmux 下快速 Cmd+Tab 后粘贴可能击败剪贴板复制的竞争问题
- 修复了终端标签标题未使用自动生成的会话描述更新的问题
- 修复了不可见的钩子附件在转录模式下夸大消息计数的问题
- 修复了远程控制会话显示通用标题而不是从第一个提示派生的问题
- 修复了远程控制 `/exit` 不可靠归档会话的问题
- 改进了 MCP 读取/搜索工具调用，折叠为单个"已查询 {server}"行（使用 Ctrl+O 展开）
- 改进了 `!` bash 模式的可发现性 — 当您需要运行交互式命令时，Claude 现在会建议它
- 改进了插件新鲜度 — ref 跟踪的插件现在每次加载时重新克隆以获取上游更改
- 改进了远程控制会话标题在第三条消息后刷新
- 更新了 MCP OAuth 以支持没有动态客户端注册的服务器的客户端 ID 元数据文档（CIMD / SEP-991）
- 更改了计划模式默认隐藏"清除上下文"选项（使用 `"showClearContextOnPlanAccept": true` 恢复）
- 在 Windows 上禁用了逐行响应流式传输（包括 Windows 终端中的 WSL）由于渲染问题
- [VSCode] 修复了使用 Git Bash 时 Bash 工具的 Windows PATH 继承问题（v2.1.78 中的回归）

## 2.1.80

- 向状态行脚本添加了 `rate_limits` 字段，用于显示 Claude.ai 速率限制使用情况（5 小时和 7 天窗口，包含 `used_percentage` 和 `resets_at`）
- 添加了 `source: 'settings'` 插件市场源 — 在 settings.json 中内联声明插件条目
- 向插件提示添加了 CLI 工具使用检测，除了文件模式匹配
- 为技能和斜杠命令添加了 `effort` frontmatter 支持，以在调用时覆盖模型 effort 级别
- 添加了 `--channels`（研究预览）— 允许 MCP 服务器向您的会话推送消息
- 修复了 `--resume` 丢失并行工具结果 — 具有并行工具调用的会话现在恢复所有 tool_use/tool_result 对，而不是显示 `[Tool result missing]` 占位符
- 修复了非浏览器 TLS 指纹上 Cloudflare 机器人检测导致的语音模式 WebSocket 故障
- 修复了通过 API 代理、Bedrock 或 Vertex 使用细粒度工具流式传输时的 400 错误
- 修复了 `/remote-control` 出现在网关和第三方提供商部署中无法工作的问题
- 修复了 `/sandbox` 标签切换不响应 Tab 或箭头键的问题
- 改进了大型 git 存储库中 `@` 文件自动完成的响应性
- 改进了 `/effort` 以显示 auto 当前解析为什么，匹配状态栏指示器
- 改进了 `/permissions` — Tab 和箭头键现在可以从列表内切换标签
- 改进了后台任务面板 — 左箭头现在从列表视图关闭
- 简化了插件安装提示，使用单个 `/plugin install` 命令而不是两步流程
- 减少了大型存储库（250k 文件存储库节省约 80 MB）的启动内存使用
- 修复了从先前会话缓存 `remote-settings.json` 时启动时未应用托管设置（`enabledPlugins`、`permissions.defaultMode`、策略设置的环境变量）

## 2.1.79

- 向 `claude auth login` 添加了 `--console` 标志，用于 Anthropic Console（API 计费）认证
- 向 `/config` 菜单添加了"显示轮次持续时间"切换
- 修复了作为子进程生成时 `claude -p` 挂起而没有显式 stdin（例如 Python `subprocess.run`）
- 修复了在 `-p`（打印）模式下 Ctrl+C 不工作的问题
- 修复了在流式传输期间触发时 `/btw` 返回主代理输出而不是回答附带问题
- 修复了设置 `voiceEnabled: true` 时语音模式在启动时未正确激活的问题
- 修复了 `/permissions` 中的左/右箭头标签导航
- 修复了 `CLAUDE_CODE_DISABLE_TERMINAL_TITLE` 未阻止启动时设置终端标题
- 修复了工作区信任阻止时自定义状态行不显示任何内容
- 修复了企业用户无法在速率限制（429）错误上重试
- 修复了使用交互式 `/resume` 切换会话时 `SessionEnd` 钩子未触发
- 改进了启动内存使用量约 18MB
- 改进了非流式 API 回退，每次尝试超时 2 分钟，防止会话无限期挂起
- `CLAUDE_CODE_PLUGIN_SEED_DIR` 现在支持由平台路径分隔符（Unix 上为 `:`，Windows 上为 `;`）分隔的多个种子目录
- [VSCode] 添加了 `/remote-control` — 将您的会话桥接到 claude.ai/code 以从浏览器或手机继续
- [VSCode] 会话标签现在根据您的第一条消息获取 AI 生成的标题
- [VSCode] 修复了思考药丸在响应完成后显示"Thinking"而不是"Thought for Ns"
- [VSCode] 修复了从左侧边栏打开会话时缺少会话差异按钮

## 2.1.78

- 添加了 `StopFailure` 钩子事件，当轮次由于 API 错误（速率限制、认证失败等）结束时触发
- 添加了 `${CLAUDE_PLUGIN_DATA}` 变量，用于插件持久状态，可在插件更新后存活；`/plugin uninstall` 在删除前会提示
- 为插件提供的代理添加了 `effort`、`maxTurns` 和 `disallowedTools` frontmatter 支持
- 终端通知（iTerm2/Kitty/Ghostty 弹出窗口、进度条）现在在 tmux 内运行时到达外部终端，使用 `set -g allow-passthrough on`
- 响应文本现在在生成时逐行流式传输
- 修复了 Linux 上沙盒 Bash 内 `git log HEAD` 因"ambiguous argument"失败，以及存根文件污染工作目录中的 `git status`
- 修复了 `cc log` 和 `--resume` 在使用子代理的大型会话（>5 MB）上静默截断对话历史
- 修复了 API 错误触发阻止钩子时出现无限循环，这些钩子将阻止错误重新提供给模型
- 修复了 `deny: ["mcp__servername"]` 权限规则在发送给模型之前未删除 MCP 服务器工具，允许它看到并尝试阻止的工具
- 修复了 `sandbox.filesystem.allowWrite` 不适用于绝对路径（以前需要 `//` 前缀）
- 修复了 `/sandbox` 依赖项标签在 macOS 上显示 Linux 先决条件而不是 macOS 特定信息
- **安全：** 修复了设置 `sandbox.enabled: true` 但缺少依赖项时静默禁用沙盒 — 现在显示可见的启动警告
- 修复了在 `bypassPermissions` 模式下 `.git`、`.claude` 和其他受保护目录无需提示即可写入
- 修复了普通模式下的 ctrl+u 滚动而不是 readline kill-line（ctrl+u/ctrl+d 半页滚动仅移至转录模式）
- 修复了语音模式修饰符组合按键说话键绑定（例如 ctrl+k）需要按住而不是立即激活
- 修复了语音模式在 WSL2 和 WSLg（Windows 11）上不工作；WSL1/Win10 用户现在获得清晰的错误
- 修复了 `--worktree` 标志不从 worktree 目录加载技能和钩子
- 修复了 `CLAUDE_CODE_DISABLE_GIT_INSTRUCTIONS` 和 `includeGitInstructions` 设置未抑制系统提示中的 git 状态部分
- 修复了从 Dock/Spotlight 启动 VS Code 时 Bash 工具找不到 Homebrew 和其他 PATH 依赖的二进制文件
- 修复了在不支持真彩色的 VS Code/Cursor/code-server 终端中淡化的 Claude 橙色
- 添加了 `ANTHROPIC_CUSTOM_MODEL_OPTION` 环境变量，向 `/model` 选择器添加自定义条目，带有可选的 `_NAME` 和 `_DESCRIPTION` 后缀变量用于显示
- 修复了使用 Haiku 模型时 `ANTHROPIC_BETAS` 环境变量被静默忽略
- 修复了排队的提示在没有换行分隔符的情况下连接
- 改进了恢复大型会话时的内存使用和启动时间
- [VSCode] 修复了已经认证时打开侧边栏时登录屏幕的短暂闪烁
- [VSCode] 修复了选择 Opus 时出现"API Error: Rate limit reached" — 模型下拉列表不再向计划层未知的订阅者提供 1M 上下文变体

## 2.1.77

- 将 Claude Opus 4.6 的默认最大输出令牌限制增加到 64k 令牌，Opus 4.6 和 Sonnet 4.6 模型的上限增加到 128k 令牌
- 添加了 `allowRead` 沙盒文件系统设置，以在 `denyRead` 区域内重新允许读取访问
- `/copy` 现在接受可选索引：`/copy N` 复制第 N 个最新的助手响应
- 修复了复合 bash 命令（例如 `cd src && npm test`）上的"Always Allow"保存完整字符串的单个规则而不是每个子命令，导致死规则和重复权限提示
- 修复了斜杠命令覆盖反复打开和关闭时自动更新器启动重叠的二进制下载，累积数十 GB 的内存
- 修复了由于记忆提取写入和主转录之间的竞争，`--resume` 静默截断最近的对话历史
- 修复了 PreToolUse 钩子返回 `"allow"` 绕过 `deny` 权限规则，包括企业托管设置
- 修复了 Write 工具在覆盖 CRLF 文件或在 CRLF 目录中创建文件时静默转换行尾
- 修复了长时间运行的会话中进度消息在压缩后仍然存在导致的内存增长
- 修复了 API 回退到非流式模式时未跟踪成本和令牌使用
- 修复了 `CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS` 未剥离 beta 工具模式字段，导致代理网关拒绝请求
- 修复了系统临时目录路径包含空格时 Bash 工具报告成功命令的错误
- 修复了粘贴后立即输入时粘贴丢失
- 修复了 `/feedback` 文本输入中的 Ctrl+D 向前删除而不是第二次按下退出会话
- 修复了将 0 字节图像文件拖入提示时的 API 错误
- 修复了 Claude Desktop 会话错误地使用终端 CLI 配置的 API 密钥而不是 OAuth
- 修复了同一 monorepo 提交的不同子目录中的 `git-subdir` 插件在插件缓存中冲突
- 修复了终端 UI 中有序列表编号不渲染
- 修复了陈旧 worktree 清理可能删除刚刚从先前崩溃恢复的代理 worktree 的竞争条件
- 修复了代理运行时打开 `/mcp` 或类似对话框时输入死锁
- 修复了 vim NORMAL 模式下 Backspace 和 Delete 键不工作
- 修复了切换 vim 模式时状态行不更新
- 修复了 VS Code、Cursor 和其他基于 xterm.js 的终端中 Cmd+click 时超链接打开两次
- 修复了默认配置下 tmux 内背景颜色渲染为终端默认
- 修复了通过 SSH 在 tmux 中选择文本时 iTerm2 会话崩溃
- 修复了 tmux 会话中剪贴板复制静默失败；复制 toast 现在指示是使用 `⌘V` 还是 tmux `prefix+]` 粘贴
- 修复了在设置、权限和沙盒对话框中导航列表时 `←`/`→` 意外切换标签
- 修复了在 tmux 或 screen 内启动时 IDE 集成不自动连接
- 修复了在右边缘裁剪时 CJK 字符在视觉上渗入相邻 UI 元素
- 修复了领导者退出时队友窗格不关闭
- 修复了 iTerm2 自动模式未检测 iTerm2 以进行原生分割窗格队友
- macOS 启动更快（约 60ms），通过并行读取钥匙串凭据和模块加载
- 更快的 `--resume` 在 fork 繁重和非常大的会话上 — 加载速度提高 45%，峰值内存减少约 100-150MB
- 改进了 Esc 以中止飞行中的非流式 API 请求
- 改进了 `claude plugin validate` 以检查技能、代理和命令 frontmatter 以及 `hooks/hooks.json`，捕获 YAML 解析错误和模式违规
- 后台 bash 任务现在在输出超过 5GB 时被杀死，防止失控进程填满磁盘
- 会话现在在接受计划时从计划内容自动命名
- 改进了无头模式插件安装以正确组合 `CLAUDE_CODE_PLUGIN_SEED_DIR`
- 当 `apiKeyHelper` 耗时超过 10s 时显示通知，防止它阻塞主循环
- Agent 工具不再接受 `resume` 参数 — 使用 `SendMessage({to: agentId})` 继续先前生成的代理
- `SendMessage` 现在在后台自动恢复停止的代理而不是返回错误
- 将 `/fork` 重命名为 `/branch`（`/fork` 仍作为别名工作）
- [VSCode] 改进了计划预览标签标题使用计划的标题而不是"Claude's Plan"
- [VSCode] 当 option+click 在 macOS 上不触发原生选择时，页脚现在指向 `macOptionClickForcesSelection` 设置

## 2.1.76

- 添加了 MCP 请求支持 — MCP 服务器现在可以通过交互式对话框（表单字段或浏览器 URL）在任务中间请求结构化输入
- 添加了新的 `Elicitation` 和 `ElicitationResult` 钩子，在响应发送回之前拦截和覆盖
- 添加了 `-n` / `--name <name>` CLI 标志，在启动时为会话设置显示名称
- 添加了 `worktree.sparsePaths` 设置，用于大型 monorepo 中的 `claude --worktree`，通过 git sparse-checkout 仅检出所需的目录
- 添加了 `PostCompact` 钩子，在压缩完成后触发
- 添加了 `/effort` 斜杠命令以设置模型 effort 级别
- 添加了会话质量调查 — 企业管理员可以通过 `feedbackSurveyRate` 设置配置样本率
- 修复了延迟工具（通过 `ToolSearch` 加载）在对话压缩后丢失其输入模式，导致数组和数字参数被拒绝并出现类型错误
- 修复了斜杠命令显示"Unknown skill"
- 修复了计划模式在计划已被接受后要求重新批准
- 修复了权限对话框或计划编辑器打开时语音模式吞掉按键
- 修复了通过 npm 安装时 `/voice` 在 Windows 上不工作
- 修复了 1M 上下文会话上使用带有 `model:` frontmatter 的技能调用时出现虚假的"Context limit reached"
- 修复了使用非标准模型字符串时出现"adaptive thinking is not supported on this model"错误
- 修复了 `Bash(cmd:*)` 权限规则在引用参数包含 `#` 时不匹配
- 修复了 Bash 权限对话框中的"don't ask again"显示管道和复合命令的完整原始命令
- 修复了连续失败后自动压缩无限重试 — 现在断路器在 3 次尝试后停止
- 修复了 MCP 重连成功后重连旋转器持续存在
- 修复了 LSP 插件在市场协调之前 LSP 管理器初始化时未注册服务器
- 修复了通过 SSH 在 tmux 中复制到剪贴板 — 现在尝试直接终端写入和 tmux 剪贴板集成
- 修复了 `/export` 在成功消息中仅显示文件名而不是完整文件路径
- 修复了选择文本后转录不自动滚动到新消息
- 修复了 Escape 键不工作以退出登录方法选择屏幕
- 修复了几个远程控制问题：服务器收割空闲环境时会话静默死亡、快速消息一个接一个排队而不是批处理、以及 JWT 刷新后陈旧工作项导致重新传递
- 修复了扩展 WebSocket 断开后桥接会话无法恢复
- 修复了输入软隐藏命令的确切名称时找不到斜杠命令
- 改进了 `--worktree` 启动性能，通过直接读取 git refs 并在远程分支已在本地可用时跳过冗余的 `git fetch`
- 改进了后台代理行为 — 现在杀死后台代理会在对话上下文中保留其部分结果
- 改进了模型回退通知 — 现在始终可见而不是隐藏在详细模式后面，使用人类友好的模型名称
- 改进了深色终端主题上的块引用可读性 — 文本现在是斜体带有左边栏而不是暗淡
- 改进了陈旧 worktree 清理 — 中断的并行运行后留下的 worktree 现在自动清理
- 改进了远程控制会话标题 — 现在从您的第一个提示派生而不是显示"Interactive session"
- 改进了 `/voice` 以在启用时显示您的听写语言，并在您的 `language` 设置不支持语音输入时发出警告
- 更新了 `--plugin-dir` 以仅接受一个路径以支持子命令 — 使用重复的 `--plugin-dir` 指定多个目录
- [VSCode] 修复了 @-mention 文件选择器中包含逗号的 gitignore 模式静默排除整个文件类型

## 2.1.75

- 为 Max、Team 和 Enterprise 计划默认添加了 Opus 4.6 的 1M 上下文窗口（以前需要额外使用量）
- 为所有用户添加了 `/color` 命令以为您的会话设置提示栏颜色
- 使用 `/rename` 时添加了会话名称显示在提示栏上
- 为记忆文件添加了最后修改时间戳，帮助 Claude 推断哪些记忆是新鲜的与陈旧的
- 当钩子需要确认时，在权限提示中添加了钩子源显示（设置/插件/技能）
- 修复了在没有切换两次 `/voice` 的新安装上语音模式未正确激活
- 修复了使用 `/model` 或 Option+P 切换模型后 Claude Code 标题未更新显示的模型名称
- 修复了附件消息计算返回未定义值时会话崩溃
- 修复了 Bash 工具在管道命令中破坏 `!`（例如 `jq 'select(.x != .y)'` 现在正确工作）
- 修复了托管禁用的插件出现在 `/plugin` 已安装标签中 — 组织强制禁用的插件现在隐藏
- 修复了思考和 `tool_use` 块的令牌估计过多，防止过早的上下文压缩
- 修复了损坏的市场配置路径处理
- 修复了在恢复分叉或继续的会话后 `/resume` 丢失会话名称
- 修复了访问配置标签后 Esc 不关闭 `/status` 对话框
- 修复了接受或拒绝计划时的输入处理
- 修复了代理团队中的页脚提示显示"↓ to expand"而不是正确的"shift + ↓ to expand"
- 改进了 macOS 非 MDM 机器的启动性能，通过跳过不必要的子进程生成
- 默认抑制异步钩子完成消息（使用 `--verbose` 或转录模式可见）
- 破坏性变更：删除了 `C:\ProgramData\ClaudeCode\managed-settings.json` 处已弃用的 Windows 托管设置回退 — 使用 `C:\Program Files\ClaudeCode\managed-settings.json`

## 2.1.74

- 向 `/context` 命令添加了可操作的建议 — 识别上下文繁重的工具、内存膨胀和容量警告，带有具体的优化提示
- 添加了 `autoMemoryDirectory` 设置以配置自动记忆存储的自定义目录
- 修复了提前终止生成器时流式 API 响应缓冲区未释放的内存泄漏，导致 Node.js/npm 路径上无限制的 RSS 增长
- 修复了托管策略 `ask` 规则被用户 `allow` 规则或技能 `allowed-tools` 绕过
- 修复了完整模型 ID（例如 `claude-opus-4-5`）在代理 frontmatter `model:` 字段和 `--agents` JSON 配置中被静默忽略 — 代理现在接受与 `--model` 相同的模型值
- 修复了回调端口已在使用时 MCP OAuth 认证挂起
- 修复了刷新令牌过期后 MCP OAuth 刷新从不提示重新认证，对于返回 HTTP 200 错误的 OAuth 服务器（例如 Slack）
- 修复了语音模式在 macOS 原生二进制上静默失败，对于终端从未被授予麦克风权限的用户 — 二进制文件现在包含 `audio-input` 权限，以便 macOS 正确提示
- 修复了退出时 `SessionEnd` 钩子在 1.5 秒后被杀死，无论 `hook.timeout` — 现在可通过 `CLAUDE_CODE_SESSIONEND_HOOKS_TIMEOUT_MS` 配置
- 修复了在 REPL 内 `/plugin install` 对具有本地源的市场插件失败
- 修复了市场更新不同步 git 子模块 — 子模块中的插件源在更新后不再中断
- 修复了带有参数的未知斜杠命令静默丢弃输入 — 现在将您的输入显示为警告
- 修复了 Hebrew、Arabic 和其他 RTL 文本在 Windows Terminal、conhost 和 VS Code 集成终端中无法正确渲染
- 修复了 Windows 上由于格式错误的文件 URI 导致 LSP 服务器不工作
- 更改了 `--plugin-dir`，以便本地开发副本现在覆盖具有相同名称的已安装市场插件（除非该插件被托管设置强制启用）
- [VSCode] 修复了未命名会话的删除按钮不工作
- [VSCode] 改进了集成终端中的滚轮响应性，带有终端感知加速

## 2.1.73

- 添加了 `modelOverrides` 设置，将模型选择器条目映射到自定义提供商模型 ID（例如 Bedrock 推理配置文件 ARN）
- 当 OAuth 登录或连接性检查由于 SSL 证书错误（企业代理、`NODE_EXTRA_CA_CERTS`）失败时添加了可操作的指导
- 修复了复杂 bash 命令的权限提示触发的冻结和 100% CPU 循环
- 修复了当许多技能文件同时更改时可能导致 Claude Code 冻结的死锁（例如在具有大型 `.claude/skills/` 目录的存储库中 `git pull` 期间）
- 修复了在同一项目目录中运行多个 Claude Code 会话时 Bash 工具输出丢失
- 修复了带有 `model: opus`/`sonnet`/`haiku` 的子代理在 Bedrock、Vertex 和 Microsoft Foundry 上被静默降级到旧模型版本
- 修复了代理退出时子代理生成的后台 bash 进程未清理
- 修复了 `/resume` 在选择器中显示当前会话
- 修复了自动安装扩展时 `/ide` 以 `onInstall is not defined` 崩溃
- 修复了在 Bedrock/Vertex/Foundry 上以及遥测被禁用时 `/loop` 不可用
- 修复了通过 `--resume` 或 `--continue` 恢复会话时 SessionStart 钩子触发两次
- 修复了 JSON 输出钩子在每轮向模型上下文注入无操作系统提醒消息
- 修复了慢速连接重叠新录音时语音模式会话损坏
- 修复了 Linux 沙盒在原生构建上以"ripgrep (rg) not found"启动失败
- 修复了 Linux 原生模块在 Amazon Linux 2 和其他 glibc 2.26 系统上不加载
- 修复了通过远程控制接收图像时出现"media_type: Field required" API 错误
- 修复了 Desktop 文件夹已存在时 `/heapdump` 在 Windows 上以 `EEXIST` 错误失败
- 改进了中断 Claude 后的上箭头 — 现在一步恢复中断的提示并倒回对话
- 改进了启动时的 IDE 检测速度
- 改进了 macOS 上的剪贴板图像粘贴性能
- 改进了 `/effort` 在 Claude 响应时工作，匹配 `/model` 行为
- 改进了语音模式，在快速按键说话重新按下期间自动重试瞬态连接失败
- 改进了远程控制生成模式选择提示，带有更好的上下文
- 更改了 Bedrock、Vertex 和 Microsoft Foundry 上的默认 Opus 模型为 Opus 4.6（原为 Opus 4.1）
- 弃用了 `/output-style` 命令 — 改用 `/config`。输出样式现在在会话开始时固定，以获得更好的提示缓存
- VSCode：修复了代理或使用 Claude 4.5 模型的 Bedrock/Vertex 用户后出现 HTTP 400 错误

## 2.1.72

- 修复了工具搜索以在设置 `ENABLE_TOOL_SEARCH` 的情况下即使使用 `ANTHROPIC_BASE_URL` 也能激活
- 在 `/copy` 中添加了 `w` 键，将焦点选择直接写入文件，绕过剪贴板（在 SSH 上有用）
- 向 `/plan` 添加了可选的描述参数（例如 `/plan fix the auth bug`），进入计划模式并立即开始
- 添加了 `ExitWorktree` 工具以离开 `EnterWorktree` 会话
- 添加了 `CLAUDE_CODE_DISABLE_CRON` 环境变量，在会话中间立即停止计划的 cron 作业
- 将 `lsof`、`pgrep`、`tput`、`ss`、`fd` 和 `fdfind` 添加到 bash 自动批准允许列表，减少常见只读操作的权限提示
- 恢复了 Agent 工具上的 `model` 参数，用于每次调用的模型覆盖
- 简化了 effort 级别为 low/medium/high（删除了 max），带有新符号（○ ◐ ●）和简短通知而不是持久图标。使用 `/effort auto` 重置为默认
- 改进了 `/config` — Escape 现在取消更改，Enter 保存并关闭，Space 切换设置
- 改进了上箭头历史记录，在运行多个并发会话时首先显示当前会话的消息
- 改进了存储库名称和常见开发术语（regex、OAuth、JSON）的语音输入转录准确性
- 通过切换到原生模块改进了 bash 命令解析 — 更快的初始化且无内存泄漏
- 减少了包大小约 510 KB
- 更改了 CLAUDE.md HTML 注释（`<!-- ... -->`）在自动注入时对 Claude 隐藏。使用 Read 工具读取时注释仍然可见
- 修复了后台任务或钩子响应缓慢时退出缓慢
- 修复了代理任务进度卡在"Initializing…"
- 修复了启用钩子的技能被模型调用时技能钩子每事件触发两次
- 修复了 `--continue` 在 `--compact` 后不从最近点恢复
- 修复了 bash 安全解析边缘情况
- 添加了对没有 `.git` 后缀的市场 git URL 的支持（Azure DevOps、AWS CodeCommit）
- 改进了市场克隆失败消息以显示诊断信息，即使 git 不产生 stderr
- 修复了几个插件问题：Windows 上 OneDrive 文件夹中的安装因 `EEXIST` 错误失败、当存在项目范围安装时市场阻止用户范围安装、`CLAUDE_CODE_PLUGIN_CACHE_DIR` 创建字面 `~` 目录、以及带有仅市场字段的 `plugin.json` 加载失败
- 修复了反馈调查在长会话中出现过于频繁
- 修复了启动时被无关设置写入重置的 `--effort` CLI 标志
- 修复了 `/clear` 后后台 Ctrl+B 查询丢失其转录或损坏新对话
- 修复了 `/clear` 杀死后台代理/bash 任务 — 现在只清除前台任务
- 修复了 worktree 隔离问题：Task 工具恢复未恢复 cwd、以及后台任务通知缺少 `worktreePath` 和 `worktreeBranch`
- 修复了 Claude 工作时运行 `/model` 不显示结果
- 修复了计划模式权限提示的文本输入中数字键选择菜单选项而不是输入
- 修复了沙盒权限问题：某些文件写入操作错误地允许而无需提示、以及输出重定向到允许列表目录（如 `/tmp/claude/`）不必要地提示
- 改进了长会话中的 CPU 利用率
- 修复了 SDK `query()` 调用中的提示缓存失效，将输入令牌成本减少高达 12 倍
- 修复了取消查询后 Escape 键无响应
- 修复了运行后台代理或任务时双 Ctrl+C 不退出
- 修复了团队代理以继承领导者的模型
- 修复了"Always Allow"保存永远不会再次匹配的权限规则
- 修复了几个钩子问题：恢复/分叉会话的 `transcript_path` 指向错误的目录、每次设置写入时代理 `prompt` 从 settings.json 静默删除、PostToolUse 阻止原因显示两次、异步钩子未通过 bash `read -r` 接收 stdin、以及验证错误消息显示验证失败的示例
- 修复了 Desktop/SDK 中 Read 返回包含 U+2028/U+2029 字符的文件时会话崩溃
- 修复了即使设置了 `CLAUDE_CODE_DISABLE_TERMINAL_TITLE` 退出时终端标题也被清除
- 修复了几个权限规则匹配问题：通配符规则不匹配带有 heredoc、嵌入换行符或无参数的命令；`sandbox.excludedCommands` 在环境变量前缀下失败；"always allow"为嵌套 CLI 工具建议过于宽泛的前缀；以及 deny 规则不应用于所有命令形式
- 修复了来自 Bash 数据 URL 输出的过大和截断的图像
- 修复了恢复包含 Bedrock API 错误的会话时崩溃
- 修复了 Edit、Bash 和 Grep 工具输入上间歇性"expected boolean, received string"验证错误
- 修复了从第一条消息包含换行的对话分叉时出现多行会话标题
- 修复了排队消息不显示附加图像、以及按 ↑ 编辑排队消息时图像丢失
- 修复了失败的 Read/WebFetch/Glob 并行工具调用取消其兄弟 — 现在只有 Bash 错误会级联
- VSCode：修复了集成终端中的滚动速度不匹配原生终端

---

*注：由于更新日志内容非常长（超过2400行），此处仅翻译了最近的版本更新内容（v2.1.72以上）。完整版本历史请参考原文档。*

早期版本主要功能亮点：

## 主要版本里程碑

### v2.1.x 系列
- **2.1.71**: 添加 `/loop` 命令、cron 调度工具、可重绑定语音激活键
- **2.1.70**: 修复 API 代理兼容性问题
- **2.1.69**: 添加 `/claude-api` 技能、支持更多语音语言
- **2.1.68**: Opus 4.6 默认使用 medium effort
- **2.1.66**: 减少虚假错误日志
- **2.1.63**: 添加 `/simplify` 和 `/batch` 斜杠命令

### v2.1.x 主要功能
- **工具搜索延迟加载**: MCP 工具描述超过上下文窗口 10% 时自动延迟发现
- **插件系统**: 完整的插件市场和管理功能
- **语音模式**: 支持多种语言的语音输入
- **远程控制**: `/remote-control` 桥接到 claude.ai/code
- **代理团队**: 多代理协作功能（实验性）
- **Worktree 隔离**: `--worktree` 标志启动隔离的 git worktree
- **自动记忆**: Claude 自动记录和回忆记忆

### v2.0.x 系列
- **2.0.0**: 新的原生 VS Code 扩展、全新 UI 设计
- **LSP 支持**: 语言服务器协议工具
- **Claude Skills**: 技能系统
- **后台代理**: 代理在后台运行
- **MCP 增强**: 多配置文件、OAuth 支持

### v1.0.x 系列
- **1.0.0**: Claude Code 正式发布
- **Sonnet 4 和 Opus 4**: 新模型支持
- **Hooks 系统**: 钩子框架发布
- **MCP 集成**: Model Context Protocol 支持
- **SDK 发布**: TypeScript 和 Python SDK

### v0.2.x 系列
- **Web 搜索**: Claude 可以搜索网络
- **PDF 支持**: 读取 PDF 文件
- **图像支持**: 复制粘贴图像到提示
- **自定义斜杠命令**: Markdown 文件作为自定义命令
- **MCP 项目范围**: `.mcp.json` 文件支持

---

如需查看完整的历史更新记录，请参阅 [原文 CHANGELOG.md](../CHANGELOG.md)。
