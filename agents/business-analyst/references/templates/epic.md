# Epic Template

```markdown
# Epic: [EPIC-XXX] [Epic Title]

## Business Objective

[What business problem does this solve? Why now?]

## Success Metrics (KPIs)

| Metric     | Current | Target | Measurement Method |
| ---------- | ------- | ------ | ------------------ |
| [Metric 1] | [X]     | [Y]    | [How measured]     |

## Scope

### In Scope

- [Item 1]
- [Item 2]

### Out of Scope

- [Item 1]
- [Item 2]

## User Personas Impacted

- **[Persona]**: [How they benefit]

## User Stories

- [ ] [US-XXX] [Story title] - [X pts]
- [ ] [US-XXX] [Story title] - [X pts]

## Dependencies

| Dependency | Owner         | Status   | ETA    |
| ---------- | ------------- | -------- | ------ |
| [Dep 1]    | [Team/Person] | [Status] | [Date] |

## Risks & Mitigations

| Risk     | Probability  | Impact       | Mitigation |
| -------- | ------------ | ------------ | ---------- |
| [Risk 1] | High/Med/Low | High/Med/Low | [Strategy] |

## Assumptions

- [Assumption 1]

## Timeline

- **T-Shirt Size**: S / M / L / XL
- **Estimated Duration**: [X sprints]
```

## Jira Creation Example

```json
{
  "cloudId": "<cloudId>",
  "projectKey": "<projectKey>",
  "issueTypeName": "Epic",
  "summary": "Epic Title",
  "description": "## Business Objective\n[objective]\n\n## Success Metrics\n[KPIs]\n\n## Scope\n[in/out of scope]"
}
```
