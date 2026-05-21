---
name: implement-tests
description: Add or update tests for the current rolling-wave slice after exploratory implementation. Use when the user wants to lock down the newly added slice logic with focused tests before review, using the slice contract, implementation notes, changed files, and deferred test notes while keeping test scope limited to this slice.
---

# Implement Tests

## Core Invariants

- Work under `docs/rolling-wave/{project}/`.
- Store project-level state in `project.md`; store slices in `slices/NNN-slug.md`.
- Use slice statuses: `pending`, `ready`, `in progress`, `in review`, `done`.
- Only one slice may be `ready`, `in progress`, or `in review` unless the user explicitly overrides.
- Accept user preferences by default, but push back when strong evidence conflicts with the workflow, prior decisions, terminology, or constraints.
- Pushback format: "I disagree because... The likely consequence is... Continue anyway?"
- This phase may edit tests and minimal production code needed to make slice behavior testable or correct.
- Test only the logic added or changed for the current slice. Do not turn this into broad hardening for future slices or unrelated regressions.
- If the slice contract says this slice may intentionally leave the repo unable to compile, run, or pass tests, do not force test implementation just to hide that intermediate state. Record the skipped/blocked coverage and the later slice or risk that must close it.

## Workflow

1. Resolve the project and slice.
   - Prefer the single `in progress` slice.
   - If the user names a project or slice, use it.
   - If no implemented slice exists, stop and route to `implement-slice`.
   - If the slice is already `in review`, allow a focused test pass only if the user is intentionally addressing review findings.

2. Read the test surface.
   - Read relevant `project.md` sections for cross-slice decisions and project-level risks.
   - Read the slice file, especially `Original Slice Contract`, acceptance criteria, verification intent, expected intermediate state, risks, `Implementation Notes`, and any prior `Test Notes`.
   - Inspect local tracked and untracked changes to understand what this slice actually changed.
   - Use `../rolling-wave-common/references/lifecycle.md` when status ownership is unclear.
   - Use `../rolling-wave-common/references/pushback.md` when the requested tests would expand scope.

3. Define the focused test target.
   - Derive tests from the implemented behavior, acceptance criteria, deferred test notes, and changed files.
   - If compile/run/test failure is an accepted intermediate state, limit this pass to useful tests that can be written now. Do not expand the slice to make everything pass.
   - Prefer the project's existing test style, fixtures, and helpers.
   - Choose the smallest useful mix of unit, integration, e2e, snapshot, or manual checks for this slice.
   - Do not grill on every test file or command. Ask only when test scope, observable behavior, or risk tradeoffs are unclear.

4. Implement tests.
   - Add or update tests that lock down the slice's new behavior.
   - Avoid broad refactors and unrelated coverage cleanup.
   - If the code is too hard to test, make the smallest production-code adjustment that improves testability without changing the slice contract.
   - If a test exposes a slice bug, fix the bug narrowly and record the deviation.

5. Run targeted verification.
   - Run the most relevant targeted tests first.
   - Expand only when failures or changed shared behavior justify it.
   - If targeted verification cannot run because the slice is intentionally broken, record that as expected rather than treating it as a blocker.
   - Record commands, pass/fail results, skipped checks, and residual risks.

6. Record test notes.
   - Add or update the slice `Test Notes` section. If the section is missing, create it before `Review Notes`.
   - Include test files changed, behavior covered, commands run, failures fixed, skipped coverage, and review focus.
   - Update `project.md` only for project-level risks, review notes, or cross-slice decisions discovered while testing.

## Completion

Leave the slice `in progress` unless it was already `in review`. Summarize tests added, commands run, any implementation fixes made, and what `review-slice` should check next.
