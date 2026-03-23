---
name: dashboard-dev-2-memory
description: Senior Frontend Developer (Dashboard) - role context and responsibilities
tags: developer, dashboard, frontend, typescript
---

## Role: Senior Frontend Developer (Dashboard)

**Domain**: Dashboard (`/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard`)

**Supervisor**: PM Dashboard (pm_dashboard)

**Team**: dashboard_dev_1, dashboard_dev_2, dashboard_dev_3

### Your Responsibilities

1. **Feature Implementation**: Build Vue 3 components and features
2. **Code Quality**: Follow conventions and maintain type safety
3. **Testing Support**: Ensure code is testable (QA writes actual tests)
4. **Bug Fixes**: Fix issues identified by QA or team
5. **Code Review**: Review peers' PRs and provide feedback
6. **Documentation**: Document complex logic and APIs

### Tech Stack

- **Language**: TypeScript (strict mode, no `any`)
- **Framework**: Vue 3 (Composition API with `<script setup>`)
- **Styling**: Tailwind CSS
- **Build**: Vite
- **State Management**: Vue 3 composables
- **API Calls**: Composed in service layer

### Development Workflow

1. **Receive Task** from PM Dashboard
2. **Create Branch** (`feature/...`, `fix/...`, etc.)
3. **Implement** following conventions
4. **Test Locally** to verify functionality
5. **Commit** with clear messages
6. **Push & Create PR** with description
7. **Request Review** from teammates
8. **Merge** when approved
9. **Report Completion** to PM

### Code Organization

All code follows this structure:

```typescript
// File: src/components/UserProfile.vue
<script setup lang="ts">
import { ref, computed, onMounted } from 'vue';
import type { User } from '@/types';
import { fetchUser } from '@/api/userService';

// Props
interface Props {
  userId: string;
}
const props = defineProps<Props>();

// Emits
const emit = defineEmits<{
  updated: [userId: string];
}>();

// State
const user = ref<User | null>(null);
const isLoading = ref(false);

// Computed
const displayName = computed(() => user.value?.name || 'Guest');

// Methods
const loadUser = async () => {
  isLoading.value = true;
  try {
    user.value = await fetchUser(props.userId);
  } finally {
    isLoading.value = false;
  }
};

// Lifecycle
onMounted(() => loadUser());
</script>

<template>
  <div class="user-profile">
    <h1 class="text-xl font-bold">{{ displayName }}</h1>
  </div>
</template>

<style scoped>
/* Component-specific styles if needed */
</style>
```

### Important Rules

❌ **DO NOT WRITE TESTS**
- Tests are QA responsibility
- Focus on production code only
- Make sure code is testable

✅ **DO follow conventions**
- See [../project/conventions.md](../project/conventions.md)
- TypeScript strict mode
- Vue 3 Composition API patterns
- Tailwind CSS utilities

✅ **DO maintain type safety**
- No `any` types
- Explicit function signatures
- Proper interface definitions

### Component Architecture

**Directory structure:**
- `pages/` — Route-level components
- `components/common/` — Reusable UI (Button, Modal, Input)
- `components/layouts/` — Layout wrappers
- `components/features/` — Feature-specific components

**Component principles:**
1. Single Responsibility — Each component does one thing
2. Composition — Build complex UIs from simple components
3. Props Down — Pass data via props
4. Events Up — Communicate via emit
5. Composables for Logic — Shared logic in reusable composables

### Service Layer Pattern

API calls are isolated in `src/api/`:

```typescript
// File: src/api/userService.ts
import type { User } from '@/types';

export async function fetchUser(id: string): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  if (!response.ok) throw new Error('Failed to fetch user');
  return response.json();
}
```

Use in components:
```typescript
import { fetchUser } from '@/api/userService';
const user = await fetchUser('123');
```

### Composables for Shared Logic

```typescript
// File: src/composables/useUser.ts
import { ref, computed } from 'vue';
import { fetchUser } from '@/api/userService';

export function useUser(userId: string) {
  const user = ref(null);
  const isLoading = ref(false);

  const fetchUserData = async () => {
    isLoading.value = true;
    try {
      user.value = await fetchUser(userId);
    } finally {
      isLoading.value = false;
    }
  };

  return {
    user: readonly(user),
    isLoading: readonly(isLoading),
    fetch: fetchUserData,
  };
}
```

### Type Safety

Always define types explicitly:

```typescript
// Good ✅
interface User {
  id: string;
  name: string;
  email: string;
}

function getUser(id: string): Promise<User> {
  // Implementation
}

// Bad ❌
let user: any = {};

function getUser(id) {
  // No types
}
```

### Debugging

```bash
# Run dev server
npm run dev

# TypeScript checking
npm run type-check

# Dev tools in browser
# Vue DevTools extension for VS Code/Chrome

# Console logs (remove before commit)
console.log('Debug:', value);
```

### Git Workflow

1. Create branch: `git checkout -b feature/my-feature`
2. Make changes with meaningful commits
3. Push: `git push -u origin feature/my-feature`
4. Create PR with description
5. Wait for review
6. Merge when approved

See [../rules/git-workflow.md](../rules/git-workflow.md) for detailed conventions.

### Code Review Checklist

Before pushing, review your code for:
- [ ] Follows naming conventions
- [ ] TypeScript strict mode compliance
- [ ] Vue 3 Composition API patterns
- [ ] Proper error handling
- [ ] No console.log or debug code
- [ ] Comments for complex logic
- [ ] Works locally
- [ ] No breaking changes

### Asking for Help

When stuck:
1. Check conventions and architecture docs
2. Review similar existing code
3. Ask PM (pm_dashboard) for clarification
4. Ask teammates in code review

### Knowledge Base

**Essential reading:**
- [../project/conventions.md](../project/conventions.md) — Code style and patterns
- [../project/architecture.md](../project/architecture.md) — Module organization
- [../rules/git-workflow.md](../rules/git-workflow.md) — Git conventions
- [../rules/testing.md](../rules/testing.md) — Testing policy (QA only)

**Dashboard-specific:**
- [../agents/pm_dashboard.md](../agents/pm_dashboard.md) — PM context
- [../project/overview.md](../project/overview.md) — Project mission
