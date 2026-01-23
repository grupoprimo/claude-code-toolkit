# Commit Analysis Guide

How to extract meaningful insights from git commits to create comprehensive PR descriptions.

## Analyzing Commit Messages

### 1. Categorize Commits by Type

Look for conventional commit prefixes:

- `feat:` → New feature
- `fix:` → Bug fix
- `docs:` → Documentation
- `refactor:` → Code refactoring
- `test:` → Tests
- `chore:` → Maintenance
- `style:` → Formatting
- `perf:` → Performance improvement

### 2. Extract Key Information

From each commit, identify:

**What was done:**

- File changes (new, modified, deleted)
- Functionality added/changed
- APIs introduced/modified

**Why it was done:**

- Problem being solved
- Requirement being met
- Improvement being made

**How it was done:**

- Implementation approach
- Libraries/tools used
- Architectural decisions

### 3. Group Related Commits

Commits often tell a story when grouped:

```
feat: add OAuth service structure
feat: implement Google OAuth flow
feat: implement Apple OAuth flow
test: add OAuth integration tests
docs: document OAuth setup
```

**Story:** "Implemented OAuth authentication with Google and Apple, including tests and documentation"

## Analyzing File Changes

### Commands to Use

```bash
# See what files changed
git diff --name-status origin/main..HEAD

# See what was added/removed
git diff --stat origin/main..HEAD

# See detailed changes
git diff origin/main..HEAD
```

### Interpret File Patterns

**New directories:**

```
A  src/auth/oauth/
A  src/auth/oauth/google.ts
A  src/auth/oauth/apple.ts
```

→ "Added new OAuth authentication module"

**Modified files:**

```
M  src/components/LoginScreen.tsx
M  src/navigation/AuthNavigator.tsx
```

→ "Updated login UI and navigation to support OAuth"

**Deleted files:**

```
D  src/auth/old-auth.ts
```

→ "Removed deprecated authentication code"

### File Location Insights

Files in certain locations indicate specific work:

- `src/screens/` → UI changes
- `src/services/` → Business logic
- `src/api/` → API integration
- `tests/` → Testing
- `docs/` → Documentation
- `.github/` → CI/CD, templates
- `package.json` → Dependencies

## Common Patterns

### Migration/Refactoring

**Indicators:**

- Many file moves (`R` in git status)
- Renamed imports
- "refactor:" commits
- "migrate:" commits

**Extract:**

- What's being migrated FROM
- What's being migrated TO
- Migration strategy (gradual, big-bang)
- Dependencies

### Feature Addition

**Indicators:**

- "feat:" commits
- New directories
- New test files
- Updated documentation

**Extract:**

- Feature name
- User-facing functionality
- Technical implementation
- Testing approach

### Bug Fix

**Indicators:**

- "fix:" commits
- Changes in error handling
- Updated tests
- Possibly only 1-2 files changed

**Extract:**

- What bug was fixed
- How to reproduce (from commit message)
- Root cause
- Solution approach

## Creating the Narrative

### Step 1: Read All Commits

```bash
git log --format="%s%n%b" --no-merges origin/main..HEAD
```

### Step 2: Identify the Theme

Ask yourself:

- What's the main thing being accomplished?
- Is this a feature, fix, refactor, or docs?
- What problem does this solve?

### Step 3: Extract Supporting Details

From commits and files:

- Key components added/changed
- Important technical decisions
- Dependencies or blockers
- Testing coverage

### Step 4: Structure the Description

**For Features:**

```
Description: What feature + why
Implementation:
  - Component A
  - Component B
  - Component C
Testing: What was tested
```

**For Fixes:**

```
Problem: What broke + how to reproduce
Root Cause: Why it broke
Solution: How it was fixed
Prevention: Tests added
```

**For Refactoring:**

```
Context: Why refactor was needed
Before: Current state
After: Target state
Migration: How we get there
Impact: What changes for users/devs
```

**For Documentation:**

```
What: What's being documented
Why: Why it's needed
Audience: Who it's for
Structure: How it's organized
```

## Examples

### Example 1: Feature from Commits

**Commits:**

```
feat: add funds list screen
feat: implement fund card component
feat: add fund detail screen
feat: integrate funds API
test: add funds feature tests
```

**Analysis:**

- Theme: New funds feature
- Components: List screen, detail screen, card component
- Integration: API integration
- Testing: Covered

**Description:**

```
Implements funds management feature allowing users to view and manage their investment funds.

Components:
- Funds list screen with filtering
- Fund detail screen with performance charts
- Reusable fund card component
- API integration for real-time data

Testing:
- Unit tests for components
- Integration tests for API
- E2E tests for user flows
```

### Example 2: Migration from Files

**Files changed:**

```
R  core/domain/Funds/fundsService.ts → application/services/funds/funds-service.ts
R  core/domain/Funds/fundsTypes.ts → domain/funds/funds-types.ts
M  features/Funds/screens/FundsList.tsx
M  package.json
```

**Analysis:**

- Theme: Architectural reorganization
- Pattern: File moves (R status)
- Impact: Import paths changed
- Dependencies: Updated package.json

**Description:**

```
Reorganizes funds module to follow Clean Architecture pattern.

Changes:
- Moves services to application layer
- Moves types to domain layer
- Updates all import paths
- No functional changes

Migration strategy: File moves only, no code changes
```

## Tips

1. **Read commit messages carefully** - they often contain the "why"
2. **Look at file patterns** - they show the "what"
3. **Check diffs for context** - they reveal the "how"
4. **Group related changes** - they form the narrative
5. **Note breaking changes** - call them out explicitly
6. **Identify dependencies** - mention blocking/blocked PRs

## Red Flags to Investigate

- Commits with no message
- Very large commits (500+ lines)
- Unrelated changes mixed together
- Missing tests for new features
- TODO/FIXME comments added

## Using GitHub CLI for Additional Context

### Check Related Issues

```bash
# Search for related issues
gh issue list --search "keyword"

# View issue details (if commit references an issue)
gh issue view 123
```

### Check CI Status Before PR

```bash
# See if there are any failing checks on the branch
gh run list --branch $(git branch --show-current)

# View specific run details
gh run view [run-id]
```

### Get Repository Context

```bash
# Check PR template exists
gh api repos/{owner}/{repo}/contents/.github/pull_request_template.md --jq .content | base64 -d

# List recent merged PRs for style reference
gh pr list --state merged --limit 5

# Check branch protection rules
gh api repos/{owner}/{repo}/branches/main/protection --jq '.required_pull_request_reviews'
```

### Link Commits to Issues

When analyzing commits, look for issue references:
- `#123` - Links to issue/PR 123
- `Fixes #123` - Closes issue 123 when PR merges
- `Closes #123` - Same as Fixes
- `Resolves #123` - Same as Fixes

Use these to provide context in the PR description:

```markdown
## Related Issues

- Fixes #123 - User authentication timeout
- Related to #456 - Security improvements roadmap
```
