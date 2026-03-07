# Component Rules

- One component per file. Filename matches component name in PascalCase.
- Co-locate related files: `ComponentName/index.tsx`, `ComponentName/ComponentName.test.tsx`
- Shared/reusable components → `src/components/`
- Feature-specific components → `src/features/<feature>/components/`
- Use named exports everywhere. No default exports except TanStack Router route files.
- Props interface named `ComponentNameProps`, defined at the top of the same file.
- Keep components focused. If a component exceeds ~150 lines, split it.
- Custom hooks that belong to one component live in the same folder: `ComponentName/useComponentName.ts`
- Shared hooks → `src/hooks/`

## Feature folder structure

```
src/features/auth/
  components/
    LoginForm/
      index.tsx
      LoginForm.test.tsx
  hooks/
    useAuth.ts
  index.ts        ← public API for the feature (re-exports only)
```

Never import directly from deep inside another feature. Use the feature's `index.ts`.
