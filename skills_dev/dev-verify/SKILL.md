---
name: dev-verify
description: Run available checks and verify acceptance criteria for /dev-verify, reporting failures and untested behavior.
---

# Verify Behavior

1. Read the request or task plan, relevant instructions, changed files and
   existing test documentation. List each acceptance criterion.
2. Identify actual available tests, validation and commands before running
   them. Run relevant checks, read their output and investigate failures.
   Record exact commands, exit/results and any skipped checks. Do not install
   tools or claim a missing command ran. Avoid unnecessary file writes;
   disclose any test artifacts or temporary processes, and clean up only those
   you created. Ask before destructive operations.
3. For each criterion report `pass`, `fail`, `not run` or `blocked`, with
   observed evidence. Parsing JSON does not validate the hierarchy; an HTTP 200
   does not prove navigation. Do not label unexercised behavior verified.
4. For relevant browser changes, check upward navigation, one and multiple
   children, valid remembered children, topic links at a different preferred
   level, missing and ambiguous mappings, valid and invalid preferences,
   reload, Back/Forward, keyboard/mobile use and assets/JSON under a GitHub
   Pages subpath. Record each applicable scenario not actually exercised.
5. For content, identify required human review of provenance, approval and
   spoiler boundaries. Report overall verification only if all required criteria
   were exercised; otherwise state what remains unverified. Do not edit code,
   commit, push or deploy as part of verification.
