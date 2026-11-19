# GPT Independent Recheck — 2026-11-19

Target: `index.html` at commit `f85fe213` on `review/chatgpt-v5`.
Review basis: static source inspection against `REVIEW/FINAL_ACCEPTANCE_MERGED_2026-11-19.md`.
This is a targeted remediation report, not authorization to redesign or rewrite the site.

## Overall result

**Not accepted yet.** The HTML file is complete (489,668 characters; 3,500 lines; ends with `</script></body></html>`). The source contains many of the requested changes, but the following issues must be addressed before final acceptance.

Keep all work on `review/chatgpt-v5`. Do not merge into protected `main`. Preserve the current working version and avoid unrelated refactoring.

## Required fixes

### P0-B — Essay Log score denominator and over-mark validation (BLOCKER)

The current code contains:

```js
function logDispTotal(r){
  const t=+r.total||0;
  return r.mock?Math.min(80,t):Math.min(20,t);
}
```

The weak-area calculation also normalizes with a fixed denominator of 80 for mocks and 20 for every non-mock record. `logOver()` likewise treats every non-mock record as having a maximum of 20.

This is incorrect for different U4 task types. Use the existing `essayTypeInfo(r).max` (or a single equivalent canonical helper) consistently.

Expected maxima:
- Section B 7(e): 14
- Section C essay: 20
- Whole Q7: 34
- Full mock paper: 80

Required changes:
1. `logDispTotal(r)` must clamp/display against the correct maximum for that record's task type, not a blanket 20.
2. Weak-area normalization must divide by the same task-specific maximum, with safe handling for missing/zero maxima.
3. `logOver()` must flag scores above the task-specific maximum and must not incorrectly accept/reject scores based on 20.
4. Ensure the same source of truth is used for display, validation, sorting, and percentages; avoid duplicated hard-coded maximums.
5. Preserve historical records and their original raw scores. Do not silently rewrite stored data.
6. Add focused tests/assertions for 14-, 20-, 34-, and 80-mark records, including blank/missing values and over-limit scores.

Acceptance examples:
- A 12/14 7(e) record displays 12/14 and normalizes to 12/14, not 12/20.
- A 17/20 Section C essay displays 17/20.
- A 28/34 whole-Q7 record displays 28/34.
- A 65/80 mock displays 65/80.
- A 15/14, 21/20, 35/34, or 81/80 record is flagged as over-limit.

### P0-A — mocknav binding fallback

The source has a guard around `BINDS.practice` in the `BINDS.mocknav` binding, but when the practice binding is unavailable the fallback does not explicitly navigate to the practice route.

Required change:
- Make the fallback deterministic, e.g. call the canonical `go("practice")` route when `BINDS.practice` is unavailable, while preserving the preferred binding call when it exists.
- Verify both paths: practice binding present and absent.
- Do not change other navigation behavior.

### P1-2 — MCQ search coverage

The current MCQ search indexes question text, options, and `why`, but does not include the `wrong` per-option explanation field.

Required change:
- Include all relevant `wrong` explanation text in the searchable text for each MCQ.
- Keep search results limited to matching questions; do not expose internal data or break current result ranking.
- Test a search term that appears only in a `wrong` explanation and verify that the corresponding MCQ is returned.

### P0-C — Section C pending-question exclusion (defensive hardening)

The current Section C selector filters by `section === "Section C"`. The present pending records lack this metadata and are therefore excluded today, but the selector does not explicitly reject pending/unverified records.

Required change:
- Explicitly exclude records marked pending, `SRC_PENDING`, or `parseCode(code).unverified` (using the actual existing schema).
- Test that a pending/unverified record cannot enter the Section C selection list even if malformed metadata says Section C.
- Preserve valid verified Section C questions.

### P0-D / P0-E — distinguish source-level fixes from runtime evidence

Source inspection confirms relevant implementation elements for import/IndexedDB fallback and mobile navigation/accessibility, but this review did not independently execute the site in a browser. The project execution notes also contain inconsistent descriptions of browser probing versus incomplete end-to-end testing.

Required before claiming these items fully pass:
1. Run the actual HTML in a browser and record the tested browser/device and date.
2. Verify JSON merge import and full-replacement import with representative data; confirm saved data after reload.
3. Exercise IndexedDB-unavailable/failure behavior and confirm the user-facing warning and safe behavior.
4. Test mobile sidebar open/close, overlay close, Escape, focus entering the menu, Tab behavior, and focus returning to the trigger.
5. Check console errors and key navigation paths.
6. Report only tests actually executed. Do not label static code inspection or mocked assertions as browser E2E tests.

If browser testing cannot be performed in the current environment, state that explicitly as **not runtime-verified** rather than claiming a pass.

### P2 — terminology consistency and unresolved scan items

The source still contains examples of half-width English parentheses with a preceding space, including:
- `生产补贴 (subsidy)`
- `普雷维什-辛格假说 (Prebisch–Singer hypothesis)`
- `贸易条件 (terms of trade)`

Required change:
- Apply the project's required format consistently: Chinese term first, English in full-width parentheses, with no space before the bracket.
- Review the remaining 7 `UNDECIDED` scan items individually. Do not bulk-replace ambiguous cases.
- Preserve correct English source wording and exam terminology.
- Provide the actual term-scan report and change list as review artifacts, or include a concise auditable summary in the completion report.
- Re-scan after changes and report remaining undecided/skip items honestly.

## Items that appear implemented (do not redo without a demonstrated defect)

- `202506A-1` is treated as an MCQ rather than an essay.
- `ESSAY_META` contains explicit section/marks/source-status metadata.
- The MCQ_OFF dataset contains 36 records in the reviewed source.
- The two specified MCQ corrections are present in both relevant render paths.
- Import-pending state is module-scoped; merge and replacement paths are present.
- IndexedDB save completion is tied to transaction completion; an unavailable-storage warning exists.
- Mobile sidebar source includes Escape/overlay close and focus-management logic.
- Planner review actions and spaced intervals are present.
- kp6x is marked as content pending / official subtopic list only.
- The two diagram-dependent items warn users to consult the original paper diagram.
- Grade boundaries are marked pending rather than fabricated.

These are source-level findings only unless supported by actual runtime test evidence.

## Scope control

- Make only the targeted fixes above.
- Do not rebuild the site or rewrite already-correct modules.
- Do not alter verified Pearson English source text.
- Do not delete or overwrite original essays, reports, backups, or historical records.
- Keep all changes on `review/chatgpt-v5`; do not touch protected `main`.
- After fixing, provide changed file paths, commit SHA, concise test evidence, and any remaining limitations.
- Then request GPT recheck; do not mark final acceptance complete before the blocker is verified.
