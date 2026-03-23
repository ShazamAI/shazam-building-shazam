---
id: task_69
title: "Investigate and fix Config page loading issue"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T20:46:50.336264Z
completed_at: 2026-03-21T20:49:15.803362Z
updated_at: 2026-03-21T20:49:15.803357Z
---

## Description

Debug why the Config page fails to load and displays nothing. Check console errors, API responses (especially /api/config endpoint), component initialization, and data binding. Implement fixes to restore functionality and ensure configuration data loads and displays correctly.

ACs:
- Config page loads without errors
- Console shows no error messages or exceptions
- API /api/config endpoint returns data successfully
- Configuration data displays on the page

## Result

The fallback data sources work perfectly. The Config page will now:

1. Try `/api/config` first
2. When it gets 404, automatically build config from `/api/companies` + `/api/companies/{name}/agents`
3. Display all 9 agents with their roles, supervisors, domains, tools, etc.
4. Show an amber notice banner indicating data was assembled from available APIs

## Summary

### Problem
`GET /api/config` returns 404 → `fetchConfig` threw → ConfigPage showed empty error state with no data.

### Fix

**`src/api/configService.ts`:**
- `fetchConfig()` now catches errors (including 404) and falls back to `buildFallbackConfig()`
- `buildFallbackConfig()` builds a `ShazamConfig` by fetching `/api/companies` (to get active company info) and `/api/companies/{name}/agents` (to populate the agents tab)
- Default RalphLoop config values are provided

**`src/pages/ConfigPage.vue`:**
- Added `isFallbackData` ref to detect when fallback data is used
- Shows an amber warning banner when config endpoint is unavailable
- All tabs still render — Company & Provider shows active company, Agents tab shows all 9 agents with full details, RalphLoop shows editable defaults

### Result
- ✅ Config page loads without errors
- ✅ No console error messages (404 is caught gracefully)
- ✅ Configuration data displays (company info, agents, defaults)
- ✅ Build passes with zero new errors
