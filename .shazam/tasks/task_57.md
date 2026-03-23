---
id: task_57
title: "Investigate and fix Sessions page loading issue"
status: completed
assigned_to: dashboard_dev_3
created_by: manager
company: Shazam
created_at: 2026-03-21T20:28:32.074375Z
completed_at: 2026-03-21T20:30:23.416990Z
updated_at: 2026-03-21T20:30:23.416987Z
---

## Description

Debug why the Sessions page fails to load on the dashboard. Check console errors, API responses, component initialization, and data binding. Implement fixes to restore functionality and ensure the page displays session data correctly.

ACs:
- Sessions page loads without errors
- Console shows no error messages or exceptions
- Session data displays correctly if backend endpoint is implemented
- Page remains responsive during loading states

## Result

All changes are clean. Here's the summary:

## Diagnosis & Fixes

### Root Cause
The Sessions page had the **same error-swallowing anti-pattern** found in ConfigPage:

1. `fetchSessionPool()` in `configService.ts` — `catch { return null }` silently swallowed all errors
2. When any error occurred (network, parsing, etc.), `pool.value` was set to `null`
3. The template showed EmptyState "No active sessions" — **indistinguishable from a real empty result**
4. No loading timeout meant a hung request would leave the page in an infinite spinner

### API Status
`GET /api/sessions` → **200 OK** — returns valid data with 4 sessions. The endpoint works.

### Changes Made

| File | Change |
|---|---|
| `src/api/configService.ts` | `fetchSessionPool()` now **re-throws errors** instead of `return null`, so callers get actionable error messages |
| `src/pages/SessionsPage.vue` | Added **10s loading timeout** to prevent infinite spinner |
| `src/pages/SessionsPage.vue` | `loadPool()` now **resets state** (`isLoading`, `error`) on each call for proper retry behavior |
| `src/pages/SessionsPage.vue` | Added **error-specific UI** with Retry button — distinguishes "API failed" from "no sessions yet" |
| `src/pages/SessionsPage.vue` | Added `onUnmounted` cleanup for timeout |

### Build Status
✅ Zero new TypeScript errors — only the 3 pre-existing `TasksPage.vue` type errors remain (unrelated).
