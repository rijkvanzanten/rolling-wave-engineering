---
name: prepare-next-slice
description: Prepare the next rolling-wave slice for implementation. Use when the user wants to continue a rolling-wave project, select the next slice, apply completed-slice learnings, grill slice-level decisions, decompose the slice into parallel implementation chunks where appropriate, identify whether the slice or chunks are better suited for agent, human, either, or hybrid work, write or update docs/rolling-wave/{project}/slices/NNN-slug.md, and mark exactly one slice ready before implementation.
---

# Prepare Next Slice

## Core Invariants

- Work under `docs/rolling-wave/{project}/`.
- Store project-level state in `project.md`; store slices in `slices/NNN-slug.md`.
- Use slice statuses: `pending`, `ready`, `in progress`, `in review`, `done`.
- Only one slice may be `ready`, `in progress`, or `in review` unless the user explicitly overrides.
- Accept user preferences by default, but push back when strong evidence conflicts with the workflow, prior decisions, terminology, or constraints.
- Pushback format: "I disagree because... The likely consequence is... Continue anyway?"
- Do not modify product code. This skill stops after marking a slice `ready`.

## Workflow

1. Resolve the project.
   - If the user names a project, use it.
   - If exactly one rolling-wave project exists, use it.
   - If multiple projects exist and none was named, ask which one to use.
   - If none exist, tell the user to run `shape-project` first.

2. Read project and slice state.
   - Read `project.md`.
   - Read all slice files in `slices/`.
   - Check for any slice with status `ready`, `in progress`, or `in review`.
   - If another active slice exists, ask before preparing a different one.

3. Select the next slice.
   - Prefer the next `pending` implementation slice that best advances the finish line.
   - Apply learnings from completed slices.
   - If learnings contradict the roadmap, explain the proposed reorder before changing it.
   - If the next pending item is only a broad phase or validation/planning activity, rewrite it into a concrete implementation step before grilling it. If it cannot be made implementation-shaped, move it to project open questions, risks, verification notes, or cross-slice decisions instead of preparing it as a slice.

4. Grill only the selected slice.
   Continue until this slice is ready to implement:
   - behavior is clear
   - verification intent is clear enough to know what `implement-tests` should later prove
   - expected intermediate state is clear, including whether compile/run/test failures are acceptable for this slice
   - likely approach is clear enough
   - parallelizable work chunks are identified or ruled out
   - execution fit is identified for the slice and each chunk
   - meaningful risks are known
   - user-facing decisions are resolved
   - scope boundaries are explicit

   Avoid file/function-level grilling unless it affects behavior, scope, risk, correctness, data shape, migration concerns, testability, or downstream ordering.
   Do not require a detailed test plan, exact test files, or final validation commands at slice-preparation time unless they are already obvious and materially affect the slice shape. Tests are locked down after implementation by `implement-tests`.
   Do not require every slice to compile, run, or pass tests. Ask whether an intermediate broken state is acceptable only when the slice is likely to leave the repo broken or partially wired. If accepted, record what may be broken and which later slice, roadmap item, or project risk will resolve it before the finish line.
   Do not prepare a slice whose only output is "decide", "research", "validate", or "design" unless the slice also has a concrete reviewable artifact or code/docs change required for implementation.

   Decompose implementation work where appropriate:
   - Create `Parallel Work Chunks` in the slice contract when the slice can be split into independent ownership areas.
   - Prefer one to three chunks. Do not invent more chunks than `implement-slice` can usefully delegate.
   - A good chunk has a concrete output, owned files/modules/responsibilities, dependencies, suggested owner, reason, human handoff, agent fallback, timebox, post-implementation test focus, and review focus.
   - Chunks should be disjoint enough that one subagent can own each chunk without frequent merge conflicts.
   - If chunks must run in order, record dependencies clearly; do not call sequential phases "parallel".
   - If the slice is tiny, tightly coupled, or has one obvious critical path, record `Parallel Work Chunks: serial/local-only` with the reason instead of forcing fake chunks.
   - Avoid grilling every individual file or validation command. Ask only about chunk boundaries when they affect behavior, risk, ownership, or parallel execution.

   Identify execution fit:
   - Use `agent` for broad search, repetitive edits, cross-file wiring, mechanical consistency, test scaffolding, and "find all places" work.
   - Use `human` for narrow changes that depend on taste, naming, API/type shape, product judgment, or cases where explaining the desired result is likely slower than editing it.
   - Use `either` when the chunk is small and well specified enough that either the user or an agent can do it efficiently.
   - Use `hybrid` when an agent should gather context or do the broad/mechanical work, but the user should make the final taste/API/type-shape decision.
   - Prefer `human` when the user could likely finish the chunk in under 10-15 minutes, the target files are obvious, and the main risk is clarification churn.
   - For human or hybrid chunks, include a concrete `Human handoff` and `Agent fallback` so `implement-slice` knows whether to pause or continue.

5. Write the slice artifact.
   - Use `../rolling-wave-common/references/artifacts.md` for the slice template.
   - Use `../rolling-wave-common/references/lifecycle.md` for status rules.
   - Use `../rolling-wave-common/references/pushback.md` when a decision needs evidence-based pushback.
   - Preserve an immutable `Original Slice Contract` section once the slice is marked `ready`.
   - Do not write skill workflow instructions into the slice artifact. Rules like "do not rewrite this section after ready", "filled by implement-slice", or "mark ready only when..." belong in the skill workflow, not the slice plan.
   - Include `Execution Fit` for the whole slice.
   - Include `Parallel Work Chunks` in the `Original Slice Contract` when the implementation can be split, or explicitly record why it is serial/local-only.
   - Include suggested owner, reason, human handoff, agent fallback, and timebox for each chunk.
   - Name new slices `NNN-slug.md`.
   - Mark the selected slice `ready`.
   - Update `project.md` with relevant roadmap pressure, decisions, and change history.

## Completion

Stop after the slice is `ready`. Do not implement. Tell the user the slice path and the unresolved items, if any were explicitly deferred.
Omit routine caveats for planning-only work. Do not say that no code tests were run or that `docs/rolling-wave/` is gitignored unless the user asked about verification, persistence, or file visibility, or unless an attempted validation step failed.
