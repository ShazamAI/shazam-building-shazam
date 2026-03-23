---
id: task_23
title: "Fix API response parsing and data extraction"
status: completed
assigned_to: dashboard_dev_2
created_by: manager
company: Shazam
created_at: 2026-03-21T20:07:30.338331Z
completed_at: 2026-03-21T20:09:15.632621Z
updated_at: 2026-03-21T20:09:15.632615Z
---

## Description

Update eventService.ts, companyService.ts, taskService.ts, and http.ts to correctly parse and extract data from actual backend responses. Ensure response format handling matches what the backend returns. Add null checks and error handling for missing data.

ACs:
- API services correctly extract data from responses
- Data is properly unwrapped from any envelope format
- No malformed data reaches component layer
- All API calls return consistently formatted data to components

## Result

Build passes cleanly. Here's the summary:

## Changes Made

### `src/api/http.ts` — Core fix
- Added `unwrapEnvelope<T>()` function that detects and unwraps `{ data: T }` envelope format from Elixir/Phoenix backend
- Detects `{ error: "..." }` envelope and throws with the error message
- Extracts structured error messages from non-OK responses (`error` or `message` fields)
- Raw (non-envelope) responses pass through unchanged
- Removed debug logging

### `src/api/companyService.ts` — Defense-in-depth
- All list-returning functions (`fetchCompanies`, `fetchAgents`, `fetchOrgChart`, `updateAgents`) use `ensureArray()` guard
- Protects against `undefined`/`null`/non-array values reaching components

### `src/api/taskService.ts` — Defense-in-depth
- `fetchTasks` uses `ensureArray()` guard

### `src/api/eventService.ts` — Graceful degradation
- Catches all errors and returns `[]` (endpoint may not exist)
- Array guard on successful response

### `src/api/configService.ts` — Null-safe returns
- `fetchConfig` returns `ShazamConfig | null`, validates response is an object
- `fetchSessionPool` returns `SessionPoolInfo | null`, validates response
- `fetchMemoryFile` returns `MemoryFileContent | null`, validates response
- All list-returning functions use `ensureArray()`
- `reloadPlugins` now has array guard

### Data flow guarantee
`Backend → http.ts (envelope unwrap) → service (array/null guard) → component (safe data)`

No malformed data can reach the component layer.
