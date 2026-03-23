---
id: task_116
title: "Tag and Prepare v0.1.0 Release"
status: completed
assigned_to: pm_dashboard
created_by: manager
company: Shazam
created_at: 2026-03-23T12:12:24.846919Z
completed_at: 2026-03-23T12:15:33.696388Z
updated_at: 2026-03-23T12:15:33.696385Z
---

## Description

After all commits are merged, create git tag for v0.1.0 and prepare release artifacts including build verification and deployment readiness.

ACs:
- All commits are on main branch
- v0.1.0 tag created with annotated message
- Production build completes successfully
- Bundle size audit confirms no regressions
- Release artifacts ready for deployment

## Result

Based on session context, there's a known build blocker — the missing `src/dev/mockApi.ts` file causes the production build to fail. This must be fixed before we can tag. Splitting into parallel tasks with one dependency.

```subtasks
[
  {
    "title": "Fix missing mockApi module and verify production build",
    "description": "The production build fails because `vite.config.ts` imports `./src/dev/mockApi` which does not exist. Fix this so the build succeeds.\n\nExpected behavior:\n- The Vite config should gracefully handle the mock API plugin — either the missing file is restored or the import is made conditional so production builds are not blocked\n- `vue-tsc --noEmit` passes with zero TypeScript errors\n- `vite build` completes successfully\n- Bundle output is printed showing per-page chunk sizes\n\nACs:\n- Production build (`vite build`) completes with zero errors\n- TypeScript check (`vue-tsc --noEmit`) passes with zero errors\n- No functionality is removed — if mock API existed before, it should still work in dev mode\n- The fix is minimal and does not change application behavior",
    "assigned_to": "dashboard_dev_2",
    "depends_on": null
  },
  {
    "title": "Create annotated v0.1.0 git tag and document bundle audit",
    "description": "After the build is verified, create the v0.1.0 release tag and audit bundle sizes.\n\nExpected behavior:\n- An annotated git tag `v0.1.0` is created on the current HEAD of main\n- The tag message summarizes the release (first public release of Shazam Dashboard with Design System v2.0, 8 pages, animation system, real-time WebSocket support)\n- Bundle size audit is performed by running the production build and capturing output\n- A summary of chunk sizes (gzipped) is documented to confirm no regressions vs known baselines: AgentsPage ~8.5kB, DashboardPage ~8.8kB, TasksPage ~9.3kB, main index ~49.6kB\n\nACs:\n- Annotated git tag `v0.1.0` exists on main branch HEAD\n- Tag message includes release summary mentioning key features\n- Production build output captured showing all chunk sizes\n- Bundle sizes confirmed within acceptable range (no chunk exceeds 60kB gzipped)\n- `git tag -v v0.1.0` displays the tag info correctly",
    "assigned_to": "dashboard_dev_3",
    "depends_on": "Fix missing mockApi module and verify production build"
  }
]
```
