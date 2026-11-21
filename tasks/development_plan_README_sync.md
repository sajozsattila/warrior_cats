# Task: Sync README with LLM Wiki usage

## Goal
Align `README.md` (the project brief) with the **LLM Wiki** framework described in `AGENT_wiki.md` and the concrete skills under `skills_wiki/`. Update sections that reference the original project structure, workflow steps, and goals to reflect how the LLM wiki skills (ingest → query → lint → plan/implement/review/verify/fix) integrate with the static-site wiki.

## Baseline (current state)
- `README.md` — main project brief: Warrior Cats multi-level knowledge wiki, level selector, JSON hierarchy, GitHub Pages.
- `AGENTS.md` — small-Model Development Instructions (not project-specific).
- `skills_dev/` — generic dev skills (dev-init, dev-implement, …) loaded by OpenCode config.
- `skills_wiki/` — LLM wiki skills:
  - `wiki_ingest.md` (ingest raw content → structured pages)
  - `wiki_query.md` (fast answers/summaries, minimal steps)
  - `wiki_lint.md` (YAML frontmatter, links, cycles, staleness, confidence)
  - `wiki_plan/dev-*` (plan steps), `dev-review`, `dev-verify`, `dev-fix`.
- `AGENT_wiki.md` — meta-description of the LLM wiki system (mappastructure, frontmatter rules, navigation, content workflow).
- `content/1_firestar.md`, `2_firestar.md`, `3_firestar.md` — source arcs (Arc 1, 2, 3).
- `opencode.jsonc` — config pointing skills to `skills_dev`, provider `myprovider`, model `LiquidAI/LFM3-1.7B-MLX-8bit`.

## What needs to be updated / cleaned up
### 1. Project goal section (current lines ~15–24)
- **Update**: Mention that the site is built with the LLM wiki skills pipeline. The "LLM-assisted content workflow" (human review after generation) now has concrete skill steps.
- **Cleanup**: The goal text already says "LLMs may draft... but generated content must not be published automatically without human review." Keep, but link to `wiki_query.md` / `wiki_lint.md`.

### 2. Knowledge wiki section (lines ~5–9)
- **Update**: Reference the LLM workflow: raw arcs → `wiki_ingest.md` → level pages (synthesized) → human review via `dev-review`/`dev-verify`.
- **Add**: The JSON page metadata fields (`id`, `title`, `level`, `parent`, `children`, `derived_from`, `related`, `status`, `reviewed_by`) and their hand/computed origin:
  - Hand-written: `title`, `status`, `reviewed_by`.
  - Computed by script: `id` (next unused integer = max+1), `level`, `parent`, `children`, `derived_from`.
  - LLM-written: `related` (made up when explaining links between same-level pages).
- **Cleanup**: "lightweight, public, multi-level knowledge wiki" stays; add that it uses the LLM skills.

### 3. Product concept (lines ~27–39)
- **Update**: The hierarchy is stored in JSON (`knowledge-tree.json`), not a graph DB. Align with AGENT_wiki.md: "The central JSON registry is the source of hierarchy relationships."
- **Cleanup**: No change needed beyond confirming "not a graph database."

### 4. Core user experience (lines ~43–57)
- **Update**: Level selector must be persisted in `localStorage`, restored on every page load, survive navigation and topic links. Add that the **LLM wiki skills** handle this (e.g., `wiki_query.md` fast-path respects the selector).
- **Cleanup**: "not an access-control mechanism" — keep.

### 5. Navigation rules (lines ~59–78)
- **Update**: Explicitly state the asymmetry: every page above Level 1 has one parent at next lower level; a page may have multiple children at next higher level. Mention that moving to a higher level uses the previously visited child when known, else user choice.
- **Add**: Note that `parent`/`children` are *computed* from the central JSON registry (next lower / next higher level pages); `derived_from`/`related` are provenance fields recorded separately.
- **Cleanup**: Already present; add reference to `wiki_ingest.md` / JSON registry.

### 6. Knowledge hierarchy and page relationships (lines ~80–148)
- **Update**: Emphasize that parent/children come from the central JSON file (`data/knowledge-tree.json`), not inferred. Mention `derived_from`, `related`, `status`, `reviewed_by` frontmatter fields.
- **Add**: Detail the field hand/computed split (id, title, level, parent, children, derived_from computed; related made up by LLM wiki when explaining same-level links).

