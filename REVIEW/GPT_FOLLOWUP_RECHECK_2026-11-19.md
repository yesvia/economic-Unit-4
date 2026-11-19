# GPT Follow-up Recheck — 2026-11-19

## Scope
Personal-use acceptance only. Keep changes minimal. Work only on `review/chatgpt-v5`; do not touch main. Do not reopen cosmetic issues or add features.

## Current file inspected
- `index.html` on `review/chatgpt-v5`
- Current blob SHA at inspection: `ed1b96fd0a699fca2d883c2d9a576c896e098a90`
- 489,878 characters / 3,500 lines; ends with `</script></body></html>`.

## Recheck status

### Passed
The question-specific total cap change is present:
- `logDispTotal(r)` obtains `essayTypeInfo(r).max`;
- `logOver(r)` checks the known question maximum;
- score-rate grouping skips records with max 0.

### Still not fixed: pending-source records can be saved and counted
The previous targeted report's remaining issue is still present in the current source:

1. `VIEWS.practice` still builds `const codes=ESSAYS.map(e=>e.code)`, so pending-source codes remain available to the Essay Log selector.
2. The `ladd` save handler still does:
   ```js
   const _ti=essayTypeInfo({code:$("#lq").value});
   const _mx=_ti.max>0?_ti.max:20;
   const total=Math.min(_mx,ao.reduce((a,b)=>a+(b==null?0:b),0));
   ```
   This converts a pending record (max 0) into a 20-mark scored record.
3. `aoTrackHTML()` still includes every non-mock record with positive AO values; it does not filter out `essayTypeInfo(r).max===0`.
4. The AO3/AO4 aggregate in `nextStep()` and the corresponding Dashboard aggregate in `strategyR3HTML()` still filter only `!r.mock` plus filled AO3/AO4 values. Pending-source records can therefore influence recommendations.

## Required minimal fix
- In the Essay Log save handler, explicitly reject a selected record when `essayTypeInfo({code}).max===0`, with a clear message; do not fallback to 20.
- Keep pending questions available in the practice selector if desired. No need to remove content.
- Exclude max-0 / pending-source records from `aoTrackHTML()`, `nextStep()`, and `strategyR3HTML()` AO aggregates, as well as score-rate aggregates. Keep legacy records in storage and, if shown in history, label them unverified / not counted.
- Do not delete, migrate, rewrite, or reinterpret historical user data.
- Do not change AO component caps without authoritative per-question AO allocations. Do not invent them.
- Limit edits to this pending-record path only.

## Minimal regression checks
1. A pending-source code cannot be saved to Essay Log as a scored record.
2. A legacy pending-source record remains stored/visible but is excluded from AO averages, AO3/AO4 recommendations, and score-rate calculations.
3. 14 / 20 / 34 / 80 total-score behavior remains unchanged.
4. Existing user data is not rewritten or deleted.

Report the exact functions changed and pass/fail results. Do not claim browser testing unless actually performed.
