### [2026-03-23 12:15] Tag and Prepare v0.1.0 Release
Based on session context, there's a known build blocker — the missing `src/dev/mockApi.ts` file causes the production build to fail. This must be fixed before we can tag. Splitting into parallel tasks with one dependency.
```subtasks
[
  {

