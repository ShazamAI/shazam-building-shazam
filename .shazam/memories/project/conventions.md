---
name: code-conventions
description: Code style, naming conventions, and development standards
tags: conventions, style, naming, standards
---

## Naming Conventions

### Files and Directories

**Vue Components**
```
components/
├── MyComponent.vue              # Pascal case for component files
├── my-component/                # kebab-case directory
│   ├── MyComponent.vue
│   ├── MyComponent.test.ts      # Test file with .test suffix
│   └── types.ts                 # Types specific to component
```

**TypeScript Files**
```
src/
├── utils/                       # kebab-case directories
│   ├── formatDate.ts            # camelCase for utilities
│   └── validateEmail.ts
├── types/                       # kebab-case directories
│   └── user.ts                  # camelCase for type files
├── api/                         # kebab-case directories
│   └── authService.ts           # camelCase for service files
```

**Constants**
```typescript
// UPPER_SNAKE_CASE for module-level constants
export const MAX_RETRY_ATTEMPTS = 3;
export const DEFAULT_TIMEOUT = 5000;

// camelCase for derived constants
const baseUrl = new URL(API_ENDPOINT);
```

### Variable and Function Names

```typescript
// camelCase for variables
let userCount = 0;
const isActive = true;
const userId = "12345";

// camelCase for functions
function fetchUserData(id: string): Promise<User> {}
const calculateTotal = (items: Item[]): number => {};

// camelCase for properties
interface User {
  userId: string;
  firstName: string;
  lastName: string;
  isActive: boolean;
}

// PascalCase for classes and interfaces
class UserManager {}
interface IUserRepository {}
```

### Vue Component Names

```typescript
// Always use PascalCase for component names
export default defineComponent({
  name: 'UserProfile',  // Name matches file name
  // ...
});
```

## TypeScript Standards

### Type Annotations

```typescript
// Always annotate function parameters and return types
function getUserById(id: string): Promise<User> {
  return api.get(`/users/${id}`);
}

// Type variables in generics
const users: User[] = [];
const userMap: Map<string, User> = new Map();
```

### No `any` Type

```typescript
// ❌ Avoid
let data: any = fetchData();

// ✅ Use proper types
let data: UserData = fetchData();

// If type is unknown, use unknown and type guard
let data: unknown = fetchData();
if (isUserData(data)) {
  // data is UserData here
}
```

### Interfaces vs Types

```typescript
// Use interface for object shapes and contracts
interface User {
  id: string;
  name: string;
}

// Use type for unions, tuples, and complex types
type UserId = string & { readonly __brand: unique symbol };
type ApiResponse<T> = T | Error;
type Coordinates = [number, number];
```

## Vue 3 Composition API Standards

### Component Structure

```typescript
// Follow this structure in .vue files
<script setup lang="ts">
import { ref, computed, onMounted } from 'vue';

// Types
interface Props {
  userId: string;
}

// Props
const props = defineProps<Props>();

// Emits
const emit = defineEmits<{
  update: [userId: string];
}>();

// State
const user = ref<User | null>(null);
const isLoading = ref(false);

// Computed
const displayName = computed(() => user.value?.name || 'Unknown');

// Methods
const fetchUser = async () => {
  isLoading.value = true;
  try {
    user.value = await api.getUser(props.userId);
  } finally {
    isLoading.value = false;
  }
};

// Lifecycle
onMounted(() => {
  fetchUser();
});
</script>

<template>
  <div class="user-profile">
    <h1>{{ displayName }}</h1>
  </div>
</template>

<style scoped>
.user-profile {
  /* Styles */
}
</style>
```

### Composables

```typescript
// File: src/composables/useUser.ts
import { ref, computed } from 'vue';

export function useUser(userId: string) {
  const user = ref<User | null>(null);
  const isLoading = ref(false);

  const fetchUser = async () => {
    isLoading.value = true;
    try {
      user.value = await api.getUser(userId);
    } finally {
      isLoading.value = false;
    }
  };

  const isAdmin = computed(() => user.value?.role === 'admin');

  return {
    user: readonly(user),
    isLoading: readonly(isLoading),
    fetchUser,
    isAdmin,
  };
}
```

## Import/Export Standards

```typescript
// Use ES6 modules
export const myFunction = () => {};
export type MyType = {};

// Named imports for clarity
import { myFunction, type MyType } from './utils';

// Default imports only for single main exports
import App from './App.vue';

// Organize imports: types, libs, locals
import { defineComponent } from 'vue';
import type { User, ApiResponse } from '@/types';
import { fetchUser } from '@/api/userService';
```

## Code Style

### Formatting
- **Indentation**: 2 spaces
- **Line length**: Aim for < 100 characters
- **Semicolons**: Required
- **Quotes**: Single quotes for strings, except in templates
- **Trailing commas**: Include in multi-line structures

### Comments

```typescript
// Single line comments for brief explanations
const count = 0;  // Number of active users

/**
 * Multi-line comments for functions and complex logic
 * @param userId - The user's unique identifier
 * @returns The user object or null if not found
 */
function getUser(userId: string): User | null {
  // Implementation
}

// TODO: Complex future work
// FIXME: Known issue that needs fixing
// NOTE: Important context for maintenance
```

## Tailwind CSS Standards (Dashboard)

```vue
<!-- Use utility classes consistently -->
<template>
  <!-- Spacing: margin and padding -->
  <div class="p-4 m-4"></div>

  <!-- Colors with semantic naming -->
  <button class="bg-blue-500 text-white hover:bg-blue-600"></button>

  <!-- Responsive design -->
  <div class="w-full md:w-1/2 lg:w-1/3"></div>

  <!-- Avoid inline styles -->
  <!-- ❌ style="color: red" -->
  <!-- ✅ class="text-red-500" -->
</template>

<style scoped>
/* Scoped styles for component-specific needs -->
.custom-component {
  @apply flex items-center justify-between;
}
</style>
```

## Error Handling

```typescript
// Explicit error handling
try {
  const data = await fetchData();
  return data;
} catch (error) {
  if (error instanceof ApiError) {
    console.error('API Error:', error.message);
  } else if (error instanceof TypeError) {
    console.error('Type Error:', error.message);
  } else {
    console.error('Unknown error:', error);
  }
  throw error;
}
```

## Documentation

All public APIs must have JSDoc comments:

```typescript
/**
 * Fetches user data from the API
 *
 * @param userId - The unique identifier of the user
 * @param options - Optional configuration for the request
 * @returns Promise resolving to the user object
 * @throws ApiError if the request fails
 * @example
 * ```typescript
 * const user = await fetchUser('123', { cache: true });
 * ```
 */
export async function fetchUser(
  userId: string,
  options?: FetchOptions
): Promise<User> {
  // Implementation
}
```

See [../rules/git-workflow.md](../rules/git-workflow.md) for commit message conventions.
