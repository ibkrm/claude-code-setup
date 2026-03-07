# TypeScript Rules

- Strict mode is on (`"strict": true` in tsconfig). Never relax it.
- No `any`. Use `unknown` and narrow with type guards.
- No type assertions (`as SomeType`) unless unavoidable — add an inline comment explaining why.
- Always type function return values explicitly. No implicit returns from untyped functions.
- Prefer `interface` for object shapes. Use `type` for unions, intersections, and aliases.
- All API response shapes must be defined in `src/types/api.ts`.
- Use `satisfies` operator instead of `as` when checking object literals against a type.
- Enums are banned. Use `const` objects + `typeof` instead:
  ```ts
  // ✅
  const Status = { Active: 'active', Inactive: 'inactive' } as const
  type Status = typeof Status[keyof typeof Status]

  // ❌
  enum Status { Active = 'active', Inactive = 'inactive' }
  ```
- Non-null assertions (`!`) are banned except in test files.
- `React.FC` is banned. Type props directly:
  ```ts
  // ✅
  function Button({ label }: ButtonProps) {}

  // ❌
  const Button: React.FC<ButtonProps> = ({ label }) => {}
  ```
