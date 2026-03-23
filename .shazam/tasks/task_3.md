---
id: task_3
title: "Build Complete VS Code Extension with Chat and Full Shazam Integration"
status: completed
assigned_to: pm_vscode
created_by: manager
company: Shazam
created_at: 2026-03-21T15:11:35.207893Z
completed_at: 2026-03-21T15:13:50.284312Z
updated_at: 2026-03-21T15:13:50.284308Z
---

## Description

Create a fully functional VS Code extension that integrates Shazam into VS Code. Features must include a chat interface for talking to agents, task creation and management UI, org chart visualization, real-time event streaming, and workspace integration. The extension should connect to the Elixir backend for all core Shazam functionality and provide seamless integration with VS Code editor.

ACs:
- Webview-based chat interface for interactive communication with agents
- Task creation, viewing, and management from within VS Code
- Real-time updates via WebSocket for task execution and events
- Org chart visualization within extension sidebar
- Status bar showing current company, task count, agent activity
- Command palette commands for common actions (/start company, /create task, /view tasks, /chat)
- Workspace folder integration - detect and connect to multiple Shazam projects
- Event log panel showing real-time execution events
- Agent configuration UI accessible from extension
- Metrics/cost tracking view
- Company management UI (create, select, switch between companies)
- All API communication with Elixir backend (port 4040) properly implemented
- Extension properly packaged, buildable, and testable locally

## Result

Decomposing VS Code extension into 3 parallel tracks across all developers. Each track is independent — shared types and service interfaces allow parallel work.

