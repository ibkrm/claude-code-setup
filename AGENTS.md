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

## Component Rules

- One component per file. Filename matches component name in PascalCase.
- Co-locate: `ComponentName/index.tsx`, `ComponentName/ComponentName.test.tsx`
- Shared components in `src/components/`. Feature-specific in `src/features/<feature>/components/`
- Use named exports. No default exports except route files.
- Props interface named `ComponentNameProps`, defined in the same file.

## TypeScript Rules

- Strict mode is on. No `any`. Use `unknown` and narrow it.
- No type assertions (`as SomeType`) unless genuinely unavoidable — add a comment explaining why.
- All API response types must be explicitly defined in `src/types/api.ts`.
- Prefer `interface` for object shapes, `type` for unions and aliases.
- No implicit `any` from missing return types. Always type function return values.

## Testing Rules

- Test runner: Vitest. DOM: `@testing-library/react`.
- Test files live next to the component: `ComponentName.test.tsx`
- Test user behavior, not implementation. Query by role/label, not class or test ID unless no alternative.
- Every new component needs at least a smoke test (renders without crashing).
- For TanStack Query: wrap in `QueryClientProvider` in tests, use `msw` to mock API calls.

## Styling Rules

- Tailwind utility classes only. No custom CSS files unless adding a Tailwind plugin.
- Use shadcn/ui components as the base. Extend via `className` prop with `cn()` helper.
- No inline `style={{}}` props.
- Responsive design mobile-first: `sm:`, `md:`, `lg:` breakpoints.
- Dark mode via `dark:` variant — do not hardcode light-only colors.

## Git Rules

- Conventional commits: `type(scope): description`
- Types: `feat`, `fix`, `refactor`, `test`, `chore`, `docs`, `style`, `perf`
- Scope is the feature or component affected: `feat(auth): add login form`
- Subject line max 72 characters, imperative mood ("add" not "added")
- No "WIP" commits on main. Squash before merging.
