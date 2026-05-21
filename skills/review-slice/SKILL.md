---
name: review-slice
description: Review a rolling-wave slice implementation against its original slice contract after implementation and focused tests. Use when the user wants to check an in-progress or in-review slice, optionally use zero to three review subagents based on risk and review scope, verify behavior and acceptance criteria, capture review notes and potential risks, and keep the slice in review until it is valid.
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
- Invoking this skill is permission to use review subagents when they are worth the token cost. Use zero to three reviewers based on risk, diff size, and uncertainty; a local-only review is valid for small or obvious changes.

## Workflow

1. Resolve the project and slice.
   - Prefer the single slice with status `in progress` or `in review`.
   - If multiple candidates exist, ask.
   - If no implementation exists, stop and route to `implement-slice`.

2. Read the review surface.
   - Read the slice file first, especially `Original Slice Contract`, acceptance criteria, verification intent, expected intermediate state, risks, `Implementation Notes`, `Test Notes`, and prior `Review Notes`.
   - Read only the relevant `project.md` sections needed for cross-slice decisions, project-level risks, and review notes. Do not re-read the whole project unless the slice context is ambiguous.
   - Use `../rolling-wave-common/references/lifecycle.md` when status ownership is unclear.
   - Use `../rolling-wave-common/references/pushback.md` when review is being asked to hide unresolved mismatch.
   - Inspect local tracked and untracked changes before choosing reviewer count.
   - If a recent `code-review` or prior `review-slice` pass already reviewed the same diff, treat it as input. Do not repeat the same broad reviewer set unless the code changed materially or the previous findings were unresolved.

3. Mark or keep the slice `in review`.
   - Set status to `in review` before review work.
   - Append a dated review pass. Do not overwrite prior review passes.

4. Run review.
   - Use `references/review-checklist.md` for the review checklist.
   - Start with a local contract pass before waiting on reviewers. Compare changed files, implementation notes, test notes, and verification results against the original slice contract.
   - Judge compile/run/test failures against the slice's expected intermediate state. They are blockers only if this slice promised a working state or the breakage is unrecorded, accidental, or not tracked to a later slice/risk.
   - Short-circuit on obvious blockers: if the local pass finds a clear unmet acceptance criterion, contract mismatch, or unaccepted missing focused tests/verification that makes the slice not ready, record that finding and stop without spawning reviewers.
   - Choose zero to three reviewers:
     - `0` reviewers for tiny diffs, docs-only/planning-only changes, obvious follow-up fixes, or cases where the local contract pass gives high confidence.
     - `1` reviewer for normal non-trivial code changes where one lens clearly dominates.
     - `2-3` reviewers only for large diffs, high-risk domains, multiple independent risk areas, unresolved previous findings, or explicit deep/adversarial review.
   - Pick reviewers by dominant risk:
     - `testing-reviewer` for test-heavy changes, destructive behavior, migrations with snapshot coverage, or acceptance criteria mostly proven by tests.
     - `security-reviewer` for auth, permission checks, public input validation, destructive actions, secrets, or privilege boundaries.
     - `api-contract-reviewer` for public API, request/response schema, serialization, MCP/tool contracts, or exported type changes.
     - `correctness-reviewer` for general logic, state transitions, data flow, and behavior matching.
     - language-specific reviewers only when the slice is primarily about language/framework-specific risk and that reviewer is available.
   - Do not spawn reviewers merely because subagents are available. Spend subagent tokens only when they are likely to find issues the local pass might miss.
   - If subagents are unavailable, continue locally. Mention that only when it materially affects confidence.
   - When spawning more than one reviewer, spawn them together and wait for them together. Do not wait for reviewers sequentially unless one result is needed to decide whether another reviewer is warranted.
   - Keep reviewer prompts narrow: include the slice path, changed file list, relevant contract bullets, implementation notes, and exact review focus. Do not ask reviewers to re-review the whole repo or whole project.
   - After collecting subagent outputs, close every spawned subagent before reporting or editing review notes. `wait_agent` returns results but does not retire the subagent.
   - Compare implementation to the original slice contract, not to a moving target.
   - Findings lead. Order by severity and include file/line references when possible.

5. Record review state.
   - Write current findings, verification results, residual risks, and required fixes into the slice `Review Notes`.
   - Record reviewer count and why that count was enough, especially when using zero reviewers.
   - If the slice is intentionally broken, record exactly what remains broken and where it is tracked for a later slice before marking the review acceptable.
   - Add project-level potential risks and PR-style review notes to `project.md` only when they are useful outside the slice.
   - If implementation discoveries change future work, record them as roadmap pressure rather than changing the original contract.

## Completion

Leave the slice `in review`. If requirements are unmet, state the needed fixes. If no blocking findings remain, say it is ready for the user's manual cleanup and `complete-slice`.
