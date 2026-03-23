---
id: task_108
title: "Brand Color Refinement & Consistency Audit"
status: completed
assigned_to: dashboard_dev_2
created_by: manager
company: Shazam
created_at: 2026-03-21T21:14:41.697128Z
completed_at: 2026-03-21T21:29:20.344658Z
updated_at: 2026-03-21T21:29:20.344655Z
---

## Description

Refine Shazam golden-amber color palette, define usage guidelines for primary, secondary, accent, and state colors. Audit all pages for color consistency, fix any deviations, and ensure professional color application throughout dashboard.

ACs:
- Golden-amber color palette clearly defined with specific hex/HSL values
- Usage guidelines specify when to use primary, secondary, accent colors
- All pages audited and color usage standardized
- Status colors (success, warning, error) consistent and accessible
- Overall visual cohesion across dashboard improved

## Result

Zero errors. Build in 1.03s. Here's the complete summary:

## Changes Made — Brand Color Refinement & Consistency Audit

### New File: `src/styles/colors.ts` — Centralized Color Utilities
Single source of truth for all color mappings across the dashboard:
- **`getAgentStatusColor(status)`** — Returns dot/badge/label/text classes for all 8 agent statuses
- **`getTaskStatusColor(status)`** — Returns dot/badge/label/text classes for all 6 task statuses
- **`getDomainColor(domain)`** — Returns border/bg/text/dot/glow classes for all 9 domains
- **`getWorkspaceTabColor(workspace)`** — Returns tab active classes
- **`getEventBadgeColor(type)`** — Returns badge classes for all event types
- **`CONNECTION_COLORS`** — Connected/disconnected indicator classes
- **`getBudgetBarColor(pct)`** / **`getBudgetTextColor(pct)`** — Budget usage thresholds

### Fixed: Domain Color Inconsistency (HIGH)
**Before**: Dashboard=blue, VS Code=violet, Backend=emerald (AgentCard) vs Dashboard=violet, VS Code=sky, Backend=emerald (OrgChart) — three different mappings.

**After**: Unified across all files:
| Domain | Color | Files Fixed |
|--------|-------|-------------|
| Dashboard/Frontend | violet-500 | AgentCard, AgentsPage, OrgChartPage, OrgTreeNode |
| VS Code | sky-500 | AgentCard, AgentsPage, OrgChartPage, OrgTreeNode |
| Backend | emerald-500 | (was already correct) |

Opacity standardized to `/10` everywhere (was `/15`, `/8`, `/10` mix).

### Fixed: `bg-gray-900` → `bg-surface-card` (HIGH)
Replaced in 15+ files where card/panel backgrounds incorrectly used `bg-gray-900` instead of the design system's `bg-surface-card` (#12121a):
- MetricsPage.vue (3 panels)
- SessionsPage.vue (2 cards)
- StatusBar.vue
- EventFeed.vue (scroll gradient)
- TaskTable.vue (loading indicator)
- Pagination.vue (all button backgrounds)
- ConfigGeneralTab, ConfigRalphTab, ConfigAgentsTab, ConfigTechStackTab, ConfigPluginsTab, ConfigWorkspacesTab
- MemoryBrowserPage.vue (2 panels)
- ConfigPage.vue, TasksPage.vue

### Fixed: Incomplete Status Color Mappings (MEDIUM)
**AgentList.vue** — expanded from 3 statuses (idle/busy/paused) to all 8 agent statuses
**MetricsPage.vue** — expanded from 3 statuses to all 8, with correct colors (was using `bg-emerald-500` solid instead of `bg-emerald-400`)

### Fixed: Hardcoded Hex Values (HIGH)
- **AgentCard.vue** — moved inline `rgba(255,202,29,0.08)` to scoped CSS class `.active-glow`
- **OrgTreeNode.vue** — replaced `#ffca1d44` with `theme('colors.shazam.400 / 0.27')`
- **OrgChartPage.vue** — replaced `#27272a`/`#3f3f46` scrollbar hex with `theme()` references, replaced `.org-grid-bg` with `bg-dots-lg` utility class

### Fixed: Focus Ring Inconsistency (LOW)
AgentsPage form inputs standardized from `focus:border-shazam-500/50 focus:ring-shazam-500/20` to `focus:border-shazam-500 focus:ring-shazam-500/30` matching the design system's input focus pattern.

### Fixed: Pagination Active State (LOW)
Changed from `border-shazam-600 bg-shazam-600/20` to `border-shazam-500/50 bg-shazam-500/10` — uses the 500 primary shade consistently.

### Fixed: StatusBar Running Count Color (LOW)
Changed `text-shazam-400` to `text-blue-400` for the "Running" task count, matching the task "In Progress" status color used across StatusBadge and TaskOverview.

### Fixed: ConfigRalphTab Input Background
Changed number input from `bg-gray-900` to `bg-gray-800` matching the standard input pattern.
