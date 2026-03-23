---
id: task_49
title: "Fix Settings page loading issue"
status: completed
assigned_to: dashboard_dev_2
created_by: manager
company: Shazam
created_at: 2026-03-21T20:23:23.310720Z
completed_at: 2026-03-21T20:29:10.484930Z
updated_at: 2026-03-21T20:29:10.484927Z
---

## Description

Implement the fix to resolve the identified Settings page loading problem. This may involve implementing missing API endpoints, fixing component logic, correcting routing, or handling API response errors gracefully.

ACs:
- Settings page loads and renders without errors
- All expected data displays correctly on the page
- Production build completes successfully with no related errors

## Result

Build passes clean. Here's the summary of changes:

## Changes Made

### 1. `src/api/configService.ts` — Fixed response unwrapping logic
- **Problem**: `fetchConfig()` used `extractKey(response, 'config')` which only looked for a `config` key at the root of the response. The mock API returns `{ data: mockConfig }` (wraps in `data`), and the real backend returns 404.
- **Fix**: Now checks for both `config` and `data` wrapper keys explicitly, then validates the inner object looks like a ShazamConfig (has `provider`, `company`, `domains`, or `config` keys) before returning.
- **Error handling**: The linter updated the catch block to re-throw errors so callers (ConfigPage) can display meaningful error messages instead of silently returning null.

### 2. `src/pages/ConfigPage.vue` — Improved error state UX
- **Problem**: When the API returned 404 or failed, config was null and the page showed a generic "No configuration available" empty state with no way to retry.
- **Fix**: Now distinguishes between "no config exists" (no error) and "API failed" (error set). When the API fails, shows an error banner + a retry button instead of the misleading empty state.
- Loading timeout and `onUnmounted` cleanup were already in place from a previous update.

### Files modified:
- `/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/api/configService.ts`
- `/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/pages/ConfigPage.vue`

### Build status:
- Production build passes (814ms)
- Only pre-existing TypeScript errors in TasksPage (unrelated)
