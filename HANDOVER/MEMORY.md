# MEMORY — Elite Economist System

**Project:** Elite Economist System
**Exam:** Pearson Edexcel International A Level Economics, Unit 4 (WEC14/01)
**Student:** viaana (communicates in Chinese; exam content in English)
**Assistant:** Viaana
**Last updated:** 2026-11-19

## Core task

Maintain and develop the Elite Economist System study website (a single static HTML file), and mark the student's practice essays in chat strictly following the official Edexcel level mark scheme.

- The website itself does **not** mark essays. Marking happens in chat: the site prepares the question + the student's answer (via the "问 Viaana" copy-question button), the student pastes it into the main chat, and Viaana marks it.
- Every essay marking must give a **level judgement** (KAA level + Evaluation level, with marks) and a **detailed evaluation comment** (what earned the level, what is missing, what to change next time).
- The official marking rubric (Section C 20 marks: KAA 12 + Evaluation 8; Section B Q7(e) 14 marks: KAA 8 + Evaluation 6) and the per-question official MS points are in `Handover_Elite_Economy.md` §3 and in the site's VER data. Never invent mark-scheme points.

## Requirements

1. Every essay must have a level judgement + detailed evaluation comment.
2. Follow all rules written inside `Handover_Elite_Economy.md` (especially §7 project rules and §0A scope constraint: do not over-develop, only fix actually-used parts).
3. Do NOT read all files in the workspace for every subtask. For marking one essay, read ONLY that essay + `Handover_Elite_Economy.md` + `Condensed_20_Chapters.md`. For website work, read ONLY the files the task needs (the site's `index.html`, the relevant REVIEW files).
4. Do NOT repeatedly load the entire conversation history for every subtask. This handover package replaces the long conversation history.
5. Avoid unnecessary auto retry loops. When confused or uncertain, pause and ask the user for confirmation instead of retrying automatically many times.
6. Only access files strictly necessary for the current work. Ignore all unused files.

## Hard rules (must follow)

1. When marking a single essay: ONLY read this current essay file + `Handover_Elite_Economy.md` + `Condensed_20_Chapters.md`. Do NOT scan / read all essays and all other files inside this workspace.
2. Do NOT repeatedly load the entire conversation history for every subtask.
3. Do NOT start infinite self-retry loops. If you encounter an error or uncertainty, pause and ask the user for approval instead of retrying automatically many times.
4. Only access files that are strictly necessary for the current work. Ignore all unused files.
5. The output standard, marking level, and evaluation logic must be 100% consistent with the previous Elite Economist System built in the old conversation (see `Handover_Elite_Economy.md` §3 for the rubric and §10 for what is still unverified — never present unverified evaluation wording as fully verified).

## Key facts (standing)

- **Website:** slug `elite-economist-system`; single HTML file, 489,668 chars, 3,500 lines, 10 script blocks; SHA-256 `f28b8f43f01780cd323e07b854966e4b6940f2becb99a03a3518c66196808aed`. Colours: pink + mint green + white, dark default.
- **Exam:** 2h, 80 marks. Section A 6 MCQ / 6; Section B Q7 (marks vary by paper); Section C choose 2 of 3 × 20 (KAA 12 + Eval 8). Never mix in UK 9EC0.
- **Question bank:** 83 Essays in ESSAYS (Section B 20, Section C 59, source-pending 4). 202506A-1 was removed from ESSAYS (it is a Section A MCQ, Lewis dual-sector model; MCQ record off-2506a-1). ESSAY_META: 83 records with explicit section / marks / qLabel / sourceStatus / verified. 202605A-7/-8/-9/-10 are source-pending. MCQ_OFF: 36 official MCQs + 12 self-written (labelled non-official).
- **GitHub:** repo yesvia/economic-Unit-4 (Public). Work branch `review/chatgpt-v5`; `main` is protected — never merge, rebase, reset or overwrite it without the user's explicit approval. Latest push: commit f85fe213 (final acceptance work order execution, 2026-11-19). Before pushing any REVIEW file, re-download the repo version and merge — GPT appends sections directly. Pushes need the user's approval-card tap.
- **Review:** ChatGPT does independent static code review (11 rounds; final integration passed at static-code-review level). DeepSeek reviews via the exported HTML file or the repo (muse.ai links return 403 for external AIs). The merged final-acceptance work order is `REVIEW/FINAL_ACCEPTANCE_MERGED_2026-11-19.md`; its §0A scope constraint is a hard rule.
- **Public link:** https://muse.ai/s/elite-economist-system-xvxn6dxjxmxu6nxuz/wSY0h-nyp-UDT38_cMVZ7LP7eexmFBy96FhsRhnao9w — currently serves the merged main version, not the latest review-branch fixes. Re-sharing needs the user's approval.
- **User's standing rules:** do not over-develop; use resources economically (minimal fixes, targeted verification only); ask about colour scheme before building a page; technical terms as Chinese + (English); "问 Viaana" copy-question button on future pages (no real in-page AI); heavy files must lazy-load (never block first paint); if unsure whether something she said is a slip of the tongue (口误）, ask first.

## Current state (2026-11-19)

- Merged final-acceptance work order execution is done and pushed (commit f85fe213); GPT independent re-check is pending (the user prompts GPT to start).
- P0-B is partially complete (3 sub-items unverified: 202310-8 demand/eval unification; INT_FREQ wording differs from the work order's specified text; logDispTotal denominators not individually confirmed). 7 UNDECIDED terminology items were left unchanged (dense long quote in Q7P oct25a; adding 7 inline English terms would hurt readability) — pending GPT's ruling.
- Still unfinished: 202605A-7 unverified; Themes 3–6 knowledge-card full text (skeletons only); official grade boundaries per series; T16 (footer submit button narrow-viewport click timing); F8 (31/36 official MCQs have empty kp); Q7 (b)–(e) MS sources (only jun25a is a Pearson direct link); two diagram questions (no diagrams drawn); no real-browser end-to-end testing.
- Deferred (not defects, not blockers): weekly heatmap, Anki/CSV export, independent today-review queue, KB three-level self-test system, MCQ auto mastery adjustment, extra analytics dashboards, P1-B dataDiag.

## How to continue

1. Read `Handover_Elite_Economy.md` + `Condensed_20_Chapters.md` + this file + the repo's REVIEW files. Do not re-read the whole conversation history.
2. Work only on `review/chatgpt-v5`. Never touch `main`.
3. After finishing a fix round, verify (fresh export hash + node --check on all 10 script blocks), update the execution records in the merged work order §7 and the DeepSeek audit §4 (mark "待 GPT 复查", never "最终验收通过"), push (user approves), then tell the user "可以让GPT检查工作了".
4. For essay marking: confirm which question and how long she had; mark against the official level criteria; never open the site's model essay / logic chain for her before she writes (she writes closed-book).
