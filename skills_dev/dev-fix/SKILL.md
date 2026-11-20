---
name: dev-fix
description: Investigate a reported bug or fix one open review finding for /dev-fix, with evidence and a focused check.
---

# Investigate Or Fix One Problem

1. If no problem or findings file is provided, ask what needs fixing. Read
   relevant instructions, files and `git status --short`; preserve existing work.
2. For a symptom, reproduce it when feasible and identify evidence for the root
   cause. Label an unproven cause as a hypothesis. If the fix is unclear, write
   an investigation plan in `tasks/fix-<slug>.md` using `/dev-plan`'s format;
   do not guess a code change. Preserve any existing plan.
3. For a findings file, select the first `open` actionable finding by severity.
   Skip findings already `fixed`, `blocked` or `needs-human`. Verify its evidence
   in context. If disputed or dependent on a human decision, record that status
   and explain; do not force a code fix.
4. Explain the intended fix, make one minimal complete change, and run the
   relevant test or reproduction. Investigate failures and report actual output.
   Do not weaken tests to make them pass. Include required tests and docs even
   if more than a few files are needed.
5. Mark a finding `fixed` only after the relevant check succeeds; record the
   evidence without erasing history. If blocked, record why. Report changed
   files, checks and outstanding risks; recommend a new review after fixes.
   Do not commit, push or publish unless explicitly requested.
