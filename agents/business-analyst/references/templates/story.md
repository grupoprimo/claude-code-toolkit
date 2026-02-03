# User Story Template

````markdown
## [US-XXX] [Story Title]

**As a** [persona/role],
**I want** [goal/capability],
**So that** [benefit/value].

### Context

[Background information that helps developers understand the "why"]

### Acceptance Criteria (BDD Format - Ready for E2E Tests)

**IMPORTANT**: All acceptance criteria MUST be in Gherkin format to facilitate automated e2e test generation with Cypress, Playwright, or similar frameworks.

```gherkin
Feature: [Feature name matching story title]

  Background:
    Given [common preconditions for all scenarios]

  @e2e @happy-path @smoke
  Scenario: [Happy Path - Main success flow]
    Given [precondition]
      And [additional context]
    When [user action]
    Then [expected outcome]
      And [additional verification]

  @e2e @edge-case
  Scenario: [Edge Case - Boundary condition]
    Given [specific precondition]
    When [edge case action]
    Then [expected edge case handling]

  @e2e @error-handling
  Scenario: [Error Handling - Invalid input/state]
    Given [precondition that leads to error]
    When [invalid action]
    Then [error message or behavior]
      And [system remains in valid state]
```

**Tags for Test Organization:**
| Tag | Purpose |
|-----|---------|
| `@e2e` | Include in e2e test suite |
| `@happy-path` | Primary success flow |
| `@edge-case` | Boundary/edge case handling |
| `@error-handling` | Error scenarios |
| `@smoke` | Critical path for smoke tests |
| `@regression` | Include in regression suite |
| `@wip` | Work in progress, skip in CI |

### Technical Notes

- [Implementation consideration]
- [Architecture guidance]

### UI/UX Requirements

- [Design link or description]

### Definition of Done

- [ ] Code complete and peer reviewed
- [ ] Unit tests passing (>=80% coverage)
- [ ] E2E tests passing for all @e2e scenarios
- [ ] Integration tests passing
- [ ] Documentation updated
- [ ] QA verified against Gherkin scenarios
- [ ] PO accepted

---

**Story Points:** [X]
**Priority:** Must Have / Should Have / Could Have / Won't Have
**Dependencies:** [US-XXX, US-YYY]
**Parent Epic:** [EPIC-XXX]
````

## Jira Creation Example

````json
{
  "cloudId": "<cloudId>",
  "projectKey": "<projectKey>",
  "issueTypeName": "Story",
  "summary": "Story Title",
  "description": "**As a** [persona]\n**I want** [goal]\n**So that** [benefit]\n\n## Acceptance Criteria\n\n```gherkin\nFeature: [Feature name]\n\n  @e2e @happy-path\n  Scenario: [Happy path]\n    Given [precondition]\n    When [action]\n    Then [outcome]\n```",
  "parent": "<epic-key>"
}
````
