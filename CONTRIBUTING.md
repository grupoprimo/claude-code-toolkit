# Contribuindo com o Claude Code Toolkit

Quer criar um comando novo ou melhorar um existente? Este guia vai te ajudar.

## Estrutura de um Comando

Um comando do Claude Code é uma pasta com arquivos markdown que definem instruções para o Claude seguir.

### Estrutura Mínima

```
commands/
└── meu-comando/
    └── SKILL.md       # Arquivo principal (obrigatório)
```

### Estrutura Completa

```
commands/
└── meu-comando/
    ├── SKILL.md           # Arquivo principal com instruções
    └── references/        # Arquivos de referência (opcional)
        ├── examples.md    # Exemplos de uso
        └── patterns.md    # Padrões a seguir
```

## Criando um Novo Comando

### 1. Criar a estrutura

```bash
mkdir -p ~/.claude/commands/meu-comando/references
```

### 2. Criar o SKILL.md

O arquivo `SKILL.md` precisa de um frontmatter YAML no início:

```markdown
---
name: meu-comando
description: Descrição curta do que o comando faz. Esta descrição aparece quando o usuário digita / no Claude Code.
---

# Nome do Comando

Descrição mais detalhada do que o comando faz.

## Workflow

### 1. Primeiro Passo

Instruções detalhadas do que o Claude deve fazer.

```bash
# Comandos que o Claude deve executar
exemplo de comando
```

### 2. Segundo Passo

Mais instruções...

## Exemplos

### Exemplo 1: Caso Simples

```
User: "faz X"
Claude: [executa Y]
```

## Dicas

- Dica 1
- Dica 2
```

### 3. Testar localmente

```bash
# Copie para o diretório do Claude
cp -r meu-comando ~/.claude/commands/

# Abra o Claude Code e teste
claude
# Digite /meu-comando
```

### 4. Submeter PR

```bash
# Clone o repo (se ainda não fez)
git clone git@github.com:GrupoPrimo/claude-code-toolkit.git
cd claude-code-toolkit

# Crie uma branch
git checkout -b feat/meu-comando

# Copie seu comando
cp -r ~/.claude/commands/meu-comando commands/

# Commit e push
git add .
git commit -m "feat: add meu-comando skill"
git push -u origin feat/meu-comando

# Abra o PR (pode usar o próprio pr-creator!)
claude
/pr-creator
```

## Boas Práticas

### DO (Faça)

- **Seja específico**: Instruções claras e detalhadas
- **Inclua exemplos**: Mostre casos de uso reais
- **Documente erros**: Liste erros comuns e soluções
- **Use markdown**: Formatação ajuda o Claude a entender a estrutura
- **Teste bastante**: Teste vários cenários antes de compartilhar
- **Peça feedback**: Interação com o usuário é importante

### DON'T (Evite)

- **Instruções vagas**: "faça algo legal" não funciona
- **Assumir contexto**: O Claude não sabe o que você está pensando
- **Ignorar edge cases**: Pense em cenários onde pode dar errado
- **Hardcode de valores**: Use variáveis e pergunte ao usuário
- **Comandos destrutivos**: Cuidado com `rm -rf`, `git push --force`, etc.

## Anatomia de um Bom SKILL.md

```markdown
---
name: exemplo-skill
description: Faz X para ajudar com Y. Use quando precisar de Z.
---

# Exemplo Skill

## O Que Este Comando Faz

Explique claramente o propósito.

## Pré-requisitos

- Ferramenta A instalada
- Configuração B feita

## Workflow

### 1. Verificações Iniciais

**SEMPRE verifique X antes de começar:**

```bash
comando para verificar
```

### 2. Coleta de Informações

Pergunte ao usuário:
- Pergunta 1
- Pergunta 2

### 3. Execução

```bash
# Comandos a executar
```

### 4. Verificação Final

Confirme que funcionou.

## Exemplos de Uso

### Caso 1: Situação Normal

```
User: "comando simples"
Claude: [faz X, Y, Z]
```

### Caso 2: Situação Especial

```
User: "comando com opções"
Claude: [trata caso especial]
```

## Troubleshooting

| Erro | Causa | Solução |
|------|-------|---------|
| Erro A | Causa A | Faça X |
| Erro B | Causa B | Faça Y |

## Referências

- Link útil 1
- Link útil 2
```

## Checklist para PR

Antes de abrir o PR, verifique:

- [ ] SKILL.md tem frontmatter válido (name, description)
- [ ] Descrição é clara e concisa
- [ ] Workflow está bem documentado
- [ ] Inclui exemplos de uso
- [ ] Testei localmente e funciona
- [ ] Não há informações sensíveis (tokens, senhas, etc.)
- [ ] Código/comandos são seguros (sem operações destrutivas não intencionais)

## Dúvidas?

Abra uma issue ou pergunte no canal Engenheiros/Devs no Teams! Estamos aqui para ajudar.