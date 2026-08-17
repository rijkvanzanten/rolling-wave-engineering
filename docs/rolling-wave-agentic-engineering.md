# Rolling-Wave Agentic Engineering

## Executive summary

We have been experimenting with a more structured way to use coding agents in real engineering work.

The main observation is that agentic engineering is not just a model-quality problem. Better models help, but the bigger unlock is giving the model the right harness, skills, artifacts, and review loops. Without those, even a strong model tends to produce plausible work that still needs a lot of human cleanup.

The workflow described here is called rolling-wave agentic engineering. It keeps a clear project finish line, sketches the broad implementation path, then fully prepares, implements, reviews, and completes concrete slices without planning the whole project in detail up front. Preparation of one slice may overlap implementation or review of another.

The point is to make agentic work easier to review. Instead of asking an agent to execute a whole project plan and then reviewing the scattered last 10-30% across every step, we keep the active review surface small and let each completed slice inform the next one.

This is both a workflow and a small set of Codex skills/agents we have been developing. The repo is a working reference implementation rather than a packaged product: it shows the shape of the system, the assumptions behind it, and the kind of repeatable agent workflows we think are worth investing in.

## Why this exists

Agentic engineering is not just "the model writes more code now." The useful shift is that the agent can inspect the repo, decide what context matters, edit multiple files, run commands, delegate work, review the result, and update project artifacts as it goes.

That also means the model is only one part of the setup.

Model choice matters, but it is not the whole workflow. The harness matters. The skills matter. The artifacts matter. The review loop matters. The way the human steers the agent matters.

A strong model in a weak harness is still limited. A strong model with vague instructions will improvise. A strong model with good skills, access to the right tools, durable project state, and tight review loops starts to feel much closer to an engineering collaborator.

This document explains the rolling-wave agentic engineering workflow I have been experimenting with. The short version:

> Keep a clear project finish line, sketch the broad path, and prepare each concrete slice only when it is close enough to execution for useful detail.

The point is not more process. The point is a smaller and more useful review surface.

Slice boundaries follow review decisions and verification methods, not commit count or product domain. Multiple independently executable or revertible chunks may stay together when one architectural intent or transformation recipe, risk class, and bounded proof cover them. This makes cross-domain mechanical batches valid slices. Split work needing separate approval decisions, unrelated mental models, distinct risk classes, incompatible intermediate states, or materially different verification. Use roughly 500 non-move changed lines as a soft default review budget; lower it for semantic risk and allow larger repetitive mechanical diffs when one proof covers the batch.

## What we built

The current implementation is a Codex plugin-style collection of skills and reviewer-agent prompts.

At a high level, it includes:

- `shape-project`: define the project finish line, success criteria, non-goals, risks, and broad slice sequence
- `prepare-next-slice`: draft the next slice locally by default; use fresh authorship or independent review only when context size, uncertainty, or semantic risk warrants it
- `implement-slice`: implement one ready slice and record what changed
- `deliver-slice`: implement locally unless delegation pays, preserve the review checkpoint, then finalize in fresh context
- `finalize-slice`: review the implementation, fix confirmed findings, and repeat until clean for user review
- `complete-slice`: accept the user's completion call, mark the slice done from any status, and carry available learnings forward
- `code-review`, `code-simplify`, `debug`, and `pr-description`: supporting workflows for normal engineering work
- reviewer-agent prompts for correctness, testing, maintainability, security, reliability, API contracts, TypeScript, Vue, Rust, and planning docs

The repo is useful in two ways:

1. As a working Codex setup
2. As a reference for how to encode an opinionated agentic engineering workflow into reusable skills

The second part is the more important one. The exact prompts will keep changing. The durable idea is the workflow shape: broad direction, narrow execution, explicit learning.

## Who this is for

This document is written for two groups:

- Engineering teammates who may use this workflow directly
- External readers who want to understand how we are thinking about agentic engineering as a practical engineering capability

You do not need to know the details of Codex to understand the model. The Codex skills are just one implementation of the broader workflow.

The main takeaway should be: agentic engineering gets much more useful when the agent is not just asked to "go build the thing", but is instead given a repeatable workflow for shaping, preparing, implementing, reviewing, and learning.

