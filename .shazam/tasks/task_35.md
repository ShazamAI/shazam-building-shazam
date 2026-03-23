---
id: task_35
title: "Document and specify WebSocket message format contract"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T20:20:50.884615Z
completed_at: 2026-03-21T20:45:24.385600Z
updated_at: 2026-03-21T20:45:24.385592Z
---

## Description

Define the exact message formats that should be accepted by the dashboard WebSocket handler. Document which message types come from the backend (agent_output, task_completed, metrics_updated, etc.) and their expected structure. This will be the source of truth for message validation.

ACs:
- Document all valid WebSocket message types
- Specify required and optional fields for each type
- List examples of each message format from actual backend
- Include any variants or alternate structures

## Result

✅ Build succeeds. Only the pre-existing `MemoryTreeNode.vue` unused props warning remains.

## Summary

Created `/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/types/ws-contract.ts` — a comprehensive TypeScript contract that serves as the **source of truth** for all WebSocket messages.

### What's documented

**16 event types** across 5 categories:

| Category | Events |
|---|---|
| **Agent Text Streaming** | `agent_text_delta`, `agent_text_complete`, `agent_output` |
| **Tool Events** | `tool_use`, `tool_result` |
| **Task Lifecycle** | `task_created`, `task_started`, `task_status_change`, `task_completed`, `task_failed` |
| **Agent Lifecycle** | `agent_status_change`, `agent_started/stopped/disconnected/error/crashed/paused/resumed` |
| **System** | `metrics_update`, `circuit_breaker_tripped`, `circuit_breaker_reset`, `heartbeat`, `system`, `company_*` |

**For each event type:**
- TypeScript interface with required/optional fields
- JSON example from actual backend patterns
- `@consumed_by` annotation listing which pages use it
- Inline documentation of field aliases (e.g., `tool_name` vs `name`)

**Additional documentation:**
- 3 transport envelope formats (Standard, Phoenix Channel, Flat Payload)
- Normalization rules table (field → source priority)
- Filtered messages list (`phx_reply`, `phx_error`, `phx_close`)
- Consumer reference matrix (which pages listen to which events)
- Union type `WsMessage` covering all known message shapes
