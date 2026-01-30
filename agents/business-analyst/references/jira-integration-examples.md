# Jira Integration Examples

Este documento contém exemplos práticos de como o agente `business-analyst` interage com o Jira via MCP.

## Example 1: Creating Epic with BDD Stories

### Request

```
Crie um epic no Jira para Sistema de Autenticação com 2 stories
```

### Workflow

**1. Get cloudId**

```
Tool: mcp__atlassian__getAccessibleAtlassianResources
Response: { "id": "abc123-...", "name": "Grupo Primo", "url": "https://grupoPrimo.atlassian.net" }
```

**2. List Projects**

```
Tool: mcp__atlassian__getVisibleJiraProjects
Parameters: { "cloudId": "abc123-..." }
Response: [
  { "key": "GRAO", "name": "GRAO Project" },
  { "key": "PLAT", "name": "Platform" },
  { "key": "CORE", "name": "Core Services" }
]
```

**3. Ask User for Project**

```
Encontrei os seguintes projetos no Jira:

1. GRAO - GRAO Project
2. PLAT - Platform
3. CORE - Core Services

Em qual projeto deseja criar o epic?

User: "GRAO"
```

**4. Get Issue Types**

```
Tool: mcp__atlassian__getJiraProjectIssueTypesMetadata
Parameters: { "cloudId": "abc123-...", "projectIdOrKey": "GRAO" }
Response: [
  { "name": "Epic", "id": "10000" },
  { "name": "Story", "id": "10001" },
  { "name": "Bug", "id": "10002" },
  { "name": "Task", "id": "10003" }
]
```

**5. Create Epic**

```
Tool: mcp__atlassian__createJiraIssue
Parameters: {
  "cloudId": "abc123-...",
  "projectKey": "GRAO",
  "issueTypeName": "Epic",
  "summary": "Sistema de Autenticação",
  "description": "## Business Objective\n\nImplementar sistema completo de autenticação para permitir login seguro de usuários.\n\n## Success Metrics\n\n| Metric | Target |\n|--------|--------|\n| Login success rate | >99% |\n| Auth response time | <200ms |\n\n## Scope\n\n### In Scope\n- Login com email/senha\n- Login social (Google)\n- Recuperação de senha\n\n### Out of Scope\n- 2FA (próxima fase)"
}
Response: { "key": "GRAO-100", "id": "12345", "self": "https://grupoPrimo.atlassian.net/rest/api/3/issue/12345" }
```

**6. Create Story 1**

````
Tool: mcp__atlassian__createJiraIssue
Parameters: {
  "cloudId": "abc123-...",
  "projectKey": "GRAO",
  "issueTypeName": "Story",
  "summary": "Login com email e senha",
  "description": "**As a** usuário cadastrado\n**I want** fazer login com meu email e senha\n**So that** possa acessar minha conta de forma segura\n\n## Acceptance Criteria\n\n```gherkin\nFeature: Login com email e senha\n\n  Background:\n    Given existe um usuário cadastrado com email \"test@example.com\"\n\n  @e2e @happy-path @smoke\n  Scenario: Login bem sucedido\n    Given estou na página de login\n    When preencho o campo email com \"test@example.com\"\n      And preencho o campo senha com minha senha válida\n      And clico no botão \"Entrar\"\n    Then sou redirecionado para o dashboard\n      And vejo a mensagem \"Bem-vindo de volta!\"\n\n  @e2e @error-handling\n  Scenario: Login com credenciais inválidas\n    Given estou na página de login\n    When preencho o campo email com \"wrong@example.com\"\n      And preencho o campo senha com \"senhaerrada\"\n      And clico no botão \"Entrar\"\n    Then vejo a mensagem de erro \"Email ou senha incorretos\"\n      And permaneço na página de login\n      And o campo senha é limpo\n\n  @e2e @edge-case\n  Scenario: Login com campos vazios\n    Given estou na página de login\n    When clico no botão \"Entrar\" sem preencher os campos\n    Then vejo mensagens de validação nos campos obrigatórios\n```",
  "parent": "GRAO-100"
}
Response: { "key": "GRAO-101", "id": "12346" }
````

**7. Create Story 2**

