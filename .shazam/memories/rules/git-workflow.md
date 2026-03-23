---
name: git-workflow
description: Git branch strategy, commit conventions, and collaboration rules
tags: git, workflow, branching, commits
---

## Branch Strategy

The project uses **GitHub Flow** for branch management:

### Main Branch (`main`)
- Represents production-ready code
- Always stable and deployable
- Protected: requires PR reviews before merge
- Automatically deployed to production

### Feature Branches
- Created from `main`
- Naming convention: `feature/description-in-kebab-case`
- Example: `feature/user-authentication`, `feature/dark-mode-toggle`
- One feature per branch
- Delete after merge

### Bug Fix Branches
- Created from `main` or appropriate release branch
- Naming convention: `fix/description-in-kebab-case`
- Example: `fix/memory-leak-in-dashboard`, `fix/command-not-registering`
- Include issue number: `fix/issue-123-command-not-registering`
- Delete after merge

### Refactor Branches
- Created from `main`
- Naming convention: `refactor/description-in-kebab-case`
- Example: `refactor/component-structure`, `refactor/api-client-layer`
- Should not change functionality
- Delete after merge

### Chore Branches
- Created from `main`
- Naming convention: `chore/description-in-kebab-case`
- Example: `chore/update-dependencies`, `chore/ci-improvements`
- For non-feature work
- Delete after merge

## Commit Conventions

### Commit Message Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types
- `feat`: A new feature
- `fix`: A bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, semicolons, etc.)
- `refactor`: Code refactoring without feature changes
- `perf`: Performance improvements
- `test`: Test-related changes
- `chore`: Build, CI, dependencies, etc.

### Subject Line Rules
- Use imperative mood: "add" not "added" or "adds"
- Don't capitalize first letter
- No period (.) at the end
- Max 50 characters
- Reference issue if applicable: `fix: resolve issue #123`

### Examples

```
feat(dashboard): add user profile component
fix(extension): resolve command registration bug
docs(readme): update installation instructions
refactor(api): simplify error handling
perf(dashboard): optimize list rendering
test(composables): add useUser tests
chore: update dependencies
```

### Body (Optional but Recommended)
- Explain WHAT and WHY, not HOW
- Wrap at 72 characters
- Separate from subject with blank line
- Use bullet points for multiple changes

```
fix(api): handle network timeout errors

- Add exponential backoff retry logic
- Set max retry attempts to 3
- Clear user feedback on timeout

Fixes #456
```

### Footer (Optional)
- Reference issues: `Fixes #123`, `Closes #456`
- Co-authors: `Co-Authored-By: Name <email@example.com>`

```
fix(auth): resolve token refresh issue

Explanation of the fix...

Fixes #789
Co-Authored-By: John Doe <john@example.com>
```

## Pull Request Workflow

### Before Creating PR
1. Create feature branch from `main`
2. Make focused, logical commits
3. Keep commits small and atomic
4. Rebase on latest `main` if needed
5. Ensure all tests pass locally

### PR Description Template

```markdown
## Description
Brief description of the changes

## Related Issues
Fixes #123
Relates to #456

## Type of Change
- [ ] Feature
- [ ] Bug Fix
- [ ] Refactor
- [ ] Documentation
- [ ] Chore

## Changes Made
- Specific change 1
- Specific change 2
- Specific change 3

## Testing Approach
Description of how the changes were tested

## Checklist
- [ ] Code follows project conventions
- [ ] Changes are properly documented
- [ ] No console errors or warnings
- [ ] Works on multiple browsers/VS Code versions
```

### Review Requirements
- **Minimum 1 approval** from team members (can be other developers)
- **All conversations resolved**
- **CI checks passing** (tests, linting, builds)
- **No merge conflicts**

### Merging
- Use "Squash and merge" for single-feature branches
- Use "Create a merge commit" for complex features with multiple commits
- **NEVER** use "Rebase and merge" for this project
- Delete branch after merge

## Local Development Workflow

```bash
# Update main with latest changes
git checkout main
git pull origin main

# Create feature branch
git checkout -b feature/my-feature

# Make changes and commit
git add .
git commit -m "feat(dashboard): add new component"

# Push to remote
git push -u origin feature/my-feature

# Create PR via GitHub UI
# Wait for reviews
# Merge when approved
```

## Conflict Resolution

When encountering merge conflicts:

1. **Update your branch**: `git pull origin main`
2. **Resolve conflicts** in your editor
3. **Run tests** to ensure everything works
4. **Commit the resolution**: `git add .` then `git commit -m "merge: resolve conflicts with main"`
5. **Push resolved branch**: `git push origin feature/my-feature`
6. **Re-request review** on the PR

## Best Practices

### Do's ✅
- Make small, focused commits
- Write descriptive commit messages
- Reference issues in commits and PRs
- Review your own code before requesting review
- Keep branches up to date with main
- Communicate with teammates about related work

### Don'ts ❌
- Don't commit directly to main (use PRs)
- Don't mix multiple features in one commit
- Don't write vague commit messages
- Don't include generated files or dependencies
- Don't rewrite history of pushed commits
- Don't force push to main or shared branches

## Emergency Hotfixes

For critical production bugs:

1. Create hotfix branch from main: `hotfix/critical-bug`
2. Make minimal fix and test thoroughly
3. Commit with clear message: `fix: critical bug affecting production`
4. Create PR with high priority
5. After merge, cherry-pick changes to any release branches if needed

## References

See [./testing.md](./testing.md) for test-related workflows.
See [../project/conventions.md](../project/conventions.md) for code style conventions.
