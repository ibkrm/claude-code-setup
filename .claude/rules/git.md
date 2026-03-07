# Git Rules

## Commit format

```
type(scope): short description

Optional longer body explaining why, not what.
```

## Types

| Type | When to use |
|------|-------------|
| `feat` | New feature or behavior |
| `fix` | Bug fix |
| `refactor` | Code change with no behavior change |
| `test` | Adding or fixing tests |
| `chore` | Tooling, deps, config |
| `docs` | Documentation only |
| `style` | Formatting, no logic change |
| `perf` | Performance improvement |

## Rules

- Scope is the feature or component: `feat(auth): add login form`
- Subject line max 72 characters. Imperative mood: "add" not "added", "fix" not "fixed".
- No "WIP", "misc", or "updates" commits on main.
- Squash fixup commits before merging a PR.
- Each commit should be a logical unit — could be reverted independently if needed.

## Branch naming

```
feat/short-description
fix/short-description
chore/short-description
```
