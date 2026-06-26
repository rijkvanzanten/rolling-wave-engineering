---
name: implement-slice
description: Implement a ready rolling-wave slice without locking it down with tests yet. Use when the user explicitly wants to code the current docs/rolling-wave/{project}/slices/NNN-slug.md slice, mark it in progress, execute the prepared parallel work chunks with one implementation subagent per agent-fit chunk where available, pause or hand off chunks marked human-fit when appropriate, record implementation notes, defer test-writing to implement-tests, and preserve the original slice contract for review.
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
- Treat implementation as exploratory: refactoring, slice-shape discoveries, and plan adjustments are expected.
- Do not write new tests in this phase unless they are required to understand or unblock the implementation. The dedicated `implement-tests` phase locks down the finished slice afterward.
- Do not silently skip delegation. Before implementation work starts, either dispatch implementation subagents or record why this slice must be implemented locally.
- Invoking this skill is the explicit request to implement the slice using the workflow below, including implementation subagents when the execution shape calls for them. Subagent use is based on necessity, execution fit, and available parallel ownership; it must not depend on whether the user separately said "subagents" in the current turn.
- Slice/project artifacts are agent state. Record implementation notes as terse table rows or `key: value` bullets; avoid narrative handoff prose unless it captures a decision-critical reason.

## Workflow

1. Resolve the project and slice.
   - If the user names a project or slice, use it.
   - Otherwise find the single `ready` slice.
   - If no `ready` slice exists, stop and route to `prepare-next-slice`.
   - If multiple candidate slices exist, ask which one to implement.

2. Read the contract.
   - Read relevant `project.md` sections for project-level decisions, risks, and completed-slice learnings.
   - Read the slice file, especially `Original Slice Contract`, acceptance criteria, verification intent, child rolling-wave project relationship, minimum implementation, scope boundaries, and risks.
   - If the slice is backed by a child rolling-wave project, read the child `project.md` and current child slice state before choosing execution shape.
   - Read `Parallel Work Chunks` from the slice contract. Treat prepared chunks as the default execution plan.
   - Read `Execution Fit` for the slice and chunk-level suggested owners.
   - Use `../rolling-wave-common/references/lifecycle.md` when status ownership is unclear.
   - Use `../rolling-wave-common/references/pushback.md` when implementation would violate the contract or lifecycle.
   - Do not rewrite the original contract. Record discoveries elsewhere.

3. Mark the slice `in progress`.
   - Update the slice status before implementation work starts.
   - Add an implementation attempt entry with date, agent, and intended ownership.

4. Choose execution shape.
   This is mandatory. Do not start editing product code until the execution shape is chosen and recorded in the implementation attempt.

   Do not use "the user did not explicitly ask for subagents this turn" as a reason to keep work local. The explicit request is the use of `implement-slice`; decide based on the slice contract, execution fit, risk, parallel ownership, and platform availability.

   Use the prepared `Parallel Work Chunks` first:
   - If the slice defines parallel chunks, spawn one implementation subagent per `agent` or suitable `either` chunk when the platform supports subagents.
   - Do not spawn subagents for chunks marked `human` unless the user asks to use the agent fallback.
   - For chunks marked `hybrid`, do the agent-fit context gathering or mechanical work, then pause for the human decision before finalizing that chunk unless the contract says the agent fallback is acceptable.
   - If a chunk is marked `human`, pause before implementation and present the human handoff, reason, timebox, and agent fallback. Ask whether the user wants to do it by hand or have the agent continue with the fallback.
   - Keep chunks as written unless a chunk has become unsafe, stale, overlapping, or impossible. If so, record the reason before adjusting execution shape.
   - Assign each subagent the chunk's concrete output, owned files/modules/responsibilities, dependencies, post-implementation test focus, and review focus.
   - Tell each subagent to skip new test coverage unless a minimal test/probe is required to unblock implementation.
   - Keep one immediate integration or critical-path task local when useful, but do not duplicate a chunk already owned by a subagent.

   If the slice has no usable chunks, default to subagents for non-trivial slices when the platform supports them. A slice is non-trivial if it touches multiple modules, has distinct implementation and test surfaces, requires both codebase discovery and code edits, or has enough contract detail that separate ownership areas can be defined.

   Use one to three implementation subagents when any disjoint ownership split is available. If more than three chunks would be needed, stop and ask to re-prepare the slice or merge chunks before implementation.

   Keep work local only when one of these is true:
   - the slice is genuinely tiny
   - all changes are tightly coupled in one file or one small function
   - all remaining chunks are marked `human` and the user chooses to do them by hand
   - the slice contract explicitly says `Parallel Work Chunks: serial/local-only` and the reason still holds
   - subagents are unavailable in the current environment
   - repository instructions explicitly prohibit subagent dispatch
   - the next action is an urgent blocker that must be done before any useful parallel work exists

   If keeping work local or pausing for human work, write the reason into `Implementation Notes` before editing product code.
   If the platform rejects spawning even though the execution shape calls for subagents, continue locally only after recording that platform blocker in the implementation notes and final response.

   When delegating:
   - Assign disjoint ownership with explicit files, modules, or responsibilities.
   - Tell each subagent it is not alone in the codebase.
   - Tell each subagent not to revert edits made by others.
   - Require each subagent to list changed files and any lightweight verification performed.
   - Require each subagent to identify the tests that `implement-tests` should add or update for its chunk.
   - Require each subagent to report whether its chunk contract was completed, partially completed, or blocked.
   - Keep one immediate critical-path task local instead of blocking entirely on subagents.
   - After each subagent result is captured and no further input is needed, close that subagent. `wait_agent` does not close it automatically.

