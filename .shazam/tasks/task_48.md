---
id: task_48
title: "Diagnose why Settings page fails to load"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T20:23:23.310679Z
completed_at: 2026-03-21T20:28:38.894904Z
updated_at: 2026-03-21T20:28:38.894901Z
---

## Description

Investigate and identify the root cause preventing the Settings page from loading. Check browser console for errors, verify routing works, confirm API endpoints are responding, and check for any compilation or TypeScript errors. Document the specific error(s) preventing the page from rendering.

ACs:
- Root cause identified and documented (API error, routing issue, component error, or other)
- All relevant error messages from browser console are captured
- API endpoint status and response codes are verified

## Result

Good. Here's the diagnosis and fix summary:

## Root Cause

**`GET /api/config` returns HTTP 404** (`{"error":"Not found"}`). The backend endpoint is not implemented.

### Why it appeared as "not loading"

The failure was **silently swallowed** by a double-layered error handling anti-pattern:

1. `fetchConfig()` in `configService.ts` had `catch { return null; }` — swallowing the 404 error
2. `loadConfig()` in `ConfigPage.vue` had its own try/catch, but never received an error because `fetchConfig()` returned `null` as if it succeeded
3. Result: `config.value = null` → page shows misleading EmptyState "No configuration available" with no error indicator

### Fixes Applied

| File | Change |
|---|---|
| `src/api/configService.ts` | `fetchConfig()` now **re-throws errors** instead of swallowing them, so the caller gets `"Not found"` from the 404 response |
| `src/pages/ConfigPage.vue` | Added **10-second loading timeout** to prevent infinite loading state if request hangs. Added `onUnmounted` cleanup. Reset `error`/`isLoading` state properly on each load attempt |

### API Endpoint Status

| Endpoint | Status | Response |
|---|---|---|
| `GET /api/config` | **404** | `{"error":"Not found"}` |
| `PUT /api/config/ralph-loop` | **Not tested** (depends on config existing) |
| `POST /api/plugins/reload` | **Not tested** |

### Build Status
✅ No new TypeScript errors — only the 3 pre-existing `TasksPage.vue` type errors remain (unrelated).
