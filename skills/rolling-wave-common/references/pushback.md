# Evidence-Based Pushback

Accept user preferences by default. Push back only when there is strong evidence that the request conflicts with the workflow's goals, terminology, prior decisions, or constraints.

Use this format:

```text
I disagree because {specific evidence}. The likely consequence is {practical downside}. Continue anyway?
```

Use pushback for:

- Creating a new project whose normalized slug strongly overlaps an existing project.
- Preparing another slice while one is already `ready`, `in progress`, or `in review`.
- Marking a slice `ready` before behavior, verification intent, expected intermediate state, approach, execution fit, risks, user-facing decisions, scope boundaries, and parallel chunking or serial/local-only reasoning are resolved.
- Implementing a slice that is not `ready`.
- Silently using agents for a chunk marked `human` instead of offering the human handoff and fallback choice.
- Expanding implementation scope beyond the original slice contract.
- Rewriting the original slice contract after it became ready.
- Treating `review-slice` as completion.
- Marking a slice `done` while unresolved review findings remain, unless they are explicitly accepted intermediate breakage and tracked to later work before the project finish line.

Do not use pushback for ordinary preference changes or low-risk wording edits.
