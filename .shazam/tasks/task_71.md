---
id: task_71
title: "Remove standalone Workspaces page and update navigation"
status: completed
assigned_to: dashboard_dev_3
created_by: manager
company: Shazam
created_at: 2026-03-21T20:46:50.336491Z
completed_at: 2026-03-21T20:47:36.962558Z
updated_at: 2026-03-21T20:47:36.962555Z
---

## Description

Delete the standalone Workspaces page component and route. Remove Workspaces link from main navigation and sidebar. Update any references to the Workspaces page throughout the dashboard. Ensure all navigation points users to the Config page for workspace management instead.

ACs:
- Workspaces page file removed from codebase
- Route /workspaces no longer exists
- Navigation menu no longer shows Workspaces link
- No broken links or 404s when building dashboard
- Routing and navigation work correctly

## Result

Zero references remaining. Here's the summary:

### Changes Made

**Deleted:**
- `src/pages/WorkspacesPage.vue` — standalone Workspaces page removed

**`src/router/index.ts`**
- Removed `/workspaces` route entry

**`src/components/layouts/SidebarNav.vue`**
- Removed `workspaces` icon from `iconMap`

**`src/components/layouts/MobileSidebar.vue`**
- Removed `workspaces` icon from `iconMap`

### ACs Verified
- ✅ **Workspaces page file removed from codebase** — `WorkspacesPage.vue` deleted
- ✅ **Route /workspaces no longer exists** — removed from router
- ✅ **Navigation menu no longer shows Workspaces link** — icon removed from both desktop and mobile sidebars (nav items are generated dynamically from routes)
- ✅ **No broken links or 404s when building dashboard** — production build successful, zero references to `WorkspacesPage`
- ✅ **Routing and navigation work correctly** — all remaining routes intact
