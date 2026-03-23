---
id: task_80
title: "Preciso de uma refatoração completa do código em Vue do dashboard, todas as chamadas, ações que acontecem na UI, devem ser separadas para services/composables em todas as telas para o código ficar legível e mais bonito para quem for criar alguma coisa, atualmente está um código tudo junto em várias telas, arquivos contendo mais de 700 linhas, faça isso para todas as telas, separe com composables, services, caso necessário faça o uso do Pinia aqui para melhorar também"
status: completed
assigned_to: manager
created_by: human
company: Shazam
created_at: 2026-03-21T20:51:23.964399Z
completed_at: 2026-03-21T20:51:49.878556Z
updated_at: 2026-03-21T20:51:49.878551Z
---

## Description

Preciso de uma refatoração completa do código em Vue do dashboard, todas as chamadas, ações que acontecem na UI, devem ser separadas para services/composables em todas as telas para o código ficar legível e mais bonito para quem for criar alguma coisa, atualmente está um código tudo junto em várias telas, arquivos contendo mais de 700 linhas, faça isso para todas as telas, separe com composables, services, caso necessário faça o uso do Pinia aqui para melhorar também

## Result

Looking at your request, this is a significant Vue dashboard refactoring task. I need to break this into manageable sub-tasks and delegate to your dashboard team to maximize parallelism.

**One-line Summary:** Complete refactoring of all dashboard pages from monolithic components to service/composable architecture with Pinia state management.

```json
[
  {
    "title": "Establish Pinia store architecture and refactoring standards for dashboard",
    "description": "Create Pinia store infrastructure, define service/composable patterns, and document refactoring standards so all developers follow consistent structure.\n\nACs:\n- Pinia store directory structure created with modules for domain areas (tasks, agents, events, metrics)\n- Refactoring guide documented covering service vs composable usage, typing patterns, and state management\n- Example refactored page published as reference\n- All developers can implement independently following established patterns",
    "assigned_to": "dashboard_dev_1",
    "depends_on": null
  },
  {
    "title": "Refactor DashboardPage from monolithic component to service-driven architecture",
    "description": "Extract all business logic from DashboardPage into services and composables: task fetching, event loading, metrics calculation, WebSocket handling, and state management.\n\nACs:\n- All API calls extracted to dedicated services\n- State moved to Pinia store or composables\n- Component file size reduced from current >700 lines to <200 lines\n- Event feed, task overview, and cost calculations use separate, testable utilities\n- Production build succeeds with no console errors",
    "assigned_to": "dashboard_dev_1",
    "depends_on": null
  },
  {
    "title": "Refactor AgentsPage and OrgChartPage to use composables and services",
    "description": "Extract agent fetching, status tracking, session management, and hierarchical data transformation from components into services. Consolidate duplicate company selection logic across both pages.\n\nACs:\n- Agent data fetching extracted to agentService\n- Status inference logic moved to composable (useAgentStatus)\n- OrgChart hierarchy building extracted to utility\n- Duplicate company selection removed, unified in single composable\n- Both pages reduced to <250 lines each\n- Real-time status updates via WebSocket handled in composable",
    "assigned_to": "dashboard_dev_2",
    "depends_on": null
  },
  {
    "title": "Refactor TasksPage with pagination, filtering, and event handling logic extracted",
    "description": "Move task fetching, pagination state, filtering, sorting, and event-driven refresh logic from TasksPage component into services and composables.\n\nACs:\n- Task pagination logic extracted to useTaskPagination composable\n- Filter/sort state managed in composable or store\n- WebSocket event handlers extracted to utility\n- Pagination component used throughout\n- Component file size reduced to <250 lines\n- All filtering and sorting operations testable in isolation",
    "assigned_to": "dashboard_dev_2",
    "depends_on": null
  },
  {
    "title": "Refactor MetricsPage with chart rendering and data transformation logic extracted",
    "description": "Extract metrics fetching, chart data transformation, time period filtering, and company-aware filtering from MetricsPage into services and composables.\n\nACs:\n- Metrics data fetching in dedicated service\n- Chart data transformation extracted to utility functions\n- Time period and company filtering extracted to composables\n- Event-driven refresh logic extracted and reusable\n- Component size reduced to <200 lines\n- Chart rendering uses only props, no embedded logic",
    "assigned_to": "dashboard_dev_3",
    "depends_on": null
  },
  {
    "title": "Refactor MemoryBrowserPage with tree traversal and file loading logic extracted",
    "description": "Extract memory tree navigation, file loading, tree normalization, and recursive rendering logic from MemoryBrowserPage into services and composables.\n\nACs:\n- Memory file API calls extracted to memoryService\n- Tree traversal and filtering logic in useMemoryTree composable\n- Node expansion/collapse state managed in composable\n- MemoryTreeNode component uses only props\n- Component file size reduced to <200 lines\n- Error handling for malformed tree structures robust",
    "assigned_to": "dashboard_dev_3",
    "depends_on": null
  },
  {
    "title": "Refactor SettingsPage and ConfigPage with form state and submission logic extracted",
    "description": "Extract config fetching, form state management, workspace management, validation, and API submission from both pages into services and composables.\n\nACs:\n- Config data fetching and caching in configService\n- Form state and validation in useConfigForm composable\n- Workspace CRUD operations in dedicated service\n- Both pages reduced to <200 lines each\n- Form submission errors handled consistently\n- Loading and error states managed in composables",
    "assigned_to": "dashboard_dev_3",
    "depends_on": null
  },
  {
    "title": "Standardize error handling and loading states across all refactored services",
    "description": "Create shared error handling patterns, loading state utilities, and retry logic that all refactored pages use consistently.\n\nACs:\n- useAsyncState composable or pattern defined for consistent loading/error handling\n- Error messages standardized across all services\n- Retry logic centralized and reusable\n- All services use consistent response handling\n- TypeScript error boundaries properly typed",
    "assigned_to": "dashboard_dev_1",
    "depends_on": null
  },
  {
    "title": "Verify complete refactoring with production build and runtime validation",
    "description": "Comprehensive validation that all refactored pages work correctly: build succeeds, no TypeScript errors, all features functional, no console errors, and code is cleaner.\n\nACs:\n- Production build succeeds with zero errors and warnings\n- All dashboard pages load without runtime errors\n- All features work identically to before refactoring\n- Code maintainability improved (smaller files, clear separation of concerns)\n- No console errors or warnings during normal usage",
    "assigned_to": "dashboard_dev_1",
    "depends_on": null
  }
]
```

**Distribution Strategy:**
- **dashboard_dev_1**: Pinia setup + DashboardPage + error handling standardization + final verification (senior lead role)
- **dashboard_dev_2**: AgentsPage/OrgChartPage + TasksPage refactoring (parallel work)
- **dashboard_dev_3**: MetricsPage + MemoryBrowserPage + SettingsPage/ConfigPage (parallel work)

All tasks can run **in parallel** — developers are working on different pages with no cross-dependencies until final verification. The Pinia setup provides guardrails but doesn't block page-by-page refactoring.
