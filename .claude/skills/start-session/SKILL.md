---
name: start-session
description: >
  Session startup workflow. Read context, pick the next feature, write a plan, then execute.
  DO NOT auto-invoke — requires explicit /start-session call.
  Use when beginning a new coding session or picking up work from a previous session.
allowed-tools: Read, Write, Bash, Glob, Grep
---

# Start Session

Run at the beginning of every autonomous or interactive session before touching any code.

## Steps

### 1. Read context
- Read `claude-progress.txt` — find the most recent session entry
- Read `features.json` — identify all features with status `in-progress` or `todo`
- Read `CLAUDE.md` — confirm rules are loaded
- Run `/memory` to verify all rules files are active

### 2. Determine task
- If any feature is `in-progress`: that is the task for this session. Do not start a new feature until it is `complete`.
- If no feature is `in-progress`: pick the first `todo` feature by ID order.
- If all features are `complete`: stop and report "All features complete. Add new features to features.json before starting a session."
- If a feature is `blocked`: skip it, note it in your session output, pick the next `todo`.

### 3. Update features.json
- Set the chosen feature's status to `in-progress`
- Do not change any other feature's status

### 4. Hand off to plan mode (interactive) or write a plan (autonomous)

**Interactive mode:**
Output the session start summary below, then stop with this message:

```
→ Press Shift+Tab twice to enter plan mode.
  Claude will research the codebase and produce a plan.
  Review and approve it before execution begins.
```

Do not write any code. Do not continue until the user has entered plan mode,
reviewed the plan, and exited plan mode to approve execution.

**Autonomous mode:**
Write a concise implementation plan covering:
- What files will be created or modified
- What the component/feature structure will look like
- What tests will be written
- Any risks or unknowns

Log the plan in the session's `claude-progress.txt` entry under DECISIONS MADE,
then proceed to execution.

### 5. Confirm acceptance criteria
Read the `acceptance` array for the chosen feature in `features.json`. These are your
definition of done. Do not mark the feature complete until every item passes.

---

## Output at start of session

```
SESSION START
─────────────────────────────
Feature:     [F-ID] [feature name]
Last session: [one-line summary from claude-progress.txt]
Acceptance:
  - [list from features.json]
─────────────────────────────
→ Press Shift+Tab twice to enter plan mode.
  Claude will research the codebase and produce a plan.
  Review and approve it before execution begins.
```
