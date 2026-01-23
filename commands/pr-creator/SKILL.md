---
name: pr-creator
description: Create comprehensive Pull Request descriptions and review messages for Git repositories. Use when the user asks to create a PR, generate PR description, write PR summary, or prepare a message for review groups. Analyzes git commits to understand changes and generates formatted content following the project's PR template.
---

# PR Creator

Creates professional Pull Request descriptions and team review messages by analyzing git commits and following project templates.

## Prerequisites Check

**ALWAYS verify these before starting:**

```bash
# 1. Check if gh CLI is installed and authenticated
gh auth status

# 2. Check if in a git repository
git rev-parse --git-dir 2>/dev/null

# 3. Check current branch
git branch --show-current

# 4. Check for uncommitted changes (warn user)
git status --porcelain
```

If `gh` is not installed or not authenticated, inform the user:
- Install: `brew install gh` (macOS) or see https://cli.github.com/
- Authenticate: `gh auth login`

## Workflow

### 1. Determine Comparison Strategy

**CRITICAL**: Always ask the user how to identify their changes to avoid including commits from other features.

Three comparison strategies:

#### Strategy A: Branch-based (Simple)

Use when base branch only has YOUR commits:

```bash
git log origin/${BASE_BRANCH}..HEAD
```

#### Strategy B: Merge-base (Shared refactor branch)

Use when base branch has commits from multiple features being worked in parallel:

```bash
# Find the common ancestor (where you branched off)
MERGE_BASE=$(git merge-base HEAD origin/${BASE_BRANCH})
git log ${MERGE_BASE}..HEAD
```

#### Strategy C: Time/Author-based (Last resort)

Use when merge-base doesn't help:

```bash
# Show only your commits
git log --author="$(git config user.email)" origin/${BASE_BRANCH}..HEAD
```

**Decision Tree:**

```
1. Ask user: "Qual estratégia usar para identificar seus commits?"

   A) "Todos os commits da minha branch vs [base]"
      → Use git log origin/base..HEAD

   B) "Apenas meus commits (tem outros commits na base que não são meus)"
      → Use git log $(git merge-base HEAD origin/base)..HEAD

   C) "Apenas commits com meu nome/email"
      → Use git log --author="email" origin/base..HEAD

2. If user doesn't know, detect scenario:
   - Check commit count: git rev-list --count origin/base..HEAD
   - If > 20 commits AND multiple authors detected
     → Suggest Strategy B (merge-base)
   - Otherwise
     → Use Strategy A (branch-based)
```

### 2. Analyze the Repository

```bash
# Check if in a git repository
git rev-parse --git-dir 2>/dev/null

# Get current branch
CURRENT_BRANCH=$(git branch --show-current)

# Get base branch (ask user or detect)
BASE_BRANCH="main"  # or develop, refactor/*, etc.

# Check if PR template exists
ls -la .github/pull_request_template.md
```

### 3. Analyze Commits (with correct strategy)

**Example: Merge-base strategy (recommended for shared branches)**

```bash
# 1. Find where you branched off
MERGE_BASE=$(git merge-base HEAD origin/${BASE_BRANCH})

# 2. Show only commits since you branched
git log --oneline --no-merges ${MERGE_BASE}..HEAD

# 3. Get detailed messages
git log --format="%s%n%b" --no-merges ${MERGE_BASE}..HEAD

# 4. Verify authors (to confirm)
git log --format="%an <%ae>" ${MERGE_BASE}..HEAD | sort -u
```

**Example: Branch-based strategy (simple case)**

```bash
git log --oneline --no-merges origin/${BASE_BRANCH}..HEAD
git log --format="%s%n%b" --no-merges origin/${BASE_BRANCH}..HEAD
```

**Example: Author-based strategy (fallback)**

```bash
USER_EMAIL=$(git config user.email)
git log --author="${USER_EMAIL}" --oneline --no-merges origin/${BASE_BRANCH}..HEAD
```

### 4. Analyze File Changes

Use the same strategy for diffs:

```bash
# Merge-base strategy
MERGE_BASE=$(git merge-base HEAD origin/${BASE_BRANCH})
git diff --name-status ${MERGE_BASE}..HEAD

# Branch-based strategy
git diff --name-status origin/${BASE_BRANCH}..HEAD
```

### 5. Verification Steps

**ALWAYS verify before generating PR:**

