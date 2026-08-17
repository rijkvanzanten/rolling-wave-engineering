# Rolling-Wave Lifecycle

Slice statuses:

- `pending`: broad future slice, not yet prepared.
- `ready`: prepared in detail and ready for implementation.
- `in progress`: implementation has started.
- `ready for review`: implementation finished and awaits finalization.
- `in review`: implementation is being checked against the original slice contract.
- `done`: explicitly declared complete by the user through `complete-slice`.

Multiple slices may be `ready`, `in progress`, `ready for review`, or `in review` concurrently so preparation, implementation, and review can run in parallel. Each workflow invocation operates on one resolved slice. If multiple slices match a workflow and the user did not identify one, infer from explicit context when unambiguous; otherwise ask which slice to use.

An unfinished predecessor blocks preparing a later slice only when concrete unresolved behavior or missing learnings prevent an honest implementation contract. Status alone is never a blocker.

## Slice Review-Budget Rule

Every executable slice is one bounded human review decision, not necessarily the smallest independently useful, executable, or revertible change. Size slices by the reviewer question and verification method:

- Keep multiple chunks together, including cross-domain chunks, when one architectural intent or transformation recipe, one risk class, and one bounded verification strategy let a reviewer judge the batch in one focused pass.
- Mechanical batches may span domains when behavior stays unchanged, each moved unit includes its consumers and existing tests, rename-aware review separates moves from edits, and non-mechanical exceptions are few and explicit.
- Split when chunks need separate product or architecture approval decisions, unrelated mental models, distinct risk classes, incompatible intermediate states, or materially different verification strategies. Revertibility or independent usefulness alone does not require a split.
- Use roughly 500 non-move changed lines as a soft default review budget. Lower the budget for semantic, stateful, public-contract, security, data, or irreversible changes; allow a larger raw diff for repetitive mechanical edits when one proof covers the batch.
- Prefer one to three implementation chunks inside a slice. Chunks divide execution and review navigation; they do not each need to be separate slices.

Commit count, file count, domain count, and raw diff size remain signals, not hard limits. Work needing multiple review decisions becomes sibling slices or a child project whose executable slices follow the same rule.

## Intermediate State Rule

A completed slice does not have to leave the repository compiling, tests passing, or the feature runnable. Record known intentional breakage and restoration targets when available, but missing records do not block explicit user completion. The project finish line and final success criteria still require the final project state to work.

## Transition Ownership

| Skill | Transition | Notes |
| --- | --- | --- |
| `shape-project` | creates `pending` slices | Keeps details broad. |
| `prepare-next-slice` | `pending -> ready` | Stops before product code changes; requires one bounded review decision. |
| `implement-slice` | `ready -> in progress -> ready for review` | Leaves partial or blocked work `in progress`; marks completed implementation `ready for review`. |
| `deliver-slice` | orchestrates implementation then finalization | Preserves `ready for review` checkpoint, then delegates fresh-context finalization and leaves clean work `in review`. |
| `finalize-slice` | `ready for review -> in review`; repeated `in review` passes | Reviews, fixes confirmed findings, asks about material project-shape changes, and repeats until clean. Never marks done. |
| `complete-slice` | `pending/ready/in progress/ready for review/in review -> done` | User completion declaration overrides missing workflow steps. Captures available material learnings. |

## Contract Rule

Once a slice is marked `ready`, preserve its `Original Slice Contract`. Later discoveries belong in implementation notes, review notes, completion learnings, project risks, or roadmap pressure.

This is a workflow rule for the skills. Do not write this rule into the slice artifact as plan content.

## Product Code Rule

Only `implement-slice` and `finalize-slice` change product or test code. The final testing/validation slice is implemented through `implement-slice` like any other slice. Other phase skills may update rolling-wave planning artifacts only.