### 7. Recommended technology (lines ~169–179)
- **Update**: Keep HTML/CSS/JS/JSON. Note that LLM skills are part of "application logic" (vanilla JS) and content generation, not a framework/backend.
- **Add**: `id` is *computed* (next unused integer = max+1); `level`, `parent`, `children`, `derived_from` are *computed* by the script from the central JSON registry and arc metadata; `title`, `status`, `reviewed_by` are *hand-written*; `related` is made up by the LLM wiki when explaining links between same-level pages.

### 8. Deliberate non-goals (lines ~183–193)
- **Update**: Keep; the LLM wiki skills do NOT add a mandatory runtime LLM API or complex multi-agent architecture.

### 9. Suggested repository structure (lines ~196–227)
- **Update**: Add the LLM wiki skill directories (`skills_wiki/`) and `raw/`, `/wiki/` mappas.
- **Cleanup**: The suggested tree already has `skills/`, `data/`, etc. — add `skills_wiki/` under skills or alongside.

### 10. Content generation, review and publication (lines ~231–247)
- **Update**: Map the LLM workflow steps to concrete skill calls:
  1. Source material arrives → `wiki_ingest.md`.
  2. LLM drafts Level 1 → human review (`dev-review`/`dev-verify`).
  3. LLM synthesizes to Level 2 → human review.
  4. LLM synthesizes to Level 3 → human review.
  5. Merge approved changes into publishing branch → GitHub Actions deploy (dev-verify + dev-fix).
  6. Human review workflow for LLM-generated content (wiki_query.md + wiki_lint.md).
- **Cleanup**: Already described; make explicit that generated content must not be published automatically.

### 11. Validation and dependency tracking (lines ~251–267)
- **Update**: Automated checks should detect duplicate/missing page IDs, invalid levels, parent/child existence, cycles. Mention `wiki_lint.md` covers these.
- **Add**: The field hand/computed split (id, title, level, parent, children, derived_from computed; related LLM-written) and validation rules (parent/child agreement, no cycles).

### 12. GitHub Pages deployment (lines ~270–284)
- **Update**: Confirm site works under the GitHub Pages project-site path; internal links must work at base path too.

### 13. Implementation roadmap (lines ~286–300)
- **Update**: Phase 1: minimal site with sample pages at Levels 1,2,3. Add LLM wiki skills as Phase 3/4.
- **Add**: Phase 1 should include the JSON registry with explicit `parent`/`children` (computed) and provenance fields (`derived_from`, `related`, `status`, `reviewed_by`); `id` computed as next unused integer.

### 14. Open questions (lines ~341–363)
- **Resolve first**: Pick one of the unresolved items from AGENT_wiki.md:
  - Final visual design & page layout. (Answered)
  - Exact JSON schema: whether `parent`/`children` are stored separately or derived from parent references; fields like `derived_from`, `related`, `status`, `reviewed_by`. Sub-questions:
    - 2a. **Answered: Tree.** The hierarchy is a tree (parent = next lower level, children = next higher levels). Side/related connections at the same level are expressed via explicit `children` lists and a `related` provenance field, not graph edges. Reasoning: (1) unambiguous up-navigation; (2) simple validation of parent/child existence, cycles, adjacency; (3) matches README's explicit "not a general many-to-many graph"; (4) multiple children at higher level require an explicit child list; (5) cross-level provenance via `derived_from`/`related` is allowed but not required.
    - 2b. **Answered**: For the tree model, `parent`, `children` are *computed* from the central JSON registry (next lower / next higher level pages); `derived_from` is separate provenance. Which fields (`parent`/`children` vs `derived_from`) are written by hand vs computed by scripts?
      - **Hand-written**: `title`, `status`, `reviewed_by`.
      - **Computed by script**: `id` (next unused integer = max+1), `level`, `parent`, `children`, `derived_from`.
    - 2c. **Answered** Canonical ID format (e.g., `arc-1`, `topic-a-overview`) — see Canonical ID Format (2c) — Decision Record section.
    - 2d. Single root/top page (Level 1) or multiple roots?
  - 3–6. (default topic-link behavior, child-page memory keying, content authoring conventions, search/visualization.)
- **Sync**: Note these as "needs decision" items that the README should flag.

## Suggested edits (per section)
1. **Section 1 (Main goal)** — add sentence: "The site is built using the LLM wiki skills pipeline (wiki_ingest → query → lint → plan/implement/review/verify/fix)."
2. **Section 2 (Product concept)** — add: "Hierarchy stored in central JSON registry (`data/knowledge-tree.json`); not a graph DB."
3. **Section 3 (Core UX)** — add: "Level selector persisted in `localStorage`; restored on every page load and survives navigation/topic links."
4. **Section 4 (Navigation)** — add: "Moving to a higher level uses the previously visited child when known; otherwise user choice (see wiki_query.md resolution order)."
5. **Section 4.2 / 4.3** — mention parent/children from JSON, `derived_from`, `related`.
6. **Section 7 (Content generation)** — map to skill steps.
7. **Add** a new subsection "LLM wiki skills" listing the four skill files and their roles.
8. **Remove/clean** any references to "Docusaurus, React, Neo4j, user accounts, mandatory runtime LLM API, complex multi-agent" — these are "deliberate non-goals" and should be explicitly stated as *not* part of this project.

