---
id: task_26
title: "Bacana, temos alguns erros no console como"
status: completed
assigned_to: manager
created_by: human
company: Shazam
created_at: 2026-03-21T20:14:52.571213Z
completed_at: 2026-03-21T20:15:04.121491Z
updated_at: 2026-03-21T20:15:04.121488Z
---

## Description

Bacana, temos alguns erros no console como 
```
http.ts:25  GET http://localhost:3000/api/events/recent 404 (Not Found)request	@	http.ts:25get	@	http.ts:74fetchRecentEvents	@	eventService.ts:6(anonymous)	@	DashboardPage.vue:137
```

## Result

## Analysis

O erro persiste: **`GET http://localhost:3000/api/events/recent 404 (Not Found)`**

Este é o endpoint que falta no backend. Deve ser implementado no lado Elixir para que o DashboardPage possa carregar eventos recentes.

## Subtask

```json
[
  {
    "title": "Implement /api/events/recent endpoint in backend",
    "description": "The backend endpoint GET /api/events/recent is missing and returning 404. Implement this endpoint in the Elixir backend to return recent events from the event bus/system log. The endpoint should return events in JSON format compatible with the frontend's eventService.ts expectations.\n\nACs:\n- Endpoint returns HTTP 200 with valid event data\n- Response format matches what eventService.ts expects (array or wrapped object)\n- Endpoint returns recent events (last N events from EventBus)\n- No 404 errors when DashboardPage fetches recent events\n- Response includes event metadata (timestamp, type, status, etc.)",
    "assigned_to": "dashboard_dev_1",
    "depends_on": null
  }
]
```

**Prioridade**: ALTA — este é um blocker para o carregamento do dashboard.

Após esta tarefa ser concluída, o erro de 404 deve desaparecer e o EventFeed deve começar a mostrar dados.
