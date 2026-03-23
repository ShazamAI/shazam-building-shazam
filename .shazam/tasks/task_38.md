---
id: task_38
title: "Remova a possibilidade do usuário selecioar a company, deve sempre auto selecionar a company que o shazam está rodando"
status: completed
assigned_to: manager
created_by: human
company: Shazam
created_at: 2026-03-21T20:21:02.032913Z
completed_at: 2026-03-21T20:21:14.403628Z
updated_at: 2026-03-21T20:21:14.403624Z
---

## Description

Remova a possibilidade do usuário selecioar a company, deve sempre auto selecionar a company que o shazam está rodando

## Result

## Analysis

O usuário está reforçando uma mudança crítica já identificada. A company **não deve ser selecionável**. Deve ser **auto-carregada** sempre baseada na company que o Shazam está executando atualmente.

Escopo claro:
1. **Remover selector/dropdown** de company do UI
2. **Auto-load** a company atual ao iniciar
3. **Sem intervenção do usuário**

## Subtasks

```json
[
  {
    "title": "Remove company selector from TopHeader and all UI",
    "description": "Remove all manual company selection UI components from the dashboard. This includes the company dropdown in TopHeader, any company picker modal, and any selectCompany() logic that allows users to manually switch companies. The company should be determined automatically by the backend.\n\nACs:\n- Company dropdown removed from TopHeader\n- No company picker visible anywhere in dashboard\n- selectCompany() calls removed or disabled\n- TopHeader shows current company as read-only display only\n- No UI elements allow company switching",
    "assigned_to": "dashboard_dev_1",
    "depends_on": null
  },
  {
    "title": "Load current company automatically on dashboard startup",
    "description": "Implement automatic company loading from the backend when the dashboard initializes. Query the backend for the currently active company (the one Shazam is running) and set it as the active company in the global state. This should happen automatically without user interaction or selection.\n\nACs:\n- On app load, fetch current company from backend\n- Active company is set in global state automatically\n- No user action needed to set company\n- Dashboard always reflects the company Shazam is currently running\n- Company updates if Shazam switches to different company",
    "assigned_to": "dashboard_dev_2",
    "depends_on": null
  },
  {
    "title": "Verify all pages use auto-loaded company and remove selection logic",
    "description": "Audit all dashboard pages to ensure they are using the automatically-loaded current company from global state. Remove any remaining selectCompany() calls or manual company selection logic. Verify that CompaniesPage is the only place where company selection/creation happens.\n\nACs:\n- No selectCompany() calls in task/agent/metrics pages\n- All pages use activeCompany from global state\n- CompaniesPage is the only place with company management\n- Data always filtered by auto-loaded company\n- No console errors related to company selection",
    "assigned_to": "dashboard_dev_3",
    "depends_on": null
  }
]
```

**Prioridade**: ALTA — isso é fundamental para manter o foco no projeto atual.
