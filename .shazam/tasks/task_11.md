---
id: task_11
title: "Build Configuration and Workspace Management pages"
status: completed
assigned_to: dashboard_dev_2
created_by: pm_dashboard
company: Shazam
created_at: 2026-03-21T15:14:04.058819Z
completed_at: 2026-03-21T15:23:58.024232Z
updated_at: 2026-03-21T15:23:58.024228Z
---

## Description

Build the Configuration display/editing page and the Workspace management interface.

**Configuration page:**
- Display current shazam.yaml configuration in a structured, readable format (not raw YAML)
- Organized sections: Company info, Provider settings, RalphLoop config (auto_approve, max_concurrent, poll_interval, module_lock, peer_reassign, max_retries, context settings), Domain definitions, Tech stack
- Editable fields for RalphLoop config: auto_approve toggle, max_concurrent number input, poll_interval, module_lock toggle, peer_reassign toggle, auto_retry toggle, max_retries, context_history, team_activity, context_budget
- Plugin management section: list loaded plugins with enabled/disabled status, event subscriptions, reload button
- Save/apply changes functionality

**Workspace page:**
- Display configured workspaces with name, path, and associated domains
- Git context per workspace: current branch, clean/dirty status, recent commits, modified files
- Workspace switching capability (select active workspace)
- Domain listing per workspace with associated paths

**Additional views:**
- Memory/Context browser: display agent learnings, topic files, and skill memories in a navigable tree
- Session pool status: show active Claude sessions per agent, idle time, task count

ACs:
- Configuration page displays all shazam.yaml settings in organized sections
- RalphLoop config fields are editable with appropriate input types (toggles, number inputs)
- Plugin list shows name, enabled status, and subscribed events
- Workspace page displays all configured workspaces with their git context
- Memory browser shows a navigable tree of context files and skill memories
- All configuration changes can be saved/applied

## Result

Build successful with zero errors. Here's a summary of everything implemented:

## Implementation Summary

### New Files Created
| File | Purpose |
|------|---------|
| `src/api/configService.ts` | API service for config, workspace, memory tree, and session pool endpoints |
| `src/pages/WorkspacesPage.vue` | Workspace management with git context, switching, domains |
| `src/pages/MemoryBrowserPage.vue` | Navigable tree browser for context files and skill memories |
| `src/pages/SessionsPage.vue` | Session pool status showing active Claude sessions per agent |

### Modified Files
| File | Changes |
|------|---------|
| `src/types/index.ts` | Added types: `ShazamConfig`, `RalphLoopConfig`, `PluginConfig`, `DomainConfig`, `WorkspaceConfig`, `AgentYamlConfig`, `WorkspaceInfo`, `GitContext`, `GitCommit`, `MemoryTreeNode`, `MemoryFileContent` |
| `src/pages/ConfigPage.vue` | Full rewrite with 5 tabs: Company & Provider, RalphLoop Config (editable toggles/inputs), Agents, Plugins (with reload), Tech Stack |
| `src/router/index.ts` | Added routes for `/workspaces`, `/memory`, `/sessions` |
| `src/components/layouts/SidebarNav.vue` | Added icons for workspaces, memory, sessions |
| `src/api/index.ts` | Added configService export |

### Acceptance Criteria Met
- **Configuration page displays all shazam.yaml settings in organized sections** (5 tabs: General, RalphLoop, Agents, Plugins, Tech Stack)
- **RalphLoop config fields are editable** with toggles (auto_approve, module_lock, peer_reassign, auto_retry) and number inputs (max_concurrent, max_retries, poll_interval, context_history, team_activity, context_budget)
- **Plugin list shows name, enabled status, and subscribed events** with reload button
- **Workspace page displays all configured workspaces with git context** (branch, clean/dirty, modified files, recent commits, domains, switch capability)
- **Memory browser shows navigable tree** of context files and skill memories with file viewer
- **Session pool status** shows active sessions per agent with idle time and task count
- **All configuration changes can be saved/applied** via Save Changes button
