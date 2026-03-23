---
id: task_68
title: "Tela de workspaces não aparece nada, tela de memory não aparece nada, tela de config não carrega nada. A tela de workspaces pode ser removida e a gente pode usar apenas a tela de Config que vai conter os workspaces lá dentro"
status: completed
assigned_to: manager
created_by: human
company: Shazam
created_at: 2026-03-21T20:46:38.358361Z
completed_at: 2026-03-21T20:46:50.335785Z
updated_at: 2026-03-21T20:46:50.335779Z
---

## Description

Tela de workspaces não aparece nada, tela de memory não aparece nada, tela de config não carrega nada. A tela de workspaces pode ser removida e a gente pode usar apenas a tela de Config que vai conter os workspaces lá dentro

## Result

**One-line Summary:** Fix Config page loading, integrate Workspaces into Config, and remove standalone Workspaces page.

```subtasks
[
  {
    "title": "Investigate and fix Config page loading issue",
    "description": "Debug why the Config page fails to load and displays nothing. Check console errors, API responses (especially /api/config endpoint), component initialization, and data binding. Implement fixes to restore functionality and ensure configuration data loads and displays correctly.\n\nACs:\n- Config page loads without errors\n- Console shows no error messages or exceptions\n- API /api/config endpoint returns data successfully\n- Configuration data displays on the page",
    "assigned_to": "dashboard_dev_1",
    "depends_on": null
  },
  {
    "title": "Integrate Workspaces section into Config page",
    "description": "Move workspace management functionality from the standalone Workspaces page into the Config page as a dedicated section/tab. Fetch workspace data from the appropriate API endpoint and display it alongside other configuration settings. Ensure workspaces can be viewed and managed within the Config page.\n\nACs:\n- Workspaces section appears in Config page\n- Workspace data loads and displays correctly\n- Workspace management functionality works in Config context\n- Layout remains organized and usable with all config sections",
    "assigned_to": "dashboard_dev_2",
    "depends_on": null
  },
  {
    "title": "Remove standalone Workspaces page and update navigation",
    "description": "Delete the standalone Workspaces page component and route. Remove Workspaces link from main navigation and sidebar. Update any references to the Workspaces page throughout the dashboard. Ensure all navigation points users to the Config page for workspace management instead.\n\nACs:\n- Workspaces page file removed from codebase\n- Route /workspaces no longer exists\n- Navigation menu no longer shows Workspaces link\n- No broken links or 404s when building dashboard\n- Routing and navigation work correctly",
    "assigned_to": "dashboard_dev_3",
    "depends_on": null
  }
]
```
