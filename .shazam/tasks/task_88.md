---
id: task_88
title: "Standardize error handling and loading states across all refactored services"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T20:51:49.879288Z
completed_at: 2026-03-21T21:05:27.804123Z
updated_at: 2026-03-21T21:05:27.804119Z
---

## Description

Create shared error handling patterns, loading state utilities, and retry logic that all refactored pages use consistently.

ACs:
- useAsyncState composable or pattern defined for consistent loading/error handling
- Error messages standardized across all services
- Retry logic centralized and reusable
- All services use consistent response handling
- TypeScript error boundaries properly typed

## Result

**Zero TypeScript errors. Production build: 103 modules, 926ms, zero errors.**

## Summary

### Created Files

**`src/api/utils.ts`** — Centralized response utilities
- `extractKey<T>(response, key)` — Extract named key from wrapped response
- `ensureArray<T>(value)` — Safe array coercion
- `extractList<T>(response, key)` — Combined extract+ensureArray
- `normalizeError(err, fallback)` — Standardized error message extraction
- `field<T>(obj, key, default)` — Type-safe field extraction

**`src/composables/useAsyncState.ts`** — Standardized async state management
- `useAsyncState<T>(fetcher, options)` — Loading/error/timeout/retry/polling composable
- `useDebouncedRefresh(fn, delay)` — Prevents rapid re-fetches from WS event bursts
- `LOAD_TIMEOUT_MS` (8s), `REFRESH_DEBOUNCE_MS` (400ms), `POLL_INTERVAL_MS` (10s) — Shared constants

### Standardization Applied

| Before | After |
|--------|-------|
| 3 duplicate `ensureArray` functions (taskService, companyService, configService) | 1 in `api/utils.ts` |
| 2 duplicate `extractKey` / `extractFromResponse` | 1 `extractKey` in `api/utils.ts` |
| `toArray` in DashboardPage | Uses `ensureArray` from utils |
| `err instanceof Error ? err.message : 'fallback'` everywhere | `normalizeError(err, 'fallback')` |
| Manual timeout guards (8s, 10s varying) | `useAsyncState` with `LOAD_TIMEOUT_MS` constant |
| Manual debounce code per page | `useDebouncedRefresh` composable |
| Manual polling per page (10s, 15s varying) | `POLL_INTERVAL_MS` constant + `pollInterval` option |
| Hand-rolled loading/error/timeout in every page | `useAsyncState` composable |

### Pages Updated
- **SessionsPage** — Full `useAsyncState` adoption (removed 25 lines of boilerplate)
- **MemoryBrowserPage** — `useAsyncState` for tree loading + `normalizeError` for file errors
- **OrgChartPage** — `useAsyncState` + `normalizeError`
- **MetricsPage** — `useAsyncState` + `useDebouncedRefresh` + `POLL_INTERVAL_MS`
- **DashboardPage** — `useDebouncedRefresh` + `ensureArray` + `normalizeError` + `LOAD_TIMEOUT_MS` + `POLL_INTERVAL_MS`

### Services Updated
- **http.ts** — Re-exports from utils (backward compatible)
- **configService.ts** — Uses centralized `extractKey`/`ensureArray`
- **taskService.ts** — Uses centralized `extractKey`/`ensureArray`
- **companyService.ts** — Uses centralized `extractKey`/`ensureArray`
- **eventService.ts** — Uses `extractList`
