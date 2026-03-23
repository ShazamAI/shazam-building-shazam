---
id: task_83
title: "Refactor AgentsPage and OrgChartPage to use composables and services"
status: completed
assigned_to: dashboard_dev_2
created_by: manager
company: Shazam
created_at: 2026-03-21T20:51:49.878944Z
completed_at: 2026-03-21T21:08:29.281834Z
updated_at: 2026-03-21T21:08:29.281830Z
---

## Description

Extract agent fetching, status tracking, session management, and hierarchical data transformation from components into services. Consolidate duplicate company selection logic across both pages.

ACs:
- Agent data fetching extracted to agentService
- Status inference logic moved to composable (useAgentStatus)
- OrgChart hierarchy building extracted to utility
- Duplicate company selection removed, unified in single composable
- Both pages reduced to <250 lines each
- Real-time status updates via WebSocket handled in composable

## Result

All ACs met. Here's the summary:

### Changes Made

**Created `src/api/agentService.ts`** (166 lines)
- `loadAgents()` / `loadAndEnrichAgents()` — agent fetching with optional status enrichment
- `submitCreateAgent()` / `submitUpdateAgent()` — CRUD operations
- `loadOrgChart()` — org chart data loading
- `enrichAgentStatuses()` — cross-references sessions + tasks to derive real status
- `normalizeAgentStatus()` — canonical status mapping (single source of truth, replaces duplicates in both AgentsPage and companyService)
- `statusDotClass()` / `formatTokens()` / `budgetPercentage()` / `budgetColor()` — display helpers
- `updateNodeStatuses()` — recursive org tree status updater
- `AVAILABLE_TOOLS` / `AVAILABLE_PROVIDERS` — constants

**Created `src/composables/useAgentStatus.ts`** (157 lines)
- Real-time WebSocket agent status tracking via event inference
- Sparkline activity data tracking with periodic bucketing
- Handles: agent lifecycle events, task events, active work events, metrics updates
- Reusable across any page that needs agent status

**Created `src/composables/useAgents.ts`** (203 lines)
- Full AgentsPage composable: data loading, form state, CRUD operations
- Uses `useActiveCompany` (unified — no duplicate company logic)
- Integrates `useAgentStatus` for real-time updates
- Loading timeout guard

**Created `src/composables/useOrgChart.ts`** (89 lines)
- OrgChart data loading with `useActiveCompany` (unified)
- WebSocket status updates for tree nodes
- Navigation to agents page

**Refactored `src/pages/AgentsPage.vue`** (725 → 246 lines, script: 435 → 33 lines)
- All logic extracted, script is imports + destructuring only

**Refactored `src/pages/OrgChartPage.vue`** (144 → 60 lines, script: 92 → 8 lines)
- Entire script is 8 lines (imports + destructuring)

**Verification:** Zero new TypeScript errors (only pre-existing stores/ issues), production build succeeds in 992ms. AgentsPage bundle shrank from 15.75 kB → 14.98 kB.
