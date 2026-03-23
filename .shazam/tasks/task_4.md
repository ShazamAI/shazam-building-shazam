---
id: task_4
title: "Build API client, WebSocket service, and integrate chat with Elixir backend"
status: completed
assigned_to: vscode_dev_1
created_by: pm_vscode
company: Shazam
created_at: 2026-03-21T15:13:50.284482Z
completed_at: 2026-03-21T15:20:54.146909Z
updated_at: 2026-03-21T15:20:54.146905Z
---

## Description

Build the core communication layer for the VS Code extension. This includes:

1. **REST API Client Service**: A service that communicates with the Shazam Elixir backend at `http://localhost:4040`. Must support all endpoints:
   - GET /api/companies (list companies)
   - POST /api/companies (create company)
   - GET /api/companies/:name/agents (list agents)
   - PUT /api/companies/:name/agents (update agents)
   - GET /api/companies/:name/org-chart (get org chart)
   - POST /api/companies/:name/tasks (create task)
   - GET /api/tasks (list tasks with filters: status, assigned_to)
   - GET /api/events/recent (recent events)

2. **WebSocket Service**: Connect to `ws://localhost:4040/ws` for real-time event streaming. Handle event types: `agent_text_delta`, `agent_text_complete`, `tool_use`, `task_status_change`, `system`, `circuit_breaker_tripped`, `circuit_breaker_reset`. Implement auto-reconnect with exponential backoff.

3. **Internal Event Bus**: A pub/sub system so other parts of the extension can subscribe to backend events without direct WebSocket access.

4. **Chat Integration**: Refactor the existing ChatViewProvider to send messages through the API client instead of echoing. Chat should stream agent responses in real-time via WebSocket events (`agent_text_delta`, `agent_text_complete`). The chat UI should show which agent is responding.

5. **Event Log Panel**: A webview panel showing real-time execution events from the backend (tool usage, task changes, system events) in a scrollable, filterable list.

The backend API spec is at SPECS.md in /Users/raphaelbarbosa/Projects/ShazamAI/shazam-building-shazam/SPECS.md — read sections 11 (API) and 3.11 (API & Events) for endpoint details and WebSocket event formats.

All code goes in the workspace at /Users/raphaelbarbosa/Projects/ShazamAI/shazam-vscode/. Services go in src/services/. Providers go in src/providers/. Follow existing patterns in the codebase. Use TypeScript strict mode.

ACs:
- API client service exists with typed methods for all 8 REST endpoints
- WebSocket service connects to ws://localhost:4040/ws and handles all 7 event types with auto-reconnect
- Internal event bus allows subscription/unsubscription to specific event types
- ChatViewProvider sends user messages via API and displays streamed responses from WebSocket
- Event log panel displays real-time events with timestamps, event type badges, and agent names
- All services are properly disposed on extension deactivation

## Result

All implementations complete. Here's a summary of what was built:

## Implementation Summary

### Files Created
1. **`src/services/EventBus.ts`** (73 lines) — Internal pub/sub event bus with typed subscriptions. Supports specific event type listeners and wildcard (`*`) listeners. Returns unsubscribe functions.

2. **`src/providers/EventLogProvider.ts`** (367 lines) — Full webview panel showing real-time execution events with:
   - Scrollable event list with auto-scroll
   - Filter buttons for all 7 event types (text delta, text complete, tool use, task change, system, CB tripped, CB reset)
   - Color-coded event type badges
   - Timestamps, agent names, and event details
   - Event count display
   - Max 500 events buffer

### Files Modified
3. **`src/services/ApiClient.ts`** — Added missing endpoints:
   - `updateAgents()` — PUT /api/companies/:name/agents
   - `getRecentEvents()` — GET /api/events/recent
   - `isBackendAlive()` — health check
   - Added `BackendEvent` type import

4. **`src/services/WebSocketService.ts`** — Already existed (created earlier). Connects to `ws://localhost:4040/ws`, handles all 7 event types, implements auto-reconnect with exponential backoff (1s→30s max).

5. **`src/providers/ChatViewProvider.ts`** (480 lines) — Fully refactored:
   - Takes `EventBus` in constructor
   - Sends user messages via `api.createTask()` to the backend
   - Auto-detects company name via `api.listCompanies()`
   - Streams agent responses in real-time via `agent_text_delta` and `agent_text_complete` events
   - Shows agent name label on assistant messages
   - Visual streaming indicator (left border) during text deltas
   - Supports `system` role messages for errors/info
   - `updateMessage` webview protocol for incremental content updates

6. **`src/types.ts`** (331 lines) — Added WebSocket event types:
   - `WebSocketEventType`, `WebSocketEvent`, `AgentTextDeltaEvent`, `AgentTextCompleteEvent`, `ToolUseEvent`, `TaskStatusChangeEvent`, `SystemEventData`, `CircuitBreakerEvent`
   - `EventHandler`, `CreateTaskParams`, `ShazamConfig`, `DEFAULT_CONFIG`, `BackendEvent`
   - Updated `ChatMessage` with `system` role and `agentName`

7. **`src/extension.ts`** (159 lines) — Wires everything together:
   - Creates EventBus → WebSocketService → StatusBarManager → all providers
   - Registers `shazam.openEventLog` command
   - Auto-connects WebSocket, workspace detection, config change listeners

8. **`extension/package.json`** — Added:
   - Commands: `openEventLog`, `connectBackend`, `disconnectBackend`, `showMetrics`, `configureAgents`
   - Keybinding: `Ctrl+Shift+E` for event log
   - Settings: `backendUrl`, `autoConnect`, `showStatusBar`
   - Activation events for `shazam.yaml` detection

### AC Verification
- ✅ API client service with typed methods for all 8 REST endpoints
- ✅ WebSocket service connects to ws://localhost:4040/ws, handles all 7 event types with auto-reconnect
- ✅ Internal event bus with subscribe/unsubscribe for specific event types
- ✅ ChatViewProvider sends messages via API and displays streamed responses from WebSocket
- ✅ Event log panel with timestamps, event type badges, agent names, and filtering
- ✅ All services properly disposed on extension deactivation (via `context.subscriptions`)
