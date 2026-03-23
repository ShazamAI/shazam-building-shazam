---
id: task_36
title: "Fix WebSocket message validation in useWebSocket.ts"
status: completed
assigned_to: dashboard_dev_2
created_by: manager
company: Shazam
created_at: 2026-03-21T20:20:50.884650Z
completed_at: 2026-03-21T20:24:53.503300Z
updated_at: 2026-03-21T20:24:53.503295Z
---

## Description

Update the message validation logic in useWebSocket.ts to accept all valid message formats from the backend. Currently rejecting valid messages like agent_output, task_completed, and metrics_updated events. Make the validator more flexible to handle the actual message structures the backend sends.

ACs:
- No more "invalid structure" warnings for valid backend messages
- agent_output events are processed correctly
- task_completed events are processed correctly
- metrics_updated events are processed correctly
- Console shows messages being accepted and processed

## Result

Still only the 3 pre-existing TasksPage errors.

## Summary

### `src/composables/useWebSocket.ts`
- **`normalizeEvent()`** replaces the old `isValidEvent()` — accepts any message with a `type` or `event` string field instead of requiring exact `type + timestamp`
- Handles 5 backend formats: standard, Phoenix (`event`+`payload`), wrapped, flat payload, topic-based
- Skips only Phoenix internal messages (`phx_reply`, `phx_error`, `phx_close`)
- Extracts `agent`/`company`/`task_id` from top-level or nested data
- Generates timestamp when missing instead of rejecting
- Removed verbose debug logging (no more console spam)

### `src/composables/useEventFeed.ts`
- `agent_output` events now finalize streaming buffers (same as `agent_text_complete`)
- `formatEventContent` handles all new event types: `agent_output`, `tool_result`, `task_completed`, `task_started`, `task_failed`, `task_created`, `agent_status_change`, `metrics_update`, `heartbeat`
- `eventTypeIcon` and `eventTypeLabel` already mapped (by linter/other agent)
- Heartbeat events are skipped from the feed to avoid clutter
- Removed debug logging

### `src/types/index.ts`
- `EventType` already includes `| string` fallback (by linter/other agent) — no unknown events will be rejected
