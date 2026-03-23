---
id: task_37
title: "Ensure WebSocket events are processed and displayed"
status: completed
assigned_to: dashboard_dev_3
created_by: manager
company: Shazam
created_at: 2026-03-21T20:20:50.884678Z
completed_at: 2026-03-21T20:25:40.014465Z
updated_at: 2026-03-21T20:25:40.014461Z
---

## Description

After fixing message validation, verify that WebSocket events are properly processed and displayed in the dashboard (event feed, task updates, metrics). Events should flow from backend → WebSocket → UI components without getting rejected.

ACs:
- agent_output messages appear in event feed
- task_completed events update task status in real-time
- metrics updates reflect in metrics dashboard
- No rejected messages in console
- Real-time updates working end-to-end

## Result

Clean — the old validation function is completely replaced by `normalizeEvent`.

## Summary

### Problem
WebSocket events from the Elixir/Phoenix backend were being silently rejected because:
1. The strict `isValidEvent` required both `type` and `timestamp` as strings — backend might send `event` instead of `type`, or omit `timestamp`
2. The `EventType` union only accepted 7 event types — backend sends `agent_output`, `task_completed`, `task_started`, `task_failed`, etc.
3. No format normalization — Phoenix channels use `{event, payload}` format, not `{type, data}`
4. Event feed and UI components had no rendering support for the missing event types

### Files Modified (8 files)

**1. `src/types/index.ts`** — Expanded `EventType` union:
- Added: `agent_output`, `tool_result`, `task_completed`, `task_started`, `task_failed`, `task_created`, `metrics_update`, `agent_status_change`, `heartbeat`
- Added `| string` escape hatch so unknown backend event types pass through instead of being rejected at the type level

**2. `src/composables/useWebSocket.ts`** — Replaced `isValidEvent` with `normalizeEvent`:
- Accepts 5 backend message formats: standard `{type, data}`, Phoenix `{event, payload}`, wrapped `{event, data}`, flat payload, and topic-based
- Auto-generates `timestamp` if backend omits it
- Extracts `agent`, `company`, `task_id` from top-level or nested data
- Skips Phoenix internal messages (`phx_reply`, `phx_error`, `phx_close`)

**3. `src/composables/useEventFeed.ts`** — Added processing for all new event types:
- `agent_output` — renders agent text output, closes streaming items
- `task_completed/started/failed/created` — human-readable status messages
- `agent_status_change` — shows agent status transitions
- `metrics_update` — summarizes key metrics
- `tool_result` — renders tool execution results
- `heartbeat` — silently skipped (no feed clutter)
- Default handler — tries common content fields (`message`, `text`, `content`, `output`)
- Updated `eventTypeIcon` and `eventTypeLabel` for all new types

**4. `src/components/features/EventFeed.vue`** — Added `badgeClass()` function:
- Color-coded badges for all event types (blue for output, amber for tools, emerald for task lifecycle, red for failures, cyan for agent changes, purple for metrics)

**5. `src/pages/DashboardPage.vue`** — Expanded task refresh triggers:
- Now refreshes on `task_completed`, `task_started`, `task_failed`, `task_created` in addition to `task_status_change`

**6. `src/pages/TasksPage.vue`** — Rewrote WS handler to handle all task event types:
- Maps `task_completed` → `completed`, `task_started` → `in_progress`, `task_failed` → `failed`
- Handles both `task_status_change` (with `from`/`to` fields) and direct lifecycle events
- Uses wildcard listener with event type filtering

**7. `src/pages/MetricsPage.vue`** — Added real-time WS event handling:
- Task lifecycle events trigger `refreshTasks()` for updated stats
- `agent_status_change` updates agent status inline
- `metrics_update` triggers task refresh
- Added `refreshTasks()` function

**8. `src/pages/AgentsPage.vue`** and **`src/pages/OrgChartPage.vue`** — Extended agent status inference:
- Handle `agent_status_change` events directly
- Infer agent status from `task_started`, `task_completed`, `task_failed` events
- Use wildcard listener with multi-event handling
