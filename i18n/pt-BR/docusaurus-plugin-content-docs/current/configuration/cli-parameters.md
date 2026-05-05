---
id: cli-parameters
title: Comandos e Parâmetros CLI
---

# Comandos e Parâmetros CLI

O PicoClaw é um assistente de IA pessoal leve. Esta página documenta todos os comandos e parâmetros CLI suportados pelo binário `picoclaw`.

:::tip
Para comandos slash de chat (ex. `/help`, `/clear`), veja [Referência de Comandos de Chat](/docs/chat-commands).
:::

## Parâmetros Globais

| Parâmetro | Descrição |
| --- | --- |
| `--no-color` | Desabilita saída colorida (preserva layout de bordas) |

Você também pode desabilitar cores através da variável de ambiente `NO_COLOR` ou `TERM=dumb`.

---

## Visão Geral dos Comandos

| Comando | Alias | Descrição |
| --- | --- | --- |
| [`picoclaw onboard`](#picoclaw-onboard) | `o` | Inicializa configuração e workspace |
| [`picoclaw agent`](#picoclaw-agent) | | Interage com o agente de IA |
| [`picoclaw auth`](#picoclaw-auth) | | Gerencia autenticação (login, logout, status) |
| [`picoclaw gateway`](#picoclaw-gateway) | `g` | Inicia o gateway do PicoClaw |
| [`picoclaw status`](#picoclaw-status) | `s` | Mostra o status do PicoClaw |
| [`picoclaw cron`](#picoclaw-cron) | `c` | Gerencia tarefas agendadas |
| [`picoclaw mcp`](#picoclaw-mcp) | | Gerencia configurações de servidores MCP |
| [`picoclaw migrate`](#picoclaw-migrate) | | Migra de outras ferramentas para o PicoClaw |
| [`picoclaw skills`](#picoclaw-skills) | | Gerencia skills |
| [`picoclaw model`](#picoclaw-model) | | Visualiza ou altera o modelo padrão |
| [`picoclaw update`](#picoclaw-update) | | Atualiza o PicoClaw |
| [`picoclaw version`](#picoclaw-version) | `v` | Mostra informações de versão |

---

## picoclaw onboard

Inicializa a configuração e workspace do PicoClaw.

```bash
picoclaw onboard [flags]
```

### Parâmetros

| Parâmetro | Descrição |
| --- | --- |
| `--enc` | Habilita criptografia de credenciais (gera chave SSH e solicita senha) |

### Exemplos

```bash
picoclaw onboard
picoclaw onboard --enc
```

---

## picoclaw agent

Interage diretamente com o agente de IA.

```bash
picoclaw agent [flags]
```

### Parâmetros

| Parâmetro | Curto | Padrão | Descrição |
| --- | --- | --- | --- |
| `--debug` | `-d` | `false` | Habilita logging de debug |
| `--message` | `-m` | | Envia uma única mensagem (modo não interativo) |
| `--session` | `-s` | `cli:default` | Chave de sessão |
| `--model` | | | Modelo a ser usado |

### Exemplos

```bash
# Modo interativo
picoclaw agent

# Modo não interativo
picoclaw agent -m "Hello, how are you?"

# Especificar modelo
picoclaw agent --model gpt-5.2 -m "Explain quantum computing"
```

---

## picoclaw auth

Gerencia autenticação, incluindo login, logout e status.

```bash
picoclaw auth [command]
```

### Subcomandos

- [`login`](#picoclaw-auth-login) - Login
- [`logout`](#picoclaw-auth-logout) - Logout
- [`status`](#picoclaw-auth-status) - Mostra status de autenticação
- [`models`](#picoclaw-auth-models) - Mostra modelos disponíveis
- [`weixin`](#picoclaw-auth-weixin) - Conecta conta pessoal do WeChat
- [`wecom`](#picoclaw-auth-wecom) - Conecta WeCom (WeChat Empresarial)

---

### picoclaw auth login

Login via OAuth ou colando token.

```bash
picoclaw auth login [flags]
```

#### Parâmetros

| Parâmetro | Curto | Descrição |
| --- | --- | --- |
| `--provider` | `-p` | **Obrigatório**. Provedor de login (`openai`, `anthropic`, `google-antigravity`, `antigravity`) |
| `--device-code` | | Usa fluxo de código de dispositivo (para ambientes sem interface) |
| `--no-browser` | | Não abre navegador automaticamente para login OAuth |
| `--setup-token` | | Usa o fluxo setup-token da Anthropic |

#### Exemplos

```bash
picoclaw auth login -p openai
picoclaw auth login -p anthropic --setup-token
picoclaw auth login -p openai --device-code
```

---

### picoclaw auth logout

Remove credenciais armazenadas.

```bash
picoclaw auth logout [flags]
```

#### Parâmetros

| Parâmetro | Curto | Descrição |
| --- | --- | --- |
| `--provider` | `-p` | Provedor para fazer logout (`openai`, `anthropic`); vazio = logout de todos |

#### Exemplos

```bash
picoclaw auth logout -p openai
picoclaw auth logout
```

---

### picoclaw auth status

Mostra o status de autenticação atual.

```bash
picoclaw auth status
```

---

### picoclaw auth models

Mostra modelos disponíveis.

```bash
picoclaw auth models
```

---

### picoclaw auth weixin

Conecta conta pessoal do WeChat via QR code.

```bash
picoclaw auth weixin [flags]
```

#### Parâmetros

| Parâmetro | Padrão | Descrição |
| --- | --- | --- |
| `--base-url` | `https://ilinkai.weixin.qq.com/` | URL base da API iLink |
| `--proxy` | | URL do proxy HTTP (ex. `http://localhost:7890`) |
| `--timeout` | `300` | Timeout de login em segundos |

#### Exemplos

```bash
picoclaw auth weixin
picoclaw auth weixin --proxy http://localhost:7890
```

---

### picoclaw auth wecom

Escaneia QR code do WeCom e configura `channels.wecom`.

```bash
picoclaw auth wecom [flags]
```

#### Parâmetros

| Parâmetro | Padrão | Descrição |
| --- | --- | --- |
| `--timeout` | `5m` | Timeout aguardando confirmação do QR code |

#### Exemplos

```bash
picoclaw auth wecom
picoclaw auth wecom --timeout 10m
```

---

## picoclaw gateway

Inicia o serviço gateway do PicoClaw.

```bash
picoclaw gateway [flags]
```

### Parâmetros

| Parâmetro | Curto | Padrão | Descrição |
| --- | --- | --- | --- |
| `--debug` | `-d` | `false` | Habilita logging de debug |
| `--no-truncate` | `-T` | `false` | Desabilita truncamento de strings nos logs de debug (deve ser usado com `--debug`) |
| `--allow-empty` | `-E` | `false` | Continua a inicialização mesmo sem modelo padrão configurado |
| `--host` | | | Endereço de host do gateway (sobrescreve config `gateway.host`) |

### Exemplos

```bash
picoclaw gateway
picoclaw gateway -d
picoclaw gateway -d -T
picoclaw gateway --host 0.0.0.0
```

---

## picoclaw status

Mostra o status atual do PicoClaw.

```bash
picoclaw status
```

---

## picoclaw cron

Gerencia tarefas agendadas.

:::tip
Para documentação detalhada sobre tipos de agendamento, modos de execução e configuração, veja [Tarefas Agendadas & Cron Jobs](/docs/cron).
:::

```bash
picoclaw cron [command]
```

### Subcomandos

- `list` - Lista todas as tarefas agendadas
- `add` - Adiciona uma nova tarefa
- `remove` - Remove uma tarefa por ID
- `enable` - Habilita uma tarefa
- `disable` - Desabilita uma tarefa

### picoclaw cron add

```bash
picoclaw cron add [flags]
```

#### Parâmetros

| Parâmetro | Curto | Descrição |
| --- | --- | --- |
| `--name` | `-n` | **Obrigatório**. Nome da tarefa |
| `--message` | `-m` | **Obrigatório**. Mensagem para enviar ao agente |
| `--every` | `-e` | Executa a cada N segundos |
| `--cron` | `-c` | Expressão cron (ex. `0 9 * * *`) |
| `--to` | | Destinatário |
| `--channel` | | Canal de entrega |

> **Nota**: `--every` e `--cron` são mutuamente exclusivos; um deve ser especificado.

#### Exemplos

```bash
# Executa a cada 60 segundos
picoclaw cron add -n "health-check" -m "Check system health" -e 60

# Executa diariamente às 9h
picoclaw cron add -n "morning-report" -m "Generate morning report" -c "0 9 * * *"

# Especificar canal de entrega
picoclaw cron add -n "reminder" -m "Take a break" -e 1800 --channel wecom --to user123
```

### Outros subcomandos cron

```bash
picoclaw cron list
picoclaw cron remove <job-id>
picoclaw cron enable <job-id>
picoclaw cron disable <job-id>
```

---

## picoclaw mcp

Gerencia configurações de servidores MCP (Model Context Protocol).

```bash
picoclaw mcp [command]
```

### Subcomandos

- [`add`](#picoclaw-mcp-add) - Adiciona ou atualiza um servidor MCP
- [`remove`](#picoclaw-mcp-remove) - Remove um servidor MCP
- [`list`](#picoclaw-mcp-list) - Lista servidores MCP configurados
- [`edit`](#picoclaw-mcp-edit) - Abre configuração no editor
- [`test`](#picoclaw-mcp-test) - Testa conexão com servidor MCP
- [`show`](#picoclaw-mcp-show) - Mostra detalhes do servidor MCP

---

### picoclaw mcp add

Adiciona ou atualiza um servidor MCP.

```bash
picoclaw mcp add [flags] <name> <command-or-url> [args...]
```

#### Parâmetros

| Parâmetro | Curto | Padrão | Descrição |
| --- | --- | --- | --- |
| `--env` | `-e` | | Variável de ambiente no formato `KEY=value` (repetível) |
| `--env-file` | | | Caminho para arquivo de variáveis de ambiente (recomendado para segredos) |
| `--header` | `-H` | | Cabeçalho HTTP no formato `Name: Value` ou `Name=Value` (repetível) |
| `--transport` | `-t` | `stdio` | Tipo de transporte: `stdio`, `http` ou `sse` |
| `--force` | `-f` | `false` | Sobrescreve servidor existente sem perguntar |
| `--deferred` | | `false` | Marca como carregamento adiado (ferramentas ocultas até serem explicitamente ativadas) |
| `--no-deferred` | | `false` | Marca como não adiado (ferramentas sempre ativas) |

#### Exemplos

```bash
# Adicionar servidor MCP stdio
picoclaw mcp add my-server /path/to/server arg1 arg2

# Adicionar servidor MCP http
picoclaw mcp add remote-server https://example.com/mcp --transport http

# Adicionar com variáveis de ambiente
picoclaw mcp add my-server /path/to/server -e API_KEY=xxx -e DEBUG=true

# Usar arquivo de variáveis de ambiente
picoclaw mcp add my-server /path/to/server --env-file .env

# Forçar sobrescrita
picoclaw mcp add my-server /path/to/new-server --force
```

---

### picoclaw mcp remove

Remove um servidor MCP.

```bash
picoclaw mcp remove <name>
```

---

### picoclaw mcp list

Lista servidores MCP configurados.

```bash
picoclaw mcp list [flags]
```

#### Parâmetros

| Parâmetro | Padrão | Descrição |
| --- | --- | --- |
| `--status` | `false` | Faz ping nos servidores habilitados e mostra status em tempo real |
| `--timeout` | `5s` | Timeout por verificação de status |

#### Exemplos

```bash
picoclaw mcp list
picoclaw mcp list --status
picoclaw mcp list --status --timeout 10s
```

---

### picoclaw mcp edit

Abre o arquivo de configuração do PicoClaw no `$EDITOR`.

```bash
picoclaw mcp edit
```

> **Nota**: Requer que a variável de ambiente `EDITOR` esteja configurada.

---

### picoclaw mcp test

Testa conexão com servidor MCP.

```bash
picoclaw mcp test <name> [flags]
```

#### Parâmetros

| Parâmetro | Padrão | Descrição |
| --- | --- | --- |
| `--timeout` | `5s` | Timeout de conexão |

#### Exemplos

```bash
picoclaw mcp test my-server
picoclaw mcp test my-server --timeout 10s
```

---

### picoclaw mcp show

Mostra detalhes e lista de ferramentas do servidor MCP.

```bash
picoclaw mcp show <name> [flags]
```

#### Parâmetros

| Parâmetro | Padrão | Descrição |
| --- | --- | --- |
| `--timeout` | `10s` | Timeout de conexão |

#### Exemplos

```bash
picoclaw mcp show my-server
picoclaw mcp show my-server --timeout 15s
```

---

## picoclaw migrate

Migra de outras ferramentas (ex. OpenClaw) para o PicoClaw.

```bash
picoclaw migrate [flags]
```

### Parâmetros

| Parâmetro | Padrão | Descrição |
| --- | --- | --- |
| `--dry-run` | `false` | Mostra o que seria migrado sem fazer alterações |
| `--from` | `openclaw` | Fonte da migração (ex. `openclaw`) |
| `--refresh` | `false` | Re-sincroniza arquivos do workspace do OpenClaw |
| `--config-only` | `false` | Migra apenas configuração, pula arquivos do workspace |
| `--workspace-only` | `false` | Migra apenas arquivos do workspace, pula configuração |
| `--force` | `false` | Pula confirmações |
| `--source-home` | `~/.openclaw` | Sobrescreve diretório de origem |
| `--target-home` | `~/.picoclaw` | Sobrescreve diretório de destino |

### Exemplos

```bash
# Migração padrão
picoclaw migrate

# Visualizar migração
picoclaw migrate --dry-run

# Apenas configuração
picoclaw migrate --config-only

# Forçar migração
picoclaw migrate --force
```

---

## picoclaw skills

Gerencia skills.

```bash
picoclaw skills [command]
```

### Subcomandos

- [`list`](#picoclaw-skills-list) - Lista skills instaladas
- [`install`](#picoclaw-skills-install) - Instala do GitHub ou registro
- [`install-builtin`](#picoclaw-skills-install-builtin) - Instala todas as skills embutidas
- [`list-builtin`](#picoclaw-skills-list-builtin) - Lista skills embutidas disponíveis
- [`remove`](#picoclaw-skills-remove) - Remove uma skill instalada
- [`search`](#picoclaw-skills-search) - Pesquisa skills disponíveis
- [`show`](#picoclaw-skills-show) - Mostra detalhes da skill

---

### picoclaw skills list

Lista skills instaladas.

```bash
picoclaw skills list
```

---

### picoclaw skills install

Instala do GitHub ou registro.

```bash
picoclaw skills install <github> [flags]
picoclaw skills install --registry <name> <slug>
```

#### Parâmetros

| Parâmetro | Descrição |
| --- | --- |
| `--registry` | Instala do registro especificado |

#### Exemplos

```bash
# Instalar do GitHub
picoclaw skills install sipeed/picoclaw-skills/weather

# Instalar do registro
picoclaw skills install --registry clawhub github
```

---

### picoclaw skills install-builtin

Instala todas as skills embutidas no workspace.

```bash
picoclaw skills install-builtin
```

---

### picoclaw skills list-builtin

Lista skills embutidas disponíveis.

```bash
picoclaw skills list-builtin
```

---

### picoclaw skills remove

Remove uma skill instalada.

```bash
picoclaw skills remove <name>
```

> **Aliases**: `rm`, `uninstall`

#### Exemplos

```bash
picoclaw skills remove weather
picoclaw skills rm weather
picoclaw skills uninstall weather
```

---

### picoclaw skills search

Pesquisa skills disponíveis.

```bash
picoclaw skills search [query]
```

#### Exemplos

```bash
# Listar todas as skills
picoclaw skills search

# Pesquisar uma skill específica
picoclaw skills search weather
```

---

### picoclaw skills show

Mostra detalhes da skill.

```bash
picoclaw skills show <name>
```

#### Exemplos

```bash
picoclaw skills show weather
```

---

## picoclaw model

Visualiza ou altera o modelo padrão.

```bash
picoclaw model [model_name] [command]
```

### Parâmetros

| Parâmetro | Descrição |
| --- | --- |
| `model_name` | Opcional. Nome do modelo para definir como padrão |

### Subcomandos

- [`add`](#picoclaw-model-add) - Adiciona um modelo de um endpoint compatível com OpenAI

### Exemplos

```bash
# Mostrar modelo padrão atual
picoclaw model

# Definir modelo padrão
picoclaw model gpt-5.2
picoclaw model claude-sonnet-4.6
picoclaw model local-model
```

---

### picoclaw model add

Adiciona um modelo de um endpoint compatível com OpenAI e o define como padrão.

```bash
picoclaw model add [flags]
```

#### Parâmetros

| Parâmetro | Curto | Padrão | Descrição |
| --- | --- | --- | --- |
| `--api-base` | `-b` | | **Obrigatório**. URL base da API |
| `--api-key` | `-k` | | **Obrigatório**. Chave da API |
| `--model` | `-m` | | ID do modelo; pula seleção interativa e chamadas de rede quando definido |
| `--name` | `-n` | `custom-prefer` | Alias local, gravado no model_list e usado como nome do modelo padrão |
| `--type` | | `openai-compatible` | Tipo de endpoint (atualmente apenas `openai-compatible`) |

#### Exemplos

```bash
# Seleção interativa de modelo
picoclaw model add -b https://api.openai.com/v1 -k sk-xxx

# Especificar modelo diretamente
picoclaw model add -b http://localhost:8000/v1 -k dummy -m my-model -n local
```

---

## picoclaw update

Atualiza o PicoClaw para a versão mais recente.

```bash
picoclaw update
```

---

## picoclaw version

Mostra informações de versão.

```bash
picoclaw version
```

---

## Variáveis de Ambiente

| Variável | Descrição |
| --- | --- |
| `NO_COLOR` | Defina como qualquer valor não vazio para desabilitar saída colorida |
| `TERM` | Defina como `dumb` para desabilitar saída colorida |
| `TZ` | Define fuso horário |
| `EDITOR` | Editor para o comando `picoclaw mcp edit` |
| `GATEWAY_HOST` | Endereço de host do gateway (pode ser sobrescrito pelo flag `--host`) |

---

## Arquivo de Configuração

O arquivo de configuração do PicoClaw está localizado em `~/.picoclaw/config.json` por padrão. Você pode visualizar e editar com:

```bash
# Mostrar caminho da configuração
picoclaw status

# Editar configuração
picoclaw mcp edit
```

---

## Parâmetros do `picoclaw-launcher`

O binário `picoclaw-launcher` fornece o launcher GUI com os seguintes parâmetros:

| Parâmetro | Descrição | Exemplo |
| --- | --- | --- |
| `-console` | Executa no terminal (sem GUI de bandeja), imprime dica de login/origem do token no startup | `picoclaw-launcher -console` |
| `-public` | Escuta em `0.0.0.0`, permite acesso ao WebUI por dispositivos na LAN | `picoclaw-launcher -public` |
| `-no-browser` | Não abre o navegador automaticamente ao iniciar | `picoclaw-launcher -no-browser` |
| `-port <port>` | Define a porta do launcher (padrão `18800`) | `picoclaw-launcher -port 19999` |
| `-lang <en\|zh>` | Define o idioma da interface do launcher | `picoclaw-launcher -lang zh` |
| `[config.json]` | Caminho posicional opcional para arquivo de configuração | `picoclaw-launcher ./config.json` |

Combinações comuns:

```bash
# Servidor headless/SSH: executa em modo console e expõe para LAN
picoclaw-launcher -console -no-browser -public

# Porta customizada com arquivo de configuração explícito
picoclaw-launcher -port 19999 ./config.json
```

---

## Obtendo Ajuda

Obtenha ajuda detalhada para qualquer comando:

```bash
picoclaw --help
picoclaw <command> --help
picoclaw <command> <subcommand> --help
```

Exemplos:

```bash
picoclaw mcp add --help
picoclaw cron add --help
picoclaw model add --help
```
