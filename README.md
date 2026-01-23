# Claude Code Toolkit

Repositório compartilhado de **comandos**, **skills** e **agentes** do [Claude Code](https://docs.anthropic.com/en/docs/claude-code) para o time de desenvolvimento do Grupo Primo.

## O que é isso?

O Claude Code permite criar comandos personalizados (skills) que automatizam tarefas repetitivas do dia a dia. Este repositório centraliza esses comandos para que todo o time possa:

- Usar comandos criados por outros devs
- Contribuir com novos comandos
- Melhorar comandos existentes
- Padronizar workflows do time

## Instalação

### 1. Instalar Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

### 2. Clonar este repositório

```bash
git clone git@github.com:GrupoPrimo/claude-code-toolkit.git
```

### 3. Copiar comandos para seu diretório local

```bash
# Copiar todos os comandos
cp -r claude-code-toolkit/commands/* ~/.claude/commands/

# Ou apenas um comando específico
cp -r claude-code-toolkit/commands/pr-creator ~/.claude/commands/
```

### 4. Verificar instalação

```bash
# Abra o Claude Code e digite /
# Os comandos instalados devem aparecer na lista
claude
```

## Comandos Disponíveis

| Comando | Descrição | Autor |
|---------|-----------|-------|
| `/pr-creator` | Cria descrições de PR e mensagens de review automaticamente | @gbrl-coelho-gp |

## Estrutura do Repositório

```
claude-code-toolkit/
├── README.md                 # Este arquivo
├── CONTRIBUTING.md           # Como contribuir
├── commands/                 # Comandos/Skills
│   └── pr-creator/
│       ├── SKILL.md          # Arquivo principal do comando
│       └── references/       # Arquivos de referência
│           ├── pr-examples.md
│           └── commit-analysis.md
└── agents/                   # Agentes personalizados (futuro)
```

## Como usar um comando

### Exemplo: `/pr-creator`

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

4. O Claude vai gerar:
   - Descrição completa do PR seguindo o template do projeto
   - Mensagem para enviar no grupo de review
   - (Opcional) Criar o PR via GitHub CLI

### Opções do pr-creator

```bash
# Criar PR com opções específicas
/pr-creator --base develop --draft

# Apenas gerar descrição (sem criar PR)
/pr-creator --description-only

# Especificar estratégia de commits
/pr-creator --strategy merge-base
```

## Contribuindo

Veja [CONTRIBUTING.md](./CONTRIBUTING.md) para instruções de como criar e compartilhar seus próprios comandos.

## Links Úteis

- [Claude Code Docs](https://docs.anthropic.com/en/docs/claude-code)
- [Creating Custom Commands](https://docs.anthropic.com/en/docs/claude-code/tutorials/custom-slash-commands)
- [Claude Code GitHub](https://github.com/anthropics/claude-code)

## Suporte

Dúvidas ou problemas? Abra uma issue ou mande mensagem no canal Engenheiros/Devs do Teams.