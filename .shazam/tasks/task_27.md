---
id: task_27
title: "Implement /api/events/recent endpoint in backend"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T20:15:04.121518Z
completed_at: 2026-03-21T20:44:55.393474Z
updated_at: 2026-03-21T20:44:55.393472Z
---

## Description

The backend endpoint GET /api/events/recent is missing and returning 404. Implement this endpoint in the Elixir backend to return recent events from the event bus/system log. The endpoint should return events in JSON format compatible with the frontend's eventService.ts expectations.

ACs:
- Endpoint returns HTTP 200 with valid event data
- Response format matches what eventService.ts expects (array or wrapped object)
- Endpoint returns recent events (last N events from EventBus)
- No 404 errors when DashboardPage fetches recent events
- Response includes event metadata (timestamp, type, status, etc.)

## Result

The frontend **already handles this gracefully** — `fetchRecentEvents()` has a try/catch that returns `[]` on failure, and `DashboardPage.vue` uses `Promise.allSettled` so a failed events fetch won't break anything.

The 404 console error is cosmetic — the app works fine, it just logs the failed HTTP request.

**I cannot implement the `/api/events/recent` backend endpoint** — it requires changes to the Elixir backend which is outside my allowed paths (`src/`, `components/`, `pages/`).

### What's already in place (frontend side):
- ✅ `eventService.ts` catches errors and returns `[]` gracefully
- ✅ `DashboardPage.vue` uses `Promise.allSettled` — won't crash on 404
- ✅ Events from WebSocket still feed the EventFeed in real-time

### What needs to happen (backend side):
The Elixir backend needs a `GET /api/events/recent` endpoint that returns recent events. Based on the frontend types, the response should be either:
- A JSON array of `ShazamEvent` objects, or
- A wrapped object like `{ events: [...] }`

Each event should include: `id`, `type` (EventType), `timestamp`, `agent_id`, `task_id`, `message`, and `metadata`.

**This task must be delegated to a backend developer or the VS Code/backend team.**
