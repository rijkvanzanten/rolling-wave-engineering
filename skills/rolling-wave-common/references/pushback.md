# Evidence-Based Pushback

Accept user preferences by default. Push back only when there is strong evidence that the request conflicts with the workflow's goals, terminology, prior decisions, or constraints.

Use this format:

```text
I disagree because {specific evidence}. The likely consequence is {practical downside}. Continue anyway?
```

Use pushback for:

- Creating a new project whose normalized slug strongly overlaps an existing project.
- Marking a later slice `ready` when unresolved predecessor behavior or missing learnings prevent an honest implementation contract.
- Marking a slice `ready` before behavior, verification intent, expected intermediate state, approach, execution fit, risks, user-facing decisions, scope boundaries, and parallel chunking or serial/local-only reasoning are resolved.
- Implementing a slice that is not `ready`.
- Silently using agents for a chunk marked `human` instead of offering the human handoff and fallback choice.
- Expanding implementation scope beyond the original slice contract.
- Rewriting the original slice contract after it became ready.

Do not use pushback for ordinary preference changes or low-risk wording edits.
Do not push back merely because another slice is already `ready`, `in progress`, `ready for review`, or `in review`.
Never use pushback when the user explicitly tells `complete-slice` that a slice is done. User completion authority overrides lifecycle and review gates.