## Agentic engineering in plain terms

Traditional AI-assisted coding is usually local:

- complete this function
- explain this error
- write this test
- refactor this file

Agentic engineering is broader. You give the agent a goal, and it can work across the repo to get to a reviewable result.

A useful setup usually has five parts:

1. A capable model
2. A harness that lets the model use tools safely
3. Skills that teach repeatable workflows
4. Artifacts that preserve decisions and state
5. Review loops that catch mistakes before they compound

The model is the reasoning engine. The harness and skills decide what kind of engineering work it can reliably do.

If the harness cannot run commands, inspect diffs, launch subagents, or edit files, the model has to talk about engineering instead of doing it. If the skills are vague, every session starts from scratch. If there is no durable project artifact, decisions live in chat history and get lost. If there is no review loop, plausible code turns into cleanup work later.

So the better question is not just "which model are we using?"

The better question is:

> What workflow is the model executing, and what tools and artifacts does it have to execute that workflow well?

## The problem with a lot of current agentic workflows

The common pattern looks like this:

1. Brainstorm the whole project
2. Produce a large implementation plan
3. Ask the agent to execute the plan
4. Review everything at the end

That can be genuinely useful. The agent often gets 70-90% of a lot of steps done. That is not nothing.

The problem is the last 10-30%.

That last part is usually where the actual engineering detail lives:

- edge cases
- unclear behavior
- missing tests
- scope mismatches
- API contract details
- migration risks
- naming and data-shape decisions
- awkward integration points
- assumptions that looked fine in the plan but did not survive implementation

When the agent works across the full breadth of a project before review, all of that detail piles up at the end. The reviewer is no longer looking at one focused change. They are looking at a wide surface area where many things are mostly done, but not quite done.

That creates a few predictable failure modes.

### Review debt spreads across the whole project

Instead of one focused review, the human has to inspect product behavior, API shape, data models, tests, docs, migration behavior, UI details, and rollout risks all at once.

Each individual piece may be close. The combined review surface is still expensive.

### The plan goes stale while the implementation teaches new facts

Real implementation changes what you know.

While building step 1, you might discover a constraint that affects step 3. While reviewing step 2, you might realize step 5 needs a different boundary. While wiring the code, you might find the abstraction from the plan is too broad, too narrow, or just pointed at the wrong layer.

That is normal. The issue is that a large upfront plan often treats later steps as if they are still correct after earlier implementation has changed the facts.

### The agent optimizes for continuing the plan

Agents are good at following the shape of the instruction. If the instruction is "execute this whole plan", the agent will often keep moving even when individual steps deserve a pause.

That can look productive, but it creates hidden cleanup work.

### "Done" becomes fuzzy

When many steps are in progress at once, it gets harder to tell whether anything is actually done. Something may be implemented but not verified. A test may exist but miss the important edge. A slice may match the old plan but not the project goal anymore.

Rolling-wave engineering is an attempt to fix that review shape.

## The rolling-wave model

Rolling-wave engineering separates two levels of planning:

1. The project shape
2. The current implementation slice

The project shape is global. It answers:

- What are we trying to finish?
- What does success look like?
- What is explicitly out of scope?
- What constraints and risks matter across the project?
- What is the rough sequence of implementation slices?

The current slice is local. It answers:

- What exact behavior are we implementing now?
- What are the acceptance criteria?
- How will we verify it?
- What scope decisions need to be resolved before coding?
- What risks should review focus on?

Future slices stay intentionally rough. They need enough shape to keep the project coherent, but not so much detail that we pretend we already know everything.

The loop is:

1. Shape the project
2. Prepare the next slice
3. Implement the slice
4. Review the slice
5. Complete the slice
6. Use what we learned to prepare the next one

That keeps the finish line visible while making the active review surface small.

## Why this matters for the business

The practical promise of agentic engineering is not just faster code generation. Faster code generation is useful, but code is rarely the only bottleneck.

The more interesting question is whether agents can help compress the full engineering loop:

- understanding the goal
- finding the relevant context
- making implementation decisions
- writing the code
- verifying behavior
- reviewing risk
- documenting the change
- carrying learnings forward

