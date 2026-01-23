# PR Examples

Real-world examples from the team to guide PR creation.

## Example 1: Documentation PR (Clean Architecture)

**Commits analyzed:**

- "docs: add clean architecture migration documentation"
- "docs: structure chapters and migration phases"
- "docs: add before/after code examples"

**Generated PR Description:**

```markdown
# Description

Este PR adiciona documentação completa de débito técnico sobre migração para Clean Architecture no App Grão, abordando reorganização estrutural do código existente em camadas bem definidas com separação clara de responsabilidades.

## Objetivo

Documentar o débito técnico de arquitetura do App, propondo reorganização do código existente (que já segue bons padrões) em 5 camadas bem definidas: Main, Presentation, Application, Domain e Infrastructure.

## Conteúdo Documentado

### 1. Contexto e Pré-requisitos

**IMPORTANTE - Esta é a última migração:**

- ✅ Depende de Nav, Redux→Zustand, UI estarem 100% concluídos
- Iniciá-la antes causará conflitos e retrabalho
- Posicionada estrategicamente após outros débitos técnicos

**Por que Clean Architecture?**

- Testabilidade: Camadas isoladamente testáveis
- Onboarding: Estrutura previsível ("Onde fica X?")
- Manutenção: Dependências unidirecionais
- Reutilização: Domain/Infra compartilhados
- Escalabilidade: Cresce de forma organizada

### 2. Descoberta Importante: Código Já Está ~80% Correto

:::tip Boa Notícia
O codebase atual **já implementa ~80%** dos padrões propostos. A migração será principalmente **reorganização estrutural**, não reescrita de código.
:::

[... more sections ...]

## Type of change

- [x] Documentation (adding tech debt documentation)

# Checklist:

- [x] Documentation is comprehensive and clear
- [x] Problem is well described with examples
- [x] Solution is detailed with code samples
- [x] Migration plan is actionable and incremental
- [x] Metrics are defined
- [x] Dependencies clearly documented
- [x] Código atual analisado (80% já correto)
- [x] Estratégia de coexistência documentada
- [x] Follows Docusaurus structure
```

**Generated Review Message:**

```
Bom dia pessoal,

Abri o PR de documentação de débito técnico de Clean Architecture no primo-docs.

Principais pontos:
📚 Doc completa sobre reorganização do código para Clean Architecture com 5 camadas
🎯 Descoberta importante: Código já está ~80% correto! UseCases, Services, Errors já seguem bons padrões
📁 Problema é organização de pastas (core/ é God Folder, domain/ contém services)
🔄 Migração é principalmente reorganização de arquivos + atualização de imports
⚠️ IMPORTANTE: Esta deve ser a ÚLTIMA migração do roadmap - depende de Nav, Redux→Zustand e UI estarem 100% concluídos
📊 Esforço: ~55 story points, ~3 meses. Ganho: clareza estrutural, onboarding -50%, velocity +33%

Link do PR: [cole aqui]
```

## Example 2: Feature PR (OAuth Integration)

**Commits analyzed:**

- "feat: add OAuth2 service integration"
- "feat: implement Google OAuth flow"
- "feat: add Apple sign-in support"
- "test: add OAuth integration tests"

**Generated PR Description:**

```markdown
# Description

Este PR implementa autenticação OAuth2 no App Grão, permitindo que usuários façam login usando suas contas Google ou Apple.

## Objetivo

Reduzir fricção no processo de onboarding permitindo autenticação social, melhorando taxa de conversão de novos usuários.

## Mudanças Implementadas

### Autenticação

- ✅ Integração OAuth2 com Google Sign-In
- ✅ Integração OAuth2 com Apple Sign-In
- ✅ Fluxo de autenticação unificado
- ✅ Armazenamento seguro de tokens (Keychain/Keystore)

### UI/UX

- ✅ Tela de seleção de provedor de autenticação
- ✅ Estados de loading durante autenticação
- ✅ Mensagens de erro user-friendly

### Segurança

- ✅ Validação de tokens no backend
- ✅ Refresh token automático
- ✅ Logout seguro (revogação de tokens)

## Type of change

- [x] New feature (non-breaking change which adds functionality)

# Checklist:

- [x] Code follows project style guidelines
- [x] Tests added for OAuth flows
- [x] Documentation updated (README with setup instructions)
- [x] Configuração necessária documentada (OAuth credentials)
```

**Generated Review Message:**

```
Bom dia pessoal,

Abri PR implementando autenticação OAuth2 no App Grão.

Principais mudanças:
✅ Login com Google e Apple
✅ Fluxo unificado de autenticação
✅ Armazenamento seguro de tokens (Keychain/Keystore)
✅ Tratamento de erros completo
✅ Testes de integração

IMPORTANTE: Após merge, precisamos configurar OAuth apps em produção (credenciais no 1Password).

Link do PR: [URL]
```

