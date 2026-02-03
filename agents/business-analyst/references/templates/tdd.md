# Technical Design Document (TDD) Template

```markdown
# TDD - [Feature/Integration Name]

## Contexto

[Descricao do projeto e motivacao. Por que estamos fazendo isso? Qual problema resolve?]

## Glossario

| Termo     | Descricao                   |
| --------- | --------------------------- |
| [Termo 1] | [Definicao clara e concisa] |
| [Termo 2] | [Definicao clara e concisa] |

## Recursos

| Papel     | Responsavel |
| --------- | ----------- |
| Tech Lead | @[Nome]     |
| PM        | @[Nome]     |
| Time      | [Nomes]     |

**Links:**

- Figma: [link]
- Epic: [link do Jira]
- Documentacao externa: [links]

## APIs / Integracoes

| API     | Descricao   | Principais Endpoints                     |
| ------- | ----------- | ---------------------------------------- |
| [API 1] | [O que faz] | `POST /endpoint`, `GET /endpoint/:id`    |
| [API 2] | [O que faz] | `POST /endpoint`, `DELETE /endpoint/:id` |

## Fluxo Tecnico

### [Nome do Fluxo Principal]

1. **[Passo 1]**: [Descricao tecnica]
2. **[Passo 2]**: [Descricao tecnica]
3. **[Passo 3]**: [Descricao tecnica]

**APIs Envolvidas:**

- [API 1] - [Funcao]
- [API 2] - [Funcao]

**Webhooks/Eventos:**

- `event.name` - [Quando e disparado e o que fazer]

## Solucao

| Tarefa         | Descricao             | Status                |
| -------------- | --------------------- | --------------------- |
| **Setup**      |                       |                       |
| [Tarefa 1]     | [Descricao detalhada] | DEFINIDO / INDEFINIDO |
| [Tarefa 2]     | [Descricao detalhada] | DEFINIDO / INDEFINIDO |
| **Integracao** |                       |                       |
| [Tarefa 3]     | [Descricao detalhada] | DEFINIDO / INDEFINIDO |
| [Tarefa 4]     | [Descricao detalhada] | FORA DE ESCOPO        |

## Riscos

| Risco     | Descricao           | Mitigacao           |
| --------- | ------------------- | ------------------- |
| [Risco 1] | [Impacto potencial] | [Acoes preventivas] |
| [Risco 2] | [Impacto potencial] | [Acoes preventivas] |

## Roadmap

[Fases de implementacao, dependencias entre tarefas, marcos importantes]
```

## TDD Best Practices

### When to Create a TDD

- New integrations with external APIs
- Complex features spanning multiple services
- Architectural changes
- Features with regulatory/compliance requirements

### Status Meanings

| Status         | Meaning                                |
| -------------- | -------------------------------------- |
| DEFINIDO       | Requirements clear, ready to implement |
| INDEFINIDO     | Needs spike or further discussion      |
| FORA DE ESCOPO | Explicitly not part of current scope   |

### Using TDD as Input for Stories

When you have a TDD, use it to generate stories:

1. Each task in "Solucao" becomes a story or task
2. Technical flows become acceptance criteria
3. Risks inform edge case scenarios
4. APIs guide technical implementation notes

See `references/tdd-example.md` for a complete TDD example (Stripe integration).