Rolling-wave engineering is aimed at that full loop.

The hypothesis is that teams will get more leverage from agents when the workflow makes the review surface smaller and the learning loop tighter. That should matter for velocity, but also for quality. A workflow that generates a large amount of nearly-finished work is not necessarily cheaper if it pushes all the hard review work to the end.

This setup is an attempt to make the agent useful in smaller, more reliable units of work.

## The artifacts

This workflow should not depend on chat history. The important state lives in the repo.

```text
docs/rolling-wave/{project}/
  project.md
  slices/
    001-example-slice.md
```

`project.md` is the project-level source of truth. It carries:

- finish line
- success criteria
- non-goals
- constraints
- assumptions
- broad roadmap
- cross-slice decisions
- open questions
- potential risks
- review notes
- change history

Each slice file is the source of truth for one implementation step. It carries:

- behavior
- acceptance criteria
- verification
- likely approach
- scope boundaries
- risks
- user-facing decisions
- implementation notes
- review notes
- completion learnings

Slice statuses are:

- `pending`
- `ready`
- `in progress`
- `ready for review`
- `in review`
- `done`

The important bit: `project.md` owns the project. Slice files own implementation steps.

A later slice is not a way to avoid deciding whether something belongs in the project. It is only a sequencing decision.

## The workflow

### 1. Shape the project

Use this when the project is new, unclear, or changing direction.

The goal is not a full implementation plan. The goal is a clear finish line and a rough implementation sequence.

Good shaping answers:

- What is true when this project is complete?
- What must be observable for us to call it successful?
- What is intentionally out of scope?
- What constraints matter?
- What are the broad implementation slices?
- What should the first slice teach us?

Example prompt:

```text
Use shape-project to shape a rolling-wave project for adding schema read/write tools to our MCP server.
Optimize for getting one reviewable implementation slice done at a time.
```

Expected output:

- `project.md`
- a rough broad roadmap
- one or more pending slices
- unresolved project questions recorded explicitly

### 2. Prepare the next slice

Use this before coding.

This is where the agent should grill the details for exactly one slice:

- behavior
- scope boundaries
- acceptance criteria
- verification
- user-facing decisions
- likely approach
- risks

Example prompt:

```text
Use prepare-next-slice for the schema-tools project.
Prepare the next pending slice and grill me on anything that has to be resolved before implementation.
```

Expected output:

- one slice marked `ready`
- behavior and acceptance criteria clear enough to implement
- verification plan defined
- risks and explicit deferrals recorded

This is not the time to prepare every future slice. Only the next slice needs full readiness.

Preparation does not require every earlier slice to be done. Multiple slices may be `ready`, `in progress`, `ready for review`, or `in review` while different agents work. A concrete unresolved dependency can still keep a later slice pending; another slice's status alone cannot.

### 3. Implement the slice

Use this when a slice is ready.

The agent should implement against the slice contract. If the contract turns out to be wrong, the right move is to pause and record the mismatch rather than silently expanding scope.

Example prompt:

```text
Use implement-slice for the current ready slice.
Use subagents if the work can be split cleanly.
```

Expected output:

- code changes
- slice status set to `ready for review` when implementation completes; partial or blocked work stays `in progress`
- implementation notes
- changed files listed
- verification performed, or explicitly not performed
- known review focus recorded

### 4. Finalize the slice

Use this after implementation.

Finalization compares the implementation to the original slice contract. It confirms findings, fixes required in-contract issues, and repeats review until no required findings remain. Findings that materially change the project shape or contradict a recorded assumption are decisions, not automatic fixes, so the skill asks before proceeding. A clean pass leaves the slice `in review`; it does not mark it done.

Example prompt:

```text
Use finalize-slice for the current ready-for-review slice.
Keep going until it is clean and in review. Ask me before changing project shape or contradicting project assumptions.
```

Expected output:

- confirmed findings fixed
- material project-shape decisions brought to the user
- review and verification repeated until clean
- project-level review notes recorded when useful
- slice status left `in review`

`finalize-slice` owns the review-fix loop. It leaves the clean slice `in review`. `complete-slice` owns the user-approved transition to `done`.

