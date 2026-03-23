---
id: task_28
title: "Outros erros como"
status: completed
assigned_to: manager
created_by: human
company: Shazam
created_at: 2026-03-21T20:15:05.693312Z
completed_at: 2026-03-21T20:15:16.606492Z
updated_at: 2026-03-21T20:15:16.606487Z
---

## Description

Outros erros como 
```
http.ts:25  GET http://localhost:3000/api/companies/Petgenda/agents 500 (Internal Server Error)useEventFeed.ts:7 [EventFeed] useEventFeed composable initializeduseWebSocket.ts:8 [WS] Listener registered for "*" (1 total)useWebSocket.ts:8 [WS] Listener removed for "circuit_breaker_tripped"useWebSocket.ts:8 [WS] Listener removed for "circuit_breaker_reset"
```

## Result

## Analysis

Novo erro de backend: **`GET http://localhost:3000/api/companies/Petgenda/agents 500 (Internal Server Error)`**

Um erro **500** indica que o backend está lançando uma exceção ao tentar buscar agentes da company "Petgenda". Pode ser:
- Bug na lógica do endpoint
- Dados faltando ou malformados
- Query ao banco de dados falhando
- Tratamento de erro inadequado

## Subtask

```json
[
  {
    "title": "Fix backend 500 error on /api/companies/{company}/agents endpoint",
    "description": "The endpoint GET /api/companies/Petgenda/agents is returning HTTP 500 Internal Server Error. Investigate the Elixir backend code for this endpoint to identify the root cause of the exception. Fix the underlying issue (missing data handling, query errors, etc.) and ensure the endpoint returns a valid response with the list of agents for the specified company.\n\nACs:\n- Endpoint returns HTTP 200 instead of 500\n- Response includes all agents for the specified company\n- Response format matches frontend expectations\n- Error handling is in place for invalid company names\n- No unhandled exceptions in backend logs when calling this endpoint",
    "assigned_to": "dashboard_dev_2",
    "depends_on": null
  }
]
```

**Nota**: Este erro está bloqueando o carregamento de agentes no dashboard. Deve ser resolvido antes que a página de Agentes funcione corretamente.