```bash
echo "=== Verification ==="
echo "Current branch: $(git branch --show-current)"
echo "Base branch: ${BASE_BRANCH}"
echo "Strategy: [branch-based | merge-base | author-based]"
echo ""

# If using merge-base
if [ -n "$MERGE_BASE" ]; then
    echo "Merge base: $MERGE_BASE"
    echo "Merge base date: $(git show -s --format=%ci $MERGE_BASE)"
fi

# Show commit count
COMMIT_COUNT=$(git rev-list --count ${MERGE_BASE:-origin/${BASE_BRANCH}}..HEAD)
echo "Total commits: $COMMIT_COUNT"

# Show authors
echo "Authors in this range:"
git log --format="%an <%ae>" ${MERGE_BASE:-origin/${BASE_BRANCH}}..HEAD | sort -u

echo ""
echo "First 10 commits:"
git log --oneline ${MERGE_BASE:-origin/${BASE_BRANCH}}..HEAD | head -10

echo ""
read -p "These look correct? (y/n): " confirm
```

### 6. Load PR Template

```bash
cat .github/pull_request_template.md
```

### 7. Generate PR Description

Create PR description that:

- Follows the template structure exactly
- Uses ONLY verified commits from strategy above
- Maintains Portuguese if template is in Portuguese
- **Clearly indicates comparison strategy used**

Include in PR description:

```markdown
**Base branch**: [branch-name]
**Comparison**: [strategy used - e.g., "merge-base" or "branch diff"]
**Commits analyzed**: [count]
```

### 8. Generate Review Message

```
[Saudação] pessoal,

[O que foi feito - 1-2 frases resumindo]

[Destaques importantes - bullets com pontos-chave, máximo 5 itens]

Base: [branch] → [current] ([X] commits)
Estratégia: [merge-base | branch-diff]
Link do PR: [URL]
```

### 9. Create PR Automatically (GitHub CLI)

**CRITICAL: Always ask the user before creating the PR automatically.**

Ask the user: "Deseja que eu crie o PR automaticamente usando a GitHub CLI?"

If yes, proceed with the following steps:

#### 9.1 Pre-flight Checks

```bash
# Verify gh is authenticated
gh auth status

# Check if branch is pushed to remote
git ls-remote --heads origin $(git branch --show-current)

# If not pushed, push first
git push -u origin $(git branch --show-current)

# Check for merge conflicts with base branch
git fetch origin ${BASE_BRANCH}
git merge-tree $(git merge-base HEAD origin/${BASE_BRANCH}) HEAD origin/${BASE_BRANCH} | grep -q "^<<<<<<<" && echo "CONFLICTS DETECTED"
```

#### 9.2 Gather PR Configuration

Ask the user about PR settings (use AskUserQuestion tool):

**Question 1: PR Type**
- "Draft PR (Recommended)" - Creates as draft, can be marked ready later
- "Ready for Review" - Creates as ready for review immediately

**Question 2: Reviewers** (optional)
- Ask: "Deseja adicionar reviewers? Se sim, liste os usernames separados por vírgula."

**Question 3: Labels** (optional)
- Show available labels: `gh label list --limit 20`
- Ask: "Deseja adicionar labels? Liste os nomes separados por vírgula."

**Question 4: Assignees** (optional)
- Ask: "Deseja se auto-atribuir ao PR?"

#### 9.3 Create the PR

```bash
# Basic PR creation
gh pr create \
  --base "${BASE_BRANCH}" \
  --title "${PR_TITLE}" \
  --body "${PR_BODY}"

# With all options
gh pr create \
  --base "${BASE_BRANCH}" \
  --title "${PR_TITLE}" \
  --body "${PR_BODY}" \
  --draft \                    # if user chose draft
  --reviewer "user1,user2" \   # if reviewers specified
  --label "label1,label2" \    # if labels specified
  --assignee "@me"             # if auto-assign chosen
```

#### 9.4 Post-creation Actions

After creating the PR:

```bash
# Get the PR URL
PR_URL=$(gh pr view --json url -q .url)

# Show PR details
gh pr view

# Open in browser (optional - ask user)
gh pr view --web
```

**Important:** Always show the PR URL to the user after creation.

#### 9.5 Complete Example

```bash
# Full workflow example
CURRENT_BRANCH=$(git branch --show-current)
BASE_BRANCH="main"

# Push branch if needed
git push -u origin ${CURRENT_BRANCH}

# Create PR
gh pr create \
  --base "${BASE_BRANCH}" \
  --title "feat: add user authentication flow" \
  --body "$(cat <<'EOF'
## Description

Este PR implementa o fluxo de autenticação de usuários.

## Mudanças

- ✅ Login screen
- ✅ Authentication service
- ✅ Token storage

## Type of change

- [x] New feature

## Checklist

- [x] Tests added
- [x] Documentation updated
EOF
)" \
  --draft \
  --assignee "@me"

# Get and display PR URL
gh pr view --json url -q .url
```

