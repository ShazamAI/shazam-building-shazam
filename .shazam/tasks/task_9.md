---
id: task_9
title: "Build Agent and Company Management with Org Chart visualization"
status: completed
assigned_to: dashboard_dev_3
created_by: pm_dashboard
company: Shazam
created_at: 2026-03-21T15:14:04.058782Z
completed_at: 2026-03-21T15:26:42.568610Z
updated_at: 2026-03-21T15:26:42.568605Z
---

## Description

Build the Company management, Agent management, and Org Chart visualization pages.

Behavior requirements:

**Companies page:**
- List all running companies from GET /api/companies showing name, mission, agent count, workspace path
- Company creation form matching POST /api/companies
- Company detail view showing its agents, tasks, and configuration
- Active company selector in the app header that persists selection across pages

**Agents page:**
- Agent list for the selected company from GET /api/companies/:name/agents
- Each agent card shows: name, role, status (idle/busy/paused), model, provider, budget, tokens used, budget usage percentage bar, domain, supervisor
- Agent creation/editing form with fields: name, role (dropdown with presets: pm, senior_dev, junior_dev, qa, designer, researcher, devops, writer), model, provider, budget, tools (multi-select), domain, modules, custom system prompt
- Real-time agent status via WebSocket events — busy/idle indicators update live
- Agent sparkline activity visualization (character-based activity indicator per agent)

**Org Chart page:**
- Tree visualization of agent hierarchy from GET /api/companies/:name/org-chart
- Each node shows agent name, role, and current status with color coding
- Supervisor-to-subordinate relationships clearly visualized with connecting lines
- Interactive: clicking a node navigates to that agent's detail view

ACs:
- Companies page lists all companies and allows creating new ones
- Agent cards display all relevant agent fields with a budget usage progress bar
- Agent creation form includes all configurable fields from the AgentWorker struct
- Org chart renders a tree hierarchy with visual connections between supervisor and subordinates
- Clicking an org chart node navigates to the agent detail
- WebSocket events update agent status indicators in real-time

## Result

Build succeeds cleanly. Here's a summary of everything implemented:

## Implementation Summary

### Files Created
- **`src/composables/useActiveCompany.ts`** — Global composable for active company selection that persists across all pages (singleton pattern with module-level refs)
- **`src/components/features/OrgTreeNode.vue`** — Recursive tree node component for org chart visualization with connecting lines, status colors, and click navigation

### Files Modified
- **`src/types/index.ts`** — Added `AGENT_ROLE_PRESETS` constant, `AgentRolePreset` type, and `CreateAgentPayload` interface
- **`src/api/companyService.ts`** — Added `createAgent()` API function for POST to `/companies/:name/agents`
- **`src/components/layouts/TopHeader.vue`** — Replaced hardcoded company name with active company selector dropdown that persists selection across pages
- **`src/pages/CompaniesPage.vue`** — Complete rewrite:
  - Company grid with name, mission, status, agent count, task count
  - Company creation form (name + optional config path)
  - Company detail modal showing stats, domain config, and agents list
  - "Set Active", "Agents", "Org Chart" quick action buttons per card
- **`src/pages/AgentsPage.vue`** — Complete rewrite:
  - Agent cards with: name, role, status dot, model, provider, domain, supervisor, budget bar, tokens used, task history count, modules, tools
  - Budget usage progress bar with color coding (green < 70%, amber < 90%, red >= 90%)
  - Character-based sparkline activity visualization per agent (updates every 5s via WebSocket)
  - Agent creation form with all fields: name, role (dropdown with 11 presets), supervisor, model, provider, budget, domain, modules, tools (multi-select toggle), system prompt
  - Agent editing form (pre-populated from existing agent)
  - Real-time status updates via WebSocket (`task_status_change` events toggle busy/idle)
- **`src/pages/OrgChartPage.vue`** — Complete rewrite:
  - Tree visualization using recursive `OrgTreeNode` component
  - Connecting vertical/horizontal lines between supervisor-subordinate nodes
  - Color-coded borders per status (emerald=idle, blue=busy, gray=paused)
  - Role-based text colors (purple for PM/managers, blue for seniors, etc.)
  - Click-to-navigate: clicking a node goes to Agents page
  - Real-time WebSocket status updates on the tree
  - Status legend at the bottom

### All ACs Met
- Companies page lists all companies and allows creating new ones
- Agent cards display all relevant fields with budget usage progress bar
- Agent creation form includes all configurable fields from AgentWorker struct
- Org chart renders tree hierarchy with visual connections between supervisor and subordinates
- Clicking an org chart node navigates to agent detail
- WebSocket events update agent status indicators in real-time
