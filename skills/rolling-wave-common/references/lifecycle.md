# Rolling-Wave Lifecycle

Slice statuses:

- `pending`: broad future slice, not yet prepared.
- `ready`: prepared in detail and ready for implementation.
- `in progress`: implementation has started.
- `in review`: implementation is being checked against the original slice contract.
- `done`: user-reviewed and completed.

Only one slice may be `ready`, `in progress`, or `in review` unless the user explicitly overrides.

## Intermediate State Rule

A completed slice does not have to leave the repository compiling, tests passing, or the feature runnable. Rolling-wave slices are allowed to be experimental intermediate states as long as the breakage is intentional, recorded in the slice, accepted by the user, and tracked into a later slice or project risk. The project finish line and final success criteria still require the final project state to work.

## Transition Ownership

| Skill | Transition | Notes |
| --- | --- | --- |
| `shape-project` | creates `pending` slices | Keeps details broad. |
| `prepare-next-slice` | `pending -> ready` | Stops before product code changes. |
| `implement-slice` | `ready -> in progress` | Leaves the slice `in progress` when implementation finishes. |
| `implement-tests` | stays `in progress` | Adds focused tests for the implemented slice before review. |
| `review-slice` | `in progress -> in review`; repeated `in review` passes | Appends review passes, does not mark done. |
| `complete-slice` | `in review -> done` | Captures learnings before marking done. |

## Contract Rule

Once a slice is marked `ready`, preserve its `Original Slice Contract`. Later discoveries belong in implementation notes, review notes, completion learnings, project risks, or roadmap pressure.

This is a workflow rule for the skills. Do not write this rule into the slice artifact as plan content.

## Product Code Rule

Only `implement-slice` and `implement-tests` change product or test code. The other phase skills may update rolling-wave planning artifacts only.
