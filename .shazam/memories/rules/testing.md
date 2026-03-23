---
name: testing-strategy
description: Testing approach, patterns, and QA responsibilities
tags: testing, qa, quality, strategy
---

## Testing Policy

**CRITICAL RULE**: Testing is the exclusive responsibility of **QA agents only**. Developers implement features and fix bugs—they do NOT write or run tests.

### Test Responsibilities

| Role | Responsibility |
|------|-----------------|
| **Developers** | Implement features, fix bugs, write production code |
| **QA Agents** | Write tests, run tests, verify fixes, test plans |

**Developers MUST NOT:**
- Write unit tests, integration tests, or E2E tests
- Create test files (`*_test.*`, `*_spec.*`, `test_*`)
- Modify existing test files
- Run test suites

## Testing Architecture

### Dashboard Testing

**Unit Tests**
- Test individual Vue components in isolation
- Test utilities and helper functions
- Test composables
- Tool: Vitest + Vue Test Utils

**Component Tests**
- Test Vue component behavior and user interactions
- Test prop passing and event emissions
- Test conditional rendering

**Integration Tests**
- Test multiple components working together
- Test API integration (with mocks)
- Test state management flows

**E2E Tests**
- Test complete user workflows
- Test across multiple pages
- Tool: Playwright or Cypress

### VS Code Extension Testing

**Unit Tests**
- Test command handlers
- Test provider implementations
- Test utilities and helpers
- Tool: Mocha + assert

**Integration Tests**
- Test extension activation and deactivation
- Test command registration
- Test webview communication

**E2E Tests**
- Test extension in VS Code environment
- Test real user workflows in the editor
- Tool: VSCode Test Environment

## Quality Standards

### Code Coverage Targets
- **Utilities/Services**: 80%+ coverage
- **Components**: 70%+ coverage
- **Critical Paths**: 90%+ coverage

### Test Naming Conventions

```typescript
// describe blocks use feature/component names
describe('UserProfile component', () => {
  // Test cases describe specific behavior
  it('should display user name when data is loaded', () => {});
  it('should show loading state while fetching data', () => {});
  it('should emit update event when save button clicked', () => {});
});
```

## QA Workflow

### Bug Report Flow
1. **QA**: Write failing test that demonstrates the bug
2. **QA**: Create bug report task with test details
3. **PM**: Create fix task and assign to developer
4. **Developer**: Fix the bug (does NOT run tests)
5. **QA**: Create verification task to confirm fix
6. **QA**: Run test suite and verify pass

### Feature Verification Flow
1. **Developer**: Implements feature
2. **PM**: Creates test task for QA
3. **QA**: Writes comprehensive tests
4. **QA**: Tests feature functionality and edge cases
5. **QA**: Reports any issues found
6. **PM**: Routes issues back to developer if needed
7. **QA**: Final verification after fixes

## Mocking and Test Doubles

### API Mocking
```typescript
// Mock external API calls
vi.mock('@/api/userService', () => ({
  fetchUser: vi.fn(() => Promise.resolve({ id: '1', name: 'John' }))
}));
```

### Component Mocking
```typescript
// Shallow render components in tests
const wrapper = mount(UserProfile, {
  shallow: true
});
```

## Test Data and Fixtures

Create test data files for consistency:

```typescript
// File: src/__fixtures__/users.ts
export const mockUser = {
  id: '123',
  name: 'John Doe',
  email: 'john@example.com',
};

export const mockUsers = [mockUser, { id: '456', name: 'Jane Doe' }];
```

## Continuous Integration

- Tests run automatically on push to any branch
- Tests must pass before merging to main
- Code coverage reports generated for each PR
- Failed tests block deployment

## References

See [./git-workflow.md](./git-workflow.md) for commit conventions and branch protection rules.
See [../project/architecture.md](../project/architecture.md) for testing framework setup.
