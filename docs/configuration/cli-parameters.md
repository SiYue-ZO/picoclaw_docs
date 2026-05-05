---
id: cli-parameters
title: CLI Commands and Parameters
---

# CLI Commands and Parameters

PicoClaw is a lightweight personal AI assistant. This page documents all CLI commands and parameters supported by the `picoclaw` binary.

:::tip
For chat slash commands (e.g. `/help`, `/clear`), see [Chat Command Reference](/docs/chat-commands).
:::

## Global Parameters

| Parameter | Description |
| --- | --- |
| `--no-color` | Disable colored output (preserves border layout) |

You can also disable colors via the `NO_COLOR` environment variable or `TERM=dumb`.

---

## Command Overview

| Command | Alias | Description |
| --- | --- | --- |
| [`picoclaw onboard`](#picoclaw-onboard) | `o` | Initialize config and workspace |
| [`picoclaw agent`](#picoclaw-agent) | | Interact with the AI agent |
| [`picoclaw auth`](#picoclaw-auth) | | Manage authentication (login, logout, status) |
| [`picoclaw gateway`](#picoclaw-gateway) | `g` | Start the PicoClaw gateway |
| [`picoclaw status`](#picoclaw-status) | `s` | Show PicoClaw status |
| [`picoclaw cron`](#picoclaw-cron) | `c` | Manage scheduled tasks |
| [`picoclaw mcp`](#picoclaw-mcp) | | Manage MCP server configurations |
| [`picoclaw migrate`](#picoclaw-migrate) | | Migrate from other tools to PicoClaw |
| [`picoclaw skills`](#picoclaw-skills) | | Manage skills |
| [`picoclaw model`](#picoclaw-model) | | View or change the default model |
| [`picoclaw update`](#picoclaw-update) | | Update PicoClaw |
| [`picoclaw version`](#picoclaw-version) | `v` | Show version information |

---

## picoclaw onboard

Initialize PicoClaw configuration and workspace.

```bash
picoclaw onboard [flags]
```

### Parameters

| Parameter | Description |
| --- | --- |
| `--enc` | Enable credential encryption (generates SSH key and prompts for passphrase) |

### Examples

```bash
picoclaw onboard
picoclaw onboard --enc
```

---

## picoclaw agent

Interact directly with the AI agent.

```bash
picoclaw agent [flags]
```

### Parameters

| Parameter | Short | Default | Description |
| --- | --- | --- | --- |
| `--debug` | `-d` | `false` | Enable debug logging |
| `--message` | `-m` | | Send a single message (non-interactive mode) |
| `--session` | `-s` | `cli:default` | Session key |
| `--model` | | | Model to use |

### Examples

```bash
# Interactive mode
picoclaw agent

# Non-interactive mode
picoclaw agent -m "Hello, how are you?"

# Specify model
picoclaw agent --model gpt-5.2 -m "Explain quantum computing"
```

---

## picoclaw auth

Manage authentication, including login, logout, and status.

```bash
picoclaw auth [command]
```

### Subcommands

- [`login`](#picoclaw-auth-login) - Login
- [`logout`](#picoclaw-auth-logout) - Logout
- [`status`](#picoclaw-auth-status) - Show authentication status
- [`models`](#picoclaw-auth-models) - Show available models
- [`weixin`](#picoclaw-auth-weixin) - Connect WeChat personal account
- [`wecom`](#picoclaw-auth-wecom) - Connect WeCom (Enterprise WeChat)

---

### picoclaw auth login

Login via OAuth or paste token.

```bash
picoclaw auth login [flags]
```

#### Parameters

| Parameter | Short | Description |
| --- | --- | --- |
| `--provider` | `-p` | **Required**. Login provider (`openai`, `anthropic`, `google-antigravity`, `antigravity`) |
| `--device-code` | | Use device code flow (for headless environments) |
| `--no-browser` | | Do not auto-open browser for OAuth login |
| `--setup-token` | | Use Anthropic's setup-token flow |

#### Examples

```bash
picoclaw auth login -p openai
picoclaw auth login -p anthropic --setup-token
picoclaw auth login -p openai --device-code
```

---

### picoclaw auth logout

Remove stored credentials.

```bash
picoclaw auth logout [flags]
```

#### Parameters

| Parameter | Short | Description |
| --- | --- | --- |
| `--provider` | `-p` | Provider to logout from (`openai`, `anthropic`); empty = logout all |

#### Examples

```bash
picoclaw auth logout -p openai
picoclaw auth logout
```

---

### picoclaw auth status

Show current authentication status.

```bash
picoclaw auth status
```

---

### picoclaw auth models

Show available models.

```bash
picoclaw auth models
```

---

### picoclaw auth weixin

Connect WeChat personal account via QR code.

```bash
picoclaw auth weixin [flags]
```

#### Parameters

| Parameter | Default | Description |
| --- | --- | --- |
| `--base-url` | `https://ilinkai.weixin.qq.com/` | iLink API base URL |
| `--proxy` | | HTTP proxy URL (e.g. `http://localhost:7890`) |
| `--timeout` | `300` | Login timeout in seconds |

#### Examples

```bash
picoclaw auth weixin
picoclaw auth weixin --proxy http://localhost:7890
```

---

### picoclaw auth wecom

Scan WeCom QR code and configure `channels.wecom`.

```bash
picoclaw auth wecom [flags]
```

#### Parameters

| Parameter | Default | Description |
| --- | --- | --- |
| `--timeout` | `5m` | Timeout waiting for QR code confirmation |

#### Examples

```bash
picoclaw auth wecom
picoclaw auth wecom --timeout 10m
```

---

## picoclaw gateway

Start the PicoClaw gateway service.

```bash
picoclaw gateway [flags]
```

### Parameters

| Parameter | Short | Default | Description |
| --- | --- | --- | --- |
| `--debug` | `-d` | `false` | Enable debug logging |
| `--no-truncate` | `-T` | `false` | Disable string truncation in debug logs (must be used with `--debug`) |
| `--allow-empty` | `-E` | `false` | Continue startup even when no default model is configured |
| `--host` | | | Gateway bind host address (overrides `gateway.host` config) |

### Examples

```bash
picoclaw gateway
picoclaw gateway -d
picoclaw gateway -d -T
picoclaw gateway --host 0.0.0.0
```

---

## picoclaw status

Show current PicoClaw status.

```bash
picoclaw status
```

---

## picoclaw cron

Manage scheduled tasks.

:::tip
For detailed documentation on schedule types, execution modes, and configuration, see [Scheduled Tasks & Cron Jobs](/docs/cron).
:::

```bash
picoclaw cron [command]
```

### Subcommands

- `list` - List all scheduled jobs
- `add` - Add a new job
- `remove` - Remove a job by ID
- `enable` - Enable a job
- `disable` - Disable a job

### picoclaw cron add

```bash
picoclaw cron add [flags]
```

#### Parameters

| Parameter | Short | Description |
| --- | --- | --- |
| `--name` | `-n` | **Required**. Job name |
| `--message` | `-m` | **Required**. Message to send to the agent |
| `--every` | `-e` | Run every N seconds |
| `--cron` | `-c` | Cron expression (e.g. `0 9 * * *`) |
| `--to` | | Recipient |
| `--channel` | | Delivery channel |

> **Note**: `--every` and `--cron` are mutually exclusive; one must be specified.

#### Examples

```bash
# Run every 60 seconds
picoclaw cron add -n "health-check" -m "Check system health" -e 60

# Run daily at 9am
picoclaw cron add -n "morning-report" -m "Generate morning report" -c "0 9 * * *"

# Specify delivery channel
picoclaw cron add -n "reminder" -m "Take a break" -e 1800 --channel wecom --to user123
```

### Other cron subcommands

```bash
picoclaw cron list
picoclaw cron remove <job-id>
picoclaw cron enable <job-id>
picoclaw cron disable <job-id>
```

---

## picoclaw mcp

Manage MCP (Model Context Protocol) server configurations.

```bash
picoclaw mcp [command]
```

### Subcommands

- [`add`](#picoclaw-mcp-add) - Add or update an MCP server
- [`remove`](#picoclaw-mcp-remove) - Remove an MCP server
- [`list`](#picoclaw-mcp-list) - List configured MCP servers
- [`edit`](#picoclaw-mcp-edit) - Open config in editor
- [`test`](#picoclaw-mcp-test) - Test MCP server connection
- [`show`](#picoclaw-mcp-show) - Show MCP server details

---

### picoclaw mcp add

Add or update an MCP server.

```bash
picoclaw mcp add [flags] <name> <command-or-url> [args...]
```

#### Parameters

| Parameter | Short | Default | Description |
| --- | --- | --- | --- |
| `--env` | `-e` | | Environment variable in `KEY=value` format (repeatable) |
| `--env-file` | | | Path to environment variable file (recommended for secrets) |
| `--header` | `-H` | | HTTP header in `Name: Value` or `Name=Value` format (repeatable) |
| `--transport` | `-t` | `stdio` | Transport type: `stdio`, `http`, or `sse` |
| `--force` | `-f` | `false` | Overwrite existing server without prompting |
| `--deferred` | | `false` | Mark as deferred loading (tools hidden until explicitly activated) |
| `--no-deferred` | | `false` | Mark as non-deferred (tools always active) |

#### Examples

```bash
# Add stdio MCP server
picoclaw mcp add my-server /path/to/server arg1 arg2

# Add http MCP server
picoclaw mcp add remote-server https://example.com/mcp --transport http

# Add with environment variables
picoclaw mcp add my-server /path/to/server -e API_KEY=xxx -e DEBUG=true

# Use env file
picoclaw mcp add my-server /path/to/server --env-file .env

# Force overwrite
picoclaw mcp add my-server /path/to/new-server --force
```

---

### picoclaw mcp remove

Remove an MCP server.

```bash
picoclaw mcp remove <name>
```

---

### picoclaw mcp list

List configured MCP servers.

```bash
picoclaw mcp list [flags]
```

#### Parameters

| Parameter | Default | Description |
| --- | --- | --- |
| `--status` | `false` | Ping enabled servers and show live status |
| `--timeout` | `5s` | Timeout per status check |

#### Examples

```bash
picoclaw mcp list
picoclaw mcp list --status
picoclaw mcp list --status --timeout 10s
```

---

### picoclaw mcp edit

Open the PicoClaw config file in `$EDITOR`.

```bash
picoclaw mcp edit
```

> **Note**: Requires the `EDITOR` environment variable to be set.

---

### picoclaw mcp test

Test MCP server connection.

```bash
picoclaw mcp test <name> [flags]
```

#### Parameters

| Parameter | Default | Description |
| --- | --- | --- |
| `--timeout` | `5s` | Connection timeout |

#### Examples

```bash
picoclaw mcp test my-server
picoclaw mcp test my-server --timeout 10s
```

---

### picoclaw mcp show

Show MCP server details and tool list.

```bash
picoclaw mcp show <name> [flags]
```

#### Parameters

| Parameter | Default | Description |
| --- | --- | --- |
| `--timeout` | `10s` | Connection timeout |

#### Examples

```bash
picoclaw mcp show my-server
picoclaw mcp show my-server --timeout 15s
```

---

## picoclaw migrate

Migrate from other tools (e.g. OpenClaw) to PicoClaw.

```bash
picoclaw migrate [flags]
```

### Parameters

| Parameter | Default | Description |
| --- | --- | --- |
| `--dry-run` | `false` | Show what would be migrated without making changes |
| `--from` | `openclaw` | Migration source (e.g. `openclaw`) |
| `--refresh` | `false` | Re-sync workspace files from OpenClaw |
| `--config-only` | `false` | Migrate config only, skip workspace files |
| `--workspace-only` | `false` | Migrate workspace files only, skip config |
| `--force` | `false` | Skip confirmation prompts |
| `--source-home` | `~/.openclaw` | Override source directory |
| `--target-home` | `~/.picoclaw` | Override target directory |

### Examples

```bash
# Default migration
picoclaw migrate

# Preview migration
picoclaw migrate --dry-run

# Config only
picoclaw migrate --config-only

# Force migration
picoclaw migrate --force
```

---

## picoclaw skills

Manage skills.

```bash
picoclaw skills [command]
```

### Subcommands

- [`list`](#picoclaw-skills-list) - List installed skills
- [`install`](#picoclaw-skills-install) - Install from GitHub or registry
- [`install-builtin`](#picoclaw-skills-install-builtin) - Install all built-in skills
- [`list-builtin`](#picoclaw-skills-list-builtin) - List available built-in skills
- [`remove`](#picoclaw-skills-remove) - Remove an installed skill
- [`search`](#picoclaw-skills-search) - Search available skills
- [`show`](#picoclaw-skills-show) - Show skill details

---

### picoclaw skills list

List installed skills.

```bash
picoclaw skills list
```

---

### picoclaw skills install

Install from GitHub or registry.

```bash
picoclaw skills install <github> [flags]
picoclaw skills install --registry <name> <slug>
```

#### Parameters

| Parameter | Description |
| --- | --- |
| `--registry` | Install from specified registry |

#### Examples

```bash
# Install from GitHub
picoclaw skills install sipeed/picoclaw-skills/weather

# Install from registry
picoclaw skills install --registry clawhub github
```

---

### picoclaw skills install-builtin

Install all built-in skills to workspace.

```bash
picoclaw skills install-builtin
```

---

### picoclaw skills list-builtin

List available built-in skills.

```bash
picoclaw skills list-builtin
```

---

### picoclaw skills remove

Remove an installed skill.

```bash
picoclaw skills remove <name>
```

> **Aliases**: `rm`, `uninstall`

#### Examples

```bash
picoclaw skills remove weather
picoclaw skills rm weather
picoclaw skills uninstall weather
```

---

### picoclaw skills search

Search available skills.

```bash
picoclaw skills search [query]
```

#### Examples

```bash
# List all skills
picoclaw skills search

# Search for a specific skill
picoclaw skills search weather
```

---

### picoclaw skills show

Show skill details.

```bash
picoclaw skills show <name>
```

#### Examples

```bash
picoclaw skills show weather
```

---

## picoclaw model

View or change the default model.

```bash
picoclaw model [model_name] [command]
```

### Parameters

| Parameter | Description |
| --- | --- |
| `model_name` | Optional. Model name to set as default |

### Subcommands

- [`add`](#picoclaw-model-add) - Add a model from an OpenAI-compatible endpoint

### Examples

```bash
# Show current default model
picoclaw model

# Set default model
picoclaw model gpt-5.2
picoclaw model claude-sonnet-4.6
picoclaw model local-model
```

---

### picoclaw model add

Add a model from an OpenAI-compatible endpoint and set it as default.

```bash
picoclaw model add [flags]
```

#### Parameters

| Parameter | Short | Default | Description |
| --- | --- | --- | --- |
| `--api-base` | `-b` | | **Required**. API base URL |
| `--api-key` | `-k` | | **Required**. API key |
| `--model` | `-m` | | Model ID; skips interactive selection and network calls when set |
| `--name` | `-n` | `custom-prefer` | Local alias, written to model_list and used as default model name |
| `--type` | | `openai-compatible` | Endpoint type (currently only `openai-compatible`) |

#### Examples

```bash
# Interactive model selection
picoclaw model add -b https://api.openai.com/v1 -k sk-xxx

# Specify model directly
picoclaw model add -b http://localhost:8000/v1 -k dummy -m my-model -n local
```

---

## picoclaw update

Update PicoClaw to the latest version.

```bash
picoclaw update
```

---

## picoclaw version

Show version information.

```bash
picoclaw version
```

---

## Environment Variables

| Variable | Description |
| --- | --- |
| `NO_COLOR` | Set to any non-empty value to disable colored output |
| `TERM` | Set to `dumb` to disable colored output |
| `TZ` | Set timezone |
| `EDITOR` | Editor for `picoclaw mcp edit` command |
| `GATEWAY_HOST` | Gateway bind host address (can be overridden by `--host` flag) |

---

## Configuration File

PicoClaw's config file is located at `~/.picoclaw/config.json` by default. You can view and edit it with:

```bash
# Show config path
picoclaw status

# Edit config
picoclaw mcp edit
```

---

## `picoclaw-launcher` Parameters

The `picoclaw-launcher` binary provides the GUI launcher with the following parameters:

| Parameter | Description | Example |
| --- | --- | --- |
| `-console` | Run in terminal mode (no tray GUI), prints login hint/token source in console startup output | `picoclaw-launcher -console` |
| `-public` | Listen on `0.0.0.0`, allow LAN devices to access WebUI | `picoclaw-launcher -public` |
| `-no-browser` | Do not auto-open browser on startup | `picoclaw-launcher -no-browser` |
| `-port <port>` | Specify launcher port (default `18800`) | `picoclaw-launcher -port 19999` |
| `-lang <en\|zh>` | Set launcher UI language | `picoclaw-launcher -lang zh` |
| `[config.json]` | Optional positional config path | `picoclaw-launcher ./config.json` |

Common combinations:

```bash
# Headless/SSH server: run in console mode and expose to LAN
picoclaw-launcher -console -no-browser -public

# Custom port with explicit config file
picoclaw-launcher -port 19999 ./config.json
```

---

## Getting Help

Get detailed help for any command:

```bash
picoclaw --help
picoclaw <command> --help
picoclaw <command> <subcommand> --help
```

Examples:

```bash
picoclaw mcp add --help
picoclaw cron add --help
picoclaw model add --help
```
