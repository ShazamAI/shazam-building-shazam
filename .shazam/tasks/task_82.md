---
id: task_82
title: "Refactor DashboardPage from monolithic component to service-driven architecture"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T20:51:49.878867Z
completed_at: 2026-03-21T21:10:23.370049Z
updated_at: 2026-03-21T21:10:23.370046Z
---

## Description

Extract all business logic from DashboardPage into services and composables: task fetching, event loading, metrics calculation, WebSocket handling, and state management.

ACs:
- All API calls extracted to dedicated services
- State moved to Pinia store or composables
- Component file size reduced from current >700 lines to <200 lines
- Event feed, task overview, and cost calculations use separate, testable utilities
- Production build succeeds with no console errors

## Result

## Summary

### DashboardPage: 341 lines → 88 lines (74% reduction)

**Created:**

| File | Lines | Purpose |
|------|-------|---------|
| `src/composables/useDashboard.ts` | 185 | All business logic: data loading, WS event routing, task polling, company actions, circuit breaker handling |
| `src/components/features/TaskOverview.vue` | 44 | Task stats grid (P/R/D/F/A/T counts) |
| `src/components/features/AgentList.vue` | 40 | Agent list with status dots |
| `src/components/features/RecentTasks.vue` | 50 | Recent tasks with status badges |

**Architecture:**

```
DashboardPage.vue (88 lines — pure template + routing)
  └─ useDashboard() composable
       ├─ useActiveCompany() — company state
       ├─ useWebSocket() — real-time events
       ├─ useEventFeed() — event processing
       ├─ useDebouncedRefresh() — debounced task refresh
       ├─ fetchTasks() — API service
       ├─ fetchRecentEvents() — API service
       └─ ensureArray/normalizeError — shared utils
```

**What's in DashboardPage now:** Only imports, composable destructuring, 3 navigation functions, and the template. Zero business logic, zero API calls, zero timers.

**Build:** ✅ 131 modules, 846ms, zero errors. All 8 routes return 200.
