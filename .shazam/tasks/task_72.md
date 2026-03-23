---
id: task_72
title: "Tela de dashboard, não aparece P, R e D, e também dependendo do evento ele da um refresh na tela e perdemos todos os eventos, task overview não funciona, tudo fica zerado, a baixo do overview as tasks ficam zeradas também, o custo mostra incorreto."
status: completed
assigned_to: manager
created_by: human
company: Shazam
created_at: 2026-03-21T20:47:41.593277Z
completed_at: 2026-03-21T20:47:50.669362Z
updated_at: 2026-03-21T20:47:50.669353Z
---

## Description

Tela de dashboard, não aparece P, R e D, e também dependendo do evento ele da um refresh na tela e perdemos todos os eventos, task overview não funciona, tudo fica zerado, a baixo do overview as tasks ficam zeradas também, o custo mostra incorreto.

## Result

**One-line Summary:** Fix multiple DashboardPage issues—missing P/R/D display, unwanted page refreshes, zero task overview, and incorrect cost calculation.

```subtasks
[
  {
    "title": "Fix missing P, R, D display and page refresh issue on Dashboard",
    "description": "Investigate why P, R, and D components/values are not appearing on the Dashboard page. Also fix the issue where the page performs unwanted refreshes when certain events arrive, causing loss of visible event data. These could be related to event handling or component rendering problems.\n\nACs:\n- P, R, and D display properly on Dashboard (identify what these represent and show them)\n- Page does not refresh/reload when events arrive\n- Event data persists on page during normal operation\n- Console shows no errors related to event handling or rendering",
    "assigned_to": "dashboard_dev_1",
    "depends_on": null
  },
  {
    "title": "Fix Task Overview showing zero values on Dashboard",
    "description": "Debug why the Task Overview section displays zero or empty values instead of actual task metrics. Check data flow from API to component, state management, and calculation logic. Ensure overview correctly aggregates and displays task counts, status distribution, and other relevant metrics.\n\nACs:\n- Task Overview displays non-zero values when tasks exist\n- Metrics correctly calculate task counts by status\n- Overview updates when new tasks arrive or statuses change\n- No console errors related to overview calculations",
    "assigned_to": "dashboard_dev_2",
    "depends_on": null
  },
  {
    "title": "Fix tasks list and cost calculation display on Dashboard",
    "description": "Investigate and fix two related issues: (1) tasks displayed below the overview section are showing as zero/empty, (2) cost calculation shows incorrect values. Check API data, component rendering, cost calculation logic, and ensure both tasks list and cost metrics display accurate information from the backend.\n\nACs:\n- Tasks list below overview displays all available tasks with correct data\n- Cost calculation shows correct values (not zero or incorrect)\n- Both update correctly when new events arrive\n- No calculation or rendering errors in console",
    "assigned_to": "dashboard_dev_3",
    "depends_on": null
  }
]
```
