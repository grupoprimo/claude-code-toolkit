# Smart Commit

Comando do Claude Code que analisa todas as mudanças pendentes, agrupa em commits lógicos, gera mensagens no padrão Conventional Commits com emoji, e executa os commits após aprovação do usuário.

## Instalação

```bash
# Copie a pasta para seu diretório de comandos
cp -r commands/smart-commit ~/.claude/commands/
```

## Uso Básico

```bash
# Abra o Claude Code no seu projeto
cd ~/seu-projeto
claude

# Execute o comando
/smart-commit
```

## O Que Ele Faz

1. **Coleta mudanças** — Analisa `git status`, `git diff`, `git diff --cached` e arquivos não rastreados
2. **Classifica arquivos** — Cada arquivo recebe uma categoria (source, test, config, ci, docs)
3. **Agrupa por contexto** — Arquivos relacionados formam commits lógicos usando heurísticas de proximidade, nomes e imports
4. **Gera mensagens** — Conventional Commits com emoji para cada grupo
5. **Apresenta proposta** — Mostra os commits propostos e pede aprovação
6. **Executa commits** — Faz stage e commit de cada grupo na ordem correta
7. **Não faz push** — Lembra o usuário de fazer `git push` manualmente

## Mapeamento de Emoji

| Tipo       | Emoji | Quando Usar                         |
| ---------- | ----- | ----------------------------------- |
| `feat`     | 🎸    | Nova feature ou melhoria            |
| `fix`      | 🐛    | Correção de bug                     |
| `test`     | ✅    | Adicionar ou atualizar testes       |
| `refactor` | 💡    | Refatoração sem mudar comportamento |
| `docs`     | ✏️    | Apenas documentação                 |
| `style`    | 💄    | Formatação, sem mudança de lógica   |
| `chore`    | 🤖    | Manutenção, config, dependências    |
| `ci`       | 💚    | Mudanças de CI/CD                   |
| `perf`     | ⚡    | Melhoria de performance             |

## Formato de Mensagem

```
{tipo}: {emoji} {descrição}
```

Ou com escopo:

```
{tipo}({escopo}): {emoji} {descrição}
```

**Exemplos:**

```
feat(auth): 🎸 add user authentication with JWT
fix(checkout): 🐛 fix total calculation when discount is applied
test(user): ✅ add unit tests for user service
refactor(api): 💡 extract HTTP client to shared utility
chore: 🤖 update dependencies to latest versions
```

## Estratégia de Agrupamento

O comando usa **inferência inteligente** — sem paths hardcoded. Funciona em qualquer projeto.

### Classificação de Arquivos

| Categoria  | Regra de Detecção                                                                   |
| ---------- | ----------------------------------------------------------------------------------- |
| **test**   | `*.test.*`, `*.spec.*`, diretórios `__tests__/`, `test/`, `tests/`, `spec/`, `e2e/` |
| **ci**     | `.github/workflows/`, `.gitlab-ci.yml`, `Jenkinsfile`, `.circleci/`                 |
| **config** | Dotfiles, `*.config.*`, `package.json`, `tsconfig*`, `Dockerfile*`, `.env*`         |
| **docs**   | `*.md`, `*.txt`, `docs/`, `LICENSE`, `CHANGELOG`                                    |
| **source** | Todo o resto                                                                        |

### Agrupamento de Source Files

Para arquivos de código, o comando usa:

1. **Proximidade de diretório** — arquivos no mesmo diretório provavelmente pertencem juntos
2. **Padrões de nome** — `user-types.ts`, `user-service.ts`, `user.adapter.ts` → mesmo grupo "user"
3. **Análise de imports** — se o diff mostra que arquivo A importa arquivo B, eles são relacionados
4. **Histórico git** — verifica como o projeto normalmente agrupa mudanças

### Ordem dos Commits

1. Tipos/modelos/schemas (core)
2. Infraestrutura/camada de dados
3. Lógica de negócio/serviços
4. UI/componentes de apresentação
5. Testes (após o código relacionado)
6. Docs
7. Config/chore/ci (por último)

## Exemplos

### Caso 1: Nova Feature Completa

Você tem mudanças em tipos, serviço, componentes e testes para uma nova feature de perfil de usuário:

```
> /smart-commit

Claude: 📋 Proposed commits (in order):

1. feat(user-profile): 🎸 add user profile domain types
   Files: src/domain/user-profile/user-profile-types.ts
          src/domain/user-profile/user-profile-errors.ts

2. feat(user-profile): 🎸 add user profile service and API
   Files: src/application/services/user-profile/user-profile-service.ts
          src/infrastructure/api/endpoints/user-profile/user-profile-api.ts

3. feat(user-profile): 🎸 add user profile page and components
   Files: src/app/profile/page.tsx
          src/presentation/components/domain/user-profile/profile-card.tsx

4. test(user-profile): ✅ add tests for user profile service
   Files: __tests__/server/user-profile/user-profile-service.test.ts

Would you like to:
  [A] Approve all and commit
  [E] Edit a specific commit
  [S] Skip a commit
  [C] Cancel

User: A

Claude: ✅ Done! 4 commit(s) created. Run `git push` when ready.
```

### Caso 2: Mix de Bug Fix + Config

```
> /smart-commit

Claude: 📋 Proposed commits (in order):

1. fix(checkout): 🐛 fix price calculation rounding error
   Files: src/services/checkout.ts

2. chore: 🤖 update eslint and prettier configs
   Files: .eslintrc.json, .prettierrc, package.json

Would you like to:
  [A] Approve all and commit
  [E] Edit a specific commit
  [S] Skip a commit
  [C] Cancel

User: E 1
Claude: What message would you like for commit 1?
User: fix(checkout): 🐛 fix rounding error in discount calculation
Claude: Updated! [re-presents full list]
User: A

Claude: ✅ Done! 2 commit(s) created. Run `git push` when ready.
```

### Caso 3: Sem Mudanças

```
> /smart-commit

Claude: No pending changes found. Working tree is clean.
```

## Casos Especiais

| Situação                      | Comportamento                                       |
| ----------------------------- | --------------------------------------------------- |
| Sem mudanças                  | Informa e para                                      |
| Arquivo único                 | Ainda pede confirmação                              |
| Mesmo arquivo staged+unstaged | Avisa e inclui todas as mudanças                    |
| Arquivos binários             | Inclui no agrupamento, nota que é binário           |
| Arquivos não rastreados       | Inclui na análise; faz stage se aprovado            |
| Conflitos de merge            | Detecta e avisa — não commita arquivos com conflito |

## Compatibilidade

Funciona com **qualquer projeto** — sem assumir framework, arquitetura ou estrutura de pastas. As heurísticas são baseadas em inferência, não em regras fixas.

## Observações

- O comando **nunca faz push** automaticamente
- A etapa de confirmação **nunca é pulada**
- Se não tiver certeza sobre um agrupamento, o Claude agrupa sob `chore:` e sinaliza como "uncategorized"
