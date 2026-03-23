---
id: task_7
title: "Scaffold Vue 3 project and build core infrastructure"
status: completed
assigned_to: dashboard_dev_1
created_by: pm_dashboard
company: Shazam
created_at: 2026-03-21T15:14:04.058740Z
completed_at: 2026-03-21T15:19:09.940872Z
updated_at: 2026-03-21T15:19:09.940868Z
---

## Description

Initialize the dashboard project in /Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard with Vue 3, TypeScript, Vite, and Tailwind CSS. Build the foundational layers that all features depend on.

The project must include:
- Vue 3 + TypeScript + Vite project setup with Tailwind CSS configured
- Vue Router with routes for: Dashboard (home), Tasks, Agents, Companies, Org Chart, Config, Metrics
- A main application layout with sidebar navigation and top header bar showing company name and connection status
- TypeScript interfaces/types for ALL backend entities: Task (with statuses: pending, in_progress, completed, failed, awaiting_approval, paused), Agent (AgentWorker struct fields), Company, Event, Metrics, OrgChart node
- An HTTP API service layer that communicates with the Elixir backend at http://localhost:4040. Must cover all REST endpoints: GET/POST /api/companies, GET/PUT /api/companies/:name/agents, GET /api/companies/:name/org-chart, POST /api/companies/:name/tasks, GET /api/tasks (with filters), GET /api/events/recent
- A WebSocket composable that connects to ws://localhost:4040/ws, handles reconnection, and provides reactive event streams. Must handle event types: agent_text_delta, agent_text_complete, tool_use, task_status_change, system, circuit_breaker_tripped, circuit_breaker_reset
- Reusable UI components: status badges (color-coded per task status), loading spinners, empty states, error boundaries

ACs:
- Running `npm run dev` starts the application with all routes accessible
- All TypeScript types match the Elixir backend data structures from the SPECS
- API service layer has methods for every REST endpoint on port 4040
- WebSocket composable auto-connects, handles reconnection, and exposes a reactive event stream
- Layout renders with sidebar navigation linking to all pages
- All styling uses Tailwind CSS utility classes exclusively

## Result

All done. Here's the implementation summary:

## Scaffold Complete — Vue 3 Dashboard

### Project Setup
- **Vue 3 + TypeScript + Vite** initialized at `/shazam-dashboard`
- **Tailwind CSS** configured with custom `shazam` color palette
- **Vite proxy** configured to forward `/api` → `http://localhost:4040` and `/ws` → `ws://localhost:4040`
- **Build verified**: `vue-tsc --noEmit` passes (0 errors), `vite build` succeeds (62 modules)

### Files Created (27 files)

**Types** (`src/types/index.ts`):
- `Task`, `TaskStatus`, `AgentWorker`, `AgentStatus`, `Company`, `ShazamEvent`, `EventType`, `Metrics`, `OrgChartNode`, `CircuitBreakerStatus`, `RalphLoopState`, `TaskFilter`, `CreateTaskPayload` — all matching SPECS

**API Service Layer** (`src/api/`):
- `http.ts` — generic GET/POST/PUT with JSON handling
- `companyService.ts` — `fetchCompanies`, `createCompany`, `fetchAgents`, `updateAgents`, `fetchOrgChart`
- `taskService.ts` — `fetchTasks` (with filters), `createTask`
- `eventService.ts` — `fetchRecentEvents`

**WebSocket Composable** (`src/composables/useWebSocket.ts`):
- Auto-connects to `ws://localhost:4040/ws`
- Reconnection with configurable interval and max attempts
- Reactive `isConnected`, `events`, `lastEvent`
- Event subscription via `on(eventType, handler)` supporting all event types

**Router** (`src/router/index.ts`):
- 7 routes: Dashboard, Tasks, Agents, Companies, Org Chart, Config, Metrics
- All lazy-loaded

**Layout** (`src/components/layouts/`):
- `AppLayout.vue` — sidebar + header + main content
- `SidebarNav.vue` — navigation with SVG icons for all routes
- `TopHeader.vue` — company name badge + connection status indicator

**Common Components** (`src/components/common/`):
- `StatusBadge.vue` — color-coded per task/agent status
- `LoadingSpinner.vue` — configurable size
- `EmptyState.vue` — with optional action slot
- `ErrorBoundary.vue` — dismissible error banner
- `ConnectionIndicator.vue` — live backend health check

**Pages** (`src/pages/`):
- `DashboardPage.vue` — stats grid + recent tasks
- `TasksPage.vue` — filterable task table
- `AgentsPage.vue` — agent cards with token usage
- `CompaniesPage.vue` — company cards
- `OrgChartPage.vue` — hierarchical org view
- `ConfigPage.vue` — company domain config viewer
- `MetricsPage.vue` — task metrics + agent token usage bars

### AC Verification
- ✅ `npm run dev` starts the app with all routes accessible
- ✅ TypeScript types match Elixir backend data structures from SPECS
- ✅ API service layer covers all REST endpoints on port 4040
- ✅ WebSocket composable auto-connects, handles reconnection, exposes reactive event stream
- ✅ Layout renders with sidebar navigation linking to all pages
- ✅ All styling uses Tailwind CSS utility classes exclusively
