# Rolling-Wave Artifacts

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

## Finish Line

What is true when the project is complete.

## Success Criteria

- Observable criteria that define success.

## Non-Goals

- Explicit exclusions.

## Constraints

- Technical, product, time, dependency, compatibility, operational, security, or organizational constraints that apply to the project itself.
- Do not record the current skill's workflow mechanics here. "Planning only in this phase", "do not modify product code", "ask one question at a time", and similar instructions describe the agent session, not the project.

## Assumptions

- Current beliefs that may need validation.

## Broad Roadmap

Sequence for reaching the finish line. This is not a substitute for project scope: requirements belong in the finish line, success criteria, non-goals, constraints, decisions, risks, or open questions before they are assigned to a slice.

Slices are implementation steps, not general project phases. Each row should describe a concrete reviewable change that can later become a slice contract. Do not use broad phase labels like "design", "validate", "harden", or "phase 1" unless the row states the specific implementation outcome.

The roadmap should reflect a discussed broad slice sequence, not a first-pass guess. Notes should briefly capture why the slice belongs in that position, especially when it reduces uncertainty, unlocks later work, or intentionally delays risk.

| Slice | Status | Purpose | Notes |
| --- | --- | --- | --- |
| 001-{slug} | pending | ... | ... |

## Cross-Slice Decisions

- Decisions that affect more than one slice.

## Open Questions

- Project-level unknowns that remain unresolved after shaping. Do not hide project-scope decisions here just because they will be implemented in a later slice.
- Do not use this section to avoid asking material shaping questions. A project-level question belongs here only if it was asked and explicitly left unanswered/deferred, or if it is safe to defer to slice preparation with a clear reason.

## Potential Risks

- Project-level risks worth surfacing in later PR or project summaries.

## Review Notes

- PR-description-style notes that accumulate across slices.

## Change History

- YYYY-MM-DD: Created/updated project shape.
```

Keep the project artifact broad. Do not use it as a detailed implementation plan. Do not use roadmap slices as accidental scope boundaries: a later slice is still part of the project unless the item is explicitly listed as a non-goal. Keep skill mechanics out of the artifact unless the user explicitly asks to document the process. Keep roadmap rows concrete enough to implement, and make their ordering rationale visible without writing full slice contracts. Move pure research, validation, and planning concerns into open questions, risks, verification notes, or cross-slice decisions.

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

## Purpose

The concrete implementation step this slice delivers and why it matters now.

## Original Slice Contract

### Behavior

- What the implemented slice must do.

### Acceptance Criteria

- Criteria the implementation must satisfy.

### Verification

- Commands, checks, or manual review needed.

### Likely Approach

- Approach clear enough to start, without over-planning every file.

### Scope Boundaries

- Included:
- Excluded:

### Risks

- Risks specific to this slice.

### User-Facing Decisions

- Resolved UX/product/API decisions.

## Readiness Notes

- Questions asked, answers received, and explicitly deferred items.

## Implementation Notes

- None yet.

## Review Notes

- None yet.

## Completion Learnings

- None yet.
```

Mark the slice `ready` only when behavior, verification, likely approach, meaningful risks, user-facing decisions, and scope boundaries are resolved for this slice.
Keep skill mechanics out of slice artifacts. Do not write instructions like "do not rewrite this section", "filled by implement-slice", or "mark ready only when..." into the generated slice file.
