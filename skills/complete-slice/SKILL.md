---
name: complete-slice
description: Complete a reviewed rolling-wave slice after user review. Use when the user says a slice is done, wants to mark an in-review slice done, capture implementation and review learnings, update docs/rolling-wave/{project}/project.md potential risks and review notes, and prepare later slices to inherit what was learned.
---

# Complete Slice

## Core Invariants

- Work under `docs/rolling-wave/{project}/`.
- Store project-level state in `project.md`; store slices in `slices/NNN-slug.md`.
- Use slice statuses: `pending`, `ready`, `in progress`, `in review`, `done`.
- Only one slice may be `ready`, `in progress`, or `in review` unless the user explicitly overrides.
- Accept user preferences by default, but push back when strong evidence conflicts with the workflow, prior decisions, terminology, or constraints.
- Pushback format: "I disagree because... The likely consequence is... Continue anyway?"
- Do not perform generic code review. If review is needed, route to `review-slice`.

## Workflow

1. Resolve the project and slice.
   - Prefer the single `in review` slice.
   - If multiple candidates exist, ask.
   - If the slice is not `in review`, push back before marking it done unless the user explicitly overrides.

2. Read completion state.
   - Read `project.md`.
   - Read the slice file: original contract, implementation notes, review notes, acceptance criteria, and deferred items.
   - Use `../rolling-wave-common/references/lifecycle.md` for completion transition rules.
   - Use `../rolling-wave-common/references/pushback.md` when completion would skip unresolved work.
   - Confirm the user is satisfied with any manual cleanup or remaining risk.

3. Refuse premature completion when needed.
   - If review notes include unresolved requirements, do not mark done.
   - Route back to `implement-slice` or `review-slice` with the concrete blocker.

4. Capture learnings.
   - Add completion learnings to the slice.
   - Update `project.md` with:
     - project-level potential risks
     - PR-style review notes
     - cross-slice decisions
     - roadmap pressure
     - change history
   - Keep future slices broad unless a learning materially changes their scope or order.

5. Mark done.
   - Set the slice status to `done`.
   - Clear the active-slice slot so `prepare-next-slice` can choose the next slice.

## Completion

Stop after marking the slice `done` and summarizing the learnings that should influence the next slice.
