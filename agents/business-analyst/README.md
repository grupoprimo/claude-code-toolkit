# Business Analyst Agent

O **business-analyst** é um agente de IA especializado em gestão de produto e análise de negócio. Ele funciona como um assistente virtual para POs, PMs e times de desenvolvimento, automatizando a criação de documentação e itens de trabalho no Jira.

## O que ele faz?

| Capacidade          | Descrição                                                                                        |
| ------------------- | ------------------------------------------------------------------------------------------------ |
| **Epics**           | Cria epics completos com objetivos de negócio, KPIs, escopo, riscos e dependências               |
| **User Stories**    | Gera stories no formato "Como um... Eu quero... Para que..." com critérios de aceite em Gherkin  |
| **Bugs**            | Documenta bugs com passos de reprodução, comportamento esperado/atual e critérios de verificação |
| **Tasks/Sub-tasks** | Quebra trabalho técnico em tarefas menores com definição de pronto                               |
| **Spikes**          | Cria itens de pesquisa com timebox, perguntas a responder e entregáveis                          |
| **Sprint Planning** | Gera documento de planejamento com capacidade do time, backlog priorizado e riscos               |
| **TDDs**            | Cria Technical Design Documents com contexto, glossário, APIs, fluxos e roadmap                  |
| **Priorização**     | Aplica frameworks como WSJF, MoSCoW e RICE para rankear backlog                                  |

## Integração com Jira

O agente cria issues diretamente no Jira via MCP (Model Context Protocol). Quando você pede para criar um epic ou story, ele:

1. Verifica os projetos disponíveis
2. Pergunta em qual projeto criar (se não especificado)
3. Cria a issue com toda a formatação
4. Retorna o link do Jira

### Exemplo de Uso

**Pedido:**

```
Crie um epic para sistema de autenticação com 2 stories: login por email e login social
```

**Resultado no Jira:**

```
GRAO-100: Sistema de Autenticação (Epic)
├── GRAO-101: Login com email e senha (Story)
└── GRAO-102: Login social com Google (Story)
```

Cada story vem com critérios de aceite completos em formato Gherkin:

```gherkin
Feature: Login com email e senha

  @e2e @happy-path @smoke
  Scenario: Login bem sucedido com credenciais válidas
    Given estou na página de login
    When preencho o campo email com "usuario@exemplo.com"
    And preencho o campo senha com uma senha válida
    And clico no botão "Entrar"
    Then sou redirecionado para o dashboard
    And vejo meu nome no header

  @e2e @error-handling
  Scenario: Login com credenciais inválidas
    Given estou na página de login
    When preencho o campo email com "usuario@exemplo.com"
    And preencho o campo senha com uma senha incorreta
    And clico no botão "Entrar"
    Then vejo a mensagem de erro "Email ou senha inválidos"
    And permaneço na página de login
```

## Critérios de Aceite em Gherkin

Todas as stories criadas pelo agente seguem o formato Gherkin (Given-When-Then). Isso traz benefícios:

| Benefício            | Descrição                                                              |
| -------------------- | ---------------------------------------------------------------------- |
| **Clareza**          | Formato estruturado que todos entendem (dev, QA, PO)                   |
| **Testável**         | Pronto para gerar testes automatizados E2E                             |
| **Tags organizadas** | `@e2e`, `@happy-path`, `@error-handling`, `@smoke` para filtrar testes |
| **Cobertura**        | Sempre inclui cenário de sucesso, edge cases e tratamento de erro      |

## Technical Design Document (TDD)

O agente pode criar TDDs completos para features complexas. Um TDD inclui:

### Estrutura do TDD

| Seção                | Conteúdo                                                          |
| -------------------- | ----------------------------------------------------------------- |
| **Contexto**         | Motivação do projeto, problema que resolve                        |
| **Glossário**        | Termos técnicos explicados para alinhar o time                    |
| **Recursos**         | Tech Lead, PM, time envolvido, links úteis                        |
| **APIs/Integrações** | Tabela com endpoints, métodos e descrições                        |
| **Fluxo Técnico**    | Passo a passo do fluxo principal com APIs envolvidas              |
| **Solução**          | Tabela de tarefas com status (DEFINIDO/INDEFINIDO/FORA DE ESCOPO) |
| **Riscos**           | Riscos identificados com ações de mitigação                       |
| **Roadmap**          | Fases de implementação e dependências                             |

### Como Pedir um TDD

```
Crie um TDD para integração com [sistema/API]
```

ou

```
Preciso de um TDD para a feature de [funcionalidade]
```

O agente vai fazer perguntas para entender o contexto e gerar o documento completo.

### Exemplo Real

Veja o arquivo `references/tdd-example.md` para um TDD completo de integração com Stripe, incluindo:

- Glossário com 20+ termos (API Key, Tokenização, Subscription, Webhook, etc.)
- Tabela de 7 APIs do Stripe com métodos e descrições
- Fluxo técnico detalhado do trial gratuito
- Tabela de solução com 10 tarefas e seus status
- 5 riscos mapeados com mitigações