````
Tool: mcp__atlassian__createJiraIssue
Parameters: {
  "cloudId": "abc123-...",
  "projectKey": "GRAO",
  "issueTypeName": "Story",
  "summary": "Login social com Google",
  "description": "**As a** usuário\n**I want** fazer login usando minha conta Google\n**So that** possa acessar rapidamente sem criar nova senha\n\n## Acceptance Criteria\n\n```gherkin\nFeature: Login social com Google\n\n  @e2e @happy-path\n  Scenario: Login com Google - usuário novo\n    Given estou na página de login\n    When clico no botão \"Continuar com Google\"\n      And autorizo o acesso no popup do Google\n    Then minha conta é criada automaticamente\n      And sou redirecionado para o dashboard\n\n  @e2e @happy-path\n  Scenario: Login com Google - usuário existente\n    Given já tenho conta vinculada ao Google\n      And estou na página de login\n    When clico no botão \"Continuar com Google\"\n      And seleciono minha conta Google\n    Then sou autenticado com sucesso\n      And sou redirecionado para o dashboard\n\n  @e2e @error-handling\n  Scenario: Login com Google cancelado\n    Given estou na página de login\n    When clico no botão \"Continuar com Google\"\n      And fecho o popup do Google sem autorizar\n    Then permaneço na página de login\n      And nenhuma conta é criada\n```",
  "parent": "GRAO-100"
}
Response: { "key": "GRAO-102", "id": "12347" }
````

**8. Output Summary**

```
✅ Criados com sucesso no projeto GRAO:

Epic: GRAO-100 - Sistema de Autenticação
├── GRAO-101 - Login com email e senha
└── GRAO-102 - Login social com Google

Links:
- Epic: https://grupoPrimo.atlassian.net/browse/GRAO-100
- Story 1: https://grupoPrimo.atlassian.net/browse/GRAO-101
- Story 2: https://grupoPrimo.atlassian.net/browse/GRAO-102

As stories incluem acceptance criteria em formato Gherkin, prontos para geração de testes e2e.
```

---

## Example 2: Creating Bug with Verification Criteria

### Request

```
Documenta bug: upload de foto falha no mobile > 5MB. Criar no Jira projeto GRAO.
```

### Workflow

**1. Get cloudId** (same as above)

**2. Create Bug**

````
Tool: mcp__atlassian__createJiraIssue
Parameters: {
  "cloudId": "abc123-...",
  "projectKey": "GRAO",
  "issueTypeName": "Bug",
  "summary": "Upload de fotos >5MB falha no mobile",
  "description": "## Summary\n\nUsuários no app mobile não conseguem fazer upload de fotos maiores que 5MB. O upload falha silenciosamente ou mostra erro genérico.\n\n## Steps to Reproduce\n\n1. Abrir app mobile (iOS ou Android)\n2. Ir para a seção de perfil\n3. Clicar em \"Alterar foto\"\n4. Selecionar uma foto maior que 5MB\n5. Aguardar upload\n\n## Expected Behavior\n\nUpload deveria funcionar para fotos até 25MB (limite configurado no backend).\n\n## Actual Behavior\n\nErro \"File too large\" é exibido para qualquer foto acima de 5MB.\n\n## Evidence\n\n- Screenshots: [anexar]\n- Error logs: `413 Payload Too Large` no console\n\n## Environment\n\n- iOS 17.2 / Android 14\n- App version: 2.3.1\n- Testado em WiFi e 4G\n\n## Impact\n\n- Usuários afetados: ~30% (usam fotos de alta resolução)\n- Workaround: Redimensionar foto antes do upload\n\n## Root Cause (Hypothesis)\n\nPossível configuração de limite no nginx/API gateway diferente do backend.\n\n## Acceptance Criteria (Fix Verification)\n\n```gherkin\nFeature: Upload de foto de perfil\n\n  @e2e @regression\n  Scenario: Upload de foto grande no mobile\n    Given estou logado no app mobile\n      And tenho uma foto de 10MB na galeria\n    When acesso a tela de perfil\n      And clico em \"Alterar foto\"\n      And seleciono a foto de 10MB\n    Then o upload é processado com sucesso\n      And a nova foto aparece no meu perfil\n      And não vejo nenhuma mensagem de erro\n\n  @e2e @regression\n  Scenario: Upload de foto no limite máximo\n    Given estou logado no app mobile\n      And tenho uma foto de 24MB na galeria\n    When faço upload da foto\n    Then o upload é processado com sucesso\n\n  @e2e @error-handling\n  Scenario: Upload de foto acima do limite\n    Given estou logado no app mobile\n      And tenho uma foto de 30MB na galeria\n    When tento fazer upload da foto\n    Then vejo mensagem \"Arquivo muito grande. Máximo: 25MB\"\n      And a foto atual do perfil permanece inalterada\n```"
}
Response: { "key": "GRAO-200", "id": "12400" }
````

**3. Output**

```
✅ Bug criado com sucesso:

GRAO-200 - Upload de fotos >5MB falha no mobile
https://grupoPrimo.atlassian.net/browse/GRAO-200

O bug inclui:
- Steps to reproduce detalhados
- Análise de impacto
- Hipótese de root cause
- Acceptance criteria em Gherkin para verificação do fix
```

---

## Example 3: Sprint Planning with Jira Search

