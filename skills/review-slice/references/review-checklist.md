# Review Checklist

Review against the immutable `Original Slice Contract`.

Check:
- Behavior matches the contract.
- Acceptance criteria are met or explicitly called out as unmet.
- Compile/run/test failures are judged against the expected intermediate state, not treated as automatic blockers.
- Verification commands or manual checks were run when practical.
- Scope boundaries were respected.
- User-facing decisions were implemented consistently.
- Implementation notes explain meaningful deviations.
- Intentional intermediate breakage is recorded with the later slice, roadmap item, or project risk that must close it.
- New risks are captured in the slice and, when project-level, in `project.md`.
- Future-slice discoveries are recorded as roadmap pressure, not silently folded into this slice.

Report findings first, ordered by severity. Include file and line references when possible.
