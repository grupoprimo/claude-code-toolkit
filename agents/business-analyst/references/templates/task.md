# Task / Sub-task Template

```markdown
## [TASK-XXX] [Task Title]

**Type:** Technical / DevOps / Documentation / Research
**Parent:** [US-XXX]
**Estimated Hours:** [X]h
**Assignee:** [TBD/Name]

### Description

[What needs to be done - start with a verb]

### Technical Approach

[How to implement]

### Definition of Done

- [ ] [Criterion 1]
- [ ] [Criterion 2]

### Sub-tasks

- [ ] [SUB-XXX] [Sub-task 1] - [Xh]
- [ ] [SUB-XXX] [Sub-task 2] - [Xh]

### Blockers/Prerequisites

- [Blocker 1]
```

## Guidelines

### Task Hours

- Keep tasks between **2-8 hours**
- Tasks > 8h should be split
- Tasks < 2h can be grouped

## Jira Creation Example

### Task

```json
{
  "cloudId": "<cloudId>",
  "projectKey": "<projectKey>",
  "issueTypeName": "Task",
  "summary": "Task Title",
  "description": "## Description\n[what needs to be done]\n\n## Technical Approach\n[how to implement]\n\n## Definition of Done\n- [ ] [criterion]",
  "parent": "<parent-key>"
}
```

### Sub-task

```json
{
  "cloudId": "<cloudId>",
  "projectKey": "<projectKey>",
  "issueTypeName": "Sub-task",
  "summary": "Sub-task Title",
  "description": "## Description\n[what needs to be done]\n\n## Definition of Done\n- [ ] [criterion]",
  "parent": "<parent-key>"
}
```

Note: `parent` is **required** for Sub-tasks.
