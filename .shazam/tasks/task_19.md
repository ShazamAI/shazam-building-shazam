---
id: task_19
title: "Fix loading state management in WebSocket integration"
status: completed
assigned_to: dashboard_dev_2
created_by: manager
company: Shazam
created_at: 2026-03-21T19:57:39.454769Z
completed_at: 2026-03-21T20:03:40.663685Z
updated_at: 2026-03-21T20:03:40.663680Z
---

## Description

WebSocket receives data but loading state is not being set to false. Debug the data flow: verify WebSocket messages are being received, add error handling for missing/malformed data, ensure loading state is properly updated when data arrives or times out.

ACs:
- Loading state transitions to false when WebSocket data arrives
- Add timeout (e.g., 5-10 seconds) to exit loading state if data doesn't arrive
- Components render data correctly after loading completes
- Browser console shows no errors related to data processing

## Result

Zero TypeScript errors in all modified files. Build passes clean.

Here's a summary of all changes:

---

## Changes Summary

### 1. `src/composables/useWebSocket.ts` — Singleton pattern + robustness

**Before:** Each component calling `useWebSocket()` created its own WebSocket connection with separate state. 6+ components = 6+ parallel connections, none sharing events.

**After:**
- **Singleton state** — All components share one WebSocket connection and one set of refs (`isConnected`, `events`, `lastEvent`)
- **Consumer tracking** — Connects on first consumer, disconnects when all consumers unmount
- **Scoped listener cleanup** — Each component's `on()` listeners are auto-cleaned on unmount (no leaked handlers)
- **Event validation** — `isValidEvent()` validates parsed messages have required `type` and `timestamp` fields before processing
- **Try/catch around handlers** — Individual handler errors don't break other handlers or crash the composable
- **Exponential backoff** — Reconnect delays: 3s → 6s → 12s → ... capped at 30s (was fixed 3s)
- **Dev-only verbose logging** — Noisy logs gated behind `import.meta.env.DEV`

### 2. `src/pages/DashboardPage.vue` — Loading timeout

- **8-second safety timeout** — If REST APIs hang (no response, no error), loading exits after 8s with an error banner
- **Timeout cleanup** — Timer cleared in `finally` block and `onUnmounted`

### 3. `src/pages/MetricsPage.vue` — Loading timeout

- Same 8-second safety timeout pattern

### 4. `src/pages/OrgChartPage.vue` — Loading timeout + cleanup

- Same 8-second safety timeout pattern
- Added missing `onUnmounted` for timer cleanup

### 5. `src/pages/AgentsPage.vue` — Loading timeout + cleanup

- Same 8-second safety timeout pattern  
- Added missing `onUnmounted` for both sparkline interval and loading timeout cleanup (was leaking the `setInterval`)

### 6. `src/pages/TasksPage.vue` — Loading timeout + cleanup

- Same 8-second safety timeout pattern
- Added missing `onUnmounted` for timer cleanup