### 10. Error Handling

Common errors and solutions:

| Error | Cause | Solution |
|-------|-------|----------|
| `gh: command not found` | gh CLI not installed | `brew install gh` |
| `not logged in` | gh not authenticated | `gh auth login` |
| `branch not found on remote` | Branch not pushed | `git push -u origin branch-name` |
| `pull request already exists` | PR already open for branch | Use `gh pr view` to see existing PR |
| `merge conflict` | Conflicts with base branch | Resolve conflicts locally first |

### 11. Alternative: Generate PR Link Only

If user prefers to create PR manually via web:

```bash
# Generate the PR creation URL
REPO=$(gh repo view --json nameWithOwner -q .nameWithOwner)
CURRENT_BRANCH=$(git branch --show-current)
BASE_BRANCH="main"

echo "https://github.com/${REPO}/compare/${BASE_BRANCH}...${CURRENT_BRANCH}?expand=1"
```

## Usage Examples

### Example 1: Shared Refactor Branch (Your Case!)

```
User: "Estou em feature/data-renewal. A base é refactor/GRAO-4560, mas tem commits
       de outras features (menu, grao-safe) que ainda não foram mergeados.
       Cria o PR só com meus commits."

Claude: "Entendi! Vou usar merge-base para pegar apenas os commits que você adicionou
         desde que criou sua branch."

[executes]
MERGE_BASE=$(git merge-base HEAD origin/refactor/GRAO-4560)
git log --oneline ${MERGE_BASE}..HEAD

[shows]
"Encontrei 8 commits desde que você criou a branch:
 - feat(data-renewal): add flat structure
 - test(data-renewal): add unit tests
 - refactor(data-renewal): extract layout component
 [...]

 Estes são apenas os seus commits, sem incluir menu/grao-safe. Correto?"

User: "Sim!"

[generates PR with]
**Base branch**: refactor/GRAO-4560-navigation-service-refactoring
**Comparison**: merge-base (para evitar commits de outras features)
**Commits analyzed**: 8
```

### Example 2: Simple Feature Branch

```
User: "Cria PR da minha feature/oauth para main"

Claude: [detects simple case]
        "Comparando sua branch com main..."

[executes]
git log origin/main..HEAD
[only 3 commits, same author]

[generates PR normally]
```

### Example 3: User Specifies Strategy

```
User: "Cria PR usando merge-base, não quero pegar os commits que já estão na develop"

Claude: "Entendido, usando merge-base para comparação."

[executes]
MERGE_BASE=$(git merge-base HEAD origin/develop)
git log ${MERGE_BASE}..HEAD
```

## Special Scenario: Shared Refactor Branch

This is common in large refactorings where:

- Main branch: `refactor/GRAO-4560-navigation-service-refactoring`
- Multiple features being merged into it:
  - `feature/menu` → refactor branch (PR pending)
  - `feature/grao-safe` → refactor branch (PR pending)
  - `feature/data-renewal` → refactor branch (your current work)

**Problem**:

```bash
# This includes ALL changes in refactor branch
git log origin/refactor-branch..feature/data-renewal
# Shows: menu commits + grao-safe commits + data-renewal commits ❌
```

**Solution**:

```bash
# Find where you branched off (before menu/grao-safe were added)
MERGE_BASE=$(git merge-base HEAD origin/refactor-branch)

# Show only YOUR commits since branching
git log ${MERGE_BASE}..HEAD
# Shows: only data-renewal commits ✅
```

### How Merge-base Works

```
main
 └── commit A
      └── refactor/GRAO-4560 (created here)
           ├── commit B
           ├── commit C (you branch here) ← MERGE_BASE
           │    └── feature/data-renewal
           │         ├── commit D (yours)
           │         ├── commit E (yours)
           │         └── commit F (yours)
           ├── commit G (menu - added later)
           └── commit H (grao-safe - added later)

git merge-base finds commit C
git log C..HEAD shows only D, E, F ✅
git log origin/refactor..HEAD shows D, E, F, G, H ❌
```

## Decision Helper

Ask user these questions to determine strategy:

1. **"A branch base ([base-branch]) tem commits de outras pessoas/features que você NÃO quer incluir no PR?"**
   - Yes → Use merge-base
   - No → Use branch-based

2. **"Quantos commits você espera que sejam seus?"**
   - If actual count >> expected → Suggest merge-base

