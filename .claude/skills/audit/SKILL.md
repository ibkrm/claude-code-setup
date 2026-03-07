---
name: audit
description: >
  Codebase compliance audit. Scans src/ and produces a violations report across API types,
  TypeScript strictness, component structure, naming, styling, and git conventions.
  DO NOT auto-invoke — requires explicit /audit call.
  Use before a cleanup sprint, after a period of autonomous sessions, or as a periodic health check.
  Does NOT fix anything — report only.
allowed-tools: Read, Bash, Glob, Grep
---

# Audit

Scan the codebase and produce a violations report. Do NOT fix anything. Report only.

## Steps

### 1. Discover scope
- Find all files under `src/` matching `**/*.ts` and `**/*.tsx`
- List them grouped by: `components/`, `features/`, `routes/`, `hooks/`, `lib/`, `types/`, and anything else found
- Note any files that don't fit the expected structure — these are structural violations

### 2. Check each category

#### API Types
- Find every file that manually defines types matching API response shapes (look for interfaces/types with fields like `id`, `createdAt`, `data`, nested objects that smell like server responses)
- Flag any type in `src/types/api.ts` that was hand-written (not generated)
- Check if `openapi.json` exists in the repo
- Check if `gen:types` script exists in `package.json`

#### API Calls
- Find every `fetch(`, `axios.`, `.get(`, `.post(`, `.put(`, `.delete(`, `.patch(` call
- Flag any that live outside a TanStack Query `queryFn` or `mutationFn`
- Note the file and line number for each violation

#### TypeScript
- Find every `any` usage (`: any`, `as any`, `<any>`)
- Find every non-null assertion `!` outside test files
- Find every type assertion `as SomeType` — list them for review, not all are violations
- Find every `React.FC` usage
- Find every function missing an explicit return type
- Check `tsconfig.json` — confirm `strict: true` is set

#### Component Structure
- For each component file, check:
  - Is it in a named folder (`ComponentName/index.tsx`) or a flat file (`ComponentName.tsx`)? Flag flat files.
  - Does it use a default export? Flag it (except route files)
  - Is the props interface named `ComponentNameProps`? Flag mismatches
  - Does a co-located test file exist? Flag missing tests

#### File & Folder Naming
- Flag any component folder or file not in PascalCase
- Flag any hook file not prefixed with `use`
- Flag any feature that imports directly from another feature's internal files (not via `index.ts`)

#### Styling
- Find every `style={{` inline style prop — flag file and line
- Find any hardcoded color values outside Tailwind classes (e.g. `#fff`, `rgb(`, `hsl(`)
- Find any standalone `.css` file in `src/` (except globals)

#### Git
- Run `git log --oneline -20` and flag any commits that don't follow conventional commit format
- Note: this is informational only, not fixable via code changes

---

## Output format

Produce the report in this exact structure:

```
# Audit Report
Generated: <date>

## Summary
| Category             | Violations |
|----------------------|------------|
| API Types            | N          |
| API Calls            | N          |
| TypeScript           | N          |
| Component Structure  | N          |
| Naming               | N          |
| Styling              | N          |
| Git                  | N          |
| TOTAL                | N          |

## Violations by Category

### API Types
- [ ] `src/types/user.ts:12` — hand-written type `UserResponse`, likely duplicates API shape
...

### API Calls
- [ ] `src/features/auth/LoginForm/index.tsx:34` — raw fetch outside queryFn
...

### TypeScript
- [ ] `src/lib/utils.ts:8` — `any` usage
...

### Component Structure
- [ ] `src/components/Button.tsx` — flat file, should be `Button/index.tsx`
- [ ] `src/components/Card/index.tsx` — default export, should be named export
- [ ] `src/features/auth/LoginForm/` — no test file found
...

### Naming
...

### Styling
...

### Git (informational)
...

## Recommended fix order
1. API Types — generate from OpenAPI first, unblocks everything else
2. API Calls — migrate to TanStack Query
3. Component Structure — rename/move files
4. TypeScript — fix any and missing types
5. Tests — add smoke tests last once structure is settled
```

## Important
- Do not modify any files
- Do not suggest fixes inline — save that for the fix phase
- If a file cannot be read, note it in the report and continue
- Update `docs/quality.md` with the audit results after completing the report
- When done, ask: "Report complete. Review it and confirm which phases you want to proceed with."
