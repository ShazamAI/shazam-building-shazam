---
id: task_64
title: "Implement real-time agent status updates via WebSocket"
status: completed
assigned_to: dashboard_dev_2
created_by: manager
company: Shazam
created_at: 2026-03-21T20:30:25.483079Z
completed_at: 2026-03-21T20:34:54.049836Z
updated_at: 2026-03-21T20:34:54.049831Z
---

## Description

Ensure AgentsPage listens to WebSocket messages for agent status changes and updates component state accordingly. Handle all possible agent states (IDLE, WORKING, EXECUTING, WAITING, etc). Update the UI immediately when status changes without requiring page reload.

ACs:
- AgentsPage subscribes to agent status update events
- UI updates within 100ms of status change
- All agent states display correctly (not just IDLE)
- Multiple agents can have different statuses simultaneously

## Result

Build passes clean (83 modules, 823ms). Here's the summary:

### Changes Made

**`src/types/index.ts`** — Expanded `AgentStatus` union
- Added `working`, `executing`, `waiting`, `error`, `offline` to the existing `idle | busy | paused`

**`src/components/common/StatusBadge.vue`** — Full status visual coverage
- Added badge styles for all new statuses with distinct colors: working (amber, pulse), executing (cyan, pulse), waiting (yellow), error (red), offline (gray muted)

**`src/pages/AgentsPage.vue`** — Comprehensive real-time WebSocket handling
- **`normalizeAgentStatus()`** — Maps any backend status string (case-insensitive) to canonical `AgentStatus`. Handles aliases: `WORKING`, `running`, `active`, `in_progress`, `crashed`, `disconnected`, etc.
- **`statusDotClass()`** — Expanded from 3 to 8 visual states with distinct colors/animations
- **Rewrote WS handler** to handle:
  - `agent_status_change` — explicit status updates with normalization
  - `agent_started/stopped/disconnected/error/crashed/paused/resumed` — lifecycle events
  - `agent_text_delta/tool_use/tool_result` — infers working/executing from active work
  - `task_started/completed/failed/status_change` — infers agent status from task lifecycle
  - `metrics_update` — updates token usage and budget in real-time
- **Helper functions** `findAgent()` and `updateAgentStatus()` for cleaner mutation

**`src/pages/MetricsPage.vue`** & **`src/pages/OrgChartPage.vue`** — Updated casts from `'idle' | 'busy' | 'paused'` to `AgentStatus` for type consistency