## Usando TDD como Input (Recomendado)

Quando você já tem um TDD pronto, pode usar ele como input para o agente. O resultado fica significativamente melhor porque o agente tem todo o contexto técnico para criar stories precisas.

### Por que funciona melhor?

| Sem TDD                | Com TDD                                      |
| ---------------------- | -------------------------------------------- |
| Stories genéricas      | Stories alinhadas com a arquitetura definida |
| ACs superficiais       | ACs que refletem os fluxos técnicos reais    |
| Estimativas imprecisas | Estimativas baseadas na tabela de solução    |
| Riscos não mapeados    | Riscos já identificados no TDD incorporados  |

### Como usar

1. **Cole o TDD na conversa** ou referencie o arquivo:

```
Aqui está o TDD da feature:
[conteúdo do TDD]

Agora crie as user stories para o epic de integração com Stripe
```

2. **Ou peça para ler o arquivo**:

```
Leia o arquivo docs/tdd-stripe.md e crie as stories baseadas nele
```

### O que o agente extrai do TDD

| Seção do TDD          | Como o agente usa                                                  |
| --------------------- | ------------------------------------------------------------------ |
| **Contexto**          | Entende o problema e gera stories com valor de negócio claro       |
| **Glossário**         | Usa os termos corretos nas stories e ACs                           |
| **APIs**              | Cria tasks técnicas para cada endpoint                             |
| **Fluxo Técnico**     | Transforma cada passo em cenários Gherkin                          |
| **Tabela de Solução** | Gera tasks/sub-tasks respeitando o que está DEFINIDO vs INDEFINIDO |
| **Riscos**            | Adiciona ACs de edge cases e error handling                        |

### Exemplo Prático

Com o TDD de integração Stripe como input, o agente gera:

```
Epic: Integração com Stripe
├── Story: Criar customer no Stripe (baseado em /v1/customers)
│   └── ACs: cenários de criação, busca por email, erro de duplicata
├── Story: Criar subscription em modo trial (baseado no fluxo técnico)
│   └── ACs: trial de 14 dias, sem método de pagamento, status trialing
├── Story: Configurar webhooks (baseado na seção de webhooks)
│   └── ACs: trial_will_end, subscription.updated, tratamento de falhas
└── Spike: Definir tratamento de erros do Stripe (item INDEFINIDO no TDD)
```

As stories ficam muito mais precisas porque refletem exatamente o que foi planejado no TDD.

## Sprint Planning

O agente pode ajudar no planejamento de sprint:

```
Ajude-me a planejar a sprint 15, busque o backlog do projeto GRAO
```

Ele vai:

1. Buscar items do backlog no Jira
2. Mostrar stories/tasks/bugs disponíveis
3. Perguntar sobre capacidade do time
4. Gerar documento de sprint com:
   - Meta da sprint
   - Capacidade por membro
   - Items comprometidos vs stretch goals
   - Dependências e riscos

## Priorização de Backlog

O agente conhece frameworks de priorização:

| Framework  | Fórmula                                         | Quando Usar                     |
| ---------- | ----------------------------------------------- | ------------------------------- |
| **WSJF**   | (Valor + Urgência + Redução de Risco) / Tamanho | Priorização contínua de backlog |
| **MoSCoW** | Must/Should/Could/Won't                         | Definir escopo de release       |
| **RICE**   | (Reach x Impact x Confidence) / Effort          | Rankear features por impacto    |

```
Priorize esses 10 items do backlog usando WSJF
```

## Comandos Disponíveis

| Comando                    | O que faz                          |
| -------------------------- | ---------------------------------- |
| `create epic [descrição]`  | Gera epic completo                 |
| `create story [descrição]` | Gera user story com ACs em Gherkin |
| `create bug [descrição]`   | Gera bug report                    |
| `create task [descrição]`  | Gera task com sub-tasks            |
| `create spike [tópico]`    | Gera spike de pesquisa             |
| `plan sprint [meta]`       | Gera documento de sprint           |
| `prioritize [items]`       | Aplica WSJF/RICE/MoSCoW            |
| `breakdown [epic/story]`   | Decompõe em items menores          |
| `refine [story]`           | Melhora ACs existentes             |
| `estimate [items]`         | Sugere story points                |
| `risks [epic/feature]`     | Análise de riscos                  |

## Exemplo Real: GRAO-4664

O epic "Sistema de Roles e Permissões - Portal de Profissionais" foi criado usando o agente. Estrutura gerada:

```markdown
## Contexto

O Portal de Profissionais é uma plataforma centralizada...
Fase 1: Módulo GRÃO (Financial Planners)
Fases Futuras: Módulo PORTFEL e outros

## Objetivos

- Segurança: cada usuário acessa apenas o que precisa
- Compliance: controle de acesso auditável
- Flexibilidade: fácil adicionar novos perfis
- Escalabilidade: preparado para novos módulos

## Estrutura de Acesso

Usuário → Módulo (BU) → Role → Features

## Roles Propostas

| Role     | Descrição                             |
| -------- | ------------------------------------- |
| Master   | Acesso total, pode gerenciar usuários |
| Manager  | Gerencia profissionais e class groups |
| Operator | Operações do dia a dia, sem excluir   |
| Viewer   | Apenas visualização, sem editar       |

## Features do Módulo GRÃO

- Planners: Gestão dos planejadores financeiros
- Class Groups: Gestão das turmas
- Audit/Logs: Histórico de ações

## Recursos

| Papel     | Responsável            |
| --------- | ---------------------- |
| Tech Lead | Fábio Fontes           |
| PO/PM     | Carolina e Vinícius    |
| Time      | Gabriel, Pedro, Rafael |
```

Link: https://timeprimo.atlassian.net/browse/GRAO-4664

## Setup

Para usar o agente, a integração com Jira precisa estar configurada:

```bash
# 1. Adicionar MCP do Atlassian
claude mcp add --transport http atlassian https://mcp.atlassian.com/v1/mcp

# 2. Autenticar (uma vez)
claude
> /mcp
# Seguir fluxo OAuth

# 3. Usar o agente
> Crie um epic para [sua feature]
```

## Configuracao de Projeto JIRA (Recomendado)

Para evitar a listagem de projetos a cada sessao e acelerar a criacao de issues, crie um arquivo de configuracao local:

### Criar `.claude/jira-config.json`

```json
{
  "cloudId": "seu-cloud-id-aqui",
  "projectKey": "PROJ",
  "siteUrl": "https://site.atlassian.net",
  "issueTypes": {
    "epic": "Epic",
    "story": "Story",
    "task": "Tarefa",
    "subtask": "Sub-tarefa",
    "bug": "Bug"
  },
  "defaults": {
    "parentEpic": null,
    "labels": [],
    "components": []
  }
}
```

### Como obter o cloudId

Execute na sessao Claude Code:

```
mcp__atlassian__getAccessibleAtlassianResources
```

Copie o valor do campo `id` do resultado.

### Como obter os issueTypes

Execute na sessao Claude Code:

```
mcp__atlassian__getJiraProjectIssueTypesMetadata(cloudId, projectKey)
```

Os nomes dos tipos podem variar por projeto (ex: "Task" vs "Tarefa").

### Beneficios

| Sem Config                   | Com Config                     |
| ---------------------------- | ------------------------------ |
| Lista projetos toda vez      | Usa projeto direto             |
| ~5 chamadas MCP por operacao | ~1-2 chamadas MCP por operacao |
| Latencia adicional           | Resposta mais rapida           |
| Precisa especificar projeto  | Projeto ja configurado         |

## Limitação: MCP em Subagentes

> **IMPORTANTE:** Quando o agente é executado como subagente (via Task tool), a autenticação MCP **NÃO é herdada** da sessão principal.

### O que acontece?

- Chamadas a `mcp__atlassian__*` falham com erro de autenticação
- O agente detecta isso e entra em modo fallback

### Comportamento Fallback

Quando MCP não está disponível, o agente:

1. **Não tenta criar arquivos locais**
2. **Gera payloads JSON** com todas as operações necessárias
3. **Retorna para a sessão principal** executar

### Como usar o fallback

Quando receber o JSON de fallback do agente:

```json
{
  "mcp_fallback": true,
  "operations": [
    {"action": "create", "tool": "mcp__atlassian__createJiraIssue", "params": {...}}
  ]
}
```

Execute as operações manualmente na sessão principal do Claude Code, que tem acesso ao MCP autenticado.

## Dicas de Uso

1. **Seja específico**: Quanto mais contexto você der, melhor o resultado
2. **Mencione o projeto**: "Crie no projeto GRAO" evita perguntas extras
3. **Peça refinamento**: Se não ficou bom, peça para ajustar
4. **Use para refinement**: O agente pode melhorar stories existentes
5. **Bulk creation**: Peça epic + stories de uma vez para manter o link

## Arquivos de Referencia

| Arquivo                                   | Conteudo                                     |
| ----------------------------------------- | -------------------------------------------- |
| `business-analyst.md`                     | Definicao do agente (otimizado, ~250 linhas) |
| `references/tdd-example.md`               | Exemplo de TDD de integracao Stripe          |
| `references/jira-integration-examples.md` | Exemplos de uso com Jira                     |
| `references/templates/epic.md`            | Template de Epic                             |
| `references/templates/story.md`           | Template de User Story com Gherkin           |
| `references/templates/bug.md`             | Template de Bug Report                       |
| `references/templates/task.md`            | Template de Task/Sub-task                    |
| `references/templates/spike.md`           | Template de Spike/Research                   |
| `references/templates/sprint.md`          | Template de Sprint Planning                  |
| `references/templates/tdd.md`             | Template de Technical Design Document        |

> **Nota:** Os templates sao carregados sob demanda (lazy loading) para reduzir o consumo de tokens.
