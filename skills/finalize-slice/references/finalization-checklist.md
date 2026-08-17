# Finalization Checklist

Review current branch state against immutable `Original Slice Contract`.

Check:

- Review surface includes target-branch diff plus local staged, unstaged, and untracked files.
- Current branch state satisfies current slice contract even when branch includes previous slices or unrelated work.
- Behavior, acceptance criteria, scope boundaries, project decisions, and expected intermediate state match.
- Added complexity serves current contract: no speculative safeguards, one-use abstractions, unnecessary options, avoidable dependencies, or redundant compatibility paths.
- Existing compile, run, typecheck, lint, test, and runtime results match accepted intermediate state.
- Required verification is practical and sufficient; broad tests remain final testing/validation slice work unless current contract requires them.
- Intentional breakage has explicit restoration target before project finish line.
- Implementation notes explain meaningful deviations.
- Child-project completion condition is met when applicable.
- New project-level risks and future-slice discoveries are recorded without expanding current slice.

Mechanical fast path when contract, fingerprint, and live surface match:

- Inspect rename-detected moves separately from non-move edits.
- Review every declared mechanical exception.
- Rerun shared stale-reference, import-boundary, or equivalent proof independently.
- Map focused results to applicable behavior and acceptance rows.
- Fall back to normal focused review on mismatch, failed proof, behavioral change, or concrete uncertainty.

For every candidate finding:

1. Confirm from code, contract, reproducible behavior, or repository evidence.
2. Classify as `required fix`, `project-shape decision`, `accepted intermediate gap`, `future slice`, or `not a finding`.
3. Fix `required fix` findings.
4. Ask user about `project-shape decision` findings before changing code or project state.
5. Track accepted gaps and future slices without treating them as current blockers.
6. Repeat review after fixes until no required finding remains.

Do not flag necessary trust-boundary validation, security controls, data-loss prevention, accessibility basics, migration safety, or explicitly requested behavior as over-engineering.
