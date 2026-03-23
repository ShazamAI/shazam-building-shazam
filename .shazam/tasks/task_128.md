---
id: task_128
title: "Create Contributing Guidelines (CONTRIBUTING.md)"
status: completed
assigned_to: pm_dashboard
created_by: manager
company: Shazam
created_at: 2026-03-23T12:22:46.865837Z
completed_at: 2026-03-23T12:23:15.044543Z
updated_at: 2026-03-23T12:23:15.044538Z
---

## Description

Create CONTRIBUTING.md with guidelines for developers contributing to the dashboard including code style, component patterns, naming conventions, TypeScript practices, and commit message standards.

ACs:
- Code style guidelines aligned with project conventions
- Component creation patterns documented
- Composable/service creation patterns documented
- TypeScript strict mode requirements
- Naming conventions for files, variables, components
- Commit message format and examples
- Pull request process overview
- File located at shazam-dashboard/CONTRIBUTING.md

## Result



This file is at `shazam-dashboard/CONTRIBUTING.md` — outside our allowed paths (`src/`, `components/`, `pages/`). Escalating to manager for placement approval or execution.

```subtasks
[
  {
    "title": "Create CONTRIBUTING.md for the dashboard project",
    "description": "Create a `CONTRIBUTING.md` file at the dashboard project root (`/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/CONTRIBUTING.md`). This file is at the project root, outside the dashboard team's allowed paths (src/, components/, pages/), so this task requires manager-level execution.\n\nThe document should cover the following sections based on the project's established conventions:\n\n## Code Style\n- Vue 3 Composition API exclusively (no Options API)\n- TypeScript strict mode enabled — all files must have explicit type annotations\n- Tailwind CSS utility classes only — no inline styles, no scoped CSS unless for keyframe animations\n- Inter font for body text, JetBrains Mono for code\n\n## Component Patterns\n- Components use `<script setup lang=\"ts\">` syntax\n- Props defined with `defineProps<T>()` using TypeScript interface\n- Emits defined with `defineEmits<T>()`\n- Template → Script → Style ordering in SFCs\n- Common components go in `src/components/common/`\n- Feature components go in `src/components/features/`\n- Layout components go in `src/components/layouts/`\n- Page components go in `src/pages/`\n\n## Composable Patterns\n- Prefix with `use` (e.g., `useAgents.ts`)\n- Located in `src/composables/`\n- Return reactive refs and computed properties\n- Clean up side effects in `onUnmounted`\n- Respect `prefers-reduced-motion` for animation composables\n\n## Service Layer\n- API calls isolated in `src/api/`\n- Use the HTTP client from `src/api/http.ts`\n- Use `extractKey` for response parsing\n- Type all request/response shapes in `src/types/`\n\n## State Management\n- Pinia stores in `src/stores/`\n- Follow layered architecture (see `src/stores/ARCHITECTURE.md`)\n- Composables wrap store access for component use\n\n## Naming Conventions\n- Files: PascalCase for Vue components (`AgentCard.vue`), camelCase for TS files (`useAgents.ts`)\n- Components: PascalCase in templates (`<AgentCard />`)\n- Variables/functions: camelCase\n- Types/interfaces: PascalCase\n- Constants: UPPER_SNAKE_CASE\n- CSS classes: Tailwind utilities only\n\n## TypeScript Requirements\n- Strict mode — no `any` types without justification\n- Explicit return types on exported functions\n- Use `interface` for object shapes, `type` for unions/intersections\n- All props and emits must be typed\n\n## Commit Messages\n- Format: `type: short description`\n- Types: feat, fix, refactor, chore, docs, style, perf\n- Examples: `feat: add agent status polling`, `fix: prevent task overview zero flash`, `refactor: extract task filters to computed properties`\n- Keep subject line under 72 characters\n- Use imperative mood (\"add\" not \"added\")\n\n## Pull Request Process\n- Branch naming: `feature/description`, `fix/description`, `refactor/description`, `chore/description`\n- PR title matches commit convention\n- Description includes summary bullets and test plan\n- TypeScript check must pass (`vue-tsc --noEmit`)\n- Production build must succeed (`vite build`)\n- No increase in bundle size beyond 10% without justification\n\n## Testing Policy\n- Developers do NOT write tests — QA handles all testing\n- Ensure code is testable with good separation of concerns\n- Composables should be independently testable\n\n## Development Setup\n- `npm install` to install dependencies\n- `npm run dev` to start Vite dev server\n- `vue-tsc --noEmit` to check types\n- `vite build` to verify production build\n\nACs:\n- File created at `/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/CONTRIBUTING.md`\n- All sections above are covered\n- Guidelines align with existing project conventions\n- Examples provided for commit messages and branch names\n- TypeScript strict mode requirements clearly stated\n- Component file placement rules documented",
    "assigned_to": "manager",
    "depends_on": null
  }
]
```
