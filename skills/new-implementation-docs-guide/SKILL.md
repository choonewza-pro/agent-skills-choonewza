---
name: new-implementation-docs-guide
description: >
  Use this skill when finishing a phase, feature, or task that came from a
  development plan/prompt document (_IMPLEMENTATION_PLANS/, docs/plans/, or any
  "TODOS/spec" markdown) and the completed work must be recorded back into that
  document. Triggers on: "update phase doc", "mark as done", "ปิด phase",
  "อัปเดตเอกสารการพัฒนา", "record status", or whenever a plan document exists
  for work you just completed. Defines the append-only STATUS section format
  (what was done with real file paths, verification commands, decisions and
  deferred items) while keeping original requirements untouched.
---

# Skill: Implementation Docs Update Guide

Use this skill when finishing a phase, feature, or task that came from a development plan/prompt document (e.g. `_IMPLEMENTATION_PLANS/*.md`, `docs/plans/*.md`, or any "TODOS/spec" markdown), and the completed work must be recorded back into that document. Triggers: "update phase doc", "mark as done", "อัปเดตเอกสารการพัฒนา", "ปิด phase", "record status", or whenever a relevant plan document exists for the completed work.

## Core Principle

The plan document is the single source of truth for **what was requested** AND **what actually happened**. Requirements stay untouched at the top; reality gets appended below. Never rewrite history — future readers must be able to diff spec vs. delivery.

## Workflow

1. **Find the source doc.** The document the task originated from (search the project's plans/prompts directory). If none exists and the task was substantial, create one named like `PHASE_<N>_<FEATURE>.md` or `<feature>-plan.md`.
2. **Do the work first.** Code, migration, verification — complete before documenting.
3. **Append the status section** to the SAME document using the template below.
4. **Never modify the original requirement lines.** Not even typos. They are the record of what was asked.

## Document Layout

```markdown
## <FEATURE> TODOS <- original requirements, UNTOUCHED

- <requirement 1>
- <requirement 2>

---

## ✅ STATUS: DONE (YYYY-MM-DD)

### สิ่งที่ทำ / What was done

1. **<Area>** — `<path/to/file>`: <what changed and why>
2. **<Area>**
   - `<path/to/file>`: <detail>

### การทดสอบ / Verification

- `<command>` ✓
- E2E/manual flow: <steps covered> ✓

### หมายเหตุ / Notes

- <decision made and its trade-off>
- <skipped item> — <when to add it>
```

## Status Header Values

| Value                           | When                                                                |
| ------------------------------- | ------------------------------------------------------------------- |
| `✅ STATUS: DONE (date)`        | All todos delivered                                                 |
| `🚧 STATUS: IN PROGRESS (date)` | Partial — add `### งานค้าง / Remaining` listing exactly what's left |
| `❌ STATUS: BLOCKED (date)`     | Add blocker reason + what unblocks it                               |

## Content Rules

- **Real paths only.** Every change cites the actual file/route/table it touched — no "updated relevant files".
- **Record side effects outside code**: schema pushes/migrations run, data backfills performed (with the rule used), env vars added, gitignore entries, new dependencies (note if already bundled/transitive).
- **Record decisions**, not just actions: e.g. "reused existing column X instead of adding Y", "public access per spec — add session check if private needed later".
- **Skipped ≠ forgotten.** Anything intentionally not built gets a line in Notes with its upgrade path.
- **Date = ISO `YYYY-MM-DD`** of completion.
- **Language follows the document's language** (Thai docs get Thai status sections).
- One status section per delivery round. Re-opening a DONE doc? Append a new dated section below rather than editing the old one.

## Anti-Patterns

- ❌ Deleting or rewording the original TODOS to match what you built
- ❌ Vague entries ("improved auth handling") without a path
- ❌ Claiming DONE without having run the verification commands listed
- ❌ Writing the status into a separate summary file instead of the source doc
- ❌ Marking DONE while test users/temp files/migration leftovers exist — clean up first
