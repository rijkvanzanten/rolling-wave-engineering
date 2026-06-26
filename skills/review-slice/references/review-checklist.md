# Review Checklist

Review against the immutable `Original Slice Contract`.

Check:
- Complexity first: code added beyond the slice contract, speculative safeguards, one-implementation abstractions, unnecessary config/options, new dependencies that stdlib/native/existing helpers cover, and logic that can be shrunk.
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

Do not flag necessary trust-boundary validation, security controls, data-loss prevention, accessibility basics, migration safety, or explicitly requested behavior as over-engineering.

Report findings only, ordered by severity. Include file and line references when possible. If there are no findings, the chat response should be exactly `No findings.` with no summary of what passed.
