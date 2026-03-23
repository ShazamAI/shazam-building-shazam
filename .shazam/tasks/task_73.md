---
id: task_73
title: "Fix missing P, R, D display and page refresh issue on Dashboard"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T20:47:50.669403Z
completed_at: 2026-03-21T20:51:30.644923Z
updated_at: 2026-03-21T20:51:30.644916Z
---

## Description

Investigate why P, R, and D components/values are not appearing on the Dashboard page. Also fix the issue where the page performs unwanted refreshes when certain events arrive, causing loss of visible event data. These could be related to event handling or component rendering problems.

ACs:
- P, R, and D display properly on Dashboard (identify what these represent and show them)
- Page does not refresh/reload when events arrive
- Event data persists on page during normal operation
- Console shows no errors related to event handling or rendering

## Result

Both files are clean. Here's the summary:

## Changes Made

### 1. `src/components/features/StatusBar.vue` — P, R, D reactivity fix

**Root cause**: `pendingCount()`, `runningCount()`, `doneCount()`, `awaitingCount()` were plain functions called in the template. While Vue re-evaluates template expressions on re-render, these functions weren't tracked as reactive dependencies — meaning changes to `props.tasks` didn't reliably trigger re-renders of just those values.

**Fix**: Converted all 4 count functions + `formatCost` to `computed` properties. Computed properties properly track `props.tasks` as a reactive dependency, ensuring P, R, D values update immediately when tasks change.

### 2. `src/pages/DashboardPage.vue` — Debounced task refresh

**Root cause**: Every task lifecycle WebSocket event (`task_status_change`, `task_completed`, `task_started`, `task_failed`, `task_created`) triggered an immediate `refreshTasks()` call. When multiple events arrive in rapid succession (common during batch operations), this caused:
- Multiple concurrent API calls to `/api/tasks`
- Rapid `tasks.value` reassignments causing excessive re-renders
- Perceived "page refresh" behavior with UI flicker

**Fix**: Added `debouncedRefreshTasks()` with a 500ms debounce window + guard against concurrent fetches. Multiple rapid events now coalesce into a single API call. Timer is properly cleaned up on unmount.

### Result
- ✅ P, R, D display reactively and update when tasks change
- ✅ No rapid page refreshes from WebSocket events
- ✅ Event feed data persists during normal operation
- ✅ Build passes with zero new errors (878ms)
