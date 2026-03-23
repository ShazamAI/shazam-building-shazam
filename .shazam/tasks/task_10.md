---
id: task_10
title: "Build real-time Event Feed and Metrics Dashboard"
status: completed
assigned_to: dashboard_dev_1
created_by: pm_dashboard
company: Shazam
created_at: 2026-03-21T15:14:04.058804Z
completed_at: 2026-03-21T15:22:39.130804Z
updated_at: 2026-03-21T15:22:39.130801Z
---

## Description

Build the main Dashboard home page with a live event feed and the Metrics page for cost/token tracking.

**Dashboard (home page):**
- Live scrollable event feed showing real-time events from WebSocket, similar to the TUI's event area
- Each event displayed with timestamp, event type icon/badge, agent name, and content
- Event types to render: agent_text_delta (streaming text), agent_text_complete (final output), tool_use (tool name + input summary), task_status_change (status transition), system events, circuit_breaker_tripped/reset
- Agent text streaming should show incremental text updates (delta events append, complete event finalizes)
- Auto-scroll to latest events with option to pause scrolling when user scrolls up
- Status bar at top showing: company name, running state, task counts (P:pending R:running D:done), total cost, connection status
- Quick action buttons: Start/Stop/Resume company, Approve All pending tasks

**Metrics page:**
- Per-agent token usage table: agent name, tokens used, budget, percentage, estimated cost ($)
- Total cost display across all agents
- Budget usage progress bars per agent with color coding (green < 50%, yellow 50-80%, red > 80%)
- Session information: active sessions count, session pool status
- Circuit breaker status indicator (healthy/tripped) with consecutive failure count
- System health: memory usage, poll interval, max concurrent setting

ACs:
- Event feed displays live WebSocket events with proper formatting per event type
- Streaming agent text (delta events) renders incrementally in the feed
- Status bar shows accurate real-time task counts and total cost
- Metrics page shows per-agent token usage with budget progress bars
- Circuit breaker status is visible and updates on breaker events
- Auto-scroll behavior works correctly (scrolls to bottom, pauses on user scroll up)

## Result

Build passes — 69 modules, zero errors. Here's the implementation summary:

## Implementation Complete

### Files Created
- **`src/composables/useEventFeed.ts`** — Event feed state manager with streaming text aggregation, cost estimation, and event-type formatting helpers (`eventTypeIcon`, `eventTypeLabel`)
- **`src/components/features/EventFeed.vue`** — Live scrollable event feed with auto-scroll that pauses when user scrolls up, streaming cursor indicator, per-event-type icon/badge/color, timestamp, agent name, and content display
- **`src/components/features/StatusBar.vue`** — Top status bar with company name, running state, task counts (P/R/D format), total cost, connection indicator, and quick action buttons (Start, Stop, Resume, Approve All)

### Files Modified
- **`src/pages/DashboardPage.vue`** — Complete rewrite: StatusBar + EventFeed (2/3 width) + right sidebar with task overview grid, active agents list with pulse indicators, and recent tasks
- **`src/pages/MetricsPage.vue`** — Complete rewrite: task stats row, per-agent token usage table with color-coded progress bars (green <50%, yellow 50-80%, red >80%), estimated cost per agent, circuit breaker status panel with failure bar, session pool panel, system health panel
- **`src/types/index.ts`** — Added `FeedItem`, `SessionPoolInfo`, `SystemHealth` types

### AC Verification
- **Event feed displays live WebSocket events** — `useWebSocket.on('*')` feeds every event into `useEventFeed.processEvent()`, rendered per event type with icons, badges, and formatted content
- **Streaming agent text renders incrementally** — Delta events accumulate in a buffer per agent+task key; existing feed item content is mutated in-place with a blinking cursor; `agent_text_complete` finalizes the stream
- **Status bar shows real-time task counts and cost** — StatusBar component shows P/R/D counts from reactive task list, total cost from token aggregation, connection status from WebSocket
- **Metrics page shows per-agent token usage with budget progress bars** — Sortable agent table with tokens, budget, percentage, color-coded bars, and estimated $ cost
- **Circuit breaker status visible and reactive** — Dedicated panel shows healthy/tripped badge, consecutive failures bar, last error; updates via WebSocket `circuit_breaker_tripped`/`circuit_breaker_reset` events
- **Auto-scroll works correctly** — Scroll listener detects user scroll-up, pauses auto-scroll, shows "Resume" button + gradient overlay; new events auto-scroll when at bottom
