---
id: task_42
title: "Remova a sessão de companies do dashboard, não vai ser necessário por agora"
status: completed
assigned_to: manager
created_by: human
company: Shazam
created_at: 2026-03-21T20:22:31.209899Z
completed_at: 2026-03-21T20:22:49.362505Z
updated_at: 2026-03-21T20:22:49.362503Z
---

## Description

Remova a sessão de companies do dashboard, não vai ser necessário por agora

## Result

Breakdown of "Remove companies section from dashboard" — delegating to dashboard team to strip Companies UI and navigation since it's not needed now.

```subtasks
[
  {
    "title": "Remove CompaniesPage and companies route",
    "description": "Completely remove the Companies management page and its route from the dashboard navigation. The companies section should no longer be accessible via routing.\n\nACs:\n- CompaniesPage component no longer exists and is removed from project structure\n- Companies route is removed from router configuration\n- Application builds and compiles without errors after removal",
    "assigned_to": "dashboard_dev_1",
    "depends_on": null
  },
  {
    "title": "Remove company selector from TopHeader navigation",
    "description": "Remove the company dropdown/selector from TopHeader and any other navigation components. Current company should only be displayed as read-only text, not selectable. All company-related navigation links should be hidden.\n\nACs:\n- Company selector dropdown removed from TopHeader\n- TopHeader displays current company as read-only display text only\n- No navigation links to Companies section appear in any header or menu",
    "assigned_to": "dashboard_dev_2",
    "depends_on": null
  },
  {
    "title": "Audit dashboard pages and remove selectCompany logic",
    "description": "Verify that all dashboard pages use the automatically-loaded activeCompany from global state and remove any manual company selection logic. Ensure no selectCompany() function calls or user-triggered company changes exist in the UI.\n\nACs:\n- All dashboard pages properly consume activeCompany from global state\n- No selectCompany() calls or manual company switching logic exists in page components\n- Application builds without TypeScript errors",
    "assigned_to": "dashboard_dev_3",
    "depends_on": null
  },
  {
    "title": "Verify dashboard builds and loads without companies section",
    "description": "Run production build, verify no console errors related to missing Companies page or routes, and confirm dashboard loads and displays current company correctly without any company selection UI.\n\nACs:\n- Production build completes successfully\n- Dashboard loads without 404 errors or missing route warnings\n- No broken links or references to removed companies section remain",
    "assigned_to": "dashboard_dev_1",
    "depends_on": null
  }
]
```
