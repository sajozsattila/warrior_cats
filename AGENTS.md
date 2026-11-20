# Small-Model Development Instructions

This is a concise supplement for Qwen 3.5 9B and similar models. `AGENTS.md`
and `README.md` remain primary; do not edit `AGENTS.md` for this workflow.
Read those files and the relevant `docs/ai/` specifications before changing code.
Documentation may describe planned work: check the actual files and commands.

## Work One Step At A Time

1. Understand the request and inspect the relevant code, tests, and docs.
2. Check `git status --short` and preserve existing changes, including staged
   and untracked files. Ask if a conflicting edit cannot be made safely.
3. Explain the intended change briefly before editing.
4. Make one small, complete logical change, following existing conventions.
   Include necessary tests and documentation; do not rewrite unrelated files.
5. Run relevant checks that actually exist. Read the results; investigate
   failures. Never invent APIs, dependencies, commands, results, or approvals.
6. Report changed files, commands and actual results, and what was not verified.

Split larger work into ordered, independently checkable steps with `/dev-plan`.
Use a few files per step as a target, not a reason to omit necessary tests or
docs. Ask one focused question for a decision that affects correctness; choose
the simplest safe option for minor reversible choices.

Ask before destructive operations. Never discard or overwrite other people's
changes. Never commit, push, publish, or create a PR unless explicitly asked.
Do not add dependencies without explaining the benefit and maintenance cost;
ask before changes to the architecture.

## Project Constraints

- Build a public static wiki with HTML, CSS, vanilla JavaScript and JSON;
  GitHub Pages hosts it and GitHub Actions validates and deploys it. Python
  standard-library scripts are the planned build/validation tools.
- Levels are spoiler boundaries, not security. Level 1, 2 and 3 correspond to
  The Prophecies Begin, The New Prophecy and Power of Three. Support Level N.
- The central JSON registry is the source of hierarchy relationships. A page
  above Level 1 has one parent at the next **lower** level; a parent may have
  multiple children at the next higher level. Never infer or invent
  relationships. If both parent and children are stored, validate their
  agreement, adjacency and cycles.
- The selector on every content page stores a valid preference in
  `localStorage`, defaulting to Level 1. Moving to a lower level follows
  parents; moving to a higher level uses a valid remembered child, the only
  child, or a user choice, in that order. Missing mappings must be explained
  without changing the preference.
- Topic links preserve the preferred level; resolve unambiguously or ask.
  Check reload, Back/Forward, accessibility, mobile layout and project subpaths.
- Keep content, data, scripts and styling separate. Preserve provenance and
  citations; structural parentage is not provenance. Never fabricate sources,
  reviewers, or approval. Keep generated drafts out of published output and
  preserve the last approved version. Mark affected dependents for review.
- Follow accepted schema and content conventions in `docs/ai/conventions.md`;
  distinguish planned conventions from actual repository state. If source IDs,
  approval, or relationships are missing or contradictory, report the gap.
- A syntax check or HTTP response is not full validation. Use existing tests
  and validators where relevant; identify missing checks and human review.

## Development Commands

- `/dev-init`: inspect the project and record actual conventions.
- `/dev-plan <request>`: create a small, ordered task plan.
- `/dev-implement <task file>`: implement and check one plan step.
- `/dev-review [task file]`: review changes and record findings.
- `/dev-verify [task file]`: run checks and report acceptance evidence.
- `/dev-fix <problem or findings file>`: investigate or fix one finding.

The commands load matching skills from `skills/`. Plans and review reports
are working artifacts under `tasks/`; do not overwrite existing artifacts without
checking them. Restart OpenCode after changing its configuration or skills.
