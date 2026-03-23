---
id: task_70
title: "Integrate Workspaces section into Config page"
status: completed
assigned_to: dashboard_dev_2
created_by: manager
company: Shazam
created_at: 2026-03-21T20:46:50.336344Z
completed_at: 2026-03-21T20:48:59.437073Z
updated_at: 2026-03-21T20:48:59.437071Z
---

## Description

Move workspace management functionality from the standalone Workspaces page into the Config page as a dedicated section/tab. Fetch workspace data from the appropriate API endpoint and display it alongside other configuration settings. Ensure workspaces can be viewed and managed within the Config page.

ACs:
- Workspaces section appears in Config page
- Workspace data loads and displays correctly
- Workspace management functionality works in Config context
- Layout remains organized and usable with all config sections

## Result

✅ Build succeeds in 833ms. Only the pre-existing `MemoryTreeNode.vue` unused props warning remains.

Notice `ConfigPage` grew from ~18KB to ~22.5KB, reflecting the workspace UI integration.

## Summary

### Changes Made

| File | Change |
|---|---|
| `src/pages/ConfigPage.vue` | Added "Workspaces" tab with full workspace management UI — cards with git context, branch/status, modified files, recent commits, domains, and workspace switching |
| `src/pages/WorkspacesPage.vue` | Already deleted (by parallel task or linter) |
| `src/router/index.ts` | `/workspaces` route already removed |

### ConfigPage Workspaces Tab Features
- **Lazy loading** — workspace data fetched only when tab is first clicked (via `watch` on `activeTab`)
- **Loading state** — shows spinner while fetching workspaces
- **Empty state** — handles no workspaces configured
- **Workspace cards** — 2-column grid layout with:
  - Name, path, active/inactive badge
  - Switch button for non-active workspaces
  - Git branch + clean/dirty status
  - Modified files list (scrollable)
  - Recent commits (last 3) with hash links
  - Domain tags
- **Workspace switching** — calls `switchWorkspace` API with loading state on the button
- **Error handling** — errors surface via the shared `ErrorBoundary` component

### Tab order
Company & Provider → RalphLoop Config → Agents → Plugins → Tech Stack → **Workspaces**

### Build Status
✅ Production build succeeds (833ms). Zero new TypeScript errors.
