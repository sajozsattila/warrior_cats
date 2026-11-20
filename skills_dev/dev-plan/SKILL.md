---
name: dev-plan
description: Break a development request into small verifiable steps for /dev-plan, without implementing production changes.
---

# Plan Small Steps

1. Identify the requested outcome. If absent, ask what should be built or fixed.
2. Read `AGENTS.md`, `README.md`, `AGENTS_small.md`, relevant docs and code.
   Check `git status --short`; preserve pre-existing changes. Inspect staged and
   untracked files when relevant. Ask only if an overlap prevents safe work.
3. Divide the work into ordered, coherent steps. Each step should have one goal,
   intended files, acceptance criteria and a real check or explicit manual
   check. Prefer a few files, but include required tests and docs together.
   Do not create unsafe intermediate publication states. Distinguish existing
   commands from checks that a later step will create.
4. Write `tasks/<slug>.md` using the format below. Create the directory if
   needed. Inspect an existing file before updating it; preserve unrelated work.
   Ask one question if a missing decision affects correctness.

```markdown
# Task: <title>
Goal: <requested outcome>
Baseline: <relevant pre-existing changes and constraints>
Acceptance: <observable outcomes>

## Steps
1. [ ] <one logical change>
   - Files: <expected paths; identify new files>
   - Check: <existing command or manual procedure and expected result>
   - Risk: <important constraint or none>

## Final Checks
- <command or manual procedure and expected result>
## Do Not Touch
- <out-of-scope files or behavior>
```

For content, identify sources and spoiler boundary, preserve the approved
version, and keep drafts outside the published artifact pending human review.
Report the plan path, first step, assumptions and unresolved decisions. Do not
implement production changes during planning.