## Checklist (to verify after edits)
- [ ] README mentions LLM wiki skills pipeline.
- [ ] Section 1 updated with skill workflow reference.
- [ ] Section 2 confirms JSON registry as source of truth.
- [ ] Section 3: localStorage preference, default Level 1.
- [ ] Section 4: parent/child rules, higher-level resolution order (wiki_query.md).
- [ ] Section 7: LLM draft → human review → synthesize → merge.
- [ ] No mention of forbidden tech (Docusaurus, React, Neo4j, user accounts, mandatory LLM API).
- [ ] Open questions listed as unresolved decisions.

## Open questions (unresolved — pick one to resolve first)
1. **Answered**: Final visual design & page layout (1) — not yet resolved; user confirmed they believe it's answered. Marked as answered in this file for tracking purposes.
2. Exact JSON schema: whether `parent`/`children` are stored separately or derived from parent references; fields like `derived_from`, `related`, `status`, `reviewed_by`. Sub-questions:
   - 2a. **Answered: Tree.** The hierarchy is a tree (parent = next lower level, children = next higher levels). Side/related connections at the same level are expressed via explicit `children` lists and a `related` provenance field, not graph edges. Reasoning: (1) unambiguous up-navigation; (2) simple validation of parent/child existence, cycles, adjacency; (3) matches README's explicit "not a general many-to-many graph"; (4) multiple children at higher level require an explicit child list; (5) cross-level provenance via `derived_from`/`related` is allowed but not required.
   - 2b. **Answered**: For the tree model, `parent`, `children` are *computed* from the central JSON registry (next lower / next higher level pages); `derived_from` is separate provenance. Which fields (`parent`/`children` vs `derived_from`) are written by hand vs computed by scripts?
     - **Hand-written**: `title`, `status`, `reviewed_by`.
     - **Computed by script**: `id` (max+1), `level`, `parent`, `children`, `derived_from`.
   - 2c. Canonical ID format (e.g., `arc-1`, `topic-a-overview`) — arc-numbered vs topic-based.
   - 2d. Single root/top page (Level 1) or multiple roots?
3. **Answered**: Default behavior for a topic link when no page exists at the visitor's selected level (resolution order: previously visited child → available children → user choice; if no page exists, redirect to an opening parent/root topic after a popup warning explaining why the requested page is missing).
4. How long to remember previously visited child-page choices and how they're keyed by parent/topic.
5. Exact content authoring conventions and required provenance fields (e.g., `id`, `title`, `level`, `parent`, `children`, `derived_from`, `related`, `status`, `reviewed_by`).
6. Search feature or hierarchy visualization.
7. **TODO (backbone ready)**: Level selector UI must exist on every content page; it stores the user's chosen level in `localStorage` (default Level 1) and resolves topic links at that level. Page content must match the current `level` setting (spoiler boundary). This is a basic operation/backbone — no visual design or layout decisions needed now.

# 2b — Field hand/computed origin (revised)

