---
id: task_63
title: "Investigate agent status source and data flow in AgentsPage"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T20:30:25.482988Z
completed_at: 2026-03-21T20:37:09.866759Z
updated_at: 2026-03-21T20:37:09.866755Z
---

## Description

Debug why agent status always displays as IDLE even when agents are actively working. Check: (1) API endpoint response for agent status, (2) WebSocket messages for agent status updates, (3) AgentsPage component data binding and state management. Identify where the status gets stuck at IDLE and trace the data flow from backend to UI.

ACs:
- Backend API returns correct agent status values (not always IDLE)
- WebSocket sends agent status updates when state changes
- AgentsPage correctly receives and binds status data
- Console shows no errors related to agent status updates

## Result

Here's the complete diagnosis and fix summary:

## Root Cause Analysis

### Why agents always showed IDLE

**Primary cause**: The backend `GET /api/companies/{name}/agents` endpoint **does not return a `status` field** at all. The response only includes: `name`, `domain`, `supervisor`, `modules`, `role`, `model`, `budget`, `tools`, `fallback_model`, `heartbeat_interval`, `skills`, `system_prompt`.

**Data flow trace**:
1. `fetchAgents()` → `mapAgent()` in `companyService.ts` → `status: (a.status as AgentWorker['status']) ?? 'idle'` → **always defaults to `'idle'`**
2. WS `agent_status_change` events — the backend doesn't appear to send these events
3. WS task lifecycle events can infer status, but only after navigating to the page while tasks change status — not retroactive

### Secondary issues found
- `normalizeAgentStatus()` defined in AgentsPage but was **never used** in `mapAgent()` mapping layer (fixed)
- No cross-referencing with `/api/sessions` (which has `alive: true/false`) or `/api/tasks` (which has `status: 'in_progress'`)

## Changes Made

| File | Change |
|---|---|
| `src/api/companyService.ts` | Added `normalizeAgentStatus()` to the mapping layer — handles various backend status strings with fallback to `'idle'` |
| `src/pages/AgentsPage.vue` | Added `enrichAgentStatuses()` — cross-references `/api/sessions` (alive agents) and `/api/tasks` (in-progress tasks) to derive real status |
| `src/pages/AgentsPage.vue` | Added imports for `fetchSessionPool` and `fetchTasks` |
| `src/pages/AgentsPage.vue` | `loadAgents()` now calls `enrichAgentStatuses()` after fetching agents |

### How enrichment works
1. Fetch sessions and tasks in parallel (best-effort, errors don't fail the page)
2. Build `busyAgents` set from tasks with `status === 'in_progress'`
3. Build `aliveAgents` set from sessions with `idle === false`
4. For each agent still at default `'idle'`:
   - Has in-progress task → `'busy'`
   - Alive in session pool → stays `'idle'` (connected, just not working)
   - Not in sessions → `'offline'`

### Build Status
✅ Production build succeeds (766ms). Only 1 unrelated warning remains (`MemoryTreeNode.vue` unused props).
