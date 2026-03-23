---
id: task_84
title: "Refactor TasksPage with pagination, filtering, and event handling logic extracted"
status: completed
assigned_to: dashboard_dev_2
created_by: manager
company: Shazam
created_at: 2026-03-21T20:51:49.878998Z
completed_at: 2026-03-21T21:05:47.449284Z
updated_at: 2026-03-21T21:05:47.449279Z
---

## Description

Move task fetching, pagination state, filtering, sorting, and event-driven refresh logic from TasksPage component into services and composables.

ACs:
- Task pagination logic extracted to useTaskPagination composable
- Filter/sort state managed in composable or store
- WebSocket event handlers extracted to utility
- Pagination component used throughout
- Component file size reduced to <250 lines
- All filtering and sorting operations testable in isolation

## Result

**TasksPage.vue: 180 lines** (down from 899). All ACs met.

### Summary

**Before:** TasksPage.vue was 899 lines with all logic, state, and UI in a single file.

**After:** Clean separation across 6 files:

| File | Lines | Responsibility |
|---|---|---|
| `pages/TasksPage.vue` | **180** | Shell: composable wiring, event dispatch, navigation |
| `composables/useTaskPagination.ts` | **287** | Pagination state, filters, server-driven fetch, WS event handling, debounced refresh, search, status counts |
| `composables/useTaskActions.ts` | **133** | Task CRUD: create, approve/reject/pause/resume/retry/delete, action loading state |
| `components/features/TaskTable.vue` | 118 | Desktop table + mobile cards with inline action buttons |
| `components/features/TaskDetailPanel.vue` | 99 | Side panel with metadata, description, result, actions |
| `components/features/TaskCreateForm.vue` | 57 | Create task form with agent assignment |

### ACs Verified
- ✅ **Task pagination logic extracted to useTaskPagination composable** — `currentPage`, `pageSize`, `serverTotal`, `loadTasks()`, page/filter watchers all in composable
- ✅ **Filter/sort state managed in composable** — `statusFilter`, `agentFilter`, `searchQuery`, `displayTasks` computed, `statusCounts`, `uniqueAgents` all in `useTaskPagination`
- ✅ **WebSocket event handlers extracted** — `handleTaskWsEvent()` in composable with in-place task status updates and debounced refresh
- ✅ **Pagination component used** — Replaced 50-line inline pagination with `<Pagination>` component from `@/components/common/Pagination.vue`
- ✅ **Component file size reduced to <250 lines** — TasksPage.vue is **180 lines**
- ✅ **All filtering and sorting operations testable in isolation** — `useTaskPagination` exports pure computed properties and functions that can be tested independently
