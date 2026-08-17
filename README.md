# Rolling-Wave Engineering

A lot of agentic engineering workflows want to scope the whole project up front, then have the agent execute across that entire plan. That works surprisingly well for getting 70-90% of a lot of things done. It also tends to leave the last 10-30% of every step spread across the whole project: unclear edge cases, fuzzy scope calls, incomplete verification, and review debt that all comes due at once.

This is at odds with my own personal working style. I personally keep a clear project finish line, sketch the broad implementation sequence, then fully prepare, implement, review, and complete concrete slices. Each completed slice updates the project context. Preparation can move ahead while another slice is ready, being implemented, or in review when the next contract does not depend on unsettled work.

A slice is one bounded human review decision, not one commit or the smallest independently useful change. It can combine multiple chunks, including cross-domain mechanical changes, when one architectural intent or transformation recipe, risk class, and verification strategy cover the batch. Split work needing separate approval decisions, unrelated mental models, distinct risk classes, or materially different verification. Roughly 500 non-move changed lines is a soft default review budget, not a universal cap.

## What It Adds

- Project shaping that defines the finish line and broad roadmap without turning the whole project into a giant upfront implementation plan
- Main-local slice preparation by default, with fresh authorship or independent grilling only when context size, uncertainty, or semantic risk warrants the handoff
- Execution-fit calls that identify whether a slice or chunk is better suited for agent work, human work, either, or a hybrid handoff
- Exploratory implementation first, with broad test coverage saved for a final testing/validation slice
- Main-local implementation for cohesive work, conditional Terra workers for parallel or large isolated work, plus mandatory fresh-context finalization
- Slice completion that records learnings, risks, and reviewer notes before moving on
- Supporting passes for local code review, simplification, debugging, and PR descriptions

## Skills

### Rolling-Wave Workflow

| Skill | Purpose |
| --- | --- |
| `shape-project` | Define or update the global project direction, success criteria, non-goals, constraints, risks, and rough slice sequence. |
| `prepare-next-slice` | Draft one pending slice locally by default, add fresh authorship or independent grilling when risk warrants it, identify execution fit, and mark it ready. |
| `implement-slice` | Implement a ready slice locally by default, delegate only when coordination pays, then mark it ready for review. |
| `deliver-slice` | Run implementation and fresh-context finalization in one invocation while preserving their lifecycle checkpoint. |
| `finalize-slice` | Review an implemented slice, fix confirmed in-contract findings, ask about material project-shape changes, and repeat until clean while leaving it in review. |
| `complete-slice` | Mark any user-declared finished slice done, capture available material learnings, and show roadmap progress regardless of prior workflow status. |

### Supporting Skills

| Skill | Purpose |
| --- | --- |
| `code-review` | Review local or branch changes with language and risk-specific reviewer agents. |
| `code-simplify` | Tighten changed code for reuse, quality, and efficiency while preserving behavior. |
| `debug` | Investigate failures from reproduction to root cause and fix. |
| `grill-me` | Pressure-test an existing plan or design artifact while preserving its current format. |
| `rolling-wave-common` | Shared references and templates used by the workflow skills. |
| `pr-description` | Draft a PR body from branch changes, rolling-wave project context, and the completed slice. |

## Reviewer Agents

The repo includes reviewer-agent prompt sources under `agents/`:

- Correctness, testing, maintainability, security, reliability, API contract, and adversarial reviewers
- Document reviewers for coherence, feasibility, scope, and flow
- Language/framework reviewers for TypeScript, Vue, and Rust

Worth noting: Codex currently loads plugin skills from `.codex-plugin/plugin.json`, but custom spawnable agents are registered separately from `~/.codex/agents/**/*.toml` or project `.codex/agents/**/*.toml`.

The `agents/*.agent.md` files in this repo are source prompts. Convert or register them as Codex agent TOML files if you want `spawn_agent` to see them.

## Project Artifacts

Rolling-wave project state lives in the repository being worked on:

```text
docs/rolling-wave/{project}/
  project.md
  slices/
    001-example-slice.md
```

`project.md` is the project-level source of truth. It carries the finish line, success criteria, broad roadmap, cross-slice decisions, open questions, risks, review notes, and change history.

Each slice file describes one concrete implementation step:

- behavior
- acceptance criteria
- verification intent
- likely approach
- execution fit
- parallel work chunks
- scope boundaries
- risks
- implementation notes
- final test backlog
- review notes
- completion learnings

Slice statuses:

- `pending`
- `ready`
- `in progress`
- `ready for review`
- `in review`
- `done`

## Workflow

1. Use `shape-project` to establish the finish line and rough slice sequence.
2. Use `prepare-next-slice` to make one pending slice ready.
3. Use `deliver-slice` to implement locally unless parallelism or scale justifies workers, preserve `ready for review`, then run `finalize-slice` in fresh context.
4. Alternatively invoke `implement-slice` and `finalize-slice` separately when you want a manual checkpoint between phases.
5. Do manual cleanup or review.
6. Use `complete-slice` when you declare the slice done. Prior implementation or finalization status does not block completion.
7. Repeat from `prepare-next-slice`.
8. Use the final testing/validation slice to add project-level test coverage once implementation has settled.

Useful supporting passes:

- Use `code-review` before opening or merging a PR
- Use `code-simplify` for a focused cleanup pass after implementation
- Use `pr-description` to draft a PR body from branch diff plus rolling-wave context
- Use `debug` when a test, runtime error, or reported bug needs root-cause analysis

## How To Use This

This repo is mainly meant as source material, not as a polished plugin distribution.

I'd urge you to make this your own:

1. Read through the skills and agents
2. Keep the parts that match how you work
3. Rewrite the parts that do not
4. Install your edited versions into your own Codex setup

The useful bit here is the shape of the workflow, not the exact wording of every prompt :)

For Codex, the skills live under:

```text
skills/
```

and the reviewer-agent source prompts live under:

```text
agents/
```

You can copy individual skill folders into your Codex skills directory, or keep this repo as a local plugin while you experiment.

## Optional Local Plugin Setup

The repo is also structured as a local Codex plugin. The manifest lives at:

```text
.codex-plugin/plugin.json
```

Clone it somewhere stable:

```bash
git clone https://github.com/rijkvanzanten/rolling-wave-engineering.git
```

Add a local marketplace entry that points at the parent folder containing this repository:

```toml
[marketplaces.local]
source_type = "local"
source = "/path/to/parent-folder"

[plugins."rolling-wave-engineering@local"]
enabled = true
```

Restart Codex after enabling the plugin or bumping the plugin version.

## Attribution

This plugin was shaped by a few related agent-workflow projects:

- [Compound Engineering](https://github.com/EveryInc/compound-engineering-plugin) by Every: several debugging, review, simplification, and reviewer-agent patterns were adapted from its MIT-licensed plugin
- [grill-me](https://github.com/mattpocock/skills/blob/main/skills/productivity/grill-me/SKILL.md) by Matt Pocock: inspired the pressure-testing workflow behind `grill-me`, `shape-project`, and slice preparation
- [superpowers](https://github.com/obra/superpowers) by Jesse Vincent: inspired the broader idea of composing durable agent skills into opinionated engineering workflows

## License

MIT
