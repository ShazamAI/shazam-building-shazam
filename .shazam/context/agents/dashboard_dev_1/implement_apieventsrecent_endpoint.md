### [2026-03-21 20:44] Implement /api/events/recent endpoint in backend
The frontend **already handles this gracefully** — `fetchRecentEvents()` has a try/catch that returns `[]` on failure, and `DashboardPage.vue` uses `Promise.allSettled` so a failed events fetch won't break anything.
The 404 console error is cosmetic — the app works fine, it just logs the failed HTTP 

