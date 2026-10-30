# Repository Readiness Audit

## BEFORE

| Dimension | Score 0-5 | Weighted | Evidence | Main Gap |
|---|---:|---:|---|---|
| Entry Point & Navigation | 2 | 6.0/15 | README contains 226 lines of course history. No REPO_MAP exists. Finding entry points requires manual traversal. | Lack of a concise repository map pointing directly to entry points and boundaries. |
| Agent Instructions | 2 | 6.0/15 | AGENTS.md has 24 lines. It lacks standardized command blocks, stop conditions, and tool adaptors. | Incomplete operational contract missing standardized command blocks and stop conditions. |
| Build / Run / Test | 2 | 6.0/15 | Pytest passes in the active environment, but no requirements.txt, lint command, or build script exists. | Absence of a reproducible verification script and explicit linting target. |
| Architecture Boundaries | 3 | 6.0/10 | ARCHITECTURE.md explains layers, but does not declare forbidden edit paths or enforce boundary rules. | Descriptive architecture without explicit protected paths or deterministic boundary checks. |
| Requirements & Traceability | 4 | 8.0/10 | Structured requirement IDs exist across REQUIREMENTS.md, SPEC.md, and docs/traceability.md. | No automated script checks whether requirement references in specs match declared requirement IDs. |
| Automation & Guardrails | 1 | 2.0/10 | No verification script, no git hook, and no CI pipeline exist. Rules depend on developer memory. | Absence of deterministic executable guardrails and automated CI validation. |
| Testability & Evidence | 3 | 9.0/15 | 42 pytest tests pass, but no unified runner records validation evidence or checks repository structure. | Manual test output without a single command producing evidence of repository compliance. |
| Safety & Permissions | 2 | 4.0/10 | Basic .gitignore exists, but no protected path declarations or stop conditions for destructive actions exist. | Lack of explicit protected path declarations and automated checks against sensitive file changes. |

TOTAL BEFORE: 47 / 100

Top 3 friction points:
1. Absence of a single reproducible verification script and automated CI workflow.
2. Incomplete operational instructions in AGENTS.md and absence of REPO_MAP.md.
3. Reliance on natural language text for rules instead of deterministic guardrail checks.
