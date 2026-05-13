---
name: implement-slice
description: Implement a ready rolling-wave slice. Use when the user explicitly wants to code the current docs/rolling-wave/{project}/slices/NNN-slug.md slice, mark it in progress, make an explicit delegation decision, dispatch one to three implementation subagents for non-trivial parallelizable work when available, record implementation notes, and preserve the original slice contract for review.
---

# Implement Slice

## Core Invariants

- Work under `docs/rolling-wave/{project}/`.
- Store project-level state in `project.md`; store slices in `slices/NNN-slug.md`.
- Use slice statuses: `pending`, `ready`, `in progress`, `in review`, `done`.
- Only one slice may be `ready`, `in progress`, or `in review` unless the user explicitly overrides.
- Accept user preferences by default, but push back when strong evidence conflicts with the workflow, prior decisions, terminology, or constraints.
- Pushback format: "I disagree because... The likely consequence is... Continue anyway?"
- Product code changes are allowed only because this skill is the explicit implementation entry point.
- Do not silently skip delegation. Before implementation work starts, either dispatch implementation subagents or record why this slice must be implemented locally.

## Workflow

1. Resolve the project and slice.
   - If the user names a project or slice, use it.
   - Otherwise find the single `ready` slice.
   - If no `ready` slice exists, stop and route to `prepare-next-slice`.
   - If multiple candidate slices exist, ask which one to implement.

2. Read the contract.
   - Read `project.md`.
   - Read the slice file, especially `Original Slice Contract`, acceptance criteria, verification, scope boundaries, and risks.
   - Use `../rolling-wave-common/references/lifecycle.md` when status ownership is unclear.
   - Use `../rolling-wave-common/references/pushback.md` when implementation would violate the contract or lifecycle.
   - Do not rewrite the original contract. Record discoveries elsewhere.

3. Mark the slice `in progress`.
   - Update the slice status before implementation work starts.
   - Add an implementation attempt entry with date, agent, and intended ownership.

4. Choose execution shape.
   This is mandatory. Do not start editing product code until the execution shape is chosen and recorded in the implementation attempt.

   Default to subagents for non-trivial slices when the platform supports them. A slice is non-trivial if it touches multiple modules, has distinct implementation and test surfaces, requires both codebase discovery and code edits, or has enough contract detail that separate ownership areas can be defined.

   Use one to three implementation subagents when any disjoint ownership split is available, for example:
   - discovery / implementation / tests
   - backend tool / adapter exposure / integration tests
   - model or schema code / API or transport code / verification
   - existing-pattern research / focused patch / test repair

   Keep work local only when one of these is true:
   - the slice is genuinely tiny
   - all changes are tightly coupled in one file or one small function
   - subagents are unavailable in the current environment
   - repository instructions explicitly prohibit subagent dispatch
   - the next action is an urgent blocker that must be done before any useful parallel work exists

   If keeping work local, write the reason into `Implementation Notes` before editing product code.

   When delegating:
   - Assign disjoint ownership with explicit files, modules, or responsibilities.
   - Tell each subagent it is not alone in the codebase.
   - Tell each subagent not to revert edits made by others.
   - Require each subagent to list changed files and verification performed.
   - Keep one immediate critical-path task local instead of blocking entirely on subagents.
   - After each subagent result is captured and no further input is needed, close that subagent. `wait_agent` does not close it automatically.

5. Implement against the slice contract.
   - Prefer existing project patterns.
   - Keep edits scoped to the slice.
   - If the slice contract is wrong or incomplete, pause, record the issue, and ask before expanding scope.

6. Record implementation notes.
   - Use `references/implementation-handoff.md` for the note format.
   - Update the slice `Implementation Notes` section with execution shape, delegation/local-only reason, ownership, changed files, commands run, known risks, deviations, and follow-up review focus.
   - Update `project.md` only for cross-slice decisions, new risks, or roadmap pressure.

## Completion

Stop with the slice still `in progress`. Summarize changed files, verification run, and what `review-slice` should check next.
