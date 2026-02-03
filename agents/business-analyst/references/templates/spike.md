# Spike / Research Template

```markdown
## [SPIKE-XXX] [Research Topic]

**Timebox:** [X hours/days]
**Owner:** [Name]
**Decision Deadline:** [Date]

### Research Question

[What are we trying to learn or decide?]

### Background

[Why is this research needed?]

### Areas to Investigate

1. [Area 1]
2. [Area 2]
3. [Area 3]

### Success Criteria

- [ ] [Question 1] answered
- [ ] [Question 2] answered
- [ ] Recommendation documented

### Deliverables

- [ ] Findings document with pros/cons
- [ ] Proof of concept (if applicable)
- [ ] Effort estimate for implementation
- [ ] Go/No-Go recommendation
```

## When to Use a Spike

Use a spike when:

- Technology choice is uncertain
- Architecture approach needs validation
- Integration complexity is unknown
- Team needs to learn before estimating

## Jira Creation Example

```json
{
  "cloudId": "<cloudId>",
  "projectKey": "<projectKey>",
  "issueTypeName": "Task",
  "summary": "[Spike] Research Topic",
  "description": "## Research Question\n[question]\n\n## Background\n[why needed]\n\n## Areas to Investigate\n1. [area]\n\n## Success Criteria\n- [ ] [deliverable]\n\n## Timebox\n[X hours/days]"
}
```
