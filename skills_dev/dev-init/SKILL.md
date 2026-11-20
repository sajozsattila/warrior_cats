---
name: dev-init
description: Analyze repository conventions and actual project state for /dev-init or when starting unfamiliar development work.
---

# Initialize Development Context

Inspect and report; do not edit application code or configuration.

1. Read `AGENTS.md`, `README.md`, `AGENTS_small.md`, and relevant project docs,
   especially `docs/ai/development_plan.md`.
2. Run `git status --short`; inspect the actual file tree, relevant code and
   available check commands. Include staged and untracked work. Preserve it.
3. Compare documented plans with existing files. Record the static stack,
   registry, hierarchy direction, navigation, content approval, base path,
   conventions and checks. Cite paths for important mismatches. Do not infer
   human approval from a status field or invent missing commands.
4. Write a concise `tasks/init-report.md` with findings, unknowns, available
   checks and a suggested next task. If it already exists, inspect it first;
   do not overwrite unrelated notes. Create `tasks/` only if needed.
5. Report the artifact, key findings and what was not checked. No tests or
   validation are implied by inspection alone.
