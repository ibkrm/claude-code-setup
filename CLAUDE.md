# Project

React UI application. Separate backend API — this repo contains frontend only.

**Stack:** React 18, TanStack Router, TanStack Query, shadcn/ui, TypeScript (strict), Tailwind CSS, Vite

**Package manager:** Always use `npm`. Never use pnpm, yarn, or bun.

## Commands

```
npm run dev          # start dev server
npm run build        # production build
npm test             # run tests (Vitest)
npm run lint         # ESLint
npm run typecheck    # tsc --noEmit
```

Always run `npm run typecheck && npm run lint` before marking a task complete.

## API

- Base URL from `import.meta.env.VITE_API_URL`
- Auth via Bearer token stored in context (never localStorage)
- All API calls go through TanStack Query — no raw fetch outside of query/mutation functions
- API types live in `src/types/api.ts`

## Project Structure

```
src/
  components/      # shared/reusable UI components
  features/        # feature-specific components and logic
  routes/          # TanStack Router route files
  hooks/           # shared custom hooks
  lib/             # utils, api client, helpers
  types/           # TypeScript types (api.ts, etc.)
```

## Docs

@docs/architecture.md
@docs/bff-contract.md
@docs/quality.md

## Rules

@.claude/rules/components.md
@.claude/rules/typescript.md
@.claude/rules/testing.md
@.claude/rules/styling.md
@.claude/rules/git.md