### 5. Complete the slice

Use this when you consider the slice done. User completion authority is final; missing implementation, review, finalization, verification, or child-project state does not block the transition.

Completion is where the workflow captures what changed about our understanding:

- what the implementation taught us
- what future slices should know
- what risks remain
- what reviewers should know for the PR
- whether the broad roadmap needs pressure

Example prompt:

```text
Use complete-slice.
Slice 006 is done. Mark it done and show roadmap progress.
```

Expected output:

- slice marked `done`
- completion learnings recorded
- project risks and review notes updated
- next slice left pending until explicitly prepared

## Supporting workflows

### Code review

Use `code-review` for local or branch review outside the slice-specific flow.

It reviews local tracked and untracked files first. If there are no local changes, it falls back to branch diff against a requested base branch or the Git Town parent branch.

Example:

```text
Use code-review on my current changes.
```

### Code simplify

Use `code-simplify` after implementation if the patch feels more complex than it needs to be.

Example:

```text
Use code-simplify on the current branch.
Keep behavior exactly the same.
```

### Debug

Use `debug` when there is a failing test, runtime error, or reported bug.

Example:

```text
Use debug to investigate this failing test and fix the root cause.
```

### PR description

Use `pr-description` when the slice or branch is ready to share.

It reads:

- branch diff
- rolling-wave project context
- completed slice context
- risks and review notes

Example:

```text
Use pr-description for this branch.
Compare against the Git Town parent branch.
```

Expected output:

```markdown
## Changes

- ...

## Potential Risks

- ...

## Review Notes

- ...
```

## What good usage looks like

Good usage is iterative:

```text
Shape the project broadly.
Prepare one slice deeply.
Implement only that slice.
Review it against the original contract.
Complete it and capture learnings.
Prepare the next slice with those learnings.
```

Bad usage is trying to turn rolling-wave into a full upfront plan:

```text
Fully specify every slice, every file, every test, and every implementation detail before writing any code.
```

That misses the point. Future slices should give enough direction to keep the project coherent, but not so much detail that we ignore what implementation teaches us.

## Team expectations

The useful norms are:

- Treat `project.md` as the global source of truth
- Treat slice files as implementation contracts
- Do not mark a slice `ready` until behavior, scope, verification, and risks are clear
- Mark a slice `done` when the user explicitly declares it complete, regardless of prior workflow status
- Record learnings as soon as they affect future work
- Let the agent push back when a suggestion conflicts with evidence
- Keep future slices broad until they are selected for preparation; selected preparation may overlap implementation or review of another slice

The goal is not to remove human judgment. The goal is to spend human judgment on smaller, better-framed decisions.

## How to evaluate this

For someone outside the team, the easiest way to evaluate the workflow is to look at a small project and ask:

1. Can we state the finish line clearly?
2. Can we sketch the broad slices without pretending every detail is known?
3. Can we prepare one slice until behavior, scope, risks, and verification are clear?
4. Can the agent implement that slice without drifting from the contract?
5. Can review compare the result to the original slice description?
6. Can completion capture learnings that make the next slice better?

If the answer is yes, the workflow is doing its job.

Useful signals:

- The first slice is small enough to review properly
- The agent asks better questions before implementation
- Review findings are concentrated in one slice, not scattered across the whole project
- Future slices improve because earlier slice learnings are recorded
- PR descriptions and review notes get easier to produce because project context already exists

Bad signals:

- The project artifact becomes a giant upfront implementation plan
- Future slices are over-specified before the current slice teaches us anything
- The agent silently expands scope during implementation
- Review is still mostly "read everything and figure out what happened"
- Learnings stay in chat instead of being written back into the project artifacts

## Why this is useful

Rolling-wave agentic engineering changes the review shape.

Instead of reviewing a whole project where many steps are almost done, the team reviews one focused slice that should actually be done. That makes the feedback loop smaller and gives the next slice better context.

It also gives the agent a better job. Instead of asking it to chase a large plan until it runs out of steam, we ask it to finish a bounded step, prove it against a contract, record what it learned, and then continue.

That is the core idea: broad direction, narrow execution, explicit learning.