3. **"Quer ver a lista de commits antes de gerar o PR?"**
   - Yes → Show commits with authors, let user decide

## Example Output

### PR with Merge-base Strategy:

```markdown
# Description

Este PR adiciona fluxo de Data Renewal com estrutura flat.

**Base branch**: refactor/GRAO-4560-navigation-service-refactoring
**Comparison strategy**: merge-base (para incluir apenas commits desta feature)
**Commits analyzed**: 8
**Authors**: João Silva <joao@example.com>

## Objetivo

Simplificar navegação do Data Renewal seguindo padrão da refatoração.

## Mudanças Implementadas

### Data Renewal

- ✅ Novo `DataRenewalFormNavigator` com estrutura flat
- ✅ Componente `DataRenewalLayout` extraído e reutilizável
  [...]
```

### Review Message:

```
Bom dia pessoal,

Abri PR de Data Renewal para mergear na branch de refatoração.

Mudanças (apenas desta feature):
✅ Data Renewal com estrutura flat
✅ Layout component extraído
✅ Testes atualizados
✅ 8 commits analisados via merge-base

⚠️ Base: refactor/GRAO-4560 (branch compartilhada)
📊 Estratégia: merge-base (commits apenas desta feature)

Link do PR: [URL]
```

## Pro Tips

### Verify Merge-base is Correct

```bash
# Show the merge-base commit
MERGE_BASE=$(git merge-base HEAD origin/${BASE_BRANCH})
git show -s --format="%h %s (%ci)" $MERGE_BASE

# Should show the commit where you branched off
```

### Show Visual Graph

```bash
# See where you branched
git log --oneline --graph --decorate origin/${BASE_BRANCH}..HEAD
```

### Count Commits by Author

```bash
# See how many commits each person contributed
git shortlog -sn origin/${BASE_BRANCH}..HEAD

# Or with merge-base
git shortlog -sn ${MERGE_BASE}..HEAD
```

## Common Mistakes to Avoid

- ❌ Using branch-diff on shared refactor branches
- ❌ Not verifying commit count before generating PR
- ❌ Not checking authors of commits included
- ❌ Including "cleanup commits" from other features
- ❌ Not documenting comparison strategy in PR

## Quick Actions Reference

### Create PR (Quick - Draft)
```bash
gh pr create --base main --draft --fill
```

### Create PR (With Body from File)
```bash
gh pr create --base main --title "feat: description" --body-file pr-body.md
```

### View Existing PR
```bash
gh pr view
gh pr view --web  # Open in browser
```

### List Open PRs
```bash
gh pr list
```

### Add Reviewers to Existing PR
```bash
gh pr edit --add-reviewer user1,user2
```

### Mark Draft as Ready
```bash
gh pr ready
```

### Merge PR
```bash
gh pr merge --squash  # or --merge or --rebase
```

## Troubleshooting

### "gh: command not found"
```bash
# macOS
brew install gh

# Ubuntu/Debian
sudo apt install gh

# Or download from https://cli.github.com/
```

### "not logged into any GitHub hosts"
```bash
gh auth login
# Follow the prompts to authenticate
```

### "branch not found on remote"
```bash
# Push your branch first
git push -u origin $(git branch --show-current)
```

### "a]pull request for branch X already exists"
```bash
# View existing PR
gh pr view

# Or list all PRs for this branch
gh pr list --head $(git branch --show-current)
```

### "merge conflict with base branch"
```bash
# Update your branch with base
git fetch origin main
git rebase origin/main
# Resolve conflicts, then force push
git push --force-with-lease
```

### "GraphQL: Could not resolve to a Repository"
```bash
# Check if you're in a git repo with remote
git remote -v

# Set upstream if needed
gh repo set-default
```

## Workflow Summary Checklist

- [ ] Verify gh CLI is installed and authenticated
- [ ] Determine comparison strategy (branch-based, merge-base, author-based)
- [ ] Analyze commits using chosen strategy
- [ ] Verify commit list with user
- [ ] Load PR template (if exists)
- [ ] Generate PR description
- [ ] Generate review message
- [ ] Ask user: Create PR automatically?
  - [ ] If yes: Configure options (draft, reviewers, labels)
  - [ ] If yes: Create PR with `gh pr create`
  - [ ] If no: Provide PR creation URL
- [ ] Share PR URL and review message with user

## References

For more examples and patterns, see:

- `references/pr-examples.md` - Real PR examples from the team
- `references/commit-analysis.md` - How to extract insights from commits
