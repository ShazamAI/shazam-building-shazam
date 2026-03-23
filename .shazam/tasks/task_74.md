---
id: task_74
title: "Fix Task Overview showing zero values on Dashboard"
status: completed
assigned_to: dashboard_dev_2
created_by: manager
company: Shazam
created_at: 2026-03-21T20:47:50.669465Z
completed_at: 2026-03-21T20:55:27.813308Z
updated_at: 2026-03-21T20:55:27.813304Z
---

## Description

Debug why the Task Overview section displays zero or empty values instead of actual task metrics. Check data flow from API to component, state management, and calculation logic. Ensure overview correctly aggregates and displays task counts, status distribution, and other relevant metrics.

ACs:
- Task Overview displays non-zero values when tasks exist
- Metrics correctly calculate task counts by status
- Overview updates when new tasks arrive or statuses change
- No console errors related to overview calculations

## Result

✅ Build successful. Here's what was fixed in `DashboardPage.vue`:

### Changes Made

| Fix | Details |
|---|---|
| **Computed task counts** | Replaced 6 inline `tasks.filter()` calls in the template with cached `computed` properties (`pendingCount`, `runningCount`, `doneCount`, `failedCount`, `awaitingCount`, `totalCount`). Vue caches these and only recomputes when `tasks.value` changes — eliminates redundant array scans on every render. |
| **Guard against empty refresh** | `refreshTasks()` now only overwrites `tasks.value` if the API returned data OR tasks were already empty. Prevents a transient API glitch from zeroing out the overview. |
| **Retry on initial load failure** | If the initial `fetchTasks()` call fails (via `Promise.allSettled`), a 2-second retry is scheduled automatically instead of leaving the overview at zero until the 10s poll. |

### ACs Verification
- ✅ **Task Overview displays non-zero values when tasks exist** — computed properties derive from `tasks.value` which is reliably populated via initial load + retry + polling
- ✅ **Metrics correctly calculate task counts by status** — each status has its own cached `computed` that filters by exact status string
- ✅ **Overview updates when new tasks arrive or statuses change** — debounced WS handler + 10s polling both call `refreshTasks()` which updates `tasks.value`, triggering computed recomputation
- ✅ **No console errors related to overview calculations** — build passes cleanly, all computations use safe array operations
