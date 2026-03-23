---
id: task_2
title: "Build Complete Vue 3 Dashboard for Shazam"
status: completed
assigned_to: pm_dashboard
created_by: manager
company: Shazam
created_at: 2026-03-21T15:11:35.207868Z
completed_at: 2026-03-21T15:14:04.058505Z
updated_at: 2026-03-21T15:14:04.058501Z
---

## Description

Create a full-featured web dashboard that mirrors all Shazam CLI functionality. This includes company/org management, real-time task monitoring, agent configuration, org chart visualization, event streaming, workspace management, and metrics tracking. The dashboard should connect to the Elixir backend via WebSocket (for real-time events) and HTTP API (for CRUD operations on port 4040).

ACs:
- Dashboard displays companies, agents, tasks with all statuses (pending, in_progress, completed, failed, awaiting_approval, paused)
- Real-time event streaming via WebSocket shows live task execution updates, agent activity, cost tracking
- Org chart visualization shows agent hierarchy with supervisor-subordinate relationships
- Full CRUD for tasks: create, edit, approve, reject, pause, resume, retry, delete
- Agent management UI for creating agents, configuring budgets, tools, domains, custom prompts
- Workspace/multi-repo navigation and management
- Configuration display and editing interface
- Metrics/cost tracking dashboard showing token usage and expenses per agent
- All views fully styled with Tailwind CSS, responsive design
- All API endpoints from Elixir backend properly integrated (companies, tasks, agents, org-chart, events)

## Result

Decomposing full Vue 3 dashboard build into 6 tasks across 3 developers, with scaffolding first then parallel feature development.

