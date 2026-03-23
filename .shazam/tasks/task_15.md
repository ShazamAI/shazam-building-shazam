---
id: task_15
title: "Fix DashboardPage.vue error handling for undefined tasks"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T19:57:00.941542Z
completed_at: 2026-03-21T19:58:30.356077Z
updated_at: 2026-03-21T19:58:30.356073Z
---

## Description

DashboardPage component is throwing TypeError when trying to call .filter() on tasks which is undefined due to failed API call. Add proper null checks and error handling to prevent render crashes when API endpoints fail.

ACs:
- Component renders without errors even if tasks is undefined
- No TypeError messages in browser console related to tasks.filter
- User sees appropriate loading/error state when API returns 404

## Result

Build passes. Here's what was fixed in `DashboardPage.vue`:

**Changes made:**

1. **Added `toArray<T>()` helper** — Safely coerces API responses to arrays. Handles cases where the backend returns `{data: [...]}` wrapper objects, `undefined`, or other non-array values. Falls back to `[]`.

2. **Applied `toArray()` in all data assignment points:**
   - `onMounted` — wraps `fetchCompanies()`, `fetchTasks()`, and `fetchRecentEvents()` results
   - `refreshTasks()` and `refreshCompanies()` — same safe wrapping

3. **Added `hasError` state** — Tracks when the backend is unreachable so users see a warning banner instead of a broken page.

4. **Added error banner UI** — Yellow warning banner shown when backend APIs fail, informing users data may be unavailable.

5. **Added `ShazamEvent` type import** — For proper typing in the `toArray<ShazamEvent>()` call.

This ensures `tasks.value` is **always** a valid array, preventing the `TypeError: tasks.filter is not a function` crash regardless of what the API returns.
