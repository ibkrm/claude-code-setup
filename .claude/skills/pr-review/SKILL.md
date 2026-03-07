---
name: pr-review
description: >
  Pre-PR checklist. Runs typecheck, lint, tests, diff review, commit hygiene check,
  and produces a PR description. DO NOT auto-invoke — requires explicit /pr-review call.
  Use before pushing a branch for review.
allowed-tools: Read, Bash, Glob, Grep
---

# PR Review

Run a pre-PR checklist before pushing a branch for review.

## Steps

1. **Typecheck** — run `npm run typecheck`. Fix all errors. No suppressions without comments.

2. **Lint** — run `npm run lint`. Fix all errors. Warnings are acceptable if intentional.

3. **Tests** — run `npm test`. All tests must pass. If you added behavior, confirm there's a test for it.

4. **Review the diff** — scan changed files for:
   - Any `any` types introduced
   - Inline `style={{}}` props
   - Hardcoded strings that should be constants or env vars
   - `console.log` left in
   - Components missing a test
   - Raw `fetch` calls outside TanStack Query
   - Hand-written API types that should be generated

5. **Commit hygiene** — check `git log` for the branch:
   - No "WIP" or "misc" commits
   - All commits follow conventional format
   - Squash fixup commits if present

6. **Summary** — produce a short PR description in this format:
   ```
   ## What
   [What changed and why]

   ## How
   [Approach taken, any notable decisions]

   ## Testing
   [How this was tested]
   ```

Report any issues found with file + line reference. Do not auto-fix without confirmation.
