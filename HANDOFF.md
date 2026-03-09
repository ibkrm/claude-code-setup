# Claude Code Harness — Full Handoff Document

## Why this exists

The conversation started with a simple frustration:

> *"Claude Code has way too many concepts around agent skills, commands, rules, project memory, auto memory etc. I am spinning up a new UI repo/project and don't know how to set this up properly."*

The goal was not to build a harness for its own sake — it was to figure out **the right, minimal, non-overwhelming way to configure Claude Code for a small team** (2–5 people) on a real frontend project, so that autonomous sessions produce consistent, reviewable output without requiring babysitting.

---

## How we got here

### Step 1 — Clarify the stack and constraints
After asking clarifying questions we landed on:
- **Stack:** React 18, TanStack Router, TanStack Query, shadcn/ui, TypeScript strict, Tailwind CSS, Vite
- **Package manager:** npm (never pnpm/yarn/bun)
- **Team size:** Small (2–5), using Claude Code + GitHub Copilot side by side
- **Backend:** Separate FastAPI BFF repo — the UI calls the BFF only, never downstream APIs directly
- **Key constraint:** The project was greenfield, so we had the opportunity to set things up right from the start rather than retrofit

### Step 2 — Research Claude Code's actual primitives
We fetched the official Claude Code docs to understand what the primitives actually are (not what the marketing says). The key finding was that Claude Code's concepts map to three things:
- **CLAUDE.md** — the main instruction file Claude Code reads on every session
- **Rules** — markdown files with coding standards, imported via `@` syntax
- **Skills (formerly Commands)** — slash-command workflows, stored as folders with a SKILL.md

We also found that Anthropic recommends skills over commands going forward, and that native plan mode (Shift+Tab twice) is superior to any custom plan skill because it enforces read-only at the tool level.

### Step 3 — Research how OpenAI and others structure harnesses
We pulled in patterns from two sources:
- **Anthropic's own pattern:** `claude-progress.txt` (append-only log) + `features.json` (structured task tracker) for session continuity
- **OpenAI's harness engineering pattern:** mechanical enforcement, teachable error messages, CI as the authoritative gate

The synthesis: use Anthropic's continuity pattern for keeping Claude oriented across sessions, and OpenAI's enforcement pattern for making architectural rules stick.

### Step 4 — Evaluate adjacent tools
We evaluated four tools against the harness:

| Tool | Decision | When to adopt |
|---|---|---|
| **Beads** | Not yet | When multi-agent/multi-person coordination becomes painful. `features.json` maps cleanly to it. |
| **Symphony** | Not yet | Codex-specific today. Revisit when a Claude Code port stabilises. |
| **ast-grep** | Recommended (deferred) | When you catch the first ESLint-invisible violation (e.g. raw `fetch` in a component). Write one rule, add to CI. |
| **prek** | Recommended (deferred) | Swap in for `pre-commit` when you add ast-grep hooks. Drop-in, no Python runtime, faster. Install via `npm add -D @j178/prek`. |

### Step 5 — Build the files
We generated the full harness — all files listed below — then zipped them for download.

---

## What was built

All files drop directly into the repo root. The folder structure:

```
CLAUDE.md                          # Main Claude Code config (uses @ imports)
AGENTS.md                          # Same rules, fully inlined (for Copilot — no @ import support)
claude-progress.txt                # Append-only session handoff log
features.json                      # Structured task tracker (JSON not Markdown — agents less likely to overwrite)
openapi.json                       # BFF contract, committed to repo
docs/
  architecture.md                  # Layer map + enforcement rationale
  bff-contract.md                  # OpenAPI pipeline + Zod validation pattern
  quality.md                       # Living quality ledger, updated after each /audit
.claude/
  rules/
    components.md                  # Folder structure, co-location, named exports
    typescript.md                  # No any, no React.FC, no enums, explicit return types
    testing.md                     # Vitest + RTL, query priority, msw, it() not test()
    styling.md                     # Tailwind only, cn() helper, shadcn/ui as base
    git.md                         # Conventional commits, branch naming, squash before merge
  skills/
    init-project/SKILL.md          # Decomposes description → features.json. Stops for human review.
    start-session/SKILL.md         # Reads progress, picks next task, then triggers native plan mode
    end-session/SKILL.md           # typecheck + lint + test → commit → update progress
    audit/SKILL.md                 # Read-only violations scan across 7 categories. Updates quality.md
    new-component/SKILL.md         # Scaffolds component + co-located test. Only auto-invoking skill.
    pr-review/SKILL.md             # Pre-PR checklist: typecheck, lint, tests, diff, commit hygiene
.github/
  workflows/harness.yml            # 5-job CI: typecheck→lint→test→openapi-drift→build→harness-gate
```

