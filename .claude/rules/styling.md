# Styling Rules

- Tailwind utility classes only. No standalone `.css` files for component styles.
- Use the `cn()` helper (from `src/lib/utils.ts`) to merge classes conditionally:
  ```ts
  import { cn } from '@/lib/utils'
  <div className={cn('base-class', isActive && 'active-class', className)} />
  ```
- No inline `style={{}}` props. If Tailwind can't do it, add a CSS variable via the Tailwind config.
- shadcn/ui components are the default for all UI primitives (Button, Input, Dialog, etc.).
  - Extend them via `className` prop — do not modify files in `src/components/ui/` directly.
  - If you need a variant that doesn't exist, use `cva()` in the component file.
- Responsive design is mobile-first: base styles for mobile, `sm:` / `md:` / `lg:` for larger.
- Dark mode: always add `dark:` variants. Never hardcode light-only colors like `text-gray-900` without a `dark:` counterpart.
- Spacing and sizing use Tailwind's scale — no arbitrary values like `w-[371px]` unless genuinely necessary.
- Avoid `!important` (`!` prefix in Tailwind). If you need it, something else is wrong.
