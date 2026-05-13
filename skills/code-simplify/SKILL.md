---
name: code-simplify
description: Review changed code for reuse, code quality, and efficiency, then apply worthwhile simplifications. Use when the user asks to simplify, clean up, tighten, or de-hack recent changes; when a patch feels more complex than necessary; or when Codex should do a focused post-pass on edited files before handing work back.
---

# Code Simplify

Review recent code changes with three lenses: reuse, quality, and efficiency. Fix concrete issues directly while preserving behavior exactly. Prefer readable, explicit code over overly compact cleverness, and avoid churn for style-only tweaks or speculative refactors.

## Workflow

1. Determine the review scope.
   - If the user named a file, directory, function, or time window, use that scope and do not widen it.
   - Otherwise, in a git repository, prefer the current branch diff against the Git Town parent branch:

```bash
CURRENT_BRANCH=$(git rev-parse --abbrev-ref HEAD)
BASE_BRANCH=$(git-town config get-parent "$CURRENT_BRANCH" 2>/dev/null || true)
```

   - If Git Town cannot resolve a parent, fall back to the branch upstream. If no base or upstream can be resolved, fall back to `git diff HEAD`.
   - If there is no git delta, inspect the files the user mentioned or the files edited earlier in the conversation.
   - If no non-empty scope is available, stop and ask what to simplify.
2. Build full context before judging the code.
   - Read the changed files.
   - Search nearby modules and shared utilities for existing helpers before keeping new logic.
   - Check the surrounding patterns so the cleanup matches the codebase instead of imposing a new style.
3. Run the three review passes.
   - If delegation is available and explicitly authorized, run the passes in parallel.
   - Otherwise run them locally, one pass at a time, against the same scope.
4. Apply the worthwhile fixes.
   - Change the code directly.
   - Skip false positives or low-value churn without arguing with them.
5. Verify the result.
   - Run typecheck and lint when configured and reasonably scoped.
   - Run tests scoped to changed paths when possible.
   - Broaden checks when simplification touched shared utilities, heavily imported modules, data flow, or behavior-sensitive code.
   - If no scoped test mechanism exists and the change has meaningful blast radius, run the full relevant suite.
   - Do not weaken tests, assertions, or types to make verification pass. Fix the simplification or revert the specific simplification that caused the regression.
   - Report what changed and any remaining risks.

## Review Passes

### Reuse

- Replace newly written helpers with existing utilities when the behavior already exists.
- Collapse hand-rolled string, path, env, parsing, or type-guard logic into established helpers.
- Remove duplicated functionality introduced under a new name.
- Prefer structural search or project tooling over plain text grep when proving something is unused.

### Quality

- Remove redundant state, cached values, or effects when the value can be derived or invoked directly.
- Shrink parameter sprawl by restructuring instead of threading more flags through old APIs.
- Merge copy-paste variants into a shared abstraction when the abstraction stays clearer than the duplication.
- Tighten leaky abstractions and stringly-typed code when existing constants, unions, or boundaries already exist.
- Flatten nested conditionals, ternary chains, and deeply nested switches when guard clauses, early returns, lookup tables, or simple `else if` chains make the flow easier to verify.
- Remove dead code, unused imports, unused exports, and unreachable branches when project tooling or a reliable search proves they are unused. Account for re-exports, dynamic imports, framework conventions, and public API surfaces; skip uncertain removals.
- Remove unnecessary wrapper elements in component-tree UI frameworks when the wrapper adds no layout, semantic, accessibility, or styling value and the child component can express the needed behavior directly.
- Delete comments that explain what the code does; keep only non-obvious why.

### Efficiency

- Remove redundant work, duplicate reads, duplicate requests, and avoidable recomputation.
- Parallelize independent work when the surrounding code supports it cleanly.
- Trim new hot-path work in render, request, startup, polling, or event-heavy paths.
- Add change-detection guards for recurring updates so no-op cycles do not notify downstream consumers.
- Verify wrapper updater/reducer helpers preserve the project's no-change signal, such as same-reference returns, so callers' no-op guards actually work.
- Prefer doing the operation and handling failure over pre-checking existence when that removes a TOCTOU pattern.
- Remove unbounded data structures, missing cleanup, listener leaks, and avoidable retained state introduced by the change.
- Reduce overly broad reads or loads when only a narrow slice is needed.

## Guardrails

- Prefer simplification over cleverness.
- Do not invent abstractions just to satisfy the review pass.
- Do not rewrite unrelated code.
- Preserve the existing architecture unless the current change clearly violates it.
- Do not remove docs, plans, rolling-wave artifacts, or other workflow/source-of-truth files just because they are not runtime code.
- Treat public exports and framework entrypoints conservatively; unused-looking code can be externally consumed.
- Escalate instead of forcing a risky refactor that would change behavior, broaden scope, or require a product decision.

## Output

- Summarize what was already good, what was simplified, and which checks ran.
- If the code was already clean enough, say so plainly.
- If checks were not run or could not run, say that explicitly.
