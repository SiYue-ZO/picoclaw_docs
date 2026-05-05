---
id: cli-parameters
title: CLI 命令与参数
---

# CLI 命令与参数

PicoClaw 是一个轻量级的个人 AI 助手。本文档介绍其命令行界面（CLI）支持的所有命令和参数。

:::tip
聊天中的 slash 命令（如 `/help`、`/clear`）请参阅[聊天命令参考](/docs/chat-commands)。
:::

## 全局参数

| 参数 | 说明 |
|------|------|
| `--no-color` | 禁用彩色输出（保留边框布局） |

也可以通过环境变量 `NO_COLOR` 或 `TERM=dumb` 来禁用颜色。

---

## 命令一览

| 命令 | 别名 | 说明 |
|------|------|------|
| [`picoclaw onboard`](#picoclaw-onboard) | `o` | 初始化配置和工作区 |
| [`picoclaw agent`](#picoclaw-agent) | | 与 AI 代理交互 |
| [`picoclaw auth`](#picoclaw-auth) | | 管理认证（登录、登出、状态） |
| [`picoclaw gateway`](#picoclaw-gateway) | `g` | 启动 PicoClaw 网关 |
| [`picoclaw status`](#picoclaw-status) | `s` | 显示 PicoClaw 状态 |
| [`picoclaw cron`](#picoclaw-cron) | `c` | 管理定时任务 |
| [`picoclaw mcp`](#picoclaw-mcp) | | 管理 MCP 服务器配置 |
| [`picoclaw migrate`](#picoclaw-migrate) | | 从其他工具迁移到 PicoClaw |
| [`picoclaw skills`](#picoclaw-skills) | | 管理技能 |
| [`picoclaw model`](#picoclaw-model) | | 查看或更改默认模型 |
| [`picoclaw update`](#picoclaw-update) | | 更新 PicoClaw |
| [`picoclaw version`](#picoclaw-version) | `v` | 显示版本信息 |

---

## picoclaw onboard

初始化 PicoClaw 配置和工作区。

```bash
picoclaw onboard [flags]
```

### 参数

| 参数 | 说明 |
|------|------|
| `--enc` | 启用凭证加密（生成 SSH 密钥并提示输入密码） |

### 示例

```bash
picoclaw onboard
picoclaw onboard --enc
```

---

## picoclaw agent

直接与 AI 代理交互。

```bash
picoclaw agent [flags]
```

### 参数

| 参数 | 短参数 | 默认值 | 说明 |
|------|--------|--------|------|
| `--debug` | `-d` | `false` | 启用调试日志 |
| `--message` | `-m` | | 发送单条消息（非交互模式） |
| `--session` | `-s` | `cli:default` | 会话密钥 |
| `--model` | | | 使用的模型 |

### 示例

```bash
# 交互模式
picoclaw agent

# 非交互模式
picoclaw agent -m "Hello, how are you?"

# 指定模型
picoclaw agent --model gpt-5.2 -m "Explain quantum computing"
```

---

## picoclaw auth

管理认证，包括登录、登出和状态查看。

```bash
picoclaw auth [command]
```

### 子命令

- [`login`](#picoclaw-auth-login) - 登录
- [`logout`](#picoclaw-auth-logout) - 登出
- [`status`](#picoclaw-auth-status) - 显示认证状态
- [`models`](#picoclaw-auth-models) - 显示可用模型
- [`weixin`](#picoclaw-auth-weixin) - 连接微信个人账号
- [`wecom`](#picoclaw-auth-wecom) - 连接企业微信

---

### picoclaw auth login

通过 OAuth 或粘贴令牌进行登录。

```bash
picoclaw auth login [flags]
```

#### 参数

| 参数 | 短参数 | 说明 |
|------|--------|------|
| `--provider` | `-p` | **必需**。登录提供商（`openai`, `anthropic`, `google-antigravity`, `antigravity`） |
| `--device-code` | | 使用设备代码流程（适用于无头环境） |
| `--no-browser` | | OAuth 登录时不自动打开浏览器 |
| `--setup-token` | | 使用 Anthropic 的 setup-token 流程 |

#### 示例

```bash
picoclaw auth login -p openai
picoclaw auth login -p anthropic --setup-token
picoclaw auth login -p openai --device-code
```

---

### picoclaw auth logout

移除存储的凭证。

```bash
picoclaw auth logout [flags]
```

#### 参数

| 参数 | 短参数 | 说明 |
|------|--------|------|
| `--provider` | `-p` | 要登出的提供商（`openai`, `anthropic`）；为空则登出所有 |

#### 示例

```bash
picoclaw auth logout -p openai
picoclaw auth logout
```

---

### picoclaw auth status

显示当前认证状态。

```bash
picoclaw auth status
```

---

### picoclaw auth models

显示可用模型。

```bash
picoclaw auth models
```

---

### picoclaw auth weixin

通过二维码连接微信个人账号。

```bash
picoclaw auth weixin [flags]
```

#### 参数

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--base-url` | `https://ilinkai.weixin.qq.com/` | iLink API 基础 URL |
| `--proxy` | | HTTP 代理 URL（如 `http://localhost:7890`） |
| `--timeout` | `300` | 登录超时时间（秒） |

#### 示例

```bash
picoclaw auth weixin
picoclaw auth weixin --proxy http://localhost:7890
```

---

### picoclaw auth wecom

扫描企业微信二维码并配置 `channels.wecom`。

```bash
picoclaw auth wecom [flags]
```

#### 参数

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--timeout` | `5m` | 等待二维码确认的超时时间 |

#### 示例

```bash
picoclaw auth wecom
picoclaw auth wecom --timeout 10m
```

---

## picoclaw gateway

启动 PicoClaw 网关服务。

```bash
picoclaw gateway [flags]
```

### 参数

| 参数 | 短参数 | 默认值 | 说明 |
|------|--------|--------|------|
| `--debug` | `-d` | `false` | 启用调试日志 |
| `--no-truncate` | `-T` | `false` | 禁用调试日志中的字符串截断（必须与 `--debug` 一起使用） |
| `--allow-empty` | `-E` | `false` | 未配置默认模型时仍继续启动 |
| `--host` | | | 网关绑定的主机地址（覆盖 `gateway.host` 配置） |

### 示例

```bash
picoclaw gateway
picoclaw gateway -d
picoclaw gateway -d -T
picoclaw gateway --host 0.0.0.0
```

---

## picoclaw status

显示 PicoClaw 当前状态。

```bash
picoclaw status
```

---

## picoclaw cron

管理定时任务。

:::tip
关于调度类型、执行模式和配置的详细文档，请参阅[定时任务与 Cron 作业](/docs/cron)。
:::

```bash
picoclaw cron [command]
```

### 子命令

- `list` - 列出所有定时任务
- `add` - 添加新任务
- `remove` - 按 ID 移除任务
- `enable` - 启用任务
- `disable` - 禁用任务

### picoclaw cron add

```bash
picoclaw cron add [flags]
```

#### 参数

| 参数 | 短参数 | 说明 |
|------|--------|------|
| `--name` | `-n` | **必需**。任务名称 |
| `--message` | `-m` | **必需**。发送给代理的消息 |
| `--every` | `-e` | 每 N 秒运行一次 |
| `--cron` | `-c` | Cron 表达式（如 `0 9 * * *`） |
| `--to` | | 接收者 |
| `--channel` | | 投递渠道 |

> **注意**：`--every` 和 `--cron` 互斥，必须指定其中一个。

#### 示例

```bash
# 每 60 秒运行一次
picoclaw cron add -n "health-check" -m "Check system health" -e 60

# 每天早上 9 点运行
picoclaw cron add -n "morning-report" -m "Generate morning report" -c "0 9 * * *"

# 指定投递渠道
picoclaw cron add -n "reminder" -m "Take a break" -e 1800 --channel wecom --to user123
```

### 其他 cron 子命令

```bash
picoclaw cron list
picoclaw cron remove <job-id>
picoclaw cron enable <job-id>
picoclaw cron disable <job-id>
```

---

## picoclaw mcp

管理 MCP（Model Context Protocol）服务器配置。

```bash
picoclaw mcp [command]
```

### 子命令

- [`add`](#picoclaw-mcp-add) - 添加或更新 MCP 服务器
- [`remove`](#picoclaw-mcp-remove) - 移除 MCP 服务器
- [`list`](#picoclaw-mcp-list) - 列出已配置的 MCP 服务器
- [`edit`](#picoclaw-mcp-edit) - 在编辑器中打开配置
- [`test`](#picoclaw-mcp-test) - 测试 MCP 服务器连接
- [`show`](#picoclaw-mcp-show) - 显示 MCP 服务器详情

---

### picoclaw mcp add

添加或更新 MCP 服务器。

```bash
picoclaw mcp add [flags] <name> <command-or-url> [args...]
```

#### 参数

| 参数 | 短参数 | 默认值 | 说明 |
|------|--------|--------|------|
| `--env` | `-e` | | 环境变量，格式为 `KEY=value`（可重复） |
| `--env-file` | | | 环境变量文件路径（推荐用于存储密钥） |
| `--header` | `-H` | | HTTP 头，格式为 `Name: Value` 或 `Name=Value`（可重复） |
| `--transport` | `-t` | `stdio` | 传输类型：`stdio`、`http` 或 `sse` |
| `--force` | `-f` | `false` | 覆盖现有服务器而不提示 |
| `--deferred` | | `false` | 标记为延迟加载（工具在显式激活前隐藏） |
| `--no-deferred` | | `false` | 标记为非延迟加载（工具始终活跃） |

#### 示例

```bash
# 添加 stdio 类型的 MCP 服务器
picoclaw mcp add my-server /path/to/server arg1 arg2

# 添加 http 类型的 MCP 服务器
picoclaw mcp add remote-server https://example.com/mcp --transport http

# 添加带环境变量的服务器
picoclaw mcp add my-server /path/to/server -e API_KEY=xxx -e DEBUG=true

# 使用环境变量文件
picoclaw mcp add my-server /path/to/server --env-file .env

# 强制覆盖
picoclaw mcp add my-server /path/to/new-server --force
```

---

### picoclaw mcp remove

移除 MCP 服务器。

```bash
picoclaw mcp remove <name>
```

---

### picoclaw mcp list

列出已配置的 MCP 服务器。

```bash
picoclaw mcp list [flags]
```

#### 参数

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--status` | `false` | Ping 已启用的服务器并显示实时状态 |
| `--timeout` | `5s` | 每次状态检查的超时时间 |

#### 示例

```bash
picoclaw mcp list
picoclaw mcp list --status
picoclaw mcp list --status --timeout 10s
```

---

### picoclaw mcp edit

在 `$EDITOR` 中打开 PicoClaw 配置文件。

```bash
picoclaw mcp edit
```

> **注意**：需要设置 `EDITOR` 环境变量。

---

### picoclaw mcp test

测试 MCP 服务器连接。

```bash
picoclaw mcp test <name> [flags]
```

#### 参数

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--timeout` | `5s` | 连接超时时间 |

#### 示例

```bash
picoclaw mcp test my-server
picoclaw mcp test my-server --timeout 10s
```

---

### picoclaw mcp show

显示 MCP 服务器详情和工具列表。

```bash
picoclaw mcp show <name> [flags]
```

#### 参数

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--timeout` | `10s` | 连接超时时间 |

#### 示例

```bash
picoclaw mcp show my-server
picoclaw mcp show my-server --timeout 15s
```

---

## picoclaw migrate

从其他工具（如 OpenClaw）迁移到 PicoClaw。

```bash
picoclaw migrate [flags]
```

### 参数

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--dry-run` | `false` | 显示将要迁移的内容，但不执行更改 |
| `--from` | `openclaw` | 迁移来源（如 `openclaw`） |
| `--refresh` | `false` | 从 OpenClaw 重新同步工作区文件 |
| `--config-only` | `false` | 仅迁移配置，跳过工作区文件 |
| `--workspace-only` | `false` | 仅迁移工作区文件，跳过配置 |
| `--force` | `false` | 跳过确认提示 |
| `--source-home` | `~/.openclaw` | 覆盖源目录 |
| `--target-home` | `~/.picoclaw` | 覆盖目标目录 |

### 示例

```bash
# 默认迁移
picoclaw migrate

# 预览迁移
picoclaw migrate --dry-run

# 仅迁移配置
picoclaw migrate --config-only

# 强制迁移
picoclaw migrate --force
```

---

## picoclaw skills

管理技能。

```bash
picoclaw skills [command]
```

### 子命令

- [`list`](#picoclaw-skills-list) - 列出已安装的技能
- [`install`](#picoclaw-skills-install) - 从 GitHub 或注册表安装技能
- [`install-builtin`](#picoclaw-skills-install-builtin) - 安装所有内置技能
- [`list-builtin`](#picoclaw-skills-list-builtin) - 列出可用的内置技能
- [`remove`](#picoclaw-skills-remove) - 移除已安装的技能
- [`search`](#picoclaw-skills-search) - 搜索可用技能
- [`show`](#picoclaw-skills-show) - 显示技能详情

---

### picoclaw skills list

列出已安装的技能。

```bash
picoclaw skills list
```

---

### picoclaw skills install

从 GitHub 或注册表安装技能。

```bash
picoclaw skills install <github> [flags]
picoclaw skills install --registry <name> <slug>
```

#### 参数

| 参数 | 说明 |
|------|------|
| `--registry` | 从指定注册表安装 |

#### 示例

```bash
# 从 GitHub 安装
picoclaw skills install sipeed/picoclaw-skills/weather

# 从注册表安装
picoclaw skills install --registry clawhub github
```

---

### picoclaw skills install-builtin

安装所有内置技能到工作区。

```bash
picoclaw skills install-builtin
```

---

### picoclaw skills list-builtin

列出可用的内置技能。

```bash
picoclaw skills list-builtin
```

---

### picoclaw skills remove

移除已安装的技能。

```bash
picoclaw skills remove <name>
```

> **别名**：`rm`, `uninstall`

#### 示例

```bash
picoclaw skills remove weather
picoclaw skills rm weather
picoclaw skills uninstall weather
```

---

### picoclaw skills search

搜索可用技能。

```bash
picoclaw skills search [query]
```

#### 示例

```bash
# 列出所有技能
picoclaw skills search

# 搜索特定技能
picoclaw skills search weather
```

---

### picoclaw skills show

显示技能详情。

```bash
picoclaw skills show <name>
```

#### 示例

```bash
picoclaw skills show weather
```

---

## picoclaw model

查看或更改默认模型。

```bash
picoclaw model [model_name] [command]
```

### 参数

| 参数 | 说明 |
|------|------|
| `model_name` | 可选。要设置为默认的模型名称 |

### 子命令

- [`add`](#picoclaw-model-add) - 从 OpenAI 兼容端点添加模型

### 示例

```bash
# 显示当前默认模型
picoclaw model

# 设置默认模型
picoclaw model gpt-5.2
picoclaw model claude-sonnet-4.6
picoclaw model local-model
```

---

### picoclaw model add

从 OpenAI 兼容端点添加模型并设置为默认。

```bash
picoclaw model add [flags]
```

#### 参数

| 参数 | 短参数 | 默认值 | 说明 |
|------|--------|--------|------|
| `--api-base` | `-b` | | **必需**。API 基础 URL |
| `--api-key` | `-k` | | **必需**。API 密钥 |
| `--model` | `-m` | | 模型 ID；设置后跳过交互式选择和网络调用 |
| `--name` | `-n` | `custom-prefer` | 本地别名，写入 model_list 并用作默认模型名称 |
| `--type` | | `openai-compatible` | 端点类型（目前仅支持 `openai-compatible`） |

#### 示例

```bash
# 交互式选择模型
picoclaw model add -b https://api.openai.com/v1 -k sk-xxx

# 直接指定模型
picoclaw model add -b http://localhost:8000/v1 -k dummy -m my-model -n local
```

---

## picoclaw update

更新 PicoClaw 到最新版本。

```bash
picoclaw update
```

---

## picoclaw version

显示版本信息。

```bash
picoclaw version
```

---

## 环境变量

| 变量 | 说明 |
|------|------|
| `NO_COLOR` | 设置为任意非空值以禁用彩色输出 |
| `TERM` | 设置为 `dumb` 以禁用彩色输出 |
| `TZ` | 设置时区 |
| `EDITOR` | 用于 `picoclaw mcp edit` 命令的编辑器 |
| `GATEWAY_HOST` | 网关绑定的主机地址（可被 `--host` 参数覆盖） |

---

## 配置文件

PicoClaw 的配置文件默认位于 `~/.picoclaw/config.json`。可以使用以下命令查看和编辑：

```bash
# 查看配置路径
picoclaw status

# 编辑配置
picoclaw mcp edit
```

---

## `picoclaw-launcher` 参数说明

`picoclaw-launcher` 二进制提供 GUI 启动器，支持以下参数：

| 参数 | 作用 | 示例 |
| --- | --- | --- |
| `-console` | 终端模式运行（不启用托盘 GUI），并在启动输出中打印登录提示和令牌来源 | `picoclaw-launcher -console` |
| `-public` | 监听 `0.0.0.0`，允许局域网设备访问 WebUI | `picoclaw-launcher -public` |
| `-no-browser` | 启动时不自动打开浏览器 | `picoclaw-launcher -no-browser` |
| `-port <port>` | 指定端口（默认 `18800`） | `picoclaw-launcher -port 19999` |
| `-lang <en\|zh>` | 指定 UI 语言 | `picoclaw-launcher -lang zh` |
| `[config.json]` | 可选：指定配置文件路径 | `picoclaw-launcher ./config.json` |

常见组合：

```bash
# 无头服务器（SSH）常用：终端模式 + 不自动开浏览器 + 局域网访问
picoclaw-launcher -console -no-browser -public

# 自定义端口 + 指定配置文件
picoclaw-launcher -port 19999 ./config.json
```

---

## 更多帮助

获取任何命令的详细帮助：

```bash
picoclaw --help
picoclaw <command> --help
picoclaw <command> <subcommand> --help
```

例如：

```bash
picoclaw mcp add --help
picoclaw cron add --help
picoclaw model add --help
```
