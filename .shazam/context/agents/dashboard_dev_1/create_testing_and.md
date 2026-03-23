### [2026-03-21 14:24] Create testing and git workflow domain rules
Both files already exist and are comprehensive. Let me verify they meet all the ACs:
**testing.md** ✅
- Testing strategy clearly explained ✅
- Role separation (developers vs QA) clearly defined ✅

### [2026-03-21 20:43] Remove company selector from TopHeader and all UI
Everything is already in the correct state:
- **TopHeader.vue** — Read-only badge showing `activeCompany?.name ?? 'Loading...'`. No dropdown, no selector, no click handler.
- **useActiveCompany.ts** — No `selectCompany()` function. `activeCompany` is a computed that auto-resolves from the backend. No