```subtasks
[
  {
    "title": "Scaffold Vue 3 project and build core infrastructure",
    "description": "Initialize the dashboard project in /Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard with Vue 3, TypeScript, Vite, and Tailwind CSS. Build the foundational layers that all features depend on.\n\nThe project must include:\n- Vue 3 + TypeScript + Vite project setup with Tailwind CSS configured\n- Vue Router with routes for: Dashboard (home), Tasks, Agents, Companies, Org Chart, Config, Metrics\n- A main application layout with sidebar navigation and top header bar showing company name and connection status\n- TypeScript interfaces/types for ALL backend entities: Task (with statuses: pending, in_progress, completed, failed, awaiting_approval, paused), Agent (AgentWorker struct fields), Company, Event, Metrics, OrgChart node\n- An HTTP API service layer that communicates with the Elixir backend at http://localhost:4040. Must cover all REST endpoints: GET/POST /api/companies, GET/PUT /api/companies/:name/agents, GET /api/companies/:name/org-chart, POST /api/companies/:name/tasks, GET /api/tasks (with filters), GET /api/events/recent\n- A WebSocket composable that connects to ws://localhost:4040/ws, handles reconnection, and provides reactive event streams. Must handle event types: agent_text_delta, agent_text_complete, tool_use, task_status_change, system, circuit_breaker_tripped, circuit_breaker_reset\n- Reusable UI components: status badges (color-coded per task status), loading spinners, empty states, error boundaries\n\nACs:\n- Running `npm run dev` starts the application with all routes accessible\n- All TypeScript types match the Elixir backend data structures from the SPECS\n- API service layer has methods for every REST endpoint on port 4040\n- WebSocket composable auto-connects, handles reconnection, and exposes a reactive event stream\n- Layout renders with sidebar navigation linking to all pages\n- All styling uses Tailwind CSS utility classes exclusively",
    "assigned_to": "dashboard_dev_1",
    "depends_on": null
  },
  {
    "title": "Build Task Management feature with full CRUD and approval flow",
    "description": "Build the complete Task Management page that mirrors the CLI's task capabilities. Users must be able to view, create, and manage tasks with all status transitions.\n\nBehavior requirements:\n- Task list view showing all tasks with columns: ID, title, status (color-coded badge), assigned agent, created by, timestamps. Support filtering by status and assigned agent\n- Task creation form with fields: title, description, assigned_to (agent dropdown), depends_on (optional task reference)\n- Task detail view showing full task information including description, result (rendered as markdown), parent task, dependencies\n- Action buttons on each task based on current status: Approve/Reject (for awaiting_approval), Pause (for pending/in_progress), Resume (for paused), Retry (for failed), Delete\n- Bulk approval action (approve all awaiting tasks)\n- Task status counts summary bar showing: Pending, Running, Completed, Failed, Awaiting Approval, Paused counts\n- Real-time task status updates via WebSocket — when a task_status_change event arrives, the task list updates automatically without page refresh\n- Search/filter functionality to find tasks by title\n\nACs:\n- Task list displays all tasks from GET /api/tasks with correct status badges\n- Users can create a new task via POST /api/companies/:name/tasks\n- Each task shows contextual action buttons matching its current status\n- Approve, reject, pause, resume, retry, and delete actions call the correct API endpoints and update the UI\n- Task detail view renders the result field as formatted markdown\n- WebSocket task_status_change events trigger automatic UI updates",
    "assigned_to": "dashboard_dev_2",
    "depends_on": "Scaffold Vue 3 project and build core infrastructure"
  },
  {
    "title": "Build Agent and Company Management with Org Chart visualization",
    "description": "Build the Company management, Agent management, and Org Chart visualization pages.\n\nBehavior requirements:\n\n**Companies page:**\n- List all running companies from GET /api/companies showing name, mission, agent count, workspace path\n- Company creation form matching POST /api/companies\n- Company detail view showing its agents, tasks, and configuration\n- Active company selector in the app header that persists selection across pages\n\n**Agents page:**\n- Agent list for the selected company from GET /api/companies/:name/agents\n- Each agent card shows: name, role, status (idle/busy/paused), model, provider, budget, tokens used, budget usage percentage bar, domain, supervisor\n- Agent creation/editing form with fields: name, role (dropdown with presets: pm, senior_dev, junior_dev, qa, designer, researcher, devops, writer), model, provider, budget, tools (multi-select), domain, modules, custom system prompt\n- Real-time agent status via WebSocket events — busy/idle indicators update live\n- Agent sparkline activity visualization (character-based activity indicator per agent)\n\n**Org Chart page:**\n- Tree visualization of agent hierarchy from GET /api/companies/:name/org-chart\n- Each node shows agent name, role, and current status with color coding\n- Supervisor-to-subordinate relationships clearly visualized with connecting lines\n- Interactive: clicking a node navigates to that agent's detail view\n\nACs:\n- Companies page lists all companies and allows creating new ones\n- Agent cards display all relevant agent fields with a budget usage progress bar\n- Agent creation form includes all configurable fields from the AgentWorker struct\n- Org chart renders a tree hierarchy with visual connections between supervisor and subordinates\n- Clicking an org chart node navigates to the agent detail\n- WebSocket events update agent status indicators in real-time",
    "assigned_to": "dashboard_dev_3",
    "depends_on": "Scaffold Vue 3 project and build core infrastructure"
  },
  {
    "title": "Build real-time Event Feed and Metrics Dashboard",
    "description": "Build the main Dashboard home page with a live event feed and the Metrics page for cost/token tracking.\n\n**Dashboard (home page):**\n- Live scrollable event feed showing real-time events from WebSocket, similar to the TUI's event area\n- Each event displayed with timestamp, event type icon/badge, agent name, and content\n- Event types to render: agent_text_delta (streaming text), agent_text_complete (final output), tool_use (tool name + input summary), task_status_change (status transition), system events, circuit_breaker_tripped/reset\n- Agent text streaming should show incremental text updates (delta events append, complete event finalizes)\n- Auto-scroll to latest events with option to pause scrolling when user scrolls up\n- Status bar at top showing: company name, running state, task counts (P:pending R:running D:done), total cost, connection status\n- Quick action buttons: Start/Stop/Resume company, Approve All pending tasks\n\n**Metrics page:**\n- Per-agent token usage table: agent name, tokens used, budget, percentage, estimated cost ($)\n- Total cost display across all agents\n- Budget usage progress bars per agent with color coding (green < 50%, yellow 50-80%, red > 80%)\n- Session information: active sessions count, session pool status\n- Circuit breaker status indicator (healthy/tripped) with consecutive failure count\n- System health: memory usage, poll interval, max concurrent setting\n\nACs:\n- Event feed displays live WebSocket events with proper formatting per event type\n- Streaming agent text (delta events) renders incrementally in the feed\n- Status bar shows accurate real-time task counts and total cost\n- Metrics page shows per-agent token usage with budget progress bars\n- Circuit breaker status is visible and updates on breaker events\n- Auto-scroll behavior works correctly (scrolls to bottom, pauses on user scroll up)",
    "assigned_to": "dashboard_dev_1",
    "depends_on": "Scaffold Vue 3 project and build core infrastructure"
  },
  {
    "title": "Build Configuration and Workspace Management pages",
    "description": "Build the Configuration display/editing page and the Workspace management interface.\n\n**Configuration page:**\n- Display current shazam.yaml configuration in a structured, readable format (not raw YAML)\n- Organized sections: Company info, Provider settings, RalphLoop config (auto_approve, max_concurrent, poll_interval, module_lock, peer_reassign, max_retries, context settings), Domain definitions, Tech stack\n- Editable fields for RalphLoop config: auto_approve toggle, max_concurrent number input, poll_interval, module_lock toggle, peer_reassign toggle, auto_retry toggle, max_retries, context_history, team_activity, context_budget\n- Plugin management section: list loaded plugins with enabled/disabled status, event subscriptions, reload button\n- Save/apply changes functionality\n\n**Workspace page:**\n- Display configured workspaces with name, path, and associated domains\n- Git context per workspace: current branch, clean/dirty status, recent commits, modified files\n- Workspace switching capability (select active workspace)\n- Domain listing per workspace with associated paths\n\n**Additional views:**\n- Memory/Context browser: display agent learnings, topic files, and skill memories in a navigable tree\n- Session pool status: show active Claude sessions per agent, idle time, task count\n\nACs:\n- Configuration page displays all shazam.yaml settings in organized sections\n- RalphLoop config fields are editable with appropriate input types (toggles, number inputs)\n- Plugin list shows name, enabled status, and subscribed events\n- Workspace page displays all configured workspaces with their git context\n- Memory browser shows a navigable tree of context files and skill memories\n- All configuration changes can be saved/applied",
    "assigned_to": "dashboard_dev_2",
    "depends_on": "Scaffold Vue 3 project and build core infrastructure"
  },
  {
    "title": "Polish responsive design, navigation integration, and cross-feature consistency",
    "description": "Ensure the entire dashboard is polished, responsive, and all features integrate seamlessly. This is the final integration pass.\n\nBehavior requirements:\n- Responsive design: all pages must work on desktop (1920px+), laptop (1366px), and tablet (768px) viewports\n- Sidebar navigation collapses to icon-only mode on smaller viewports with a hamburger menu toggle\n- Consistent component styling across all pages: same button styles, card styles, badge styles, form input styles\n- Dark mode support using Tailwind's dark mode utilities\n- Cross-page navigation flows: clicking an agent name anywhere navigates to agent detail, clicking a task ID navigates to task detail, clicking company name navigates to company detail\n- Error handling on all API calls: show toast notifications for failures, loading states for async operations, graceful degradation when backend is unreachable\n- WebSocket connection indicator in the header: green dot when connected, red dot with 'Reconnecting...' when disconnected\n- Empty state illustrations/messages for all list views when no data exists\n- Keyboard shortcuts: Escape to close modals/overlays, Enter to submit forms\n- Page title updates per route for browser tab clarity\n\nACs:\n- All pages render correctly at 1920px, 1366px, and 768px viewports\n- Sidebar collapses with toggle on smaller screens\n- Dark mode toggle switches all pages to dark theme consistently\n- Cross-page links (agent names, task IDs, company names) navigate to the correct detail views\n- API errors display toast notifications and loading states show spinners\n- WebSocket connection status indicator is visible in the header at all times",
    "assigned_to": "dashboard_dev_3",
    "depends_on": "Build real-time Event Feed and Metrics Dashboard"
  }
]
```