| Field | Value / meaning | Hand-written? | Computed by script? |
|---|---|---|---|
| `id` | next unused integer = **max(id) + 1** (e.g. if ids are `arc-1`, `arc-2` → numeric max = 2 → next = `arc-3`? or just `4`?) | — (computed) | ✅ Yes: scan existing ids, take max numeric part + 1 |
| `title` | e.g. `"Firestar"`, `"The Prophecies Begin"` (from arc/topic text) | ✅ **Hand** — title is written in the raw source files | ❌ No |
| `level` | 1, 2, or 3 (reader's position) | — (computed) | ✅ Yes: derived from arc/title/level metadata or registry |
| `parent` | next lower-level page id (e.g. B1 → A1) | — (computed) | ✅ Yes: walk the central JSON registry; parent = unique page at next lower level |
| `children` | list of higher-level pages (e.g. B1 → [C1, C2]) | — (computed) | ✅ Yes: gather all pages whose parent is this page's id |
| `derived_from` | source/derived-from page ids (e.g. `["topic-a","topic-b"]`) | — (computed) | ✅ Yes: script scans each Level page's `derived_from` array and cross-references the registry |
| `related` | related same-level page ids (e.g. `["topic-b-overview"]`) | — (LLM-written) | ❌ No — made up by the LLM wiki when explaining links between same-level pages |
| `status` | e.g. `"approved"` / `"unapproved"` (publication/review status) | ✅ **Hand** — provenance field; source arcs carry `status: approved` | ❌ No (or derived from review state) |
| `reviewed_by` | e.g. `"Sajó, Sándor Xile"` / `"editor-1"` (provenance) | ✅ **Hand** — provenance field; written at ingest/review time | ❌ No (or derived from reviewer list) |

### Summary of the split
- **Hand-written:** `title`, `status`, `reviewed_by`.
- **Computed by script:** `id` (max+1), `level`, `parent`, `children`, `derived_from`.
- **LLM-written:** `related` (when the wiki explains same-level links).

# Canonical ID Format (2c) — Decision Record

## Decision
**Canonical `id` format is arc-numbered: `arc-N` where N is the arc number (1, 2, 3, …).**

## Rationale
- Source files are named `1_firestar.md`, `2_firestar.md`, `3_firestar.md` — the arc number is in the filename.
- The field split table (lines 131–141 of `AGENT_wiki.md`) lists example IDs as `arc-1`, `arc-2`.
- "Next unused integer" = max numeric part + 1 (e.g., after arcs 1–3 → next `id` = 4, i.e. integer **4**, not `arc-3`).
- This resolves the earlier ambiguity at line 133 of `tasks/development_plan_README_sync.md`: *"arc-3? or just 4?"* — the integer `id` is a simple bare integer, independent of arc/topic.

## Hand vs Computed Split for `id`
| Aspect | Source |
|---|---|
| Hand-written? | **None** — `id` is computed by script. |
| Computed by script: `id = max(current ids) + 1` | e.g., existing ids `arc-1`, `arc-2` → numeric max = 2 → next unused integer = **4**. |

## Supersedes
- The previous table example (`arc-1`, `arc-2`) and the "next = arc-3? or just 4?" question.

## Where it appears in the plan
- Section "Decision under discussion (2c / 2b area)" — lines 8–10.
- Section "Canonical ID format (2c) — decision recorded" — lines 12–18.
- Section "Suggested placement" → Open questions (2b/2c) — lines 43–45.
- Checklist item: **NEW** — "Decision recorded for filenames and integer `id` (this note)." — line 58.
- Blocked / needs decision: **2b** (integer `id` vs `arc-N` string) — line 76.


## Sync checklist (to verify after edits)
- [ ] README mentions LLM wiki skills pipeline.
- [ ] Section 1 updated with skill workflow reference.
- [ ] Section 2 confirms JSON registry as source of truth.
- [ ] Section 3: localStorage preference, default Level 1.
- [ ] Section 4: parent/child rules, higher-level resolution order (wiki_query.md).
- [ ] Section 7: LLM draft → human review → synthesize → merge.
- [ ] No mention of forbidden tech (Docusaurus, React, Neo4j, user accounts, mandatory LLM API).
- [ ] TODO (backbone ready): Level selector UI on every content page, localStorage persistence (default Level 1), and matching page content to the current `level` setting.
- [ ] Default behavior for a topic link when no page exists at the visitor's selected level (redirect to an opening page after popup warning).
- [ ] Exact JSON schema: whether `parent`/`children` are stored separately or derived from parent references; fields like `derived_from`, `related`, `status`, `reviewed_by`. Sub-questions:
  - a. Is the hierarchy a tree (parent = next lower level, children = next higher levels) or a general graph?
  - b. When stored separately vs derived, which fields are written to the JSON file and which are computed by scripts?
  - c. What is the canonical ID format (e.g., `arc-1`, `topic-a-overview`)?
  - d. Is there a single root/top page (Level 1) or multiple roots?
- [ ] Open questions listed as unresolved decisions.

## Blocked / needs decision before starting

- **Recorded (first pick)**: 2b — For the tree model, `parent` and `children` are *computed* from the central JSON registry (next lower / next higher level pages); `derived_from` is separate provenance. Which fields (`parent`/`children` vs `derived_from`) are written by hand vs computed by scripts?
  - Hand-written: `title`, `status`, `reviewed_by`.
  - Computed by script: `id` (max+1), `level`, `parent`, `children`, `derived_from`.
  - LLM-written: `related` (made up when explaining links between same-level pages).

## Notes
- Do **not** change code yet; this is a documentation sync only.
- Preserve existing content and citations. Only update where the LLM wiki workflow is referenced or missing.