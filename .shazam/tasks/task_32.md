---
id: task_32
title: "Implement automatic current company loading from backend"
status: completed
assigned_to: dashboard_dev_2
created_by: manager
company: Shazam
created_at: 2026-03-21T20:16:24.174036Z
completed_at: 2026-03-21T20:17:54.572925Z
updated_at: 2026-03-21T20:17:54.572919Z
---

## Description

Implement mechanism to automatically load the current/active company from the Shazam backend when dashboard initializes. The backend should return which company is currently active, and the frontend should load this automatically without user interaction.

ACs:
- Backend endpoint returns the currently active company
- Frontend loads active company on app initialization
- Active company is stored in global state
- No user action required to set company
- Company updates when Shazam switches contexts

## Result

Down from 6 errors to 3 — fixed all CompaniesPage unused variable warnings. Remaining 3 are pre-existing TasksPage type issues unrelated to this task.

## Summary

### Changes Made

**`src/composables/useActiveCompany.ts`** — Core fix
- Auto-selects the **backend-active company** (where `status === 'active'`) instead of blindly picking the first company
- Selection priority: current selection (if still valid) → backend active company → first company
- Removed `localStorage` persistence (backend is the source of truth for which company is active)
- Added `initialized` flag to track whether companies have been loaded
- Deduplicated concurrent `loadCompanies()` calls remain intact

**`src/App.vue`** — App-level initialization
- Calls `loadCompanies()` on mount, so the active company is loaded **before** any page renders
- Since `useActiveCompany` is a singleton (module-level state), this single call populates global state for all components

**`src/pages/CompaniesPage.vue`** — Cleanup
- Removed unused destructured variables (`activeCompany`, `companiesLoading`, `companiesError`) that caused TS warnings

### How It Works
1. App mounts → `App.vue` calls `loadCompanies()`
2. Backend returns `{ companies: [{ name: "Shazam", active: true }, ...] }`
3. `companyService.ts` maps `active: true` → `status: "active"`
4. `useActiveCompany` finds the company with `status === 'active'` and auto-selects it
5. All pages using `useActiveCompany()` get the active company immediately — no user interaction needed
6. `TopHeader` polls every 30s to pick up context switches
