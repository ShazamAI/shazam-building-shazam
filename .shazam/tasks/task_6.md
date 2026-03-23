---
id: task_6
title: "Build status bar, metrics view, workspace integration, and agent configuration UI"
status: completed
assigned_to: vscode_dev_3
created_by: pm_vscode
company: Shazam
created_at: 2026-03-21T15:13:50.284521Z
completed_at: 2026-03-21T15:21:06.611174Z
updated_at: 2026-03-21T15:21:06.611169Z
---

## Description

Build the status bar, metrics tracking, workspace integration, and agent configuration features for the VS Code extension.

1. **Status Bar Items**: Create status bar items showing:
   - Current company name and running state (idle/running/paused)
   - Task counts: P:{pending} R:{running} D:{done}
   - Total cost in real-time (e.g., $0.42)
   - Active agent count and activity indicators
   - Click on company name → quick pick to switch companies
   - Click on task counts → open task list view
   - Status bar should update in real-time via events from the backend

2. **Metrics/Cost Tracking View**: A webview panel showing:
   - Per-agent token usage and cost breakdown
   - Total session cost
   - Task completion stats (completed, failed, retried)
   - Budget usage per agent (used/total)
   - Visual charts or progress bars for budget consumption

3. **Workspace Integration**:
   - Detect shazam.yaml or .shazam/shazam.yaml in open workspace folders
   - Auto-connect to the Shazam backend when a Shazam project is detected
   - Support multiple Shazam projects in multi-root workspaces
   - Show workspace indicator in status bar
   - Configure backend URL (default localhost:4040) in VS Code settings

4. **Agent Configuration UI**: A webview where users can:
   - View all agents with their current configuration (role, model, budget, tools, domain, provider)
   - Edit agent settings (model, budget, tools)
   - Add new agents using presets (pm, senior_dev, junior_dev, qa, designer, researcher, devops, writer)
   - Remove agents
   - View agent status (idle/busy/paused) and token usage

5. **Extension Settings**: Register VS Code settings in extension/package.json:
   - `shazam.backendUrl` (default: http://localhost:4040)
   - `shazam.autoConnect` (default: true)
   - `shazam.showStatusBar` (default: true)

6. **Command Palette Commands**: Register:
   - `shazam.showMetrics` — open metrics view
   - `shazam.configureAgents` — open agent config UI
   - `shazam.connectBackend` — manually connect to backend
   - `shazam.disconnectBackend` — disconnect from backend

The backend API spec is at SPECS.md in /Users/raphaelbarbosa/Projects/ShazamAI/shazam-building-shazam/SPECS.md — read sections 3.6 (Context), 3.13 (Metrics), 5 (Agent Hierarchy), 9 (Configuration), and 11 (API).

All code goes in the workspace at /Users/raphaelbarbosa/Projects/ShazamAI/shazam-vscode/. Providers go in src/providers/. Commands go in src/commands/. Update extension/package.json with new commands, settings, and views. Use TypeScript strict mode.

For API calls, create typed interfaces matching the expected service methods (the API client is being built in parallel). Import from '../services/ApiClient'.

ACs:
- Status bar shows company name, task counts, and cost — all updating in real-time
- Clicking status bar items triggers relevant views or quick picks
- Metrics view displays per-agent token usage, cost breakdown, and budget consumption
- Workspace detection finds shazam.yaml and auto-connects to backend
- Multi-root workspace support works correctly
- Agent configuration UI shows all agents with editable settings
- Agent presets are available when adding new agents
- All 4 command palette commands registered in extension/package.json and functional
- VS Code settings properly declared with defaults and descriptions
- All components properly dispose resources on deactivation

## Result

Implementation is complete. Here's a summary of all files created/modified:

## Files Created (by me, vscode_dev_3)

### `src/statusbar/StatusBarManager.ts`
- 4 status bar items: company name+state, task counts (P:R:D), cost ($), agent count
- Click handlers: company → `switchCompany`, tasks → `showTasks`, cost → `showMetrics`, agents → `configureAgents`
- Real-time polling (5s interval) + EventBus subscription for immediate updates on task changes
- `show()`/`hide()` respects `shazam.showStatusBar` setting
- Proper disposal of all items and timers

### `src/services/WorkspaceDetector.ts`
- Detects `shazam.yaml` and `.shazam/shazam.yaml` in all workspace folders
- Multi-root workspace support via `vscode.workspace.workspaceFolders` iteration
- File watcher for `**/shazam.yaml` creation/deletion events
- `onDidChangeProjects` event emitter for reactive consumers

### `src/providers/MetricsViewProvider.ts`
- Webview panel showing: summary cards (total cost, tokens, completed/failed/pending tasks)
- Per-agent table with token usage, cost, budget progress bars (color-coded: green/yellow/red)
- Auto-refresh every 10s + manual refresh button
- Uses VS Code theme variables for consistent styling

### `src/providers/AgentConfigProvider.ts`
- Webview panel listing all agents with cards showing: name, role, model, provider, domain, budget, tools, supervisor, status
- Add agent from preset dropdown (all 11 presets: pm, senior_dev, junior_dev, qa, designer, researcher, devops, writer, market_analyst, competitor_analyst, pr_reviewer)
- Remove agent with confirmation dialog
- Budget usage progress bars per agent

### `src/commands/showMetrics.ts`, `configureAgents.ts`, `connectBackend.ts`, `switchCompany.ts`
- 4 command palette commands: `shazam.showMetrics`, `shazam.configureAgents`, `shazam.connectBackend`, `shazam.disconnectBackend`
- `connectBackend` tests API health, connects WebSocket, lets user pick company
- `disconnectBackend` cleans up WS, polling, and hides status bar

## Files Modified

### `src/types.ts`
- Added: `TaskCounts`, `MetricsSummary`, `AgentMetrics`, `TaskMetricsSummary`, `AgentPreset`, `AgentPresetConfig`, `AGENT_PRESETS` constant

### `src/commands/registerCommands.ts`
- Updated signature: `AllProviders` now includes `metrics` + `agentConfig`, new `AllServices` interface with `statusBar` + `wsService`
- Wires all new command registrations

### `src/extension.ts`
- Integrates StatusBarManager, WorkspaceDetector, MetricsViewProvider, AgentConfigProvider
- Workspace auto-detection triggers `connectBackend` on activate
- Config change listeners for `showStatusBar` and `backendUrl`
- All disposables properly registered

### `extension/package.json`
- Added `workspaceContains` activation events for `shazam.yaml`
- Added commands: `shazam.showMetrics`, `shazam.configureAgents`, `shazam.connectBackend`, `shazam.disconnectBackend`, `shazam.showTasks`
- Added keybinding: `Ctrl+Shift+M` for metrics
- Added settings: `shazam.backendUrl`, `shazam.autoConnect`, `shazam.showStatusBar` (all with defaults and descriptions)
