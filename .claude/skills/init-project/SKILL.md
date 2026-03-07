---
name: init-project
description: >
  Project initialisation workflow. Decomposes a high-level product description into
  a structured features.json and initialises claude-progress.txt.
  DO NOT auto-invoke — requires explicit /init-project call.
  Use when starting a new project or a new body of work from scratch.
  Do not run if features.json already has in-progress or complete features — use /audit instead.
allowed-tools: Read, Write, Bash
argument-hint: "<high-level description of what to build>"
---

# Init Project

Run once at the start of a new project or a new body of work. Generates features.json and
a blank claude-progress.txt from a high-level description.

## Usage
/init-project "[high-level description of what to build]"

Example:
/init-project "A UI for a team task manager. Users can log in, see their assigned tasks in
a dashboard, create new tasks, and manage their profile settings. Connects to a FastAPI BFF."

---

## Steps

### 1. Understand the scope
Read the description carefully. If it is ambiguous or missing critical information, ask
at most 2 clarifying questions before proceeding. Do not ask about implementation details
— only ask if the product scope itself is unclear.

### 2. Decompose into features
Break the description into discrete, shippable features. Rules for decomposition:
- Each feature must be completable in a single Claude Code session (~1-2 hours of work)
- If a feature feels too large, split it
- Order features so each one builds on the previous — no feature should depend on a later one
- Always include infrastructure features first (scaffold, type generation) before product features
- Each feature needs 3-5 concrete, testable acceptance criteria — not vague goals

### 3. Identify the standard infrastructure features
Always prepend these unless they already exist in the repo:

- **F001 – Project scaffold**: Vite + React + TanStack Router + TanStack Query + shadcn/ui +
  Tailwind + TypeScript strict. Acceptance: dev server starts, typecheck passes, lint passes.

- **F002 – OpenAPI type generation**: openapi.json committed, gen:types script wired up,
  src/types/api.ts generated. Acceptance: npm run gen:types runs clean, no hand-written API types.

Then add product features starting at F003.

### 4. Generate features.json
Output the complete features.json in this format:

```json
{
  "_readme": "Structured feature tracker. Claude reads and updates this file incrementally. Do not convert to Markdown. Add features here before starting work on them.",
  "_statuses": ["todo", "in-progress", "complete", "blocked"],
  "features": [
    {
      "id": "F001",
      "name": "Feature name",
      "status": "todo",
      "description": "One sentence describing what this feature delivers.",
      "acceptance": [
        "Specific, verifiable criterion",
        "Another criterion",
        "And another"
      ],
      "depends_on": [],
      "notes": ""
    }
  ]
}
```

`depends_on` is an array of feature IDs that must be complete before this one starts.
Leave empty `[]` if no dependency.

### 5. Write features.json to disk
Write the generated file to `features.json` at the repo root.

### 6. Initialise claude-progress.txt
If `claude-progress.txt` does not exist, create it with this content:

```
# Claude Progress Log
# Updated at the end of every session by /end-session
# Read at the start of every session by /start-session
# Do not delete entries — append only. Keep last 10 sessions max then archive.

================================================================================
SESSION: [DATE] INIT
STATUS: complete
FEATURE: Project initialised
================================================================================

WHAT WAS DONE:
- Generated features.json from project description
- [N] features defined, ready to begin with F001

DECISIONS MADE:
- [list any scope decisions made during decomposition]

NEXT SESSION SHOULD:
- Run /start-session to begin F001

BLOCKERS / OPEN QUESTIONS:
- [anything that needs a human decision before work starts]

================================================================================
```

### 7. Present summary for human review

Output a readable summary in this format:

```
PROJECT INIT COMPLETE
─────────────────────────────────────────────────
[N] features generated. Review features.json before starting work.

ID     Name                          Depends on
──     ────────────────────────────  ──────────
F001   Project scaffold              —
F002   OpenAPI type generation       F001
F003   [next feature]                F002
...

Estimated sessions: [N]
─────────────────────────────────────────────────
⚠  Review features.json now. Adjust scope, acceptance criteria, or
   order before running /start-session. Once sessions begin, changes
   to completed features require a new feature entry — not edits.
─────────────────────────────────────────────────
```

### 8. Stop
Do not write any application code. Do not run /start-session automatically.
Wait for explicit human confirmation that features.json looks correct.
