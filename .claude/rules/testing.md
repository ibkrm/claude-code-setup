# Testing Rules

- Test runner: Vitest. DOM environment: `@testing-library/react` + `@testing-library/user-event`.
- Test files live next to the component: `ComponentName/ComponentName.test.tsx`
- Utility/hook tests: `useHookName.test.ts` next to the hook file.

## What to test

- Every component needs at minimum a smoke test (renders without crashing).
- Test user-visible behavior, not implementation details.
- Do not test internal state directly. Test what the user sees and does.

## Query priority (in order)

1. `getByRole` — prefer always
2. `getByLabelText` — for form fields
3. `getByText` — for visible text
4. `getByTestId` — last resort only, add `data-testid` sparingly

## TanStack Query in tests

- Wrap components in a fresh `QueryClientProvider` per test.
- Use `msw` (Mock Service Worker) to mock API responses — do not mock `fetch` directly.
- Reset handlers in `afterEach`.

## Structure

```ts
describe('ComponentName', () => {
  it('renders without crashing', () => { ... })
  it('shows error state when API fails', () => { ... })
  it('calls onSubmit with correct values', async () => { ... })
})
```

- `it()` descriptions start with a verb, describe behavior: "shows", "calls", "navigates", "disables".
- No `test()` — use `it()` consistently.
- Avoid `beforeAll` / `afterAll` — prefer isolated per-test setup.
