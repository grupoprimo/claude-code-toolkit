# PR Creator

Comando do Claude Code para criar descrições de Pull Request e mensagens de review automaticamente.

## Instalação

```bash
# Copie a pasta para seu diretório de comandos
cp -r commands/pr-creator ~/.claude/commands/
```

## Uso Básico

```bash
# Abra o Claude Code no seu projeto
cd ~/seu-projeto
claude

# Execute o comando
/pr-creator
```

## O Que Ele Faz

1. **Analisa seus commits** - Identifica automaticamente quais commits incluir no PR
2. **Detecta o template** - Usa o PR template do projeto (`.github/pull_request_template.md`)
3. **Gera descrição** - Cria descrição completa seguindo o template
4. **Gera mensagem de review** - Cria mensagem formatada para enviar no grupo
5. **Cria o PR** - (Opcional) Cria o PR automaticamente via GitHub CLI

## Estratégias de Comparação

O comando suporta 3 estratégias para identificar seus commits:

| Estratégia | Quando Usar |
|------------|-------------|
| **Branch-based** | Branch simples onde todos os commits são seus |
| **Merge-base** | Branch compartilhada com commits de outras features |
| **Author-based** | Quando precisa filtrar apenas seus commits |

## Exemplos

### Caso 1: Branch Simples

```
> /pr-creator

Claude: "Qual a branch base?"
User: "main"

Claude: "Encontrei 5 commits. Todos são seus. Gerar PR?"
User: "Sim"

[Gera descrição e mensagem]
```

### Caso 2: Branch Compartilhada

```
> /pr-creator

Claude: "Qual a branch base?"
User: "refactor/GRAO-4560"

Claude: "Detectei 47 commits, mas parecem ser de múltiplas features.
         Usar merge-base para pegar apenas seus commits?"
User: "Sim"

Claude: "Com merge-base, encontrei 8 commits seus. Correto?"
User: "Sim"

[Gera descrição apenas com os 8 commits]
```

### Caso 3: Criar PR Automaticamente

```
> /pr-creator

[... análise de commits ...]

Claude: "Deseja criar o PR automaticamente?"
User: "Sim, como draft"

Claude: "Adicionar reviewers?"
User: "joao, maria"

Claude: "PR criado: https://github.com/org/repo/pull/123"
```

## Pré-requisitos

- **GitHub CLI** instalado e autenticado
  ```bash
  brew install gh
  gh auth login
  ```

## Output Gerado

### Descrição do PR

```markdown
# Description

Este PR implementa [feature] no [módulo].

**Base branch**: main
**Comparison**: branch-based
**Commits analyzed**: 5

## Objetivo

[Objetivo extraído dos commits]

## Mudanças Implementadas

- Feature 1
- Feature 2
- Feature 3

## Type of change

- [x] New feature

## Checklist

- [x] Tests added
- [x] Documentation updated
```

### Mensagem de Review

```
Bom dia pessoal,

Abri PR de [feature] para [base].

Principais mudanças:
- Mudança 1
- Mudança 2
- Mudança 3

Link do PR: https://github.com/org/repo/pull/123
```

## Troubleshooting

| Problema | Solução |
|----------|---------|
| `gh: command not found` | Instale: `brew install gh` |
| `not logged in` | Execute: `gh auth login` |
| Muitos commits incluídos | Use estratégia `merge-base` |
| PR já existe | Use `gh pr view` para ver o existente |

## Arquivos

- `SKILL.md` - Instruções principais do comando
- `references/pr-examples.md` - Exemplos de PRs do time
- `references/commit-analysis.md` - Como analisar commits

## Contribuindo

Sugestões de melhoria? Abra um PR ou issue!
