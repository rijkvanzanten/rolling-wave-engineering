---
name: review-slice
description: Review a rolling-wave slice implementation against its original slice contract. Use when the user wants to check an in-progress or in-review slice, run one to three review subagents, verify behavior and acceptance criteria, capture review notes and potential risks, and keep the slice in review until it is valid.
---

# Review Slice

## Core Invariants

- Work under `docs/rolling-wave/{project}/`.
- Store project-level state in `project.md`; store slices in `slices/NNN-slug.md`.
- Use slice statuses: `pending`, `ready`, `in progress`, `in review`, `done`.
- Only one slice may be `ready`, `in progress`, or `in review` unless the user explicitly overrides.
- Accept user preferences by default, but push back when strong evidence conflicts with the workflow, prior decisions, terminology, or constraints.
- Pushback format: "I disagree because... The likely consequence is... Continue anyway?"
- Do not behave as completion. This skill reviews and records findings; `complete-slice` marks done.
- Invoking this skill is explicit user authorization to use review subagents for the slice review. Do not ask for separate permission to spawn reviewers, and do not announce that reviewers will be skipped unless the user explicitly asks for subagents.

## Workflow

1. Resolve the project and slice.
   - Prefer the single slice with status `in progress` or `in review`.
   - If multiple candidates exist, ask.
   - If no implementation exists, stop and route to `implement-slice`.

2. Read the review surface.
   - Read `project.md`.
   - Read the slice file, especially `Original Slice Contract`, acceptance criteria, verification, risks, and `Implementation Notes`.
   - Use `../rolling-wave-common/references/lifecycle.md` when status ownership is unclear.
   - Use `../rolling-wave-common/references/pushback.md` when review is being asked to hide unresolved mismatch.
   - Inspect local changes and relevant code.

3. Mark or keep the slice `in review`.
   - Set status to `in review` before review work.
   - Append a dated review pass. Do not overwrite prior review passes.

4. Run review.
   - Use `references/review-checklist.md` for the review checklist.
   - Use one to three review subagents when the platform supports them and the review has any meaningful implementation, contract, or verification surface.
   - For non-trivial slices, include at least one adversarial or correctness-focused pass when subagents are available.
   - If subagents appear blocked only because the user did not separately say "subagents", treat this skill invocation as the explicit user request for slice review subagents.
   - If the platform still rejects spawning, continue locally only after recording that blocker in the review notes and final response.
   - After collecting subagent outputs, close every spawned subagent before reporting or editing review notes. `wait_agent` returns results but does not retire the subagent.
   - Compare implementation to the original slice contract, not to a moving target.
   - Findings lead. Order by severity and include file/line references when possible.

5. Record review state.
   - Write current findings, verification results, residual risks, and required fixes into the slice `Review Notes`.
   - Add project-level potential risks and PR-style review notes to `project.md` when they are useful outside the slice.
   - If implementation discoveries change future work, record them as roadmap pressure rather than changing the original contract.

## Completion

Leave the slice `in review`. If requirements are unmet, state the needed fixes. If no blocking findings remain, say it is ready for the user's manual cleanup and `complete-slice`.
