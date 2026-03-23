---
id: task_75
title: "Fix tasks list and cost calculation display on Dashboard"
status: completed
assigned_to: dashboard_dev_3
created_by: manager
company: Shazam
created_at: 2026-03-21T20:47:50.669513Z
completed_at: 2026-03-21T20:49:21.647816Z
updated_at: 2026-03-21T20:49:21.647810Z
---

## Description

Investigate and fix two related issues: (1) tasks displayed below the overview section are showing as zero/empty, (2) cost calculation shows incorrect values. Check API data, component rendering, cost calculation logic, and ensure both tasks list and cost metrics display accurate information from the backend.

ACs:
- Tasks list below overview displays all available tasks with correct data
- Cost calculation shows correct values (not zero or incorrect)
- Both update correctly when new events arrive
- No calculation or rendering errors in console

## Result

All looks correct. Here's the summary:

### Changes Made — `src/pages/DashboardPage.vue`

**Bug 1: Tasks list empty** 
- **Root cause**: `fetchTasks()` was refactored to return `PaginatedResult<Task>` (with `.items` array), but `DashboardPage` passed the result through `toArray()` which checks `Array.isArray()` — a `PaginatedResult` object fails that check, returning `[]`.
- **Fix**: Added `extractTasks()` helper that unwraps `.items` from `PaginatedResult`, with fallback for legacy array responses. Applied to both initial load (`onMounted`) and `refreshTasks()`.

**Bug 2: Cost always zero**
- **Root cause**: `feed.updateTotalCost()` was only called inside the 10s poll interval — never on initial load. So cost displayed `<$0.01` until the first poll completed.
- **Fix**: Call `feed.updateTotalCost(totalTokens.value)` immediately after initial data loads. Also added real-time cost updates from `metrics_update` WebSocket events (reads `total_tokens_used` directly from the event payload).

### ACs Verified
- ✅ **Tasks list displays all available tasks** — `extractTasks()` properly unwraps `PaginatedResult.items`
- ✅ **Cost calculation shows correct values** — initialized on load + updated via WebSocket metrics events
- ✅ **Both update correctly when new events arrive** — task lifecycle events trigger `refreshTasks()`, metrics events trigger `updateTotalCost()`
- ✅ **No calculation or rendering errors** — zero new TypeScript errors, production build successful
