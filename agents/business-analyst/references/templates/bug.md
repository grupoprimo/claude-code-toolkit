# Bug Report Template

````markdown
## [BUG-XXX] [Concise Bug Title]

**Severity:** Critical / Major / Minor / Trivial
**Priority:** P0 / P1 / P2 / P3
**Environment:** Production / Staging / Development
**Reported By:** [Name] | **Date:** [YYYY-MM-DD]

### Summary

[One-line description]

### Steps to Reproduce

1. [Step 1]
2. [Step 2]
3. [Step 3]

### Expected Behavior

[What should happen]

### Actual Behavior

[What actually happens]

### Evidence

- **Screenshots/Video:** [Link]
- **Browser/Device:** [e.g., Chrome 120, iPhone 15]
- **Error Logs:**

```
[Relevant logs]
```

### Impact

- **Users Affected:** [Count or %]
- **Business Impact:** [Description]
- **Workaround:** [Available? Describe]

### Root Cause (if known)

[Technical explanation]

### Acceptance Criteria

```gherkin
Given the fix is deployed
When [reproduction steps]
Then [correct behavior]
  And no regression in [related area]
```

---

**Story Points:** [X]
**Assignee:** [TBD]
````

## Jira Creation Example

````json
{
  "cloudId": "<cloudId>",
  "projectKey": "<projectKey>",
  "issueTypeName": "Bug",
  "summary": "Bug Title",
  "description": "## Summary\n[description]\n\n## Steps to Reproduce\n1. [step]\n\n## Expected vs Actual\n- Expected: [expected]\n- Actual: [actual]\n\n## Acceptance Criteria (Fix Verification)\n```gherkin\n@e2e @regression\nScenario: [verification]\n  Given [precondition]\n  When [action]\n  Then [fixed behavior]\n```"
}
````
