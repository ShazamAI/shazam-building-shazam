---
id: task_93
title: "Replace buttons in remaining pages and components"
status: completed
assigned_to: dashboard_dev_3
created_by: dashboard_dev_3
company: Shazam
created_at: 2026-03-21T20:52:47.475537Z
completed_at: 2026-03-21T20:56:38.062793Z
updated_at: 2026-03-21T20:56:38.062789Z
---

## Description

Replace inline button styles with AppButton component across these files:

**1. ConfigPage.vue** (/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/pages/ConfigPage.vue)
- Error state 'Retry' (line ~189): <AppButton variant="primary" @click="loadConfig">Retry</AppButton>
- 'Save Changes' (line ~305): <AppButton variant="primary" :disabled="isSaving" @click="saveRalphLoopConfig">{{ isSaving ? 'Saving...' : 'Save Changes' }}</AppButton>
- 'Reload Plugins' (line ~546): <AppButton variant="secondary" size="sm" :disabled="isReloadingPlugins" @click="handleReloadPlugins">{{ isReloadingPlugins ? 'Reloading...' : 'Reload Plugins' }}</AppButton>
- Workspace 'Switch' (line ~671): <AppButton variant="secondary" size="sm" :disabled="switchingTo === ws.name" @click="handleSwitchWorkspace(ws.name)">{{ switchingTo === ws.name ? 'Switching...' : 'Switch' }}</AppButton>
- DO NOT replace toggle switches (they're custom toggle UI) or tab buttons (custom tab UI)

**2. SessionsPage.vue** (/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/pages/SessionsPage.vue)
- Error 'Retry' (line ~68): <AppButton variant="primary" @click="loadPool">Retry</AppButton>
- 'Refresh' (line ~94): <AppButton variant="secondary" size="sm" @click="loadPool">Refresh</AppButton>

**3. MemoryBrowserPage.vue** (/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/pages/MemoryBrowserPage.vue)
- 'Expand All' (line ~122): <AppButton variant="secondary" size="sm" @click="expandAll">Expand All</AppButton>
- 'Collapse All' (line ~128): <AppButton variant="secondary" size="sm" @click="collapseAll">Collapse All</AppButton>

**4. StatusBar.vue** (/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/components/features/StatusBar.vue)
- 'Approve All' (line ~90): <AppButton variant="info" size="sm" @click="emit('approveAll')"> with SVG icon in icon slot
- 'Resume' (line ~100): <AppButton variant="success" size="sm" @click="emit('resume')"> with SVG icon
- 'Stop' (line ~110): <AppButton variant="danger" size="sm" @click="emit('stop')"> with SVG icon
- 'Start' (line ~121): <AppButton variant="primary" size="sm" @click="emit('start')"> with SVG icon

**5. EventFeed.vue** (/Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/components/features/EventFeed.vue)
- 'Jump to latest' (line ~113): <AppButton variant="primary" size="xs" @click="scrollToBottom"> with SVG icon

For ALL files: Add import AppButton from '@/components/common/AppButton.vue';
DO NOT replace structural buttons (hamburger, sidebar collapse, tree node buttons, toast dismiss, error dismiss).
DO NOT write tests.

## Result

All inline button styles replaced. Here's the summary:

### Changes Made

**5 files updated** with `AppButton` component replacing inline button styles:

**`src/pages/ConfigPage.vue`** (4 buttons replaced)
- Error "Retry" → `<AppButton variant="primary">`
- "Save Changes" → `<AppButton variant="primary" :disabled="isSaving">`
- "Reload Plugins" → `<AppButton variant="secondary" size="sm">`
- Workspace "Switch" → `<AppButton variant="secondary" size="sm">`
- Toggle switches and tab buttons left untouched (custom UI)

**`src/pages/SessionsPage.vue`** (2 buttons replaced)
- Error "Retry" → `<AppButton variant="primary">`
- "Refresh" → `<AppButton variant="secondary" size="sm">`

**`src/pages/MemoryBrowserPage.vue`** (2 buttons replaced)
- "Expand All" → `<AppButton variant="secondary" size="sm">`
- "Collapse All" → `<AppButton variant="secondary" size="sm">`

**`src/components/features/StatusBar.vue`** (4 buttons replaced)
- "Approve All" → `<AppButton variant="info" size="sm">`
- "Resume" → `<AppButton variant="success" size="sm">`
- "Stop" → `<AppButton variant="danger" size="sm">`
- "Start" → `<AppButton variant="primary" size="sm">`

**`src/components/features/EventFeed.vue`** (1 button replaced)
- "Jump to latest" → `<AppButton variant="primary" size="xs">`

Zero TypeScript errors. Production build successful.
