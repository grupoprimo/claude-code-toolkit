# Claude Code Toolkit

Repositório compartilhado de **comandos**, **skills** e **agentes** do [Claude Code](https://docs.anthropic.com/en/docs/claude-code) para o time de desenvolvimento do Grupo Primo.

## O que é isso?

O Claude Code permite criar comandos personalizados (skills) e agentes que automatizam tarefas repetitivas do dia a dia. Este repositório centraliza esses recursos para que todo o time possa:

- Usar comandos e agentes criados por outros devs
- Contribuir com novos comandos e agentes
- Melhorar recursos existentes
- Padronizar workflows do time

## Conceitos

### Commands vs Agents

| Tipo        | Descrição                                      | Invocação            | Exemplo            |
| ----------- | ---------------------------------------------- | -------------------- | ------------------ |
| **Command** | Skill invocada manualmente pelo usuário        | `/comando`           | `/pr-creator`      |
| **Agent**   | Subagente delegado automaticamente pelo Claude | Automático ou manual | `business-analyst` |

**Commands** são úteis para tarefas que você quer executar sob demanda (criar PR, gerar documentação).

**Agents** são úteis para tarefas especializadas que o Claude pode delegar automaticamente quando detecta a necessidade (análise de negócios, revisão de código).

## Instalação

### 1. Instalar Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

### 2. Clonar este repositório

```bash
git clone git@github.com:GrupoPrimo/claude-code-toolkit.git
```

### 3. Instalar Commands

```bash
# Copiar todos os comandos
cp -r claude-code-toolkit/commands/* ~/.claude/commands/

# Ou apenas um comando específico
cp -r claude-code-toolkit/commands/pr-creator ~/.claude/commands/
```

### 4. Instalar Agents

```bash
# Criar diretório de agentes (se não existir)
mkdir -p ~/.claude/agents

# Copiar agente
cp claude-code-toolkit/agents/business-analyst/business-analyst.md ~/.claude/agents/
```

### 5. Verificar instalação

```bash
# Abra o Claude Code - os comandos e agentes estarão disponíveis
claude
```

## Recursos Disponíveis

### Commands

| Comando       | Descrição                                                   | Autor           |
| ------------- | ----------------------------------------------------------- | --------------- |
| `/pr-creator` | Cria descrições de PR e mensagens de review automaticamente | @gbrl-coelho-gp |

### Agents

| Agente             | Descrição                                                                                          | Autor           |
| ------------------ | -------------------------------------------------------------------------------------------------- | --------------- |
| `business-analyst` | Product management e business analysis: epics, stories, bugs, tasks, spikes, sprint planning, TDDs | @gbrl-coelho-gp |

## Estrutura do Repositório

```
claude-code-toolkit/
├── README.md
├── CONTRIBUTING.md
├── commands/                    # Commands/Skills (/comando)
│   └── pr-creator/
│       ├── SKILL.md             # Arquivo principal do comando
│       └── references/          # Arquivos de referência
│           ├── pr-examples.md
│           └── commit-analysis.md
└── agents/                      # Agentes (subagents)
    └── business-analyst/
        ├── business-analyst.md  # Arquivo principal do agente
        └── references/          # Arquivos de referência
            └── tdd-example.md   # Exemplo de TDD
```

## Usando o Agente business-analyst

O agente `business-analyst` é automaticamente invocado quando o Claude detecta tarefas de product management. Você também pode invocá-lo manualmente:

```
Use o business-analyst agent para criar uma user story para autenticação via Google
```

### Capacidades

- **Epics**: Criação de épicos com KPIs, escopo, riscos
- **User Stories**: Stories com acceptance criteria em Gherkin
- **Bugs**: Reports detalhados com steps to reproduce
- **Tasks/Sub-tasks**: Breakdown técnico de trabalho
- **Spikes**: Documentação de research timeboxado
- **Sprint Planning**: Planejamento com capacidade e riscos
- **TDDs**: Technical Design Documents completos
- **Priorização**: WSJF, MoSCoW, RICE scoring

### Integração com Jira

O agente está configurado para usar ferramentas MCP do Atlassian (`mcp__atlassian__*`). Para funcionar, você precisa ter o MCP do Jira configurado no seu ambiente.

## Como Criar Novos Recursos

### Criando um Command

1. Crie um diretório em `commands/`:

```bash
mkdir -p commands/meu-comando/references
```

2. Crie o arquivo `SKILL.md` com frontmatter:

```markdown
---
name: meu-comando
description: Descrição do que o comando faz
---

# Meu Comando

[Instruções detalhadas para o Claude executar o comando]
```

3. Adicione referências se necessário em `references/`

4. Copie para `~/.claude/commands/` para testar

### Criando um Agent

1. Crie um diretório em `agents/`:

```bash
mkdir -p agents/meu-agente/references
```

2. Crie o arquivo do agente com frontmatter:

```markdown
---
name: meu-agente
description: "Descrição detalhada com exemplos de quando usar.\n\nExamples:\n\n<example>\nContext: [contexto]\nuser: \"[pergunta do usuário]\"\nassistant: \"[como o agente responde]\"\n</example>"
tools: Glob, Grep, Read, Write, Edit, Bash
model: sonnet
---

[System prompt detalhado do agente]
```

3. Adicione referências se necessário em `references/`

4. Copie para `~/.claude/agents/` para testar

### Frontmatter de Agents

| Campo         | Obrigatório | Descrição                                        |
| ------------- | ----------- | ------------------------------------------------ |
| `name`        | Sim         | Identificador único (lowercase, hífens)          |
| `description` | Sim         | Descrição com exemplos de uso                    |
| `tools`       | Não         | Ferramentas disponíveis (herda todas se omitido) |
| `model`       | Não         | `sonnet`, `opus`, `haiku` ou `inherit`           |
| `color`       | Não         | Cor para identificação visual                    |

### Ferramentas MCP

Para agentes que usam MCP (Jira, Confluence, etc), adicione as ferramentas no campo `tools`:

```yaml
tools: Glob, Grep, Read, Write, mcp__atlassian__*
```

O wildcard `mcp__atlassian__*` permite todas as ferramentas do MCP Atlassian.

## Usando os Commands

### `/pr-creator`

1. Abra o Claude Code no diretório do seu projeto:

```bash
cd ~/seu-projeto
claude
```

2. Digite o comando:

```
/pr-creator
```

3. Siga as instruções interativas:
   - Escolha a estratégia de comparação de commits
   - Confirme os commits que serão incluídos
   - Escolha se quer criar o PR automaticamente

## Contribuindo

Veja [CONTRIBUTING.md](./CONTRIBUTING.md) para instruções de como criar e compartilhar seus próprios comandos e agentes.

## Links Úteis

- [Claude Code Docs](https://docs.anthropic.com/en/docs/claude-code)
- [Creating Custom Commands](https://docs.anthropic.com/en/docs/claude-code/tutorials/custom-slash-commands)
- [Creating Custom Agents](https://code.claude.com/docs/en/sub-agents)
- [Claude Code GitHub](https://github.com/anthropics/claude-code)

## Suporte

Dúvidas ou problemas? Abra uma issue ou mande mensagem no canal Engenheiros/Devs do Teams.
