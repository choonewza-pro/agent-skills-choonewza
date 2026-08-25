---
name: swe-legacy-system-migration
description: Guides a structured migration of an old or legacy codebase to a new language, framework, or stack, using the sequence Reverse Engineer → Specify → Clean Scaffold → Incremental Migration → Automated Verification. Produces a docs/ folder of verifiable specs (architecture, API contract, data model, business rules, authorization, dependencies, external services, edge cases, migration plan, verification strategy, deviations) that capture the old system's real behavior before new code is written, then migrates and verifies incrementally instead of rewriting from memory. Use whenever the user wants to rewrite, port, or migrate an existing/legacy project to a different language or framework, reverse-engineer an old codebase before changing it, or produce migration/architecture docs ahead of a rewrite — even if they just say "rewrite this old service in X", "migrate this API to a new stack", or "port this to Y" without naming the workflow.
---

# Legacy System Migration

## Why this workflow

The most dangerous bugs in a rewrite are not the ones that fail to compile — they're the ones that compile fine but silently change behavior: a dropped edge case, a middleware that ran implicitly in the old framework and has no equivalent in the new one, an error response shape that's "close enough" but not identical. Reading the old code and rewriting it directly invites this failure mode, because understanding and implementation happen in the same pass and nothing forces the gaps to surface.

This workflow separates "understand the old system" from "build the new one" with a written, checkable specification in between, and then migrates and verifies in small increments rather than one big rewrite-and-hope cutover. Treat it as documentation-first migration, not code-first migration.

## Workflow overview

```
OLD PROJECT
    │
    ▼
Reverse Engineering ── Architecture, API, Database, Business Logic,
    │                  Authentication, Dependencies, External Services,
    │                  Edge Cases
    ▼
Specification (docs/)
    ▼
Clean Scaffold ── idiomatic structure in the new stack, no logic yet
    ▼
Incremental Migration ── one module/endpoint at a time, verified as it lands
    ▼
Automated Verification ── contract diffing + regression suite
```

Loop back: if Phase 4 or 5 surfaces behavior that Phase 1 missed, update the spec doc first, then fix the code. Don't just patch the new system quietly — an undocumented fix is a fact about the old system that the next person (or the next migration) won't have.

## Phase 1 — Reverse Engineering

Goal: establish ground truth about what the old system actually does, not what its README or comments claim it does. Pull from the code, its tests, its error-handling paths, and production logs where available — not from documentation, which drifts.

Work through each area and note it in the corresponding spec doc as you go, rather than trying to hold it all in your head until the end:

- **Architecture** — draw the real component/data-flow diagram from the code's actual imports and calls.
- **API** — every route, plus every middleware that intercepts a request (auth guards, rate limiters, CORS, logging). These are easy to miss because they usually live outside the handler function itself.
- **Database** — schema plus any constraint the code implicitly relies on (a unique index enforcing business logic with no comment explaining why).
- **Business logic** — focus on conditional branches and calculations, since these are where behavior actually lives.
- **Authentication** — the full token/session lifecycle: issuance, verification, refresh, revocation.
- **Dependencies** — for each library, what it's actually used for in this codebase, not just its name. A heavy dependency used for one function is worth flagging — it may not need a direct equivalent in the new stack.
- **External services** — timeout values, retry policy, and what happens on failure (fallback, error surfaced, silent skip).
- **Edge cases** — the hardest to find. Mine the error-handling code and, if available, production logs and past incident reports; these surface the inputs that actually occurred, not just the ones the happy path assumes.

If the migration crosses frameworks or paradigms with different default behavior (e.g. how panics/exceptions become HTTP responses, how validation errors are shaped), call that out explicitly — these differences cause quiet mismatches that are easy to miss during Specification.

## Phase 2 — Specification

Write each doc so it's _verifiable_, not just descriptive — something you could later diff against real output, not only read. For an API, that means the exact request/response schema with a real example, not "returns a user object":

```markdown
### GET /users/:id

**Response 200**
{"id": "usr_123", "email": "a@b.com", "created_at": "2026-01-01T00:00:00Z"}
**Response 404**
{"error": "not_found", "message": "user not found"}
```

## Phase 3 — Clean Scaffold

Build the new project's structure the idiomatic way for its stack — routing, config, DB connection, project layout — wired to match the spec, but without full business logic yet. The goal here is a skeleton that already matches the target architecture, so Phase 4 is filling in logic, not also fighting project structure.

## Phase 4 — Incremental Migration

Migrate one module or endpoint at a time and verify it immediately after — don't migrate everything and test at the end, since that reintroduces the "big rewrite" risk this workflow is meant to avoid. Where feasible, run the old and new systems in parallel (e.g. on different ports) so each migrated piece can be checked against a live reference, not just against the spec doc.

## Phase 5 — Automated Verification

Two complementary layers:

- **Contract/diff testing** — fire the same request at the old and new systems and diff the responses (status, headers, body shape) at the schema or byte level. This catches drift the spec doc didn't anticipate.
- **Regression suite** — test cases built directly from the Edge Cases doc, so the behaviors that were hardest to find in Phase 1 are the ones most explicitly protected.

## Output: docs/ structure

```
docs/
├── SYSTEM_OVERVIEW.md        # why migrate, scope, non-goals
├── ARCHITECTURE.md           # diagrams, components, data flow, deployment
├── API_CONTRACT.md           # every endpoint: method, path, headers, request/response schema + real examples, status codes
├── DATA_MODEL.md             # schema, relationships, indexes/constraints with logic implications
├── BUSINESS_RULES.md         # conditions, calculations, state machines
├── AUTHORIZATION.md          # auth flow, role/permission matrix, token lifecycle
├── DEPENDENCIES.md           # what each library is actually used for, and its replacement in the new stack
├── EXTERNAL_SERVICES.md      # each external service: timeout/retry policy, failure behavior
├── EDGE_CASES.md             # each edge case and the exact behavior that must be reproduced
├── MIGRATION_PLAN.md         # order of migration by dependency, cutover strategy
├── VERIFICATION_STRATEGY.md  # how each endpoint/module is verified, pass criteria
└── DEVIATIONS.md             # places the new system intentionally differs from the old, and why
```

`DEVIATIONS.md` matters more than it looks: mid-migration you will hit old behavior that looks like a bug. If the decision to reproduce it or fix it isn't written down, it gets re-litigated later by someone who assumes the difference is an accident.

## Working notes

- Scale the doc set to the project. A small internal script doesn't need twelve files — some can merge (e.g. fold AUTHORIZATION into BUSINESS_RULES) or drop entirely (no EXTERNAL_SERVICES if there are none). A production system handling money or user data should keep all of them.
- If the codebase is large, consider drafting one doc (e.g. API_CONTRACT.md for a single module) as a sample and confirming the level of detail with the user before generating the full set — the right granularity varies a lot by project, and it's cheap to check early.
- Keep the "why" visible in each doc, not just the "what" — a constraint with no rationale is much easier to accidentally drop during migration than one with a sentence of context attached.
