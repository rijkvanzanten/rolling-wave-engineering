# Implementation Handoff

Append implementation attempts to the slice `Implementation Notes` section.

```markdown
### YYYY-MM-DD Implementation Attempt

Status: in progress

Changed files:
- `path/to/file`

Execution shape:
- Subagents used: yes/no
- Reason: ...

Ownership:
- Local agent: ...
- Subagent 1: ...

Verification run:
- `command`

Notes:
- Important implementation choices.

Known risks:
- Risks review should inspect.

Deviations:
- Anything that differs from the original slice contract.
```

Delegation rules:
- Do not silently skip delegation for non-trivial slices.
- If subagents are unavailable or unsafe for this slice, record the local-only reason before editing product code.
- Prefer one to three subagents when work can be split into disjoint ownership areas.

When using subagents:
- Assign disjoint ownership.
- Tell each subagent it is not alone in the codebase.
- Tell each subagent not to revert edits made by others.
- Require each subagent to list changed files and verification performed.
- Close each subagent after its output is captured and no further input is needed.
