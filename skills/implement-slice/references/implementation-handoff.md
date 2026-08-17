# Implementation Handoff

Append implementation attempts to the slice `Implementation Notes` section. Keep the format compact and agent-readable.

```markdown
| date | status | owner_shape | changed_files | verification | notes |
| --- | --- | --- | --- | --- | --- |
| YYYY-MM-DD | in progress | `terra:C1,C2; root:integration` | `path/to/file` | `command` | ... |

Keyed details when needed:
- execution_reason: ...
- worker_models: `gpt-5.6-terra` | none, reason: ...
- chunk_adjustments: none | ...
- human_handoff: none | ...
- simplest_path: ...
- skipped_yagni: none | ...
- existing_helper_used: none | ...
- shortcut_ceiling: none | ...
- upgrade_trigger: none | ...
- accepted_validation_failures: none | compile | typecheck | lint | tests | runtime | ...
- completion_gate: `{contract rows}: {exact command/result}` | incomplete, reason: ...
- verification_fingerprint: `HEAD`; changed paths or owned surface; exact commands/results
- temporary_green_shims_added: no | yes, reason: ...
- final_test_backlog: none | ...
- known_risks: none | ...
- deviations: none | ...
```

Delegation rules:
- Choose delegation by net operating cost.
- Keep small, cohesive, serial implementation in the main context.
- Use subagents for genuine parallel chunks, large finite mechanical batches, broad isolated discovery, or specialist ownership.
- Record the selected execution shape and reason before editing product code.
- Prefer the prepared `Parallel Work Chunks` from the slice contract.
- Use one subagent per prepared agent-fit chunk when chunks provide genuine parallel progress and subagents are available.
- Pause for chunks marked `human` unless the user authorizes the agent fallback.
- For `hybrid` chunks, do the agent-fit work and pause for the human decision unless the contract says the fallback is acceptable.
- Prefer one to three chunks/subagents when work can be split into disjoint ownership areas.

When using subagents:
- Assign disjoint ownership.
- Tell each subagent it is not alone in the codebase.
- Tell each subagent not to revert edits made by others.
- Tell each subagent to apply the minimum-implementation ladder before adding custom code: skip what is not needed, prefer stdlib/native/existing helpers, avoid new abstractions or dependencies, and write only the minimum custom code that satisfies the chunk.
- Tell each subagent not to add temporary shims, fake fallbacks, placeholder adapters, broad validation, or throwaway wiring just to make intermediate validation pass. Accepted intermediate failures should be recorded, not hidden.
- Require each subagent to list changed files and any lightweight verification performed.
- Require each subagent to identify final testing/validation backlog created by its chunk.
- Require each subagent to report what it deliberately skipped as YAGNI and any shortcut ceiling or upgrade trigger.
- Require each subagent to report completed, partially completed, or blocked for its chunk.
- Close each subagent after its output is captured and no further input is needed.
