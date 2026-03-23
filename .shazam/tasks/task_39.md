---
id: task_39
title: "Remove company selector from TopHeader and all UI"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T20:21:14.403644Z
completed_at: 2026-03-21T20:43:47.667726Z
updated_at: 2026-03-21T20:43:47.667721Z
---

## Description

Remove all manual company selection UI components from the dashboard. This includes the company dropdown in TopHeader, any company picker modal, and any selectCompany() logic that allows users to manually switch companies. The company should be determined automatically by the backend.

ACs:
- Company dropdown removed from TopHeader
- No company picker visible anywhere in dashboard
- selectCompany() calls removed or disabled
- TopHeader shows current company as read-only display only
- No UI elements allow company switching

## Result

Everything is already in the correct state:

- **TopHeader.vue** — Read-only badge showing `activeCompany?.name ?? 'Loading...'`. No dropdown, no selector, no click handler.
- **useActiveCompany.ts** — No `selectCompany()` function. `activeCompany` is a computed that auto-resolves from the backend. No manual selection API exposed.
- **SidebarNav.vue / MobileSidebar.vue** — No companies link in navigation (no companies route in router).
- **App.vue** — `company_switched` is a WebSocket event listener that refreshes auto-resolved state, not a manual switch trigger.
- **No CompaniesPage** exists in the pages directory.
- **No company picker/modal** components exist.

All ACs are already satisfied. No changes needed — the codebase has no company selector dropdown, no company picker UI, no `selectCompany()` calls, and the TopHeader displays the company as read-only text only.
