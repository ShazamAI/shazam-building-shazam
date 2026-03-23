---
id: task_33
title: "Update all dashboard pages to use automatically-loaded company"
status: completed
assigned_to: dashboard_dev_3
created_by: manager
company: Shazam
created_at: 2026-03-21T20:16:24.174071Z
completed_at: 2026-03-21T20:19:05.403114Z
updated_at: 2026-03-21T20:19:05.403109Z
---

## Description

Verify that all dashboard pages (Tasks, Agents, OrgChart, Metrics, etc.) are using the automatically-loaded current company instead of allowing manual selection. Ensure data is always filtered by the active company and components render correctly.

ACs:
- All pages use the current company from global state
- Tasks/agents/metrics filtered by current company
- No company selector logic in individual pages
- Pages update correctly when company changes

## Result

Only CompaniesPage uses `selectCompany` — that's correct, since it's the companies management page where users explicitly choose which company to set as active. All other pages just consume `activeCompany` from the composable.

## Summary

### Files Modified (3 pages)

**1. `src/pages/AgentsPage.vue`**
- Removed local `companies` ref and `selectedCompany` ref
- Removed `loadCompaniesAndAgents()` — replaced with `loadData()` that uses `activeCompany` from composable
- Removed `onCompanyChange()` function
- Replaced company `<select>` dropdown with a passive company name badge
- All API calls (`createAgent`, `updateAgents`, `fetchAgents`) now use `activeCompany.value.name`
- Watch on `activeCompany` reloads agents automatically on change

**2. `src/pages/OrgChartPage.vue`**
- Removed local `companies` ref and `selectedCompany` ref
- Removed `onCompanyChange()` function
- Simplified `loadData()` to use composable's `loadCompanies()` + `activeCompany`
- Replaced company `<select>` with passive company name badge
- Watch on `activeCompany` reloads org chart automatically

**3. `src/pages/MetricsPage.vue`**
- Removed direct `fetchCompanies` import — now uses `useActiveCompany` composable
- Removed local `companies` ref (uses composable's `companies`)
- `loadData()` now calls `loadCompanies()` from composable + `loadAgents()` using `activeCompany.value.name`
- Added `watch` on `activeCompany` to reload agents when selection changes

### Pages already correct (no changes needed)
- **TasksPage.vue** — Already used `useActiveCompany` properly
- **DashboardPage.vue** — Already fixed in previous task
- **CompaniesPage.vue** — Correctly uses `selectCompany()` to let users set the active company

### Data flow now
1. TopHeader loads companies on mount + polls every 30s → `useActiveCompany` singleton updates
2. Any page navigated to calls `loadCompanies()` which deduplicates via shared promise
3. `activeCompany` is a computed that derives from `selectedCompanyName` (persisted in localStorage)
4. All pages watch `activeCompany` and reload their data when it changes
5. No page has its own company selector UI (except CompaniesPage's "Set Active" button)
