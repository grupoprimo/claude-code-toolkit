# Sprint Planning Document Template

```markdown
# Sprint [X] Planning

## Sprint Goal

[One clear, measurable goal]

## Duration

- **Start:** [YYYY-MM-DD]
- **End:** [YYYY-MM-DD]
- **Working Days:** [X]

## Team Capacity

| Member | Role | Availability | Points/Hours |
| ------ | ---- | ------------ | ------------ |
| [Name] | Dev  | 100%         | [X]          |
| [Name] | Dev  | 80% (PTO)    | [X]          |
| [Name] | QA   | 100%         | [X]          |

**Total Capacity:** [X pts] -> **Commitment Target (85%):** [Y pts]

## Sprint Backlog

### Committed

| ID      | Title   | Points | Assignee | Priority |
| ------- | ------- | ------ | -------- | -------- |
| US-XXX  | [Title] | X      | [Name]   | High     |
| BUG-XXX | [Title] | X      | [Name]   | Critical |

**Total Committed:** [X pts]

### Stretch Goals

| ID     | Title   | Points |
| ------ | ------- | ------ |
| US-XXX | [Title] | X      |

## Dependencies

| Item   | Depends On   | Owner  | Status      | ETA   |
| ------ | ------------ | ------ | ----------- | ----- |
| US-XXX | External API | [Name] | In Progress | Day 3 |

## Risks

| Risk   | Mitigation |
| ------ | ---------- |
| [Risk] | [Plan]     |

## Ceremonies

- **Daily Standup:** [Time, Link]
- **Sprint Review:** [Date, Time]
- **Retrospective:** [Date, Time]
```

## Planning Guidelines

### Capacity Calculation

- **Commitment Target**: 85% of total capacity
- Account for meetings, code reviews, and unexpected work
- Factor in PTO, holidays, and on-call rotations

### Healthy Sprint Composition

| Type      | Percentage |
| --------- | ---------- |
| Features  | 60-70%     |
| Bugs      | 15-20%     |
| Tech Debt | 10-15%     |
| Spikes    | 5-10%      |
