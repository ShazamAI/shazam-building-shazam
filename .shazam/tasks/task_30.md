---
id: task_30
title: "Outra coisa, a company não deve ser selecionavel, deve ser carregada pelo shazam que está rodando atualmente, dessa forma mantemos o foco sempre no projeto atual, verifique isso"
status: completed
assigned_to: manager
created_by: human
company: Shazam
created_at: 2026-03-21T20:16:12.616994Z
completed_at: 2026-03-21T20:16:24.173794Z
updated_at: 2026-03-21T20:16:24.173790Z
---

## Description

Outra coisa, a company não deve ser selecionavel, deve ser carregada pelo shazam que está rodando atualmente, dessa forma mantemos o foco sempre no projeto atual, verifique isso

## Result

## Analysis

Mudança de requirements: A **company não deve ser selecionável manualmente**. Em vez disso, deve ser **carregada automaticamente** baseada na company que o Shazam está executando atualmente. Isso mantém o foco sempre no projeto/company ativo.

Arquitetura esperada:
- Company "atual" vem do backend/contexto do Shazam
- Frontend carrega automaticamente ao iniciar
- Sem dropdown/selector manual
- Todos os dados (tasks, agents, eventos) filtrados pela company atual

## Subtasks

```json
[
  {
    "title": "Remove manual company selection UI from dashboard",
    "description": "Remove the company selector dropdown/picker from the TopHeader and other UI components. Company should no longer be user-selectable. Clean up any company selection logic that's no longer needed.\n\nACs:\n- Company selector removed from TopHeader\n- No manual company selection UI visible\n- Cleaner, focused dashboard UI\n- Navigation no longer depends on selected company",
    "assigned_to": "dashboard_dev_1",
    "depends_on": null
  },
  {
    "title": "Implement automatic current company loading from backend",
    "description": "Implement mechanism to automatically load the current/active company from the Shazam backend when dashboard initializes. The backend should return which company is currently active, and the frontend should load this automatically without user interaction.\n\nACs:\n- Backend endpoint returns the currently active company\n- Frontend loads active company on app initialization\n- Active company is stored in global state\n- No user action required to set company\n- Company updates when Shazam switches contexts",
    "assigned_to": "dashboard_dev_2",
    "depends_on": null
  },
  {
    "title": "Update all dashboard pages to use automatically-loaded company",
    "description": "Verify that all dashboard pages (Tasks, Agents, OrgChart, Metrics, etc.) are using the automatically-loaded current company instead of allowing manual selection. Ensure data is always filtered by the active company and components render correctly.\n\nACs:\n- All pages use the current company from global state\n- Tasks/agents/metrics filtered by current company\n- No company selector logic in individual pages\n- Pages update correctly when company changes",
    "assigned_to": "dashboard_dev_3",
    "depends_on": null
  }
]
```

Isso alinha o dashboard com o modelo de foco do Shazam: sempre operando na company/projeto atualmente ativo.
