# Implementation Handoff

Append implementation attempts to the slice `Implementation Notes` section. Keep the format compact and agent-readable.

```markdown
| date | status | owner_shape | changed_files | verification | notes |
| --- | --- | --- | --- | --- | --- |
| YYYY-MM-DD | in progress | local | subagents | human | `path/to/file` | `command` | ... |

Keyed details when needed:
- execution_reason: ...
- chunk_adjustments: none | ...
- human_handoff: none | ...
- simplest_path: ...
- skipped_yagni: none | ...
- existing_helper_used: none | ...
- shortcut_ceiling: none | ...
- upgrade_trigger: none | ...
- deferred_tests: none | ...
- known_risks: none | ...
- deviations: none | ...
```

Delegation rules:
- Do not silently skip delegation for non-trivial slices.
- Do not use "the user did not explicitly ask for subagents this turn" as a local-only reason. The `implement-slice` invocation is the request to follow this workflow, including subagents when needed.
- If subagents are unavailable or unsafe for this slice, record the local-only reason before editing product code.
- Prefer the prepared `Parallel Work Chunks` from the slice contract.
- Use one subagent per prepared agent-fit chunk when chunks are present and subagents are available.
- Pause for chunks marked `human` unless the user authorizes the agent fallback.
- For `hybrid` chunks, do the agent-fit work and pause for the human decision unless the contract says the fallback is acceptable.
- Prefer one to three chunks/subagents when work can be split into disjoint ownership areas.

When using subagents:
- Assign disjoint ownership.
- Tell each subagent it is not alone in the codebase.
- Tell each subagent not to revert edits made by others.
- Tell each subagent to apply the minimum-implementation ladder before adding custom code: skip what is not needed, prefer stdlib/native/existing helpers, avoid new abstractions or dependencies, and write only the minimum custom code that satisfies the chunk.
- Require each subagent to list changed files and any lightweight verification performed.
- Require each subagent to identify focused tests `implement-tests` should add or update for its chunk.
- Require each subagent to report what it deliberately skipped as YAGNI and any shortcut ceiling or upgrade trigger.
- Require each subagent to report completed, partially completed, or blocked for its chunk.
- Close each subagent after its output is captured and no further input is needed.
