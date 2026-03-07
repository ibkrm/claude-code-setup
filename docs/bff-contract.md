# BFF Contract

## Overview

The UI connects exclusively to a FastAPI BFF (Backend for Frontend).
The BFF's OpenAPI schema is the contract between the two repos.
This document describes how to keep the contract in sync and what to do when it changes.

## The Type Generation Pipeline

```
FastAPI BFF
  └── auto-generates /openapi.json at runtime
        ↓
  committed to this repo as /openapi.json
        ↓
  npm run gen:types
        ↓
  src/types/api.ts  (GENERATED — never edit manually)
        ↓
  TypeScript catches all mismatches at build time
```

## How to Update Types When the BFF Changes

1. BFF developer updates their Pydantic models and merges to their main branch
2. Pull the updated schema:
   ```bash
   curl http://localhost:8000/openapi.json > openapi.json
   # or from staging:
   curl https://api.staging.yourdomain.com/openapi.json > openapi.json
   ```
3. Regenerate types:
   ```bash
   npm run gen:types
   ```
4. Run typecheck — TypeScript will surface every place the UI is now broken:
   ```bash
   npm run typecheck
   ```
5. Fix all type errors before committing
6. Commit both `openapi.json` and `src/types/api.ts` together in the same commit:
   ```
   chore(types): update API types from BFF schema [date]
   ```

## Rules

- `openapi.json` must always be committed — it is the contract record
- `src/types/api.ts` must always be committed — it is the generated output
- Neither file should ever be in `.gitignore`
- Hand-writing types that duplicate API shapes is banned — see `typescript.md`
- All BFF endpoints must use Pydantic response models — untyped endpoints won't appear in the schema

## Runtime Validation

TypeScript types are erased at runtime. For critical API boundaries, use Zod to validate
the actual shape returned:

```ts
// src/features/auth/hooks/useCurrentUser.ts
import { z } from 'zod'
import { components } from '@/types/api'

// Mirror the generated type with a Zod schema for runtime validation
const UserSchema = z.object({
  id: z.string(),
  email: z.string().email(),
  name: z.string(),
})

export function useCurrentUser() {
  return useQuery({
    queryKey: ['current-user'],
    queryFn: async () => {
      const data = await apiClient.get('/users/me')
      return UserSchema.parse(data) // throws if BFF returns unexpected shape
    },
  })
}
```

Use Zod validation on any endpoint where a shape mismatch would cause a silent UI bug
(auth, user data, critical business data). Not required on every endpoint.

## CI Check

The CI pipeline validates that `src/types/api.ts` is not out of date with `openapi.json`.
See `.github/workflows/harness.yml`.

If CI fails on this check, it means someone updated `openapi.json` without regenerating
types. Fix by running `npm run gen:types` and committing the result.
