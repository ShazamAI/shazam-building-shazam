---
id: task_79
title: "Replace all dashboard buttons with standardized Button component"
status: completed
assigned_to: dashboard_dev_3
created_by: manager
company: Shazam
created_at: 2026-03-21T20:50:00.134213Z
completed_at: 2026-03-21T20:52:47.475235Z
updated_at: 2026-03-21T20:52:47.475232Z
---

## Description

Update all pages and components to use the new standardized Button component. Replace inline button styling with the reusable component. Verify that Add Task, Add Agent, and all other buttons now follow the same size, color, and styling patterns throughout the dashboard.

ACs:
- All buttons use the standardized Button component
- Add Task, Add Agent buttons match other button styling
- Consistent button sizes across all pages
- Consistent button colors matching dashboard theme
- No inline button styles remain in components

## Result

Now I have a complete picture. This is a large task touching 8+ files. Let me break it into parallel subtasks:

```subtasks
[
  {
    "title": "Create AppButton component",
    "description": "Create src/components/common/AppButton.vue — a standardized Vue 3 Button component with variants (primary, secondary, danger, ghost, success, warning, info), sizes (xs, sm, md), disabled/loading support, icon slot. Also add btn-success, btn-warning, btn-info to main.css and a btn-xs size class. Use existing CSS patterns from main.css as reference. The component should use <script setup lang=\"ts\"> with TypeScript strict typing.\n\nVariant classes:\n- primary: bg-shazam-500 text-gray-950 hover:bg-shazam-400 focus:ring-shazam-400\n- secondary: border border-gray-700 bg-transparent text-gray-300 hover:bg-gray-800 focus:ring-gray-500\n- danger: bg-red-600/20 text-red-300 hover:bg-red-600/30 focus:ring-red-500\n- ghost: bg-transparent text-gray-400 hover:bg-gray-800 hover:text-gray-200 focus:ring-gray-500\n- success: bg-emerald-600/20 text-emerald-300 hover:bg-emerald-600/30 focus:ring-emerald-500\n- warning: bg-yellow-600/20 text-yellow-300 hover:bg-yellow-600/30 focus:ring-yellow-500\n- info: bg-purple-600/20 text-purple-300 hover:bg-purple-600/30 focus:ring-purple-500\n\nSize classes:\n- xs: px-2 py-1 text-[10px]\n- sm: px-2.5 py-1.5 text-xs\n- md: px-4 py-2 text-sm\n\nBase: inline-flex items-center justify-center gap-1.5 rounded-lg font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-offset-surface disabled:cursor-not-allowed disabled:opacity-50\n\nLoading state: show a small spinner SVG before slot content. Props: variant, size, disabled, loading, type (button/submit/reset), block (boolean for w-full).\n\nFile location: /Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/components/common/AppButton.vue",
    "assigned_to": "dashboard_dev_3",
    "depends_on": null
  },
  {
    "title": "Replace buttons in TasksPage",
    "description": "Replace inline button styles in /Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/pages/TasksPage.vue with the AppButton component (import from '@/components/common/AppButton.vue').\n\nButtons to replace:\n1. Header 'Approve All' button (line ~419): <AppButton variant=\"secondary\" size=\"sm\" @click=\"handleApproveAll\">Approve All ({{ statusCounts.awaiting_approval }})</AppButton>\n2. Header '+ New Task' toggle (line ~426): <AppButton variant=\"primary\" size=\"sm\" @click=\"showCreateForm = !showCreateForm\">{{ showCreateForm ? 'Cancel' : '+ New Task' }}</AppButton>\n3. Form 'Cancel' (line ~493): <AppButton variant=\"secondary\" size=\"sm\" type=\"button\" @click=\"resetCreateForm\">Cancel</AppButton>\n4. Form 'Create Task' submit (line ~494): <AppButton variant=\"primary\" size=\"sm\" type=\"submit\" :disabled=\"isCreating || !createForm.title.trim() || !activeCompany\">{{ isCreating ? 'Creating...' : 'Create Task' }}</AppButton>\n5. Error 'Retry' (line ~551): <AppButton variant=\"primary\" @click=\"loadTasks()\">Retry</AppButton>\n6. Desktop table inline actions (approve/reject/pause/resume/retry/delete at lines ~629-681): Use <AppButton size=\"xs\" variant=\"success/danger/warning/primary/info/ghost\"> for each. Keep the :disabled and @click bindings. For delete button use variant=\"ghost\" since it has the gray-to-red hover pattern.\n7. Mobile card actions (lines ~715-744): Same pattern as desktop but with size=\"sm\".\n8. Detail panel actions (lines ~895-941): Same as mobile with size=\"sm\".\n9. Close detail panel button (line ~825): Leave as-is (it's btn-ghost with icon only, structural).\n10. Pagination buttons: Leave as-is (custom pagination UI).\n11. Entity-link buttons: Leave as-is.\n12. Stat card filter buttons: Leave as-is.\n\nDO NOT change structural buttons (pagination, stat cards, entity links). Only change action buttons.\nDO NOT write tests. Add the import: import AppButton from '@/components/common/AppButton.vue';",
    "assigned_to": "dashboard_dev_3",
    "depends_on": ["Create AppButton component"]
  },
  {
    "title": "Replace buttons in AgentsPage",
    "description": "Replace inline button styles in /Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/pages/AgentsPage.vue with the AppButton component (import from '@/components/common/AppButton.vue').\n\nButtons to replace:\n1. Header '+ Add Agent' (line ~447): <AppButton variant=\"primary\" size=\"sm\" @click=\"openCreateForm\">+ Add Agent</AppButton>\n2. Empty state 'Add Agent' (line ~464): <AppButton variant=\"primary\" @click=\"openCreateForm\">Add Agent</AppButton>\n3. Card 'Edit Agent' (line ~562): <AppButton variant=\"ghost\" size=\"sm\" block @click=\"openEditForm(agent)\">Edit Agent</AppButton> — needs block/w-full, and style: mt-3 w-full rounded-md bg-gray-800 py-1.5 text-xs → use ghost variant with block prop\n4. Form submit (line ~729): <AppButton variant=\"primary\" type=\"submit\" :disabled=\"isSubmitting || !formData.name.trim()\">{{ isSubmitting ? 'Saving...' : showEditForm ? 'Update Agent' : 'Create Agent' }}</AppButton>\n5. Form cancel (line ~736): <AppButton variant=\"secondary\" type=\"button\" @click=\"closeForm\">Cancel</AppButton>\n\nDO NOT replace:\n- Close modal × button (line ~579): structural icon-only button\n- Tool multi-select chips (line ~699): toggle UI, not action buttons\n\nAdd the import: import AppButton from '@/components/common/AppButton.vue';",
    "assigned_to": "dashboard_dev_3",
    "depends_on": ["Create AppButton component"]
  },
  {
    "title": "Replace buttons in remaining pages and components",
    "description": "Replace inline button styles with AppButton component across these files:\n\n**1. ConfigPage.vue** (/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/pages/ConfigPage.vue)\n- Error state 'Retry' (line ~189): <AppButton variant=\"primary\" @click=\"loadConfig\">Retry</AppButton>\n- 'Save Changes' (line ~305): <AppButton variant=\"primary\" :disabled=\"isSaving\" @click=\"saveRalphLoopConfig\">{{ isSaving ? 'Saving...' : 'Save Changes' }}</AppButton>\n- 'Reload Plugins' (line ~546): <AppButton variant=\"secondary\" size=\"sm\" :disabled=\"isReloadingPlugins\" @click=\"handleReloadPlugins\">{{ isReloadingPlugins ? 'Reloading...' : 'Reload Plugins' }}</AppButton>\n- Workspace 'Switch' (line ~671): <AppButton variant=\"secondary\" size=\"sm\" :disabled=\"switchingTo === ws.name\" @click=\"handleSwitchWorkspace(ws.name)\">{{ switchingTo === ws.name ? 'Switching...' : 'Switch' }}</AppButton>\n- DO NOT replace toggle switches (they're custom toggle UI) or tab buttons (custom tab UI)\n\n**2. SessionsPage.vue** (/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/pages/SessionsPage.vue)\n- Error 'Retry' (line ~68): <AppButton variant=\"primary\" @click=\"loadPool\">Retry</AppButton>\n- 'Refresh' (line ~94): <AppButton variant=\"secondary\" size=\"sm\" @click=\"loadPool\">Refresh</AppButton>\n\n**3. MemoryBrowserPage.vue** (/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/pages/MemoryBrowserPage.vue)\n- 'Expand All' (line ~122): <AppButton variant=\"secondary\" size=\"sm\" @click=\"expandAll\">Expand All</AppButton>\n- 'Collapse All' (line ~128): <AppButton variant=\"secondary\" size=\"sm\" @click=\"collapseAll\">Collapse All</AppButton>\n\n**4. StatusBar.vue** (/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/components/features/StatusBar.vue)\n- 'Approve All' (line ~90): <AppButton variant=\"info\" size=\"sm\" @click=\"emit('approveAll')\"> with SVG icon in icon slot\n- 'Resume' (line ~100): <AppButton variant=\"success\" size=\"sm\" @click=\"emit('resume')\"> with SVG icon\n- 'Stop' (line ~110): <AppButton variant=\"danger\" size=\"sm\" @click=\"emit('stop')\"> with SVG icon\n- 'Start' (line ~121): <AppButton variant=\"primary\" size=\"sm\" @click=\"emit('start')\"> with SVG icon\n\n**5. EventFeed.vue** (/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/components/features/EventFeed.vue)\n- 'Jump to latest' (line ~113): <AppButton variant=\"primary\" size=\"xs\" @click=\"scrollToBottom\"> with SVG icon\n\nFor ALL files: Add import AppButton from '@/components/common/AppButton.vue';\nDO NOT replace structural buttons (hamburger, sidebar collapse, tree node buttons, toast dismiss, error dismiss).\nDO NOT write tests.",
    "assigned_to": "dashboard_dev_3",
    "depends_on": ["Create AppButton component"]
  }
]
```
