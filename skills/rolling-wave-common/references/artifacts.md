# Rolling-Wave Artifacts

These artifacts are agent state, not human-facing documentation. Optimize for fast retrieval, low ambiguity, stable structure, and cheap updates.

## Artifact Style

- Use stable headings and terse `key: value` bullets.
- Prefer tables for ordered state such as roadmaps, chunks, risks, and decisions.
- Use canonical statuses only: `pending`, `ready`, `in progress`, `ready for review`, `in review`, `done`.
- Put each fact in one canonical place; link to it elsewhere instead of duplicating prose.
- Prefer IDs over paragraphs: `S1`, `D1`, `R1`, `Q1`, `C1`, `L1`.
- Keep rationale short with `why:` or `basis:` fields.
- Use paths for references, for example `docs/rolling-wave/{project}/slices/001-slug.md`.
- Preserve chronological append-only notes for implementation, tests, review, and completion.
- Do not write polished narrative, background essays, or human-facing summaries. Use `exec-summary` for that.
- Keep skill mechanics out of artifacts unless the user explicitly asks to document the process.
- Prefer sparse artifacts. Create optional sections only when they contain non-default information or useful append-only state.
- Use defaults instead of explicit `none` rows when the default is obvious from this reference.
- Treat one executable slice as one bounded human review decision. Multiple independently executable or revertible chunks may stay together when one intent or transformation recipe, risk class, and verification strategy cover the batch. Split separate approval decisions, unrelated mental models, distinct risk classes, or materially different verification strategies. Use roughly 500 non-move changed lines as a soft default, not a universal cap; count rename-detected moves separately and let low-semantic mechanical batches span domains. Use chunks to divide ownership and review navigation.

## Project Artifact

Create or update `docs/rolling-wave/{project}/project.md` with this shape. Keep `State`, `Success Criteria`, `Scope`, and `Roadmap`. Create `Constraints`, `Assumptions`, `Decisions`, `Open Questions`, `Risks`, `Review Notes`, and `Change Log` only when they contain real state.

```markdown
---
project: {project}
status: active
created: YYYY-MM-DD
updated: YYYY-MM-DD
---

# {Project Name}

## State

- finish_line: {observable completed state}
- current_slice: none | NNN-slug (optional focus pointer; does not imply only one active slice)
- current_child_project: none | docs/rolling-wave/{child-project}/project.md
- default_base_branch: git-town parent | {branch}

## Success Criteria

| id | criterion | verification_signal |
| --- | --- | --- |
| S1 | ... | ... |

## Scope

| id | type | item | basis |
| --- | --- | --- | --- |
| SC1 | include | ... | direct/prior/reasoned |
| SC2 | exclude | ... | direct/prior/reasoned |

## Constraints

| id | constraint | impact |
| --- | --- | --- |
| C1 | ... | ... |

## Assumptions

| id | assumption | confidence | validation |
| --- | --- | --- | --- |
| A1 | ... | high/medium/low | ... |

## Roadmap

Slices are implementation steps, not general phases. Child-project-backed slices stay as one parent row and link to their own project.

| slice | status | purpose | child_project | depends_on | why_now |
| --- | --- | --- | --- | --- | --- |
| 001-{slug} | pending | ... | none | none | ... |

## Decisions

| id | decision | basis | affects |
| --- | --- | --- | --- |
| D1 | ... | direct/prior/reasoned | project/001-slug/cross-slice |

## Open Questions

| id | question | status | owner | blocks | defer_reason |
| --- | --- | --- | --- | --- | --- |
| Q1 | ... | open/deferred/answered | user/agent | none | ... |

## Risks

| id | risk | impact | mitigation | owner |
| --- | --- | --- | --- | --- |
| R1 | ... | low/medium/high | ... | ... |

## Review Notes

| id | note | source | applies_to |
| --- | --- | --- | --- |
| RN1 | ... | slice/review/completion/PR | ... |

## Change Log

| date | change |
| --- | --- |
| YYYY-MM-DD | ... |
```

Project artifacts stay broad. Do not use roadmap rows as accidental scope boundaries: a later slice is still part of the project unless it is explicitly excluded in `Scope`. Move pure research, validation, and planning concerns into `Open Questions`, `Risks`, `Review Notes`, or `Decisions` instead of making them fake slices.

## Slice Artifact

Create slice files at `docs/rolling-wave/{project}/slices/NNN-slug.md`.
Use the sparse shape by default. Add optional sections only when they carry information beyond the defaults.

