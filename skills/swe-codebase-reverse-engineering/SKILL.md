---
name: swe-codebase-reverse-engineering
description: Reverse-engineers an old, legacy, or undocumented codebase into accurate, verifiable specification documents covering architecture, API, database, business logic, authentication, dependencies, external services, and edge cases — pulled from the real code, tests, and error-handling paths rather than from a stale README or memory. Produces a docs/ folder capturing what the system actually does today. Use this whenever the user wants to understand, document, or audit an existing codebase before making major changes to it — ahead of a migration or rewrite, before onboarding someone onto it, before a large refactor, or when a codebase has little to no documentation — even if they just say things like "help me understand this old project", "document how this system actually works", "I need to onboard someone onto this legacy code", or "audit this codebase before we touch it".
---

# Codebase Reverse Engineering

## Why source-first, not docs-first

READMEs and comments drift from what the code actually does the moment someone fixes a bug without updating them. Treat the code, its tests, its error-handling paths, and — where available — production logs and past incident reports as the ground truth. Documentation is a useful pointer to _where_ to look, never evidence of _what happens_.

This matters most for the parts that are easy to skip past: behavior that lives in middleware rather than handlers, constraints enforced by a database index with no comment explaining why, edge cases that only show up in an error-handling branch nobody documented. These are exactly the things that get silently lost when someone works from memory or from docs instead of from source.

## What to extract

Work through each area and write it into the matching doc as you go — don't try to hold it all in your head until the end.

- **Architecture** — draw the real component/data-flow diagram from the code's actual imports and calls, not from any existing diagram.
- **API** — every route, plus every middleware that intercepts a request (auth guards, rate limiters, CORS, logging). These are easy to miss because they usually live outside the handler function itself.
- **Database** — schema plus any constraint the code implicitly relies on (a unique index enforcing business logic with no comment explaining why).
- **Business logic** — focus on conditional branches and calculations; that's where behavior actually lives, more than in the data structures around it.
- **Authentication** — the full token/session lifecycle: issuance, verification, refresh, revocation.
- **Dependencies** — for each library, what it's actually used for in this codebase, not just its name and version.
- **External services** — timeout values, retry policy, and what happens on failure (fallback, error surfaced, silent skip).
- **Edge cases** — the hardest to find. Mine the error-handling code and, if available, logs and past incidents; these surface inputs that actually occurred, not just ones the happy path assumes.

If the codebase mixes idioms or shows signs of workarounds (odd conditionals, comments like "don't remove this", retry loops around a specific input), flag them explicitly rather than smoothing them over — they're usually load-bearing.

## Output: docs/ structure

```
docs/
├── SYSTEM_OVERVIEW.md      # what the system does, scope, who/what depends on it
├── ARCHITECTURE.md         # diagrams, components, data flow, deployment
├── API_CONTRACT.md         # every endpoint: method, path, headers, request/response schema + real examples, status codes
├── DATA_MODEL.md           # schema, relationships, indexes/constraints with logic implications
├── BUSINESS_RULES.md       # conditions, calculations, state machines
├── AUTHORIZATION.md        # auth flow, role/permission matrix, token lifecycle
├── DEPENDENCIES.md         # what each library is actually used for
├── EXTERNAL_SERVICES.md    # each external service: timeout/retry policy, failure behavior
└── EDGE_CASES.md           # each edge case and the exact behavior it triggers
```

Write each doc so it's _verifiable_ later, not just descriptive — a real example beats a description. For an API, that means the exact request/response, not "returns a user object":

```markdown
### GET /users/:id

**Response 200**
{"id": "usr_123", "email": "a@b.com", "created_at": "2026-01-01T00:00:00Z"}
**Response 404**
{"error": "not_found", "message": "user not found"}
```

## Working notes

- Scale the doc set to the codebase. A small internal script doesn't need all nine files — merge what's thin (e.g. fold AUTHORIZATION into BUSINESS_RULES) or drop what doesn't apply (no EXTERNAL_SERVICES if there are none). A production system handling money or user data should keep all of them.
- If the codebase is large, draft one doc first (e.g. API_CONTRACT.md for a single module) and confirm the level of detail wanted before generating the rest — the right granularity varies a lot by project and it's cheap to check early.
- Keep the "why" visible, not just the "what" — a constraint with a sentence of rationale attached survives future changes much better than a bare fact.
- If the goal is a migration or rewrite, these docs are exactly the input the next stage needs: turn each into a spec the new system must match, then scaffold, migrate incrementally, and verify against them.
