---
id: task_55
title: "Investigate and fix Workspaces page loading issue"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T20:28:32.074262Z
completed_at: 2026-03-21T20:32:14.822685Z
updated_at: 2026-03-21T20:32:14.822680Z
---

## Description

Debug why the Workspaces page fails to load on the dashboard. Check console errors, API responses, component initialization, and data binding. Implement fixes to restore functionality and ensure the page displays workspace data correctly.

ACs:
- Workspaces page loads without errors
- Console shows no error messages or exceptions
- Workspace data displays correctly if backend endpoint is implemented
- Page remains responsive during loading states

## Result

Build passes clean. Here's the summary:

## Changes Made

### 1. `src/api/configService.ts` — Fixed response unwrapping for all endpoints

**Root cause**: All service functions used `extractKey(response, 'domainKey')` which only looks for the domain-specific key (e.g., `workspaces`). The mock API returns `{ data: [...] }` instead, and `extractKey` falls back to returning the raw wrapper object — not the array inside. `ensureArray` then correctly rejects the non-array object, returning `[]`.

**Fix**: Added `extractFromResponse<T>(response, domainKey)` helper that:
1. Tries the domain-specific key first (e.g., `workspaces` for real backend)
2. Falls back to `data` key (for mock API format)
3. Returns raw response if neither key exists

Applied to: `fetchWorkspaces`, `reloadPlugins`, `fetchMemoryTree`, `fetchMemoryFile`, `fetchSessionPool`.

### 2. `src/pages/WorkspacesPage.vue` — Added loading resilience

- **Loading timeout** (10s) prevents infinite spinner when backend hangs
- **`onUnmounted` cleanup** prevents timeout from firing after navigation
- **Retry button** when API fails — distinguishes "no workspaces configured" (empty state) from "API failed" (error + retry)
- **Reset state on retry** — clears previous error before retrying

### Files modified:
- `/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/api/configService.ts`
- `/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/pages/WorkspacesPage.vue`

### Build status:
- Production build passes (827ms)
- Zero new TypeScript errors introduced
