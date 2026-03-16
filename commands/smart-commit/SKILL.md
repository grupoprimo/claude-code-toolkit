---
name: smart-commit
description: Analyze unstaged/staged changes, group them into logical commits, generate conventional commit messages with emoji, and execute commits sequentially. Use when the user wants to commit their changes intelligently.
---

# Smart Commit

Analyzes all pending changes, groups them into logical commits, generates conventional commit messages with emoji, and executes them after user approval.

## Emoji Mapping

| Type     | Emoji | When to Use                         |
| -------- | ----- | ----------------------------------- |
| feat     | 🎸    | New feature or improvement          |
| fix      | 🐛    | Bug fix                             |
| test     | ✅    | Adding or updating tests            |
| refactor | 💡    | Refactoring without behavior change |
| docs     | ✏️    | Documentation only                  |
| style    | 💄    | Formatting, no code logic change    |
| chore    | 🤖    | Maintenance, config, dependencies   |
| ci       | 💚    | CI/CD changes                       |
| perf     | ⚡    | Performance improvement             |

## Commit Message Format

```
{type}: {emoji} {description}
```

Or with scope:

```
{type}({scope}): {emoji} {description}
```

Rules:

- Description in imperative mood (e.g., "add user authentication", not "added")
- Max ~72 characters for the full first line
- No period at the end
- Lowercase after the emoji

## Workflow

### Step 1: Gather Changes

Run all of these in parallel:

```bash
git status --porcelain
git diff
git diff --cached
git stash list
```

Also check for untracked files that may need to be included.

If there are **no changes at all** (clean working tree, nothing staged), stop and inform the user.

### Step 2: Warn About Mixed State

If the same file has both staged and unstaged changes, warn:

> ⚠️ File `{file}` has both staged and unstaged changes. I'll include all pending changes for this file. Consider reviewing manually if you want to split them.

### Step 3: Classify Every File

Assign each changed file (staged + unstaged + untracked) to one of these categories using universal heuristics:

| Category   | Detection Rule                                                                                                                                                                                                                      |
| ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **test**   | `*.test.*`, `*.spec.*`, `*.test-utils.*`, directories: `__tests__/`, `test/`, `tests/`, `spec/`, `e2e/`, `cypress/`                                                                                                                 |
| **ci**     | `.github/workflows/`, `.gitlab-ci.yml`, `Jenkinsfile`, `.circleci/`, `.travis.yml`                                                                                                                                                  |
| **config** | Root-level dotfiles (`.*rc`, `.config.*`), `*.config.*`, `*.config.js/ts`, `package.json`, `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`, `tsconfig*`, `Dockerfile*`, `docker-compose*`, `.husky/`, `.env*`, `*.toml`, `*.ini` |
| **docs**   | `*.md`, `*.txt` (non-code), `docs/`, `LICENSE`, `CHANGELOG`, `*.mdx`                                                                                                                                                                |
| **source** | Everything else                                                                                                                                                                                                                     |

### Step 4: Group Source Files by Concern

For **source** files, use smart inference (no hardcoded paths — works on any project):

1. **Directory proximity** — files in the same directory or adjacent sibling directories likely belong together
2. **Naming patterns** — files sharing a base name belong together (e.g., `user-types.ts`, `user-service.ts`, `user.adapter.ts` all relate to "user")
3. **Import/reference analysis** — if the diff shows file A imports from file B, group them together
4. **Git history context** — check `git log --oneline -10` to see how this project typically groups changes

**Each group becomes one commit.**

Non-source categories (test, ci, config, docs) each become their own commit (or merge with a closely related source group if clearly tied — e.g., tests added for newly created source files).

### Step 5: Determine Commit Type Per Group

Analyze the diff content of each group:

- New files adding new functionality → `feat`
- Modifications that fix broken behavior (look for "fix", "bug", "error", "crash", "wrong", "incorrect" in diff context, or purely corrective changes) → `fix`
- Restructuring, renaming, extracting, without changing observable behavior → `refactor`
- Only whitespace, formatting, import ordering, lint fixes → `style`
- Performance improvements (memoization, caching, algorithm changes) → `perf`
- Test files → `test`
- CI/CD files → `ci`
- Config, deps, tooling → `chore`
- Documentation → `docs`

### Step 6: Determine Scope (Optional)

If the group clearly relates to a single module, feature, or domain, add a scope:

- Examples: `feat(auth)`, `fix(checkout)`, `test(user-profile)`
- Infer scope from directory names or shared naming patterns
- Omit scope if changes span multiple areas or scope is unclear

### Step 7: Order Commits Logically

Apply this ordering before presenting:

1. Core types / models / schemas / domain
2. Infrastructure / data layer (APIs, adapters, DB)
3. Business logic / services / use cases
4. UI / presentation / components
5. Tests (after their related source)
6. Docs
7. Config / chore / ci (last)

### Step 8: Present Proposed Commits

Show a numbered list with all proposed commits before executing anything:

```
📋 Proposed commits (in order):

1. feat(user): 🎸 add user authentication domain types
   Files: src/domain/user/user-types.ts, src/domain/user/user-errors.ts

2. feat(user): 🎸 add user authentication service and API
   Files: src/application/services/user/user-service.ts
          src/infrastructure/api/endpoints/user/user-api.ts

3. test(user): ✅ add tests for user authentication service
   Files: __tests__/server/user/user-service.test.ts

4. chore: 🤖 update dependencies
   Files: package.json, package-lock.json

Would you like to:
  [A] Approve all and commit
  [E] Edit a specific commit (specify which one)
  [S] Skip a commit
  [C] Cancel
```

Wait for user response before proceeding.

**If the user edits:** apply their changes and re-present the full list before executing.

### Step 9: Execute Commits

For each approved commit, in order:

```bash
# Stage the specific files
git add {file1} {file2} ...

# Commit with the message
git commit -m "{type}({scope}): {emoji} {description}"
```

After all commits complete, run:

```bash
git log --oneline -$(count of commits made)
```

Show the result to the user.

### Step 10: Remind About Push

End with:

> ✅ Done! {N} commit(s) created. Run `git push` when ready to push to remote.

Do **not** push automatically.

## Edge Cases

| Situation                               | Behavior                                                            |
| --------------------------------------- | ------------------------------------------------------------------- |
| No changes                              | Inform user and stop                                                |
| Single file                             | Still show proposal and ask for confirmation                        |
| Binary files                            | Include in grouping, note they are binary in the file list          |
| Files with both staged+unstaged changes | Warn user, include all pending changes                              |
| Files that don't fit any group          | Group under `chore:` catch-all, flag as "uncategorized"             |
| Untracked files                         | Include in analysis; stage them if approved                         |
| Empty commit message from user edit     | Ask again                                                           |
| Merge conflicts                         | Detect and inform user — do not attempt to commit conflicting files |

## Notes

- This command works on **any project** — no assumptions about architecture, framework, or folder structure
- The grouping heuristics are inference-based, not rule-based — always present proposals for user review
- Never skip the confirmation step (Step 8)
- Never auto-push
