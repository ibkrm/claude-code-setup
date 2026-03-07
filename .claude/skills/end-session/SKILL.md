---
name: end-session
description: >
  Session close-out workflow. Verify quality, commit clean code, update progress log.
  DO NOT auto-invoke — requires explicit /end-session call.
  Use at the end of every coding session before closing. Do not skip even if the task feels incomplete.
allowed-tools: Read, Write, Bash
---

# End Session

Run at the end of every session before closing. Do not skip even if the task feels incomplete.

## Steps

### 1. Verify code quality
- Run `npm run typecheck` — fix all errors before proceeding
- Run `npm run lint` — fix all errors before proceeding
- Run `npm test` — all tests must pass
- If any check fails: fix it now. Do not proceed to commit with failing checks.

### 2. Assess feature status
Check each acceptance criterion from `features.json` for the current feature:
- If ALL criteria pass → mark feature `complete` in `features.json`
- If SOME criteria pass → keep status as `in-progress`, note what remains in progress log
- If BLOCKED by something outside your control → set status to `blocked`, describe the blocker clearly

### 3. Commit
- Stage all changed files
- Write a conventional commit message: `type(scope): description`
- Every session must end with at least one clean commit — no WIP code left uncommitted
- If the feature is only partially done, commit what's clean: `feat(auth): add login form UI, pending error states`

### 4. Update claude-progress.txt
Prepend a new entry at the top of `claude-progress.txt` using this format:

```
================================================================================
SESSION: [today's date] [your identifier]
STATUS: [complete | in-progress | blocked]
FEATURE: [F-ID] [feature name]
================================================================================

WHAT WAS DONE:
- [bullet list of actual changes made]

COMMITS:
- [hash] [commit message]

DECISIONS MADE:
- [any choices made about approach, structure, libraries — future sessions need this]

NEXT SESSION SHOULD:
- [specific, actionable next step — not vague]

BLOCKERS / OPEN QUESTIONS:
- [anything that needs a human decision or is unresolved]

================================================================================
```

### 5. Final output

```
SESSION END
─────────────────────────────
Feature:     [F-ID] [feature name]
Status:      [complete | in-progress | blocked]
Checks:      typecheck ✓  lint ✓  tests ✓
Commit:      [hash] [message]
Next:        [one-line summary of next session's task]
─────────────────────────────
```

## Rules
- Never end a session with uncommitted code
- Never mark a feature complete unless all acceptance criteria in features.json pass
- Never skip updating claude-progress.txt — it is the only memory the next session has
- If tests cannot pass due to a genuine blocker (missing env var, external service down), document it clearly and set status to `blocked`
