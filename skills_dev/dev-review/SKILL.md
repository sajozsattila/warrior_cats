---
name: dev-review
description: Review current or planned changes for /dev-review, using objective evidence and recording actionable findings.
---

# Review Changes

Review only; do not modify application code. A review report is the only
intended write.

1. Read the request or task file and relevant instructions. Run
   `git status --short`, `git diff --stat` and `git diff HEAD`; inspect relevant
   untracked files directly because Git diffs omit them. Preserve existing work.
2. Look for actionable bugs, regressions, scope changes and missing tests. Check
   static architecture, dependencies, accessibility, safe rendering and GitHub
   Pages subpaths where relevant. Check changed JSON against the documented
    schema and existing validators, if any. A child's parent is at level - 1;
    check references, agreement, adjacency and cycles without guessing fixes.
3. For navigation, check saved preferences, valid child memory, ambiguous and
   missing mappings, topic links and Back/Forward. For content, check the draft
   publication boundary, provenance and spoiler limits. Human approval and
   spoiler safety cannot be established by metadata or syntax checks alone.
4. Run relevant safe existing checks and read results. Do not use an incomplete
   ad hoc script as a full validator. A syntax check is not a behavioral test.
   Do not expose credentials in output or reports; use redacted locations.
5. Write `tasks/review-findings.md`, or an explicitly requested report path.
   Inspect any existing report first; preserve unrelated notes. For each finding
   record severity, `open` status, path and line, impact and evidence. Record
   checks run, results and unverified risks. If no findings exist, say so.
6. Report findings first in severity order, followed by checks and limitations.
   Do not claim a pass when required evidence is missing. Do not commit or push.
