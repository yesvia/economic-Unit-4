# GPT Final Targeted Recheck — 2026-11-19

## Acceptance scope
Personal-use website only. Do not reopen cosmetic or low-impact issues. Do not add features or refactor unrelated modules. Work only on `review/chatgpt-v5`; do not touch main.

## Result of the previous requested total-cap fix

The earlier total-cap change is present:
- `logDispTotal(r)` now uses `essayTypeInfo(r).max`;
- `logOver(r)` checks the question-specific maximum for known scored records;
- the existing strategy / planner rate calculations use the same type maximum and exclude max-0 records.

This part is a pass for the 14 / 20 / 34 / 80 total-score caps.

## One remaining material issue: pending-source records can still be saved and enter AO statistics

I inspected the actual current flow in `index.html`:

1. `VIEWS.practice` creates `const codes=ESSAYS.map(e=>e.code)` and uses that list for both the practice selector and the Essay Log selector (`#lq`). This includes entries with `sourceStatus === "pending"`.
2. On save, the handler calls `essayTypeInfo({code:$(\"#lq\").value})`. For pending entries this returns `max:0`, but the code then falls back to `_mx=20`:
   ```js
   const _ti=essayTypeInfo({code:$(\"#lq\").value});
   const _mx=_ti.max>0?_ti.max:20;
   const total=Math.min(_mx,ao.reduce((a,b)=>a+(b==null?0:b),0));
   ```
   Therefore a pending-source item can still be saved as a scored 20-mark record, contrary to the stated max-0 / excluded status.
3. `aoTrackHTML()` currently includes every non-mock record with any positive AO score; it does not exclude records where `essayTypeInfo(r).max===0`.
4. The AO3/AO4 comparison branch in `nextStep()` similarly filters only `!r.mock` and filled AO3/AO4 values, so a pending-source record can influence its recommendations.

### Minimal required correction
- Keep pending questions available in the practice selector if desired, but do not allow them to be saved as scored Essay Log entries. Either omit pending codes from the `#lq` selector or block the save with a clear message. Do not use a fallback maximum of 20 for a record whose type maximum is 0.
- Preserve any existing pending-source records in storage. Do not delete, rewrite, or reinterpret their historical values.
- Exclude max-0 / pending-source records from AO tracking and AO3/AO4 recommendation aggregates, as well as all score-rate calculations. Keep the records visible in history if useful, but label them as unverified / not counted.
- Keep the patch limited to this pending-record path.

## AO component limits — do not guess
The current UI also hardcodes each AO input as 0–20 and clamps each component to 20. I have not verified a reliable per-question AO allocation for every 14-, 20-, and 34-mark record from the current metadata. Do not invent allocations or mass-rewrite saved AO values. Inspect whether the existing source metadata actually provides authoritative AO maxima. If it does not, leave historical AO values untouched and report that the per-AO maximum model remains unverified; do not expand this task into a redesign.

## Minimal regression checks
- A pending-source code cannot be saved as a scored Essay Log entry.
- A legacy pending-source record remains stored and visible, but does not affect AO averages, AO3/AO4 recommendations, or score-rate calculations.
- 14 / 20 / 34 / 80 scored records retain their correct total maximum behavior.
- Existing user data is not migrated, deleted, or rewritten.

Report only the exact functions changed and the above pass/fail results. Do not claim browser testing unless actually performed.
