---
id: task_56
title: "Investigate and fix Memory page loading issue"
status: completed
assigned_to: dashboard_dev_2
created_by: manager
company: Shazam
created_at: 2026-03-21T20:28:32.074328Z
completed_at: 2026-03-21T20:32:39.495931Z
updated_at: 2026-03-21T20:32:39.495928Z
---

## Description

Debug why the Memory page fails to load on the dashboard. Check console errors, API responses, component initialization, and data binding. Implement fixes to restore functionality and ensure the page displays memory data correctly.

ACs:
- Memory page loads without errors
- Console shows no error messages or exceptions
- Memory data displays correctly if backend endpoint is implemented
- Page remains responsive during loading states

## Result

Build passes. Here's a summary of all fixes:

### Root Causes Fixed

**1. Data extraction mismatch** (`configService.ts`)
- `fetchMemoryTree` used `extractKey(response, 'tree')` but mock API wraps in `{ data: [...] }` — `extractKey` fallback returned the whole object, then `ensureArray` returned `[]` because it got an object instead of an array
- Already fixed by teammate's `extractFromResponse` which tries domain key first, then `data` key

**2. Missing data normalization** (`configService.ts`)
- Added `normalizeTreeNodes()` — recursively ensures every node has `name`, `path`, `type`, and `children` array, preventing crashes from backend returning incomplete data
- `fetchMemoryFile` now normalizes response to guarantee `frontmatter` (Record) and `content` (string) exist — prevents `Object.keys(undefined)` crash in template

**3. No loading timeout** (`MemoryBrowserPage.vue`)
- Added 8-second timeout guard — if API hangs, page exits loading state and shows error message instead of spinning forever

**4. Unsafe template access** (`MemoryBrowserPage.vue`)
- Replaced `Object.keys(selectedFile.frontmatter)` with a computed `hasFrontmatter` that null-checks first
- Separated `fileError` from tree `error` so file load failures don't hide the tree
- EmptyState now only shows when there's no error AND no content

**5. Hard-coded 4-level nesting replaced with recursive component** (`MemoryTreeNode.vue`)
- New `MemoryTreeNode.vue` handles any depth via self-referencing recursion
- Removed dead code (`<component :is="'memory-tree-item'" v-if="false" />`)
- Uses `Array.isArray(node.children)` guard for safety
- Reactive `expandedDirs` is a new `Set` on each toggle (Vue reactivity trigger)

### Files Changed
- `src/api/configService.ts` — data normalization in `fetchMemoryTree` and `fetchMemoryFile`
- `src/pages/MemoryBrowserPage.vue` — rewritten with timeout, defensive patterns, recursive component
- `src/components/features/MemoryTreeNode.vue` — **new** recursive tree node component
