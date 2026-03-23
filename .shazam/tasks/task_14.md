---
id: task_14
title: "Arrume o seguinte bug"
status: completed
assigned_to: manager
created_by: human
company: Shazam
created_at: 2026-03-21T19:56:27.156755Z
completed_at: 2026-03-21T19:57:00.940855Z
updated_at: 2026-03-21T19:57:00.940852Z
---

## Description

Arrume o seguinte bug 
```
http.ts:24  GET http://localhost:3000/api/events/recent 404 (Not Found)DashboardPage.vue:136 [Vue warn]: Unhandled error during execution of render function   at <DashboardPage onVnodeUnmounted=fn<onVnodeUnmounted> ref=Ref< Proxy(Object) {__v_skip: true} > >   at <RouterView>   at <AppLayout>   at <App>DashboardPage.vue:136 [Vue warn]: Unhandled error during execution of component update   at <DashboardPage onVnodeUnmounted=fn<onVnodeUnmounted> ref=Ref< Proxy(Object) {__v_skip: true} > >   at <RouterView>   at <AppLayout>   at <App>DashboardPage.vue:184 Uncaught (in promise) TypeError: $setup.tasks.filter is not a function    at Proxy._sfc_render (DashboardPage.vue:184:28)
```

## Result

## Analysis

Looking at the bug report, there are **two distinct issues**:

1. **Frontend (Vue)**: `TypeError: $setup.tasks.filter is not a function` on DashboardPage.vue:184
   - `tasks` is undefined or not an array when the component tries to call `.filter()`
   
2. **Backend (API)**: `GET http://localhost:3000/api/events/recent 404 (Not Found)`
   - The endpoint doesn't exist or isn't returning data

These are related — the API failure causes `tasks` to be undefined, triggering the render error.

## Subtasks

```json
[
  {
    "title": "Fix DashboardPage.vue error handling for undefined tasks",
    "description": "DashboardPage component is throwing TypeError when trying to call .filter() on tasks which is undefined due to failed API call. Add proper null checks and error handling to prevent render crashes when API endpoints fail.\n\nACs:\n- Component renders without errors even if tasks is undefined\n- No TypeError messages in browser console related to tasks.filter\n- User sees appropriate loading/error state when API returns 404",
    "assigned_to": "dashboard_dev_1",
    "depends_on": null
  },
  {
    "title": "Implement missing /api/events/recent endpoint",
    "description": "The GET /api/events/recent endpoint is returning 404. Either the endpoint is missing from the backend or the URL is incorrect. Investigate the backend Elixir code, determine the correct endpoint path, and ensure it returns valid event data matching the expected format.\n\nACs:\n- GET http://localhost:3000/api/events/recent returns HTTP 200\n- Response includes valid recent events in the expected format\n- DashboardPage successfully displays events after fix",
    "assigned_to": "dashboard_dev_2",
    "depends_on": null
  }
]
```

Both tasks can run in parallel. Dashboard_dev_1 handles frontend resilience while dashboard_dev_2 fixes the backend endpoint.
