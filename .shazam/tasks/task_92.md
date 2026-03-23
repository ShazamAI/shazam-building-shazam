---
id: task_92
title: "Replace buttons in AgentsPage"
status: completed
assigned_to: dashboard_dev_3
created_by: dashboard_dev_3
company: Shazam
created_at: 2026-03-21T20:52:47.475467Z
completed_at: 2026-03-21T20:56:50.532419Z
updated_at: 2026-03-21T20:56:50.532416Z
---

## Description

Replace inline button styles in /Users/raphaelbarbosa/Projects/ShazamAI/shazam-dashboard/src/pages/AgentsPage.vue with the AppButton component (import from '@/components/common/AppButton.vue').

Buttons to replace:
1. Header '+ Add Agent' (line ~447): <AppButton variant="primary" size="sm" @click="openCreateForm">+ Add Agent</AppButton>
2. Empty state 'Add Agent' (line ~464): <AppButton variant="primary" @click="openCreateForm">Add Agent</AppButton>
3. Card 'Edit Agent' (line ~562): <AppButton variant="ghost" size="sm" block @click="openEditForm(agent)">Edit Agent</AppButton> — needs block/w-full, and style: mt-3 w-full rounded-md bg-gray-800 py-1.5 text-xs → use ghost variant with block prop
4. Form submit (line ~729): <AppButton variant="primary" type="submit" :disabled="isSubmitting || !formData.name.trim()">{{ isSubmitting ? 'Saving...' : showEditForm ? 'Update Agent' : 'Create Agent' }}</AppButton>
5. Form cancel (line ~736): <AppButton variant="secondary" type="button" @click="closeForm">Cancel</AppButton>

DO NOT replace:
- Close modal × button (line ~579): structural icon-only button
- Tool multi-select chips (line ~699): toggle UI, not action buttons

Add the import: import AppButton from '@/components/common/AppButton.vue';

## Result

✅ Build succeeds in 900ms with no errors.

### Changes Made

**File: `src/pages/AgentsPage.vue`**

| Button | Before | After |
|---|---|---|
| Header '+ Add Agent' | `<button class="btn-primary btn-sm sm:btn">` | `<AppButton variant="primary" size="sm">` |
| Empty state 'Add Agent' | `<button class="rounded-lg bg-shazam-500 px-4 py-2 ...">` | `<AppButton variant="primary">` |
| Card 'Edit Agent' | `<button class="mt-3 w-full rounded-md bg-gray-800 py-1.5 ...">` | `<AppButton variant="ghost" size="sm" block class="mt-3">` |
| Form submit | `<button type="submit" :disabled="..." class="rounded-lg bg-shazam-500 ...">` | `<AppButton variant="primary" type="submit" :disabled="...">` |
| Form cancel | `<button type="button" class="rounded-lg border border-gray-700 ...">` | `<AppButton variant="secondary" type="button">` |

**Not replaced** (as specified):
- Close modal × button — structural icon-only button
- Tool multi-select chips — toggle UI, not action buttons

Import added: `import AppButton from '@/components/common/Button.vue';` (uses existing `Button.vue` component which has all required props: `variant`, `size`, `block`, `type`, `disabled`).
