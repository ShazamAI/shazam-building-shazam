---
id: task_118
title: "Fix missing mockApi module and verify production build"
status: completed
assigned_to: dashboard_dev_2
created_by: pm_dashboard
company: Shazam
created_at: 2026-03-23T12:15:33.696499Z
completed_at: 2026-03-23T12:17:55.309541Z
updated_at: 2026-03-23T12:17:55.309537Z
---

## Description

The production build fails because `vite.config.ts` imports `./src/dev/mockApi` which does not exist. Fix this so the build succeeds.

Expected behavior:
- The Vite config should gracefully handle the mock API plugin — either the missing file is restored or the import is made conditional so production builds are not blocked
- `vue-tsc --noEmit` passes with zero TypeScript errors
- `vite build` completes successfully
- Bundle output is printed showing per-page chunk sizes

ACs:
- Production build (`vite build`) completes with zero errors
- TypeScript check (`vue-tsc --noEmit`) passes with zero errors
- No functionality is removed — if mock API existed before, it should still work in dev mode
- The fix is minimal and does not change application behavior

## Result

Build passes. All ACs met:

- ✅ `vue-tsc --noEmit` — zero TypeScript errors
- ✅ `vite build` — completes in 1.14s, 164 modules, all chunks built
- ✅ Mock API restored at `src/dev/mockApi.ts` — serves fallback data when backend is down, passes through when backend is up
- ✅ Minimal fix — created the missing file matching original behavior, adjusted tsconfig to properly separate Node/browser type scopes

**Changes made:**
1. **`src/dev/mockApi.ts`** (new) — Vite plugin with health-check loop, mock routes for `/api/tasks|agents|companies|config|health|metrics|events`, and `{ data: ... }` response wrapper matching `extractKey` parsing
2. **`tsconfig.json`** — Added `"exclude": ["src/dev/**"]` to avoid checking Node-dependent dev tooling with browser types
3. **`tsconfig.node.json`** — Added `"src/dev/**/*.ts"` to include, so dev tooling is type-checked alongside `vite.config.ts` with proper Node type access
