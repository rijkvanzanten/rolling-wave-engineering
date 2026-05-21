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
   - Read the slice file: original contract, expected intermediate state, implementation notes, test notes, review notes, acceptance criteria, and deferred items.
   - Use `../rolling-wave-common/references/lifecycle.md` for completion transition rules.
   - Use `../rolling-wave-common/references/pushback.md` when completion would skip unresolved work.
   - Confirm the user is satisfied with any manual cleanup or remaining risk.

3. Refuse premature completion when needed.
   - If review notes include unresolved requirements that contradict the accepted expected intermediate state, do not mark done.
   - It is valid to mark a slice done while the repo does not compile, run, or pass tests if that broken state is intentional, reviewed, accepted by the user, and tracked to a later slice, roadmap item, project risk, or review note.
   - Do not mark done if the broken state threatens the final project finish line and no later work is identified to fix it.
   - Route back to `implement-slice` or `review-slice` with the concrete blocker.

4. Capture learnings.
   - Add completion learnings to the slice.
   - Update `project.md` with:
     - project-level potential risks
     - PR-style review notes
     - cross-slice decisions
     - roadmap pressure
     - change history
   - If the slice completed in an intentionally broken intermediate state, capture that in completion learnings and project risks/review notes so future slices inherit it.
   - Keep future slices broad unless a learning materially changes their scope or order.
   - Apply completion learnings when identifying the next slice, especially if they change roadmap order, expose a blocker, or make the nominal next slice less valuable.

5. Mark done.
   - Set the slice status to `done`.
   - Clear the active-slice slot so `prepare-next-slice` can choose the next slice.

6. Identify the next slice.
   - Inspect `project.md` roadmap and all slice statuses after marking the current slice `done`.
   - Select the next likely pending slice that best advances the finish line and incorporates the completed slice's learnings.
   - If the roadmap order still makes sense, choose the next pending roadmap item.
   - If completion learnings suggest a different pending slice should come next, explain the reorder pressure without preparing the slice.
   - If no pending slice remains, say the roadmap appears complete and name any remaining open questions, risks, or review notes that might require a new slice.

## Completion

Stop after marking the slice `done`, summarizing the learnings that should influence the next slice, and naming the next likely slice with a short reason. Do not prepare the next slice unless the user explicitly asks.
Omit routine caveats for planning-doc-only completion work. Do not say that no code tests were run or that `docs/rolling-wave/` is gitignored unless the user asked about verification, persistence, or file visibility, or unless an attempted validation step failed.
