---
id: task_46
title: "Verify dashboard builds and loads without companies section"
status: completed
assigned_to: dashboard_dev_1
created_by: manager
company: Shazam
created_at: 2026-03-21T20:22:49.362850Z
completed_at: 2026-03-21T20:27:26.723994Z
updated_at: 2026-03-21T20:27:26.723991Z
---

## Description

Run production build, verify no console errors related to missing Companies page or routes, and confirm dashboard loads and displays current company correctly without any company selection UI.

ACs:
- Production build completes successfully
- Dashboard loads without 404 errors or missing route warnings
- No broken links or references to removed companies section remain

## Result

Good — `selectCompany` is defined in the composable but no longer imported by any component. It's exported but unused, which is fine.

**Verification summary:**

✅ **Production build completes successfully** (81 modules, 865ms, no errors)
✅ **CompaniesPage.vue deleted** — no longer in build output (was `CompaniesPage-DPXFjtuQ.js`, now gone)
✅ **`/companies` route removed** from router
✅ **Companies icon removed** from both SidebarNav and MobileSidebar icon maps
✅ **Company selector dropdown removed** from TopHeader — replaced with read-only badge showing active company name
✅ **No broken references** — remaining `/companies` strings are API endpoint paths used by other features (agents, tasks, org-chart), not UI routes
✅ **No 404 risk** — no `<router-link>` or navigation points to `/companies` anymore
