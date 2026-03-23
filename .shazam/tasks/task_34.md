---
id: task_34
title: "Estamos com alguns erros também, quando as events no console do dashboard"
status: completed
assigned_to: manager
created_by: human
company: Shazam
created_at: 2026-03-21T20:20:35.072780Z
completed_at: 2026-03-21T20:20:50.884602Z
updated_at: 2026-03-21T20:20:50.884599Z
---

## Description

Estamos com alguns erros também, quando as events no console do dashboard 
```
useWebSocket.ts:13 [WS] Message #16 has invalid structure — skipping {"type":"tool_use","event":"agent_output","agent":"dashboard_dev_3","content":"Grep: %{\"output_mode\" => \"content\", \"path\" => \"/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/pages\wsWarn @ useWebSocket.ts:13socket.onmessage @ useWebSocket.ts:97useWebSocket.ts:13 [WS] Message #17 has invalid structure — skipping {"type":"text","event":"agent_output","agent":"dashboard_dev_3","content":"Only CompaniesPage uses `selectCompany` — that's correct, since it's the companies management page where users explicitly chowsWarn @ useWebSocket.ts:13socket.onmessage @ useWebSocket.ts:97useWebSocket.ts:13 [WS] Message #18 has invalid structure — skipping {"type":"text","event":"agent_output","agent":"dashboard_dev_3","content":"Tokens: 9613 (in: 18, out: 9595) | Cost: $4.2442"}wsWarn @ useWebSocket.ts:13socket.onmessage @ useWebSocket.ts:97useWebSocket.ts:13 [WS] Message #19 has invalid structure — skipping {"type":"result","event":"agent_output","agent":"dashboard_dev_3","content":"Completed"}wsWarn @ useWebSocket.ts:13socket.onmessage @ useWebSocket.ts:97useWebSocket.ts:13 [WS] Message #20 has invalid structure — skipping {"event":"task_completed","task":{"id":"task_33","status":"completed","title":"Update all dashboard pages to use automatically-loaded company","assigned_to":"dashboard_dev_3","created_by":"manager"}}wsWarn @ useWebSocket.ts:13socket.onmessage @ useWebSocket.ts:97useWebSocket.ts:13 [WS] Message #21 has invalid structure — skipping {"event":"metrics_updated"}wsWarn @ useWebSocket.ts:13socket.onmessage @ useWebSocket.ts:97useWebSocket.ts:13 [WS] Message #22 has invalid structure — skipping {"event":"task_completed","company":"Shazam","agent":"dashboard_dev_3","task_id":"task_33"}
```

## Result

## Analysis

**WebSocket message validation está muito rigorosa**: O frontend está rejeitando mensagens válidas do backend porque não correspondem à estrutura esperada.

Mensagens sendo rejeitadas:
- `{"type":"tool_use","event":"agent_output",...}` — output de agents
- `{"type":"text","event":"agent_output",...}` — texto simples
- `{"event":"task_completed",...}` — eventos de tasks
- `{"event":"metrics_updated"}` — atualizações de métricas

**Problema**: O validador em `useWebSocket.ts:13` está muito restritivo. Precisa aceitar estes formatos do backend.

## Subtasks

```json
[
  {
    "title": "Document and specify WebSocket message format contract",
    "description": "Define the exact message formats that should be accepted by the dashboard WebSocket handler. Document which message types come from the backend (agent_output, task_completed, metrics_updated, etc.) and their expected structure. This will be the source of truth for message validation.\n\nACs:\n- Document all valid WebSocket message types\n- Specify required and optional fields for each type\n- List examples of each message format from actual backend\n- Include any variants or alternate structures",
    "assigned_to": "dashboard_dev_1",
    "depends_on": null
  },
  {
    "title": "Fix WebSocket message validation in useWebSocket.ts",
    "description": "Update the message validation logic in useWebSocket.ts to accept all valid message formats from the backend. Currently rejecting valid messages like agent_output, task_completed, and metrics_updated events. Make the validator more flexible to handle the actual message structures the backend sends.\n\nACs:\n- No more \"invalid structure\" warnings for valid backend messages\n- agent_output events are processed correctly\n- task_completed events are processed correctly\n- metrics_updated events are processed correctly\n- Console shows messages being accepted and processed",
    "assigned_to": "dashboard_dev_2",
    "depends_on": null
  },
  {
    "title": "Ensure WebSocket events are processed and displayed",
    "description": "After fixing message validation, verify that WebSocket events are properly processed and displayed in the dashboard (event feed, task updates, metrics). Events should flow from backend → WebSocket → UI components without getting rejected.\n\nACs:\n- agent_output messages appear in event feed\n- task_completed events update task status in real-time\n- metrics updates reflect in metrics dashboard\n- No rejected messages in console\n- Real-time updates working end-to-end",
    "assigned_to": "dashboard_dev_3",
    "depends_on": null
  }
]
```

O foco é aceitar os formatos reais que o backend envia, em vez de rejeitar tudo que não corresponde a uma estrutura esperada muito específica.
