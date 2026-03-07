---
name: new-component
description: >
  Scaffold a new React component following project conventions — named folder, co-located test,
  typed props, no default export, Tailwind + cn() styling.
  Auto-invoke when the user asks to create, add, or build a new component.
  Also invokable manually with /new-component.
allowed-tools: Read, Write, Bash
argument-hint: "<ComponentName> [feature/<featureName>]"
---

# New Component

Scaffold a new component following project conventions.

## Usage
/new-component <ComponentName> [feature/<featureName>]

## Steps

1. Determine location:
   - If a feature is specified: `src/features/<featureName>/components/<ComponentName>/`
   - Otherwise: `src/components/<ComponentName>/`

2. Create `index.tsx`:
   - Named export only (no default export)
   - Props interface named `<ComponentName>Props` at top of file
   - Typed return (no `React.FC`)
   - Tailwind classes via `cn()` if conditional classes are needed
   - Import `cn` from `@/lib/utils`

3. Create `<ComponentName>.test.tsx`:
   - Import from `@testing-library/react` and `vitest`
   - Wrap in `QueryClientProvider` if component uses TanStack Query
   - Include at minimum a smoke test

4. If the component has local state logic worth isolating, create `use<ComponentName>.ts` in the same folder.

5. Read `docs/architecture.md` to confirm correct location before creating files.

## Output template

```tsx
// index.tsx
import { cn } from '@/lib/utils'

interface <ComponentName>Props {
  className?: string
}

export function <ComponentName>({ className }: <ComponentName>Props) {
  return (
    <div className={cn('', className)}>
      {/* */}
    </div>
  )
}
```

```tsx
// <ComponentName>.test.tsx
import { render, screen } from '@testing-library/react'
import { describe, it } from 'vitest'
import { <ComponentName> } from '.'

describe('<ComponentName>', () => {
  it('renders without crashing', () => {
    render(<<ComponentName> />)
  })
})
```
