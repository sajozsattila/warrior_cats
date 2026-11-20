---
name: dev-implement
description: Implement one planned development step for /dev-implement, run its checks, and report actual results.
---

# Implement One Step

1. Read the named task file and relevant instructions. If no task file is
   provided, ask for it. Read the whole plan for dependencies and acceptance.
   Choose the requested step, or the first unchecked step. If none remain, stop.
2. Check `git status --short` and inspect relevant staged, unstaged and
   untracked changes. Preserve them. Read the code, tests and docs needed to
   understand the step, including files not listed in the plan.
3. Briefly explain the intended change. Implement only that logical step,
   including its necessary tests and docs. If the planned file list is incomplete,
   update it when safe; explain why. Ask if a conflict or design decision prevents
   a safe complete change. Do not add an unapproved dependency or architecture.
4. Run the applicable step checks and read their output. Investigate failures;
   fix problems within scope and rerun. If a check is missing, blocked or needs
   human review, report it rather than claiming success.
5. Mark the step complete only when its acceptance and required checks are
   satisfied. Record command results and remaining manual checks in the task
   file; preserve other notes. If blocked, leave the step unchecked.
6. Report the step, changed files, checks and actual results, and remaining
   work. Stop after one step. Never commit or push without explicit permission.

For content work, preserve the last approved version and provenance; generated
drafts must not enter the published artifact before human review. Respect the
level's spoiler boundary. Do not infer missing relationships or approval.
