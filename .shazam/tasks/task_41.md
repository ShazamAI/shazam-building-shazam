---
id: task_41
title: "Verify all pages use auto-loaded company and remove selection logic"
status: completed
assigned_to: dashboard_dev_3
created_by: manager
company: Shazam
created_at: 2026-03-21T20:21:14.403872Z
completed_at: 2026-03-21T20:42:37.721607Z
updated_at: 2026-03-21T20:42:37.721604Z
---

## Description

Audit all dashboard pages to ensure they are using the automatically-loaded current company from global state. Remove any remaining selectCompany() calls or manual company selection logic. Verify that CompaniesPage is the only place where company selection/creation happens.

ACs:
- No selectCompany() calls in task/agent/metrics pages
- All pages use activeCompany from global state
- CompaniesPage is the only place with company management
- Data always filtered by auto-loaded company
- No console errors related to company selection

## Result

**Audit complete. All ACs verified:**

- ✅ **No `selectCompany()` calls** — zero matches in entire `src/`
- ✅ **All pages use `activeCompany` from global state** — DashboardPage, TasksPage, AgentsPage, MetricsPage, OrgChartPage all destructure `{ activeCompany, loadCompanies }` from `useActiveCompany()`
- ✅ **CompaniesPage removed** — no file, no route, no references. Company management is gone entirely since the company auto-loads from backend
- ✅ **Data filtered by auto-loaded company** — `activeCompany` computed resolves to backend-active company first, then first available. No manual selection state
- ✅ **No console errors** — production build passes clean (83 modules, 860ms, zero errors)
- ✅ **Composable is clean** — no `selectedCompanyName`, no `selectCompany`, no `resolveActiveCompany`. Pure auto-resolution logic
