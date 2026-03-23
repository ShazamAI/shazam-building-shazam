---
id: task_24
title: "Fix company selection and global state initialization"
status: completed
assigned_to: dashboard_dev_3
created_by: manager
company: Shazam
created_at: 2026-03-21T20:07:30.338365Z
completed_at: 2026-03-21T20:09:49.173913Z
updated_at: 2026-03-21T20:09:49.173907Z
---

## Description

Implement correct company selection and persistence logic. Ensure that when dashboard loads, a company is automatically selected and stored in global state. Update TopHeader and all components that depend on activeCompany to properly react to state changes.

ACs:
- Default company is automatically selected on dashboard load
- Selected company persists across page navigation
- All components receive and react to company selection state
- No undefined errors related to activeCompany

## Result

Everything looks solid. Here's the summary of what was fixed:

## Changes Summary

### Root Cause
`DashboardPage.vue` maintained its own local `companies` ref and `activeCompany` computed, completely disconnected from the global `useActiveCompany` singleton. `CompaniesPage.vue` also shadowed `loadCompanies` with a local function bypassing global state. No persistence meant company selection was lost on navigation.

### Files Modified (3 files)

**1. `src/composables/useActiveCompany.ts`** — Rebuilt with:
- **`localStorage` persistence** — selected company name saved to `shazam:activeCompany` key, restored on page load
- **Computed `activeCompany`** — derives from `selectedCompanyName` + `companies` list, with automatic fallback to first company
- **Request deduplication** — concurrent `loadCompanies()` calls share the same promise instead of firing multiple requests
- **Auto-selection logic** — if persisted name is stale (company deleted), auto-selects first available company

**2. `src/pages/DashboardPage.vue`** — Replaced local company state with global composable:
- Removed local `companies` ref and `activeCompany` computed
- Removed `fetchCompanies` import — now uses `loadCompanies` from `useActiveCompany()`
- `refreshCompanies()` delegates to composable's `loadCompanies()`
- `onMounted` calls `loadCompanies()` instead of `fetchCompanies()` directly

**3. `src/pages/CompaniesPage.vue`** — Fixed shadowed `loadCompanies`:
- Removed local `loadCompanies` function that called `fetchCompanies()` directly
- Now uses composable's `loadCompanies` wrapped in `refreshCompanies()` for error handling
- Shares the same `companies` ref from the composable (no more local duplicate)

### How It Works Now
1. On first page load, `useActiveCompany` restores the last selected company name from `localStorage`
2. When `loadCompanies()` resolves, it checks if the persisted name still exists in the list
3. If yes → that company stays active. If no → first company auto-selected and persisted
4. All pages (Dashboard, Tasks, Agents, OrgChart, Companies, TopHeader) share the exact same reactive state
5. Selecting a company on any page immediately propagates everywhere