```subtasks
[
  {
    "title": "Build API client, WebSocket service, and integrate chat with Elixir backend",
    "description": "Build the core communication layer for the VS Code extension. This includes:\n\n1. **REST API Client Service**: A service that communicates with the Shazam Elixir backend at `http://localhost:4040`. Must support all endpoints:\n   - GET /api/companies (list companies)\n   - POST /api/companies (create company)\n   - GET /api/companies/:name/agents (list agents)\n   - PUT /api/companies/:name/agents (update agents)\n   - GET /api/companies/:name/org-chart (get org chart)\n   - POST /api/companies/:name/tasks (create task)\n   - GET /api/tasks (list tasks with filters: status, assigned_to)\n   - GET /api/events/recent (recent events)\n\n2. **WebSocket Service**: Connect to `ws://localhost:4040/ws` for real-time event streaming. Handle event types: `agent_text_delta`, `agent_text_complete`, `tool_use`, `task_status_change`, `system`, `circuit_breaker_tripped`, `circuit_breaker_reset`. Implement auto-reconnect with exponential backoff.\n\n3. **Internal Event Bus**: A pub/sub system so other parts of the extension can subscribe to backend events without direct WebSocket access.\n\n4. **Chat Integration**: Refactor the existing ChatViewProvider to send messages through the API client instead of echoing. Chat should stream agent responses in real-time via WebSocket events (`agent_text_delta`, `agent_text_complete`). The chat UI should show which agent is responding.\n\n5. **Event Log Panel**: A webview panel showing real-time execution events from the backend (tool usage, task changes, system events) in a scrollable, filterable list.\n\nThe backend API spec is at SPECS.md in /Users/raphaelbarbosa/Projects/ShazamAI/shazam-building-shazam/SPECS.md — read sections 11 (API) and 3.11 (API & Events) for endpoint details and WebSocket event formats.\n\nAll code goes in the workspace at /Users/raphaelbarbosa/Projects/ShazamAI/shazam-vscode/. Services go in src/services/. Providers go in src/providers/. Follow existing patterns in the codebase. Use TypeScript strict mode.\n\nACs:\n- API client service exists with typed methods for all 8 REST endpoints\n- WebSocket service connects to ws://localhost:4040/ws and handles all 7 event types with auto-reconnect\n- Internal event bus allows subscription/unsubscription to specific event types\n- ChatViewProvider sends user messages via API and displays streamed responses from WebSocket\n- Event log panel displays real-time events with timestamps, event type badges, and agent names\n- All services are properly disposed on extension deactivation",
    "assigned_to": "vscode_dev_1",
    "depends_on": null
  },
  {
    "title": "Build task management, company management, and org chart webview UIs",
    "description": "Build the task management, company management, and org chart visualization features for the VS Code extension.\n\n1. **Task Management Webview**: A sidebar or panel view where users can:\n   - View all tasks with filters (by status: pending, in_progress, completed, failed, awaiting_approval, paused; by assigned agent)\n   - Create new tasks (title, description, assign to agent)\n   - Approve/reject tasks in awaiting_approval status\n   - Pause/resume tasks\n   - Kill/retry failed tasks\n   - Delete tasks\n   - View task details (description, result, timestamps, parent task, dependencies)\n   - Task status should be color-coded and show the task lifecycle states\n\n2. **Company Management UI**: A view where users can:\n   - See list of running companies\n   - Create a new company (from shazam.yaml or manual config)\n   - Select/switch between companies\n   - View company details (name, mission, agent count, workspace)\n\n3. **Org Chart Visualization**: A tree view or webview showing the agent hierarchy:\n   - Display agent roles, names, and status (idle/busy/paused)\n   - Show supervisor-subordinate relationships\n   - Visual indicators for agent activity\n   - Token usage per agent\n\n4. **Command Palette Commands**: Register commands for:\n   - `shazam.createTask` — open task creation form\n   - `shazam.viewTasks` — open task list view\n   - `shazam.startCompany` — start/create a company\n   - `shazam.switchCompany` — quick pick to switch companies\n   - `shazam.viewOrgChart` — show org chart\n   - `shazam.approveTask` — approve selected task\n   - `shazam.rejectTask` — reject selected task\n\nThe backend API spec is at SPECS.md in /Users/raphaelbarbosa/Projects/ShazamAI/shazam-building-shazam/SPECS.md — read sections 3 (Core Modules), 4 (Task Lifecycle), 5 (Agent Hierarchy), and 11 (API) for data structures and endpoints.\n\nAll code goes in the workspace at /Users/raphaelbarbosa/Projects/ShazamAI/shazam-vscode/. Webview providers go in src/providers/. Commands go in src/commands/. Types go in src/types.ts. Update extension/package.json with new commands and views. Use TypeScript strict mode.\n\nFor API calls, create typed interfaces matching the expected service methods (the API client is being built in parallel — use the same method signatures). Import from '../services/ApiClient' assuming it exports methods like `listCompanies()`, `createTask()`, `listTasks()`, etc.\n\nACs:\n- Task list view displays tasks with status filtering and color-coded status badges\n- Task creation form allows setting title, description, and agent assignment\n- Task actions (approve, reject, pause, resume, kill, retry, delete) work from the UI\n- Company list view shows running companies with details\n- Company creation and switching between companies works\n- Org chart renders agent hierarchy with roles, status indicators, and relationships\n- All 7 command palette commands are registered in extension/package.json and functional\n- All views properly dispose resources on deactivation",
    "assigned_to": "vscode_dev_2",
    "depends_on": null
  },
  {
    "title": "Build status bar, metrics view, workspace integration, and agent configuration UI",
    "description": "Build the status bar, metrics tracking, workspace integration, and agent configuration features for the VS Code extension.\n\n1. **Status Bar Items**: Create status bar items showing:\n   - Current company name and running state (idle/running/paused)\n   - Task counts: P:{pending} R:{running} D:{done}\n   - Total cost in real-time (e.g., $0.42)\n   - Active agent count and activity indicators\n   - Click on company name → quick pick to switch companies\n   - Click on task counts → open task list view\n   - Status bar should update in real-time via events from the backend\n\n2. **Metrics/Cost Tracking View**: A webview panel showing:\n   - Per-agent token usage and cost breakdown\n   - Total session cost\n   - Task completion stats (completed, failed, retried)\n   - Budget usage per agent (used/total)\n   - Visual charts or progress bars for budget consumption\n\n3. **Workspace Integration**:\n   - Detect shazam.yaml or .shazam/shazam.yaml in open workspace folders\n   - Auto-connect to the Shazam backend when a Shazam project is detected\n   - Support multiple Shazam projects in multi-root workspaces\n   - Show workspace indicator in status bar\n   - Configure backend URL (default localhost:4040) in VS Code settings\n\n4. **Agent Configuration UI**: A webview where users can:\n   - View all agents with their current configuration (role, model, budget, tools, domain, provider)\n   - Edit agent settings (model, budget, tools)\n   - Add new agents using presets (pm, senior_dev, junior_dev, qa, designer, researcher, devops, writer)\n   - Remove agents\n   - View agent status (idle/busy/paused) and token usage\n\n5. **Extension Settings**: Register VS Code settings in extension/package.json:\n   - `shazam.backendUrl` (default: http://localhost:4040)\n   - `shazam.autoConnect` (default: true)\n   - `shazam.showStatusBar` (default: true)\n\n6. **Command Palette Commands**: Register:\n   - `shazam.showMetrics` — open metrics view\n   - `shazam.configureAgents` — open agent config UI\n   - `shazam.connectBackend` — manually connect to backend\n   - `shazam.disconnectBackend` — disconnect from backend\n\nThe backend API spec is at SPECS.md in /Users/raphaelbarbosa/Projects/ShazamAI/shazam-building-shazam/SPECS.md — read sections 3.6 (Context), 3.13 (Metrics), 5 (Agent Hierarchy), 9 (Configuration), and 11 (API).\n\nAll code goes in the workspace at /Users/raphaelbarbosa/Projects/ShazamAI/shazam-vscode/. Providers go in src/providers/. Commands go in src/commands/. Update extension/package.json with new commands, settings, and views. Use TypeScript strict mode.\n\nFor API calls, create typed interfaces matching the expected service methods (the API client is being built in parallel). Import from '../services/ApiClient'.\n\nACs:\n- Status bar shows company name, task counts, and cost — all updating in real-time\n- Clicking status bar items triggers relevant views or quick picks\n- Metrics view displays per-agent token usage, cost breakdown, and budget consumption\n- Workspace detection finds shazam.yaml and auto-connects to backend\n- Multi-root workspace support works correctly\n- Agent configuration UI shows all agents with editable settings\n- Agent presets are available when adding new agents\n- All 4 command palette commands registered in extension/package.json and functional\n- VS Code settings properly declared with defaults and descriptions\n- All components properly dispose resources on deactivation",
    "assigned_to": "vscode_dev_3",
    "depends_on": null
  }
]
```