```markdown
---
slice: NNN-slug
status: ready
created: YYYY-MM-DD
updated: YYYY-MM-DD
---

# NNN. {Slice Title}

## State

- purpose: {concrete implementation step and why it matters now}
- parent_project: docs/rolling-wave/{project}/project.md
- child_project: none
- owner_fit: agent | human | either | hybrid
- expected_repo_state: working | intentionally broken
- active_attempt: none | YYYY-MM-DD

## Original Slice Contract

### Contract

| id | type | item | signal |
| --- | --- | --- | --- |
| B1 | behavior | ... | ... |
| AC1 | acceptance | ... | inspection/manual/final-test |
| SB1 | include | ... | ... |
| SB2 | exclude | ... | ... |

### Expected Intermediate State

- repo_state: working | intentionally broken
- allowed_breakage: none | ...
- allowed_validation_failures: none | compile | typecheck | lint | tests | runtime | ...
- temporary_green_shims_allowed: no | yes, reason: ...
- restoration_target: none | later slice | roadmap item | project risk
- restoration_ref: none | ...

### Likely Approach

- approach: ...
- avoid: ...

<!-- Optional contract sections below. Include only when non-default or useful. -->

### Review Shape

- review_question: ...
- review_method: semantic | mechanical-batch | mixed
- expected_non_move_lines: ...
- shared_proof: ...
- mechanical_exceptions: none | ...

### Minimum Implementation

- simplest_path: ...
- existing_options_checked: stdlib | native | platform | existing helper | installed dependency | none
- not_building_yet: ...
- shortcut_ceiling: none | ...
- upgrade_trigger: none | ...

### Child Rolling-Wave Project

- child_project: docs/rolling-wave/{child-project}/project.md
- parent_completion_condition: ...
- child_scope_in_parent: ...
- child_scope_outside_parent: ...

### Execution Fit

- suggested_owner: agent | human | either | hybrid
- reason: ...
- human_handoff: none | ...
- agent_fallback: none | ...
- timebox: ...

### Parallel Work Chunks

| chunk | output | ownership | depends_on | owner | test_focus | review_focus |
| --- | --- | --- | --- | --- | --- | --- |
| C1 | ... | ... | none | agent/human/either/hybrid | ... | ... |

If serial/local-only:

- serial_reason: ...

### Risks

| id | risk | mitigation |
| --- | --- | --- |
| R1 | ... | ... |

### Decisions

| id | decision | basis |
| --- | --- | --- |
| D1 | ... | direct/prior/reasoned |

```

Defaults when an optional section is absent:
- `child_project: none`
- `owner_fit: agent` or `either`
- `temporary_green_shims_allowed: no`
- `parallel_work: serial/local-only`
- `review_method: semantic`
- `risks: none known`
- `decisions: none`

Create append-only state sections lazily when a workflow first has real state to record:
- `## Readiness Notes`: material questions answered or explicitly deferred during preparation.
- `## Implementation Notes`: execution shape, changed files, accepted breakage, final-test backlog, deviations.
- `## Test Backlog`: final testing/validation slice work discovered before the final slice.
- `## Review Notes`: findings, accepted risks, target branch, reviewed surface.
- `## Completion Learnings`: learnings that affect parent, child, later slices, or PR notes.

Mark the slice `ready` when behavior, scope boundaries, expected intermediate state, likely approach, and review decision are clear enough to implement, and one bounded review method can judge the executable scope. Verification intent only needs to identify what the final testing/validation slice should eventually prove. Add `Review Shape` when a slice is cross-domain, multi-chunk, mechanical-batch, mixed, or near its review budget; omit it for obvious small slices. Minimum implementation, execution fit, parallel chunks, risks, and decisions are required only when they prevent likely overbuilding, misrouting, or hidden risk. A slice may intentionally leave the repo unable to compile, run, or pass tests if that state is recorded and tracked to a later slice or project risk. Do not add temporary code, compatibility shims, fake fallbacks, broad validation, or placeholder wiring just to make compile/typecheck/lint/tests pass for an intermediate slice unless the slice explicitly allows that workaround and names the reason. Broad test coverage belongs in the final testing/validation slice, not in every implementation slice.

A child rolling-wave project is a planning relationship, not a filesystem nesting requirement. Store sibling projects under `docs/rolling-wave/{project}/`; link them by path from the parent slice artifact. The parent slice is complete only when its declared child-project completion condition is met and any child learnings needed by the parent project have been captured in the parent slice or parent `project.md`.