5. Implement against the slice contract.
   - If the parent slice points to a child rolling-wave project, implement only the child slice or child-project work that is currently ready. Do not collapse the child project's remaining roadmap into this parent-slice implementation.
   - Keep parent implementation notes focused on parent-visible progress, child project path, active child slice, and child learnings that affect the parent project.
   - Before writing code for each local or delegated chunk, apply the minimum-implementation ladder:
     1. Does this code need to exist for this slice?
     2. Does the standard library or language runtime already do it?
     3. Does a native platform, browser, database, framework, or OS feature cover it?
     4. Does an already-installed dependency or existing project helper solve it?
     5. Can this be one direct path instead of a new abstraction, option, fallback, or dependency?
     6. Only then write the minimum custom code that satisfies the slice.
   - Prefer deletion, direct wiring, and existing primitives over future-proofed scaffolding.
   - Do not add config knobs, wrappers, factories, compatibility layers, broad normalization, or edge-case guards unless the slice contract, a real caller, a trust boundary, or repo evidence requires them.
   - Do not simplify away input validation at trust boundaries, security controls, data-loss prevention, accessibility basics, migration safety, or behavior the user explicitly requested.
   - When taking an intentional shortcut with a known ceiling, record the ceiling and upgrade trigger in implementation notes. Add a short code comment only when future maintainers would otherwise misread the shortcut as accidental or unsafe.
   - Prefer existing project patterns.
   - Keep edits scoped to the slice.
   - Favor getting the behavior into the right shape over prematurely locking tests around churn.
   - Do not broaden the slice just to make a future test plan cleaner.
   - Respect `Execution Fit`: use agents for broad/mechanical work, pause for human-fit taste/API/type-shape calls, and resume only after the user provides the result or authorizes the fallback.
   - If the slice contract is wrong or incomplete, pause, record the issue, and ask before expanding scope.

6. Record implementation notes.
   - Use `references/implementation-handoff.md` for the note format. Resolve this path relative to this `implement-slice` skill directory, not `rolling-wave-common`.
   - Update the slice `Implementation Notes` section with execution shape, human-fit pauses or handoffs, delegation/local-only reason, ownership, changed files, commands run, skipped/deferred tests, known risks, deviations, and follow-up test/review focus.
   - Include what was deliberately skipped or simplified under the minimum-implementation decision, plus any upgrade trigger.
   - Preserve the artifact's compact structure. Prefer appending one dated row plus short keyed details over adding paragraphs.
   - Update `project.md` only for cross-slice decisions, new risks, or roadmap pressure.

## Completion

Stop with the slice still `in progress`. Summarize changed files, any lightweight verification run, tests intentionally deferred to `implement-tests`, and what `implement-tests` should lock down next.
