# Rolling-Wave Artifacts

These artifacts are agent state, not human-facing documentation. Optimize for fast retrieval, low ambiguity, stable structure, and cheap updates.

## Artifact Style

- Use stable headings and terse `key: value` bullets.
- Prefer tables for ordered state such as roadmaps, chunks, risks, and decisions.
- Use canonical statuses only: `pending`, `ready`, `in progress`, `in review`, `done`.
- Put each fact in one canonical place; link to it elsewhere instead of duplicating prose.
- Prefer IDs over paragraphs: `S1`, `D1`, `R1`, `Q1`, `C1`, `L1`.
- Keep rationale short with `why:` or `basis:` fields.
- Use paths for references, for example `docs/rolling-wave/{project}/slices/001-slug.md`.
- Preserve chronological append-only notes for implementation, tests, review, and completion.
- Do not write polished narrative, background essays, or human-facing summaries. Use `exec-summary` for that.
- Keep skill mechanics out of artifacts unless the user explicitly asks to document the process.

## Project Artifact

Create or update `docs/rolling-wave/{project}/project.md` with this shape.

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
- current_slice: none | NNN-slug
- current_child_project: none | docs/rolling-wave/{child-project}/project.md
- default_base_branch: git-town parent | {branch}

## Success Criteria

| id | criterion | verification_signal |
| --- | --- | --- |
| S1 | ... | ... |

## Scope

| id | type | item | basis |
| --- | --- | --- | --- |
| SC1 | include | ... | direct | prior | reasoned |
| SC2 | exclude | ... | direct | prior | reasoned |

## Constraints

| id | constraint | impact |
| --- | --- | --- |
| C1 | ... | ... |

## Assumptions

| id | assumption | confidence | validation |
| --- | --- | --- | --- |
| A1 | ... | high | medium | low | ... |

## Roadmap

Slices are implementation steps, not general phases. Child-project-backed slices stay as one parent row and link to their own project.

| slice | status | purpose | child_project | depends_on | why_now |
| --- | --- | --- | --- | --- | --- |
| 001-{slug} | pending | ... | none | none | ... |

## Decisions

| id | decision | basis | affects |
| --- | --- | --- | --- |
| D1 | ... | direct | prior | reasoned | project | 001-slug | cross-slice |

## Open Questions

| id | question | status | owner | blocks | defer_reason |
| --- | --- | --- | --- | --- | --- |
| Q1 | ... | open | deferred | answered | user | agent | none | ... |

## Risks

| id | risk | impact | mitigation | owner |
| --- | --- | --- | --- | --- |
| R1 | ... | low | medium | high | ... | ... |

## Review Notes

| id | note | source | applies_to |
| --- | --- | --- | --- |
| RN1 | ... | slice | review | completion | PR | ... |

## Change Log

| date | change |
| --- | --- |
| YYYY-MM-DD | ... |
```

Project artifacts stay broad. Do not use roadmap rows as accidental scope boundaries: a later slice is still part of the project unless it is explicitly excluded in `Scope`. Move pure research, validation, and planning concerns into `Open Questions`, `Risks`, `Review Notes`, or `Decisions` instead of making them fake slices.

## Slice Artifact

Create slice files at `docs/rolling-wave/{project}/slices/NNN-slug.md`.

```markdown
---
slice: NNN-slug
status: pending
created: YYYY-MM-DD
updated: YYYY-MM-DD
---

# NNN. {Slice Title}

## State

- purpose: {concrete implementation step and why it matters now}
- parent_project: docs/rolling-wave/{project}/project.md
- child_project: none | docs/rolling-wave/{child-project}/project.md
- owner_fit: agent | human | either | hybrid
- expected_repo_state: working | intentionally broken
- active_attempt: none | YYYY-MM-DD

## Original Slice Contract

### Behavior

| id | behavior |
| --- | --- |
| B1 | ... |

### Acceptance Criteria

| id | criterion | verification_signal |
| --- | --- | --- |
| AC1 | ... | ... |

### Verification Intent

| id | prove | method_hint |
| --- | --- | --- |
| V1 | ... | test | typecheck | manual | inspection |

### Expected Intermediate State

- repo_state: working | intentionally broken
- allowed_breakage: none | ...
- restoration_target: none | later slice | roadmap item | project risk
- restoration_ref: none | ...

### Child Rolling-Wave Project

- child_project: none | docs/rolling-wave/{child-project}/project.md
- parent_completion_condition: none | ...
- child_scope_in_parent: none | ...
- child_scope_outside_parent: none | ...

### Likely Approach

- approach: ...
- avoid: ...

### Minimum Implementation

- simplest_path: ...
- existing_options_checked: stdlib | native | platform | existing helper | installed dependency | none
- not_building_yet: ...
- shortcut_ceiling: none | ...
- upgrade_trigger: none | ...

### Execution Fit

- suggested_owner: agent | human | either | hybrid
- reason: ...
- human_handoff: none | ...
- agent_fallback: none | ...
- timebox: ...

### Parallel Work Chunks

| chunk | output | ownership | depends_on | owner | test_focus | review_focus |
| --- | --- | --- | --- | --- | --- | --- |
| C1 | ... | ... | none | agent | human | either | hybrid | ... | ... |

If serial/local-only:

- serial_reason: ...

### Scope Boundaries

| id | type | item |
| --- | --- | --- |
| SB1 | include | ... |
| SB2 | exclude | ... |

### Risks

| id | risk | mitigation |
| --- | --- | --- |
| R1 | ... | ... |

### Decisions

| id | decision | basis |
| --- | --- | --- |
| D1 | ... | direct | prior | reasoned |

## Readiness Notes

| date | item | result |
| --- | --- | --- |
| YYYY-MM-DD | question | answer | deferred |

## Implementation Notes

| date | status | owner_shape | changed_files | verification | notes |
| --- | --- | --- | --- | --- | --- |
| YYYY-MM-DD | none | local | subagents | human | ... | ... | ... |

## Test Notes

| date | coverage | command | result | gaps |
| --- | --- | --- | --- | --- |
| YYYY-MM-DD | ... | `...` | pass | fail | skipped | ... |

## Review Notes

| date | severity | finding | status | ref |
| --- | --- | --- | --- | --- |
| YYYY-MM-DD | P0 | P1 | P2 | P3 | ... | open | fixed | accepted | ... |

## Completion Learnings

| date | learning | affects |
| --- | --- | --- |
| YYYY-MM-DD | ... | parent | child | later slice | PR notes |
```

Mark the slice `ready` only when behavior, verification intent, expected intermediate state, child-project relationship, likely approach, minimum implementation, execution fit, parallel chunks or serial reason, meaningful risks, decisions, and scope boundaries are resolved for this slice. A slice may intentionally leave the repo unable to compile, run, or pass tests if that state is recorded and tracked to a later slice or project risk.

A child rolling-wave project is a planning relationship, not a filesystem nesting requirement. Store sibling projects under `docs/rolling-wave/{project}/`; link them by path from the parent slice artifact. The parent slice is complete only when its declared child-project completion condition is met and any child learnings needed by the parent project have been captured in the parent slice or parent `project.md`.