## Example 3: Bug Fix PR

**Commits analyzed:**

- "fix: prevent crash on empty fund list"
- "test: add test for empty list scenario"

**Generated PR Description:**

```markdown
# Description

Corrige crash que ocorria quando usuário acessava tela de fundos sem ter nenhum fundo cadastrado.

## Problema

App crashava com `IndexOutOfBoundsException` quando:

1. Usuário novo (sem fundos cadastrados)
2. Usuário remove todos os fundos
3. Acessa tela de fundos

**Root cause:** Lista vazia não era tratada antes de acessar primeiro elemento.

## Solução

- Adiciona verificação de lista vazia antes de acessar elementos
- Exibe estado vazio com mensagem apropriada
- Adiciona testes para cenário de lista vazia

## Type of change

- [x] Bug fix (non-breaking change which fixes an issue)

# Checklist:

- [x] Bug reproduzido e confirmado
- [x] Root cause identificado
- [x] Fix testado localmente
- [x] Teste adicionado para prevenir regressão
```

**Generated Review Message:**

```
Bom dia pessoal,

Fix para crash em tela de fundos quando lista está vazia.

Problema: App crashava com IndexOutOfBoundsException ao acessar primeiro elemento de lista vazia
Solução: Adiciona verificação + estado vazio
Teste: Novo test case previne regressão

Link do PR: [URL]
```

## Pattern Observations

### Common Elements in Good PRs:

1. **Clear Title**: Verb + what changed
2. **Context**: Why this change was needed
3. **Details**: What specifically was done
4. **Impact**: Who/what is affected
5. **Testing**: How it was validated
6. **Next Steps**: What comes after merge (if applicable)

### Review Message Patterns:

- **Greeting**: "Bom dia/Boa tarde pessoal"
- **Summary**: 1-2 sentences max
- **Key Points**: 3-5 bullets
- **Important Notes**: Dependencies, blockers, next steps
- **Link**: Always at the end

## Example 4: Auto-created PR with GitHub CLI

**User Request:**
```
/pr-creator - cria o PR automaticamente
```

**Workflow Executed:**

```bash
# 1. Verify prerequisites
gh auth status
# ✓ Logged in to github.com as gabriel-cano

# 2. Check branch status
git branch --show-current
# feature/user-settings

# 3. Push branch if needed
git push -u origin feature/user-settings

# 4. Analyze commits (merge-base strategy)
MERGE_BASE=$(git merge-base HEAD origin/main)
git log --oneline ${MERGE_BASE}..HEAD
# abc1234 feat: add user settings screen
# def5678 feat: implement theme toggle
# ghi9012 test: add settings tests

# 5. Create PR with options
gh pr create \
  --base main \
  --title "feat: add user settings with theme toggle" \
  --body "$(cat <<'EOF'
## Description

Implementa tela de configurações do usuário com opção de alternar tema.

## Mudanças

- ✅ Nova tela de configurações
- ✅ Toggle de tema (light/dark)
- ✅ Persistência de preferências
- ✅ Testes unitários

## Type of change

- [x] New feature

## Checklist

- [x] Tests added
- [x] Follows design system
EOF
)" \
  --draft \
  --assignee "@me" \
  --label "feature,frontend"

# 6. Get PR URL
gh pr view --json url -q .url
# https://github.com/org/repo/pull/123
```

**Generated Review Message:**

```
Bom dia pessoal,

Abri PR adicionando tela de configurações do usuário com toggle de tema.

Mudanças:
✅ Nova tela de configurações
✅ Toggle de tema (light/dark)
✅ Persistência de preferências
✅ Testes completos

📋 Status: Draft
🏷️ Labels: feature, frontend
👤 Assignee: @gabriel-cano

Link do PR: https://github.com/org/repo/pull/123
```

## Example 5: PR with Reviewers and Labels

**Commands Used:**

```bash
# List available labels first
gh label list --limit 10
# bug
# feature
# documentation
# needs-review
# priority-high

# Create PR with reviewers and labels
gh pr create \
  --base develop \
  --title "fix: resolve payment timeout issue" \
  --body-file /tmp/pr-body.md \
  --reviewer "tech-lead,senior-dev" \
  --label "bug,priority-high" \
  --assignee "@me"

# After creation, mark as ready for review
gh pr ready

# Add more reviewers later if needed
gh pr edit --add-reviewer "qa-engineer"
```

**Output:**

```
Creating pull request for fix/payment-timeout into develop in org/repo

https://github.com/org/repo/pull/456
```
