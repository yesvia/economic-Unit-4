# GPT Targeted Recheck — 2026-11-19

## Scope decision from the user

This is a personal-use study website. Do **not** apply commercial-launch standards, do not overdevelop, and do not reopen low-impact issues.

Only change something when it materially affects normal use, especially:
- the site opening and core navigation;
- answering / marking / self-assessment;
- correct score recording and score interpretation;
- saving, backup, import, and restore.

Do not spend time on cosmetic consistency, minor search enhancements, optional accessibility refinements, extra reports, new learning features, or refactoring that does not fix a real user-facing problem. Do not modify modules that already work.

## One confirmed issue to fix

### Essay Log maximum-score handling

In `index.html`, the current implementation is:

```js
function logDispTotal(r){
  const t=+r.total||0;
  return r.mock?Math.min(80,t):Math.min(20,t);
}
function logOver(r){
  if(r.mock)return (+r.total||0)>80;
  return (+r.total||0)>20||(r.ao||[]).some(function(v){return +v>20||+v<0;});
}
```

But `essayTypeInfo(r)` already distinguishes:
- Section B 7(e): 14 marks;
- Section C essay: 20 marks;
- Section B full Q7: 34 marks;
- full-paper mock: 80 marks;
- pending-source records: max 0 (not to be included in score-rate calculations).

The current fixed 20-mark cap/check for every non-mock record can misrepresent a 34-mark Q7 in Essay Log and can fail to flag totals above the correct maximum for 14/34-mark tasks. This is a real score-recording/display issue, not a cosmetic concern.

**Required targeted fix:**
1. Reuse `essayTypeInfo(r).max` as the single source of truth for each record's maximum score wherever Essay Log displays/caps a recorded total or checks whether the total exceeds its valid maximum.
2. Preserve the existing 80-mark handling for full-paper mocks.
3. Do not count pending-source records (max 0) as scored work or include them in score-rate calculations.
4. Inspect the actual stored record shape before changing AO subscore validation. Do not assume every AO component has a 20-mark maximum; preserve valid existing AO data and validate against the correct component limits only where those limits are known.
5. Keep the patch minimal. Do not redesign Essay Log or rewrite unrelated statistics.

**Minimal regression checks after the patch:**
- a 14-mark 7(e) record displays out of 14 and flags a score above 14;
- a 20-mark Section C record displays out of 20;
- a 34-mark full Q7 record displays out of 34 and flags a score above 34;
- an 80-mark mock remains out of 80;
- a pending-source record is excluded from scored-rate calculations;
- existing records and saved data remain intact.

If the stored record data does not reliably identify one of these types, do not guess or silently reinterpret historical scores. Report the ambiguity and preserve the data.

## Explicitly do not fix in this pass

Unless a concrete reproduction shows that normal use is broken, leave these alone:
- minor terminology/bracket/spacing inconsistencies;
- adding `wrong` explanations to MCQ search;
- optional mock-navigation fallback refinements;
- small ARIA/focus refinements;
- extra dashboards, heatmaps, Anki/CSV features, or other new functionality;
- missing screenshots, reports, or commercial-grade QA paperwork;
- broad refactoring or reformatting.

These are not blockers for the user's stated personal-use acceptance standard.

## Completion response

After the minimal score fix:
- state exactly which function(s) changed;
- report the regression checks above as pass/fail;
- confirm no unrelated modules or stored data were changed;
- do not claim browser end-to-end tests unless they were actually run.

This instruction supersedes the earlier broad review's low-impact suggestions. The only currently confirmed code change requested is the Essay Log maximum-score handling above.
