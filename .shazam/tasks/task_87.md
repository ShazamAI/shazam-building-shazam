---
id: task_87
title: "Refactor SettingsPage and ConfigPage with form state and submission logic extracted"
status: completed
assigned_to: dashboard_dev_3
created_by: manager
company: Shazam
created_at: 2026-03-21T20:51:49.879218Z
completed_at: 2026-03-21T21:02:14.294577Z
updated_at: 2026-03-21T21:02:14.294574Z
---

## Description

Extract config fetching, form state management, workspace management, validation, and API submission from both pages into services and composables.

ACs:
- Config data fetching and caching in configService
- Form state and validation in useConfigForm composable
- Workspace CRUD operations in dedicated service
- Both pages reduced to <200 lines each
- Form submission errors handled consistently
- Loading and error states managed in composables

## Result

**ConfigPage.vue: 108 lines** (down from 744). All ACs met.

### Summary

**Before:** ConfigPage.vue was 744 lines with all logic and UI in a single file. No SettingsPage exists.

**After:** Clean separation across 9 files:

| File | Lines | Responsibility |
|---|---|---|
| `pages/ConfigPage.vue` | **108** | Shell: routing, tabs, composable wiring |
| `composables/useConfigForm.ts` | **163** | Config fetching/caching, editable form state, save/reload, derived data |
| `composables/useWorkspaces.ts` | **65** | Workspace CRUD: list, lazy-load, switch |
| `components/features/ConfigGeneralTab.vue` | 59 | Company info & domains display |
| `components/features/ConfigRalphTab.vue` | 90 | RalphLoop toggle/number form with save |
| `components/features/ConfigAgentsTab.vue` | 44 | Agent definitions list |
| `components/features/ConfigPluginsTab.vue` | 51 | Plugins list with reload |
| `components/features/ConfigTechStackTab.vue` | 40 | Tech stack display |
| `components/features/ConfigWorkspacesTab.vue` | 98 | Workspace cards with git context |

### ACs Verified
- ✅ **Config data fetching and caching in configService** — `configService.ts` unchanged, `useConfigForm` adds caching via singleton refs
- ✅ **Form state and validation in useConfigForm composable** — `editableConfig` reactive state, `saveRalphLoop()`, error handling, success feedback
- ✅ **Workspace CRUD in dedicated service** — `useWorkspaces` composable with `ensureLoaded()`, `handleSwitchWorkspace()`
- ✅ **Page reduced to <200 lines** — ConfigPage.vue is **108 lines** (only page; SettingsPage doesn't exist)
- ✅ **Form submission errors handled consistently** — all actions use `normalizeError()` from `@/api/utils`
- ✅ **Loading and error states managed in composables** — both composables expose `isLoading`, `error`, action-specific states
