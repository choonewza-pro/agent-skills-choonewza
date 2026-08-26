# Agent Skills Repository

This is a **skill collection** for OpenCode AI agents — not an app. Each `skills/<name>/SKILL.md` is a self-contained skill with frontmatter (`name`, `description`) that agents load via the `skill` tool.

## Repo structure

```
.agent/skills/git-commit-guide/  # local skill for this repo's own workflow
skills/                          # 19 publishable skills
docs/                            # reference docs (baseline-mcp-server, agent skills guide)
opencode.json                    # custom provider (Matcha AI), MCP config, permission rules
```

## Key facts

- **19 skills** in `skills/` (see `README.md` for the full catalog)
- `opencode.json` uses a custom Matcha AI provider at `https://aigateway.ntictsolution.com/v1` with models: `gpt-5-mini`, `gpt-5-nano`, `gpt-4o-mini`, `gpt-4.1*`, `ict-ollama/qwen3.5:27b`, `rnd-vllm/openai/gpt-oss-120b`
- **Permission rules**: `git add *`, `git commit *`, `rm *` require user approval ("ask")
- **MCP enabled**: `next-devtools-mcp` — but this is a skill repo, not a Next.js app, so MCP tools will likely fail here
- `.gitignore` only ignores `.serena/` — do not commit `node_modules/` or other artifacts
- All skill files use UTF-8 encoding (Thai content is common)
- The `.agent/skills/git-commit-guide` skill applies to commits in **this repo** — consult it for commit style

## Creating/editing a skill

- Every skill needs frontmatter: `name` and `description`
- Place under `skills/<name>/SKILL.md`
- Register new skills in `README.md` table (the README is the canonical catalog)

<!-- headroom:rtk-instructions -->
# RTK (Rust Token Killer) - Token-Optimized Commands

When running shell commands, **always prefix with `rtk`**. This reduces context
usage by 60-90% with zero behavior change. If rtk has no filter for a command,
it passes through unchanged — so it is always safe to use.

## Key Commands
```bash
# Git (59-80% savings)
rtk git status          rtk git diff            rtk git log

# Files & Search (60-75% savings)
rtk ls <path>           rtk read <file>         rtk grep <pattern>
rtk find <pattern>      rtk diff <file>

# Test (90-99% savings) — shows failures only
rtk pytest tests/       rtk cargo test          rtk test <cmd>

# Build & Lint (80-90% savings) — shows errors only
rtk tsc                 rtk lint                rtk cargo build
rtk prettier --check    rtk mypy                rtk ruff check

# Analysis (70-90% savings)
rtk err <cmd>           rtk log <file>          rtk json <file>
rtk summary <cmd>       rtk deps                rtk env

# GitHub (26-87% savings)
rtk gh pr view <n>      rtk gh run list         rtk gh issue list

# Infrastructure (85% savings)
rtk docker ps           rtk kubectl get         rtk docker logs <c>

# Package managers (70-90% savings)
rtk pip list            rtk pnpm install        rtk npm run <script>
```

## Rules
- In command chains, prefix each segment: `rtk git add . && rtk git commit -m "msg"`
- For debugging, use raw command without rtk prefix
- `rtk proxy <cmd>` runs command without filtering but tracks usage
<!-- /headroom:rtk-instructions -->
