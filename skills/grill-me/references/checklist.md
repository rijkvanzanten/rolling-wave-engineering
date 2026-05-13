# Grill Me Checklist

Use this file to choose the next question and to keep coverage honest.
Do not walk the list mechanically. Prioritize the highest-risk unresolved branch first.

## Core Branches

Check each branch unless it is clearly irrelevant to the scoped artifact.

### Goal and Success Criteria
- What outcome is the plan trying to achieve?
- How will the user know it succeeded?
- Is the readiness verdict aligned with that success bar?

### Scope Boundaries
- What is in scope right now?
- What is explicitly not solved here?
- Are there hidden side quests masquerading as requirements?

### Facts, Assumptions, Decisions
- What is known from evidence?
- What is guessed but not proven?
- What is an actual choice?
- Are assumptions being written like facts?

### Constraints
- What technical, organizational, timing, or compatibility constraints shape the plan?
- Which constraints are hard, and which are just preferences?

### Dependencies and Sequencing
- What must already be true before this step works?
- Which branch is the real blocker?
- Is the sequence causal, or just the order the author happened to write it down?

### Interfaces and Contracts
- What APIs, schemas, events, jobs, permissions, or external systems does the plan rely on?
- Which contracts could drift or be misunderstood?

### Failure Modes
- What would have to be true for this branch to fail?
- What hidden assumption breaks the plan?
- What is the ugliest realistic failure, not the prettiest one?

### Rollback and Recovery
- If the plan goes wrong, how does the team recover?
- Is rollback possible, partial, or fake?
- What state becomes hard to unwind?

### Validation and Testing
- How will the work be verified?
- Which checks prove the risky parts, not just the happy path?
- Is there a crisp "done" signal?

### Ownership and Operations
- Who owns the unresolved work?
- Who operates or monitors the outcome?
- Does the plan rely on unnamed people doing unnamed work?

### Deferred Work
- What is intentionally deferred?
- Why is the deferral safe?
- What does the deferral block or complicate later?
- What later PR, stage, or trigger closes it?

## Interrogation Heuristics

- Resolve from disk before asking the user.
- Auto-answer and continue when existing evidence supports a defensible recommendation.
- Surface auto-answers with their evidence, but do not pause for confirmation.
- Ask only when evidence conflicts, the recommendation depends on unstated intent, or confidence is low.
- If multiple branches are open, ask the question that collapses the most downstream uncertainty.
- If the user answer conflicts with code or docs, reconcile the contradiction immediately.
- If the user replies `lgtm`, record the most recent recommended answer as accepted unless they also give conflicting instructions.
- If the user rejects the recommendation, record the rationale, downside, and revisit trigger before moving on.
- If a branch remains unresolved after the turn, write it into the artifact immediately in the artifact's existing format.
- If the artifact gets messy, add the minimum structure needed to keep it interrogable without converting it to a new template.

## Status Tracking

Prefer the artifact's existing conventions for status, readiness, blockers, open questions, and deferred work. Do not force this exact shape into documents that already track those concepts differently.

If the artifact has no status convention and the missing state would otherwise be lost, add the smallest useful status marker. This is an example, not a required template:

```md
## Status
- Readiness: Ready for PR1 scope / Not ready / Blocked
- Scope: Entire plan / specific section
- Critical blockers: ...
- Branch coverage: goals, scope, assumptions, constraints, dependencies, interfaces, failure modes, rollback, validation, ownership, deferred work
```
