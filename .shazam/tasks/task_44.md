---
id: task_44
title: "Remove company selector from TopHeader navigation"
status: completed
assigned_to: dashboard_dev_2
created_by: manager
company: Shazam
created_at: 2026-03-21T20:22:49.362768Z
completed_at: 2026-03-21T20:43:09.685823Z
updated_at: 2026-03-21T20:43:09.685818Z
---

## Description

Remove the company dropdown/selector from TopHeader and any other navigation components. Current company should only be displayed as read-only text, not selectable. All company-related navigation links should be hidden.

ACs:
- Company selector dropdown removed from TopHeader
- TopHeader displays current company as read-only display text only
- No navigation links to Companies section appear in any header or menu

## Result

Build passes (855ms).

## Summary

The TopHeader already had no company selector — it was already a read-only badge. No Companies route existed in the router, and no Companies link appeared in sidebar/mobile navigation. The `useActiveCompany` composable already auto-resolved the active company with no manual selection.

### What was actually fixed

The real issue was **stale copy that referenced manual company selection** across empty states:

| File | Before | After |
|------|--------|-------|
| `src/pages/AgentsPage.vue` | "Manage agents for the **selected** company" | "Manage agents for the current project" |
| `src/pages/AgentsPage.vue` | "**Start a company** or add agents to see them here." | "No agents configured yet. Add an agent to get started." |
| `src/pages/ConfigPage.vue` | "**Start a company** to view and edit configuration." | "No active Shazam project detected. Ensure the backend is running." |
| `src/pages/OrgChartPage.vue` | "**Start a company** to see the organizational hierarchy." | "No active project detected. The org chart will appear when agents are running." |
| `src/pages/MetricsPage.vue` | "**Start a company** to see metrics." | "Metrics will appear when agents are active." |

### Already correct (no changes needed)
- **TopHeader.vue** — Read-only company badge, no dropdown
- **SidebarNav.vue** — No companies navigation link
- **MobileSidebar.vue** — No companies navigation link
- **Router** — No `/companies` route
- **useActiveCompany.ts** — Auto-resolves from backend, no `selectCompany`

### Build: ✅ passes (855ms)