---

## Core architectural rule (enforced by audit + CI)

```
openapi.json
    ↓
src/types/api.ts        ← GENERATED via npm run gen:types (openapi-typescript). Never hand-written.
    ↓
src/lib/api-client.ts   ← Only place raw fetch() is allowed
    ↓
TanStack Query hooks    ← Only place API calls are composed
    ↓
Components / Routes     ← Never import api-client directly
```

Nothing skips a layer. The `openapi-drift` CI job regenerates `src/types/api.ts` from `openapi.json` on every PR and fails with a teachable error message if drift is detected.

---

## Session flow (how to use this day-to-day)

```
# First time
/init-project "description of what to build"
→ Claude decomposes into features.json
→ STOP. Review features.json and approve before any code is written.

# Every session
/start-session
→ Claude reads claude-progress.txt + features.json
→ Picks highest-priority unblocked task, marks it in-progress
→ Outputs a summary, then says: "Press Shift+Tab twice to enter plan mode"
→ You review the plan, approve, Claude executes

# End of session
/end-session
→ Runs typecheck + lint + tests
→ Commits clean (never ends with uncommitted code)
→ Updates claude-progress.txt

# Periodic quality check
/audit
→ Read-only scan, never fixes anything
→ Produces violations report, updates docs/quality.md
```

---

## Pending items (pick these up next)

- [ ] Add `npm run gen:types` to package.json: `openapi-typescript ./openapi.json -o src/types/api.ts`
- [ ] Configure GitHub branch protection: set `harness-gate` as required status check
- [ ] Run `/audit` on the existing codebase for a baseline violations report
- [ ] Populate `features.json` via `/init-project "..."` with actual project features
- [ ] Fill in real `VITE_API_URL` and auth pattern in CLAUDE.md once BFF is defined
- [ ] Add `openapi.json` from the actual BFF (currently a placeholder)

---

## Key design decisions worth knowing

**Why JSON not Markdown for task tracking?**
Agents are much less likely to accidentally overwrite structured JSON than a checklist in a `.md` file. `features.json` is also trivially parseable for a future migration to Beads.

**Why no custom /plan skill?**
Claude Code's native plan mode (Shift+Tab twice) enforces read-only at the *tool* level — write tools are actually disabled, not just prompted. It also uses Opus for planning and Sonnet for execution. Any prompt-based plan skill is strictly inferior.

**Why AGENTS.md mirrors CLAUDE.md?**
GitHub Copilot doesn't support `@` imports, so all rules must be inlined. Both files are maintained in parallel.

**Why teachable CI error messages?**
The `openapi-drift` job and future ast-grep rules are written to fail with messages that tell Claude exactly what command to run to fix the issue. This allows autonomous sessions to self-correct without human intervention.

**Why features.json over Beads right now?**
Beads is the right long-term answer but adds friction at project start. The `depends_on` field in `features.json` maps directly to Beads dependency edges — keeping this populated is the only thing required for a clean migration later.

---

## Tools to adopt when the time is right

### ast-grep — structural linting
ESLint works on text and tokens. ast-grep works on AST structure, so it can enforce rules ESLint can't express — like "raw fetch() is only allowed inside api-client.ts" or "components may not import from api-client.ts directly."

Adopt when: you catch the first violation that ESLint misses. Write one YAML rule, add `ast-grep scan` to CI alongside `npm run lint`.

Example rules for this stack:
```yaml
# No raw fetch outside api-client
id: no-raw-fetch
language: TypeScript
rule:
  pattern: fetch($$$)
message: "Raw fetch only allowed in src/lib/api-client.ts. Use a TanStack Query hook instead."

# No React.FC
id: no-react-fc
language: TypeScript
rule:
  pattern: const $COMP: React.FC<$$$> = $$$
message: "React.FC is banned. Use typed props directly: function $COMP({ ... }: Props) {}"
```

### prek — faster pre-commit
Rust rewrite of `pre-commit`. Drop-in compatible (same `.pre-commit-config.yaml`), single binary, no Python runtime required, parallel hook execution.

Adopt when: you add ast-grep hooks. Both are Rust-native, install together cleanly.
```bash
npm add -D @j178/prek
```

### Beads — graph-based task tracker
Replaces `features.json` with a dependency-aware graph, atomic task claiming, and audit trail. Claude Code-native.

Adopt when: multi-agent or multi-person coordination becomes painful. Migration is ~30 lines of Node that reads `features.json` and shells out `bd create` + `bd dep add` per feature.

### Symphony (OpenAI)
Issue tracker → isolated workspace → agent run → proof of work → land PR. The right pattern, but Codex-specific today.

Adopt when: a Claude Code port stabilises. This harness is the prerequisite it requires.
