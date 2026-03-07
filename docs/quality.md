# Quality

Tracks the current state of code quality across the codebase.
Updated after each `/audit` run. Claude should read this before planning any refactor or cleanup work.

## Last Audit

Date: not yet run
Run by: —

## Status by Domain

| Area | Status | Notes |
|---|---|---|
| API Types | 🔴 not audited | |
| API Calls | 🔴 not audited | |
| TypeScript strictness | 🔴 not audited | |
| Component structure | 🔴 not audited | |
| Test coverage | 🔴 not audited | |
| Styling | 🔴 not audited | |

Status key: 🟢 clean · 🟡 minor issues · 🔴 violations present / not audited

## Known Gaps

> Add known issues here as they are discovered, even if not yet fixed.
> Format: [area] — [description] — [added date]

- None recorded yet

## Decisions and Exceptions

> If a rule from the rules files is intentionally not followed somewhere, record it here
> with a reason. This prevents Claude from "fixing" things that were deliberate exceptions.

- None recorded yet

## Audit History

| Date | Total violations | Resolved | Added |
|---|---|---|---|
| — | — | — | — |

## How to Update This File

After running `/audit`:
1. Update the Last Audit date
2. Update each area's status based on the report
3. Move resolved items out of Known Gaps
4. Add any new findings to Known Gaps
5. Append a row to Audit History

Claude should update this file as part of `/end-session` if an audit was run during the session.
