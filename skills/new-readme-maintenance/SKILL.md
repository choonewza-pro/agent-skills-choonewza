---
name: new-readme-maintenance
description: Guide and rules for writing, maintaining, and updating the README.md files for every feature in the codebase. Triggered when creating, modifying, or developing any feature.
---

# Feature Documentation & README Maintenance Skill

This skill enforces the rule that **every feature module** in the codebase MUST have a clear, comprehensive, and up-to-date `README.md` file located at its root directory (e.g., `src/features/<feature-name>/README.md`).

## Core Rules

1. **Mandatory Creation**: When developing a new feature, a `README.md` file MUST be created at the root of that feature folder.
2. **Proactive Updates**: Every time a feature is modified, refactored, or expanded (e.g., database schema changes, new services, new UI components, new actions, or new tests), the feature's `README.md` MUST be updated immediately in the same turn/commit.
3. **Language Conventions**:
   - Technical explanations, architecture diagrams, and commands should be in English.
   - Summaries, user-facing guides, and general explanations should be in Thai to align with the project's localization standard.

## README Structure Template

Every feature `README.md` should follow this structure, adjusting sections as necessary:

```markdown
# [Feature Name in Thai] ([Feature Name in English])

[Brief description of what the feature does, its business value, and UX impact in Thai]

---

## 🏗️ สถาปัตยกรรมระบบ (Architecture)

[Describe the folder structure and clean architecture layers of this feature in English/Thai]
[Provide a directory tree if helpful]

---

## 🔒 ฟีเจอร์หลักและการทำงาน (Core Features & Logic)

[Detail the business logic, database transaction handling, locking, or API integrations here]
- **Feature 1:** ...
- **Feature 2:** ...

---

## ⚙️ การตั้งค่าและการใช้งาน (Configuration & Usage)

[Detail any environment variables, database schema relations, or seed data needed]

---

## 🧪 การทดสอบระบบ (Testing)

[Detail how to run unit and integration tests for this specific feature]
```bash
npx vitest run src/features/<feature-name>
```

## 📅 สถานะการพัฒนาและแผนงาน (Development Status & Roadmap)

[List the development phases and check off completed tasks]
- [x] Phase 1: ...
- [ ] Phase 2: ...
```