### Request

```
Ajude-me a planejar a sprint 15, busque o backlog do projeto GRAO
```

### Workflow

**1. Search Backlog**

```
Tool: mcp__atlassian__searchJiraIssuesUsingJql
Parameters: {
  "cloudId": "abc123-...",
  "jql": "project = GRAO AND status = 'To Do' AND sprint IS EMPTY ORDER BY priority DESC, created ASC",
  "fields": ["summary", "status", "priority", "issuetype", "customfield_10016"]
}
Response: [
  { "key": "GRAO-150", "summary": "Implementar logout", "priority": "High", "issuetype": "Story" },
  { "key": "GRAO-151", "summary": "Adicionar remember me", "priority": "Medium", "issuetype": "Story" },
  { "key": "GRAO-200", "summary": "Upload de fotos >5MB falha", "priority": "High", "issuetype": "Bug" },
  ...
]
```

**2. Present to User**

```
📋 Backlog disponível para Sprint 15 (GRAO):

| Prioridade | Key | Tipo | Summary |
|------------|-----|------|---------|
| 🔴 High | GRAO-200 | Bug | Upload de fotos >5MB falha no mobile |
| 🔴 High | GRAO-150 | Story | Implementar logout |
| 🟡 Medium | GRAO-151 | Story | Adicionar remember me |
| 🟡 Medium | GRAO-152 | Task | Configurar monitoring de auth |
| 🟢 Low | GRAO-153 | Story | Melhorar mensagens de erro |

Qual é a capacidade da sprint? Quantos story points?
```

**3. Generate Sprint Plan (Local Document)**

After user provides capacity, generate sprint planning document with selected items.

---

## Example 4: Adding Comment to Existing Issue

### Request

```
Adicione um comentário no GRAO-100 sobre a decisão de usar JWT
```

### Workflow

```
Tool: mcp__atlassian__addCommentToJiraIssue
Parameters: {
  "cloudId": "abc123-...",
  "issueIdOrKey": "GRAO-100",
  "commentBody": "## Decisão Técnica: Uso de JWT\n\n**Data:** 2024-01-30\n**Participantes:** @TechLead, @Backend\n\n### Contexto\nPrecisamos definir o formato do token de autenticação.\n\n### Decisão\nVamos usar JWT (JSON Web Tokens) com as seguintes características:\n- Algoritmo: RS256\n- Expiração: 1 hora (access token)\n- Refresh token: 7 dias\n\n### Motivos\n1. Stateless - não precisa consultar banco a cada request\n2. Padrão de mercado\n3. Facilita integração com serviços externos\n\n### Trade-offs\n- Não é possível invalidar token individual (mitigado com refresh token curto)\n- Payload pode ficar grande (mitigado limitando claims)"
}
Response: { "id": "10500", "self": "..." }
```

**Output:**

```
✅ Comentário adicionado ao GRAO-100

https://grupoPrimo.atlassian.net/browse/GRAO-100?focusedCommentId=10500
```

---

## BDD Tags Reference

| Tag               | Uso                            | Quando usar                                   |
| ----------------- | ------------------------------ | --------------------------------------------- |
| `@e2e`            | Incluir na suite de testes e2e | Todos os cenários que devem ser automatizados |
| `@happy-path`     | Fluxo principal de sucesso     | Cenário mais comum/esperado                   |
| `@edge-case`      | Casos de borda                 | Limites, valores extremos                     |
| `@error-handling` | Tratamento de erros            | Inputs inválidos, falhas                      |
| `@smoke`          | Testes críticos rápidos        | Verificação básica de funcionamento           |
| `@regression`     | Suite de regressão             | Após correções de bugs                        |
| `@wip`            | Work in progress               | Cenários ainda em desenvolvimento             |
| `@manual`         | Teste manual necessário        | Não pode ser automatizado                     |

---

## Error Handling Examples

### MCP Not Configured

```
❌ Erro: Não foi possível conectar ao Jira.

Solução:
1. Execute no terminal:
   claude mcp add --transport http atlassian https://mcp.atlassian.com/v1/mcp

2. Dentro do Claude Code, autentique:
   > /mcp
   # Selecione "Authenticate" para Atlassian

3. Tente novamente.
```

### Project Not Found

```
❌ Erro: Projeto "INVALID" não encontrado.

Projetos disponíveis:
- GRAO - GRAO Project
- PLAT - Platform
- CORE - Core Services

Em qual projeto deseja criar a issue?
```

### Permission Denied

```
❌ Erro: Você não tem permissão para criar issues no projeto CORE.

Opções:
1. Solicite acesso ao admin do projeto
2. Escolha outro projeto: GRAO, PLAT

Em qual projeto deseja criar a issue?
```
