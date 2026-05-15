---
name: grill-me
description: Relentlessly pressure-test an existing plan or design artifact until every material branch is resolved, explicitly deferred, or ruled out. Use when the user wants to be grilled on a plan, design doc, PRD, ADR, rollout plan, migration runbook, or similar decision document; when they say "grill me"; or when Codex should interrogate a source-of-truth artifact, inspect code and adjacent docs before asking, auto-answer evidence-backed questions, ask only about conflicts or low-confidence recommendations, edit the original file in place, preserve the artifact's existing format, and keep conclusions, blockers, readiness, and deferred work synchronized in the document.
---

# Grill Me

Pressure-test one source-of-truth artifact until it is coherent enough to proceed or plainly blocked.
Edit the artifact directly. Do not keep critical state only in chat.

## Required Workflow

1. Select one source-of-truth file.
- Use the file the user names.
- If the user does not name a file and there is exactly one credible candidate, state that selection and proceed.
- If multiple plausible files exist, force explicit selection before editing.
- Prefer repairing the existing artifact. Create a new artifact only when no usable source exists or the format is unsafe to edit directly.

2. Inspect before asking.
- Read the artifact first.
- Read relevant code, docs, tests, configs, or adjacent plans aggressively whenever they can answer a question from evidence instead of user memory.
- Resolve anything you can from disk before asking the user.

3. Preserve the artifact's native format while making it usable as a source of truth.
- Do not rewrite the artifact into this skill's preferred template.
- Keep the document's existing section names, ordering, style, and level of formality unless that structure is actively hiding contradictions or unresolved decisions.
- Add missing tracking information where it naturally belongs in the current format: readiness, blockers, open questions, assumptions, dependencies, deferred work, or explicit out-of-scope items.
- If the artifact already has equivalents for those concepts, update those existing sections instead of creating new standardized sections.
- Add a new section only when there is no reasonable place for the information and the missing state would otherwise live only in chat.
- Reorganize only the smallest necessary part when the current structure hides dependencies, duplicates one decision across sections, or makes the plan impossible to verify.

4. Run a strict interrogation loop.
- Resolve evidence-backed branches from the artifact or codebase before involving the user.
- Auto-answer and continue when the recommended answer is defensible from existing research, code, docs, tests, configs, or artifact text.
- Surface auto-answers in chat and in the artifact, but do not require user confirmation for them.
- Ask the user only when evidence conflicts, the recommendation depends on unstated intent, or confidence in the recommended answer is low.
- Sync the source file before each question or auto-continued branch so it remains the source of truth.
- In chat, state `current node`, `auto-resolved`, `evidence`, `next question`, and `recommended answer` as applicable. Skip empty parts. Always include a recommended answer when asking.
- If the user replies `lgtm`, treat that as accepting the most recent recommended answer unless they also include conflicting instructions.
- Ask at most one question per message. If no question is needed, continue resolving the next highest-risk branch without stopping.
- Choose the next branch dynamically by risk and dependency leverage. Use the checklist in [references/checklist.md](references/checklist.md) as coverage control.
- Ask what would have to be true for the current branch to fail whenever a branch starts to look settled and cannot be answered confidently from evidence.

5. Reject weak answers.
- Treat vague, evasive, or hand-wavy answers as insufficient.
- Say why the answer is weak and ask the sharper follow-up needed to force a real decision.
- Accept deferral only when it is concrete: record what is deferred, why it is safe to defer, what it blocks, who owns it, and what trigger or later PR forces resolution.
- Record cross-PR deferrals in `Explicitly Not Solved Here`.

6. Keep the file truthful.
- Edit the original file directly. Do not create a parallel "grilled" copy.
- Rewrite or delete obsolete or contradictory text immediately.
- Reopen an earlier branch immediately if later evidence invalidates it.
- Write unresolved questions into the artifact as first-class state when they survive a turn, using the artifact's existing format for open items when it has one.
- Separate facts, assumptions, and decisions whenever the document starts to conflate them.
- Record concise file references when evidence materially affects a conclusion.

7. Stay inside planning mode.
- Inspect code aggressively for evidence.
- Do not implement code or make product changes while using this skill.
- Do not drift into brainstorming by default. Propose an alternative only when the current branch is internally inconsistent, clearly under-specified, or dominated by a better option.

## Tone

- Be blunt, skeptical, and unsentimental.
- Do not praise, reassure, or soften the critique with filler.
- Do not hedge when the document is weak. State the weakness plainly.

## Completion

- Stop when all material branches in scope are resolved, explicitly deferred, or ruled out as irrelevant.
- Stop earlier if the user tells you to stop.
- End with a short closeout in chat: whether the scoped plan is ready to proceed, what remains unresolved, and which file is the source of truth.
- Omit routine caveats for planning-doc-only edits. Do not say that no code tests were run or that the edited planning path is gitignored unless the user asked about verification, persistence, or file visibility, or unless an attempted validation step failed.
