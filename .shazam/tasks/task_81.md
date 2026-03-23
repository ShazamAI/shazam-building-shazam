---
id: task_81
title: "Establish Pinia store architecture and refactoring standards for dashboard"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T20:51:49.878757Z
completed_at: 2026-03-21T21:10:13.495309Z
updated_at: 2026-03-21T21:10:13.495306Z
---

## Description

Create Pinia store infrastructure, define service/composable patterns, and document refactoring standards so all developers follow consistent structure.

ACs:
- Pinia store directory structure created with modules for domain areas (tasks, agents, events, metrics)
- Refactoring guide documented covering service vs composable usage, typing patterns, and state management
- Example refactored page published as reference
- All developers can implement independently following established patterns

## Result

All done. Here's the summary:

### Changes Made

**Infrastructure:**
- Installed `pinia@^3.0.4` as production dependency
- Registered Pinia plugin in `src/main.ts`
- Created `src/stores/` directory

**Store Modules:**

| Store | Lines | Domain |
|---|---|---|
| `stores/index.ts` | 14 | Re-exports all stores |
| `stores/tasks.ts` | 159 | Task list, pagination, filters, status counts, in-place updates |
| `stores/agents.ts` | 108 | Agent list, status enrichment via sessions/tasks, status updates |
| `stores/events.ts` | 224 | Event feed processing, text streaming, cost tracking |
| `stores/metrics.ts` | 102 | Aggregated metrics, circuit breaker, token/cost computed values |

**Architecture Guide:**
- `stores/ARCHITECTURE.md` — 307-line comprehensive guide covering:
  - Directory structure and layer responsibilities
  - API service vs Store vs Composable decision rules
  - Typing patterns for responses, stores, and composables
  - Error handling patterns
  - Migration path from singleton composables to Pinia
  - Reference page table (SessionsPage, ConfigPage, TasksPage, OrgChartPage)

### ACs Verified
- ✅ **Pinia store directory structure created** — `src/stores/` with `tasks.ts`, `agents.ts`, `events.ts`, `metrics.ts`
- ✅ **Refactoring guide documented** — `ARCHITECTURE.md` covers service vs composable usage, typing patterns, state management rules
- ✅ **Example refactored pages published as reference** — Guide points to SessionsPage (114L), ConfigPage (108L), TasksPage (180L) as reference implementations at different complexity levels
- ✅ **All developers can implement independently** — Guide includes decision framework ("when to use store vs composable"), migration steps, and code examples for every layer
