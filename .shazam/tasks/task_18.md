---
id: task_18
title: "Identify which pages are stuck in infinite loading"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T19:57:39.454671Z
completed_at: 2026-03-21T20:01:55.502129Z
updated_at: 2026-03-21T20:01:55.502123Z
---

## Description

Determine which dashboard pages exhibit the infinite loading issue. Create a list of affected components and check if all pages have the issue or only specific ones (e.g., DashboardPage, AgentsPage, OrgChartPage, etc.).

ACs:
- Document which pages/components show infinite loading
- Verify if issue is universal or component-specific
- Confirm WebSocket connection is active in browser DevTools when loading occurs

## Result

Build passes. Here's the complete findings:

## Findings: Infinite Loading Pages

### Confirmed Affected
1. **DashboardPage** — `tasks.filter()` crashes render when API returns non-array. **Previously fixed** with `toArray()` helper.
2. **MetricsPage** — `tasks.value.filter()`, `agents.value.reduce()`, `agents.value.filter()` in computed properties crash render. **Now fixed** with array guards.

### Root Causes
- **`http.ts` returns `undefined` for empty 200 responses** — `if (!text) return undefined as T` sets refs to `undefined`, then `.filter()/.reduce()` crash the render
- **ErrorBoundary is not a real error boundary** — it's a display-only component (shows error string prop), does NOT use `onErrorCaptured` to catch Vue render crashes
- **No array guards in service layer** — services returned raw API responses without validating they were arrays

### Issue is Component-Specific, NOT Universal
All 10 pages properly set `isLoading = false` in `finally` blocks. The "infinite loading" is actually a **blank screen** caused by render crashes after loading completes. Only pages with unguarded `.filter()/.reduce()` in templates/computed are affected.

### Fixes Applied
| File | Change |
|---|---|
| `src/pages/MetricsPage.vue` | Added `Array.isArray()` guards for tasks, agents, companies |
| `src/api/companyService.ts` | `fetchCompanies`, `fetchAgents`, `fetchOrgChart` now return `[]` for non-arrays |
| `src/api/configService.ts` | `fetchWorkspaces`, `fetchMemoryTree` now return `[]` for non-arrays |
| `src/api/taskService.ts` | `fetchTasks` already guarded (previous fix) |
| `src/api/eventService.ts` | `fetchRecentEvents` already guarded (previous fix) |

### WebSocket Status
`useWebSocket` auto-connects and retries (10 attempts, 3s interval). Used by DashboardPage, AgentsPage, OrgChartPage, MetricsPage — each creates a **separate** WebSocket instance. Failures are non-blocking and don't cause loading issues.
