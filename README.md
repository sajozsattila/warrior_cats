# Main goal

Create a Warrior Cats website where users select how far they have read in the series (their **level**) and look up information without spoilers. The selected level represents the reader's current position in the Warrior Cats serial: each level corresponds to a subseries (arc) in publication order. Based on that level, the site filters each topic so it only reveals what a reader at that point could already know. This is achieved through a hierarchical knowledge model stored in JSON, not a graph database.

# Knowledge Wiki

A lightweight, public, multi-level knowledge wiki in which each level corresponds to the reader's position in the Warrior Cats serial (a subseries/arc) and each higher-level page synthesizes knowledge from pages at the level below it.

The project is intentionally built as a static website: **HTML, CSS, vanilla JavaScript and JSON**, hosted on **GitHub Pages**. Visitors choose how far they have read in the series, and the site navigates them to the corresponding page in the knowledge hierarchy without spoiling later arcs.

This README is the project brief and implementation guide for developers and OpenCode.

## 1. Project goals

Build a website that:

- Publishes a structured body of knowledge at multiple levels, where each level represents how far the reader has progressed through the Warrior Cats serial and acts as a spoiler boundary.
- Lets each visitor choose their current position in the serial (their preferred level) from a dropdown available on every page.
- Preserves that preference while the visitor navigates between pages and reloads the site.
- Navigates to the corresponding page at the selected level, rather than merely hiding or showing navigation links.
- Maintains explicit relationships between source pages and the higher-level pages synthesized from them.
- Supports an LLM-assisted content workflow in which generated changes are reviewed by humans before publication.
- Can be hosted as a static site on GitHub Pages without a runtime backend.

The initial example should demonstrate three levels. The data model should not unnecessarily prevent adding further levels later.

## 2. Product concept

The wiki organizes information by **where the reader currently is in the Warrior Cats serial**. Each level maps to one subseries (arc) in publication order and sets a spoiler boundary: a visitor who selects a level has read every arc up to and including that subseries, and content at that level must never reveal events from later arcs.

| Level | Subseries (arc) | Reader has finished | Content may reveal knowledge up to |
|---|---|---|---|
| Level 1 | The Prophecies Begin | Arc 1 | The Prophecies Begin only |
| Level 2 | The New Prophecy | Arcs 1–2 | The Prophecies Begin and The New Prophecy |
| Level 3 | Power of Three | Arcs 1–3 | The Prophecies Begin, The New Prophecy and Power of Three |

For example, a reader who has finished only *The Prophecies Begin* selects Level 1 and sees no information from *The New Prophecy* or *Power of Three*. A reader who has also finished *The New Prophecy* selects Level 2; that level may include spoilers from the first two arcs but still none from *Power of Three* onward.

Because each level builds on the knowledge of the levels before it, each higher-level page is synthesized from one or more pages at the level immediately below it. The level therefore serves two linked purposes: it names the reader's place in the serial, and it defines how much of the story the page is allowed to draw on.

The visitor's selected level is a **reading and navigation preference**. It is not an access-control mechanism. The website is public, and all published pages can be accessed by anyone who knows their URLs.

## 3. Core user experience

Every content page has a visible knowledge-level selector. The selector shows the subseries (arc) titles so visitors can pick the level that matches how far they have read, not an abstract tier.

For example, a visitor reading a Level 1 (*The Prophecies Begin*) page can select Level 2 (*The New Prophecy*) once they have finished that arc. The site navigates to that page's Level 2 child. If the visitor then opens another topic, the selected level remains in effect and the site resolves the corresponding page for that topic, still respecting the new spoiler boundary.

The selected preference must:

- Be stored in browser `localStorage`.
- Be restored on every page load.
- Remain active when navigating through ordinary wiki links.
- Be updated when the visitor changes the selector.
- Have a sensible default (Level 1) when no preference has been saved.

The selector changes the destination page; it does not simply filter the sidebar or page contents.

### 3.1 Moving between levels

The hierarchy has an important asymmetry:

- Every page above Level 1 has exactly one parent at the next lower level.
- A page can have multiple children at the next higher level.

Therefore:

**Moving to a lower level** (for example, Level 2 to Level 1) is unambiguous: navigate to the current page's parent at the target level.

**Moving to a higher level** (for example, Level 1 to Level 2) may have multiple possible destinations. Use this resolution order:

1. Navigate to the previously visited child page for that parent and target level, if a valid one is known.
2. Otherwise, show the available child pages and let the visitor choose.
3. Do not silently select an arbitrary child.

The same principle applies when a visitor follows a link to another topic while a different preferred level is selected: resolve the linked topic to the corresponding page at the visitor's preferred level. If the relationship cannot be resolved uniquely, ask the visitor to choose from the valid alternatives rather than guessing.

The implementation should make these rules consistent across the level selector, internal links, browser navigation and page reloads.

## 4. Knowledge hierarchy and page relationships

The structure is a hierarchy (a tree across successive levels), not a general many-to-many graph.

A page above Level 1 has one parent at the next lower level. A page may have several children at the next higher level. Relationships must be stored explicitly in a central JSON data file; do not infer them from filenames or hardcode them independently in page scripts.

Illustrative relationship:

```text
Level 1: A1 — Foundational page
  └── Level 2: B1 — Intermediate overview
        ├── Level 3: C1 — Strategic overview
        └── Level 3: C2 — Strategic overview
```

In this example, B1 has A1 as its one Level 1 parent. C1 and C2 each have B1 as their Level 2 parent.

### 4.1 Illustrative page metadata

A page may have metadata like:

```js
const page = {
  id: "A1",
  title: "Topic A - Foundations",
  level: 1,
  parent: null,
  children: ["B1"]
};
```

A Level 2 page may have:

```js
const page = {
  id: "B1",
  title: "Topic A - Intermediate",
  level: 2,
  parent: "A1",
  children: ["C1", "C2"]
};
```

A Level 3 page may have:

```js
const page = {
  id: "C1",
  title: "Topic A - Strategic",
  level: 3,
  parent: "B1",
  children: []
};
```

These snippets illustrate the relationship model. The implementation should define one canonical JSON schema and avoid maintaining duplicate, conflicting relationship data. If both `parent` and `children` are stored, validation must ensure they agree.

### 4.2 Document provenance

The knowledge model should preserve where synthesized content came from. At minimum, metadata should support:

- A stable page ID.
- A title.
- A level.
- The parent page ID, where a parent exists.
- Child page IDs, where applicable, if stored redundantly.
- Source/derived-from page IDs for provenance.
- Related page IDs, where useful.
- Publication or review status.

An illustrative document front matter:

```yaml
---
id: topic-a-overview
title: Understanding Topic A
level: 2
derived_from:
  - topic-a
  - topic-b
related:
  - topic-b-overview
status: approved
reviewed_by: editor-1
---
```

The final schema should distinguish the structural parent relationship from provenance. The parent is the unique page at the next lower level; provenance is recorded separately in `derived_from`.

## 5. Recommended technology

| Area | Technology | Responsibility |
|---|---|---|
| Page content | HTML | Individual wiki pages |
| Presentation | CSS | Layout and responsive styling |
| Application logic | Vanilla JavaScript | Level selection, navigation and preference persistence |
| Knowledge metadata | JSON | Page registry and hierarchy relationships |
| Hosting | GitHub Pages | Public static website |
| Source control and review | GitHub | Version history and Pull Requests |
| Validation and deployment | GitHub Actions | Validate approved content, build/check the site and publish it |

Do not introduce a framework or backend unless a concrete requirement makes it necessary.

### 5.1 Deliberate non-goals for the initial implementation

Do not add these by default:

- Docusaurus or another documentation framework.
- React or another frontend framework.
- A database or graph database such as Neo4j.
- User accounts, login or server-side authorization.
- A mandatory runtime LLM API.
- A complex multi-agent architecture.

A future need for large-scale graph queries, synchronized user profiles, strict permissions or a commercial application may justify revisiting these choices. They are not requirements for the initial public wiki.

## 6. Suggested repository structure

Start with a simple structure along these lines:

```text
knowledge-wiki/
├── index.html
├── pages/
│   ├── A1.html
│   ├── A2.html
│   ├── A3.html
│   ├── B1.html
│   └── C1.html
├── data/
│   └── knowledge-tree.json
├── js/
│   ├── navigation.js
│   └── level-selector.js
├── css/
│   └── style.css
├── scripts/
│   ├── validate-documents.py
│   └── check-references.py
├── docs/
│   ├── PRODUCT_SPEC.md
│   └── ARCHITECTURE.md
├── .github/
│   └── workflows/
│       ├── validate.yml
│       └── deploy.yml
└── README.md
```

This is a suggested starting point, not a requirement to create every file in the first implementation step. Keep content, metadata, navigation logic and presentation separate. The LLM content workflow should be able to create or update documents and metadata without needing to rewrite the website application.

## 7. Content generation, review and publication

LLMs may draft foundational and synthesized documents, but generated content must not be published automatically without human review.

Recommended workflow:

1. Source material arrives (for example, PDFs, articles, research papers or notes).
2. An LLM drafts Level 1 documents.
3. A human reviews and approves the Level 1 documents.
4. An LLM synthesizes approved Level 1 documents into Level 2 documents.
5. A human reviews the Level 2 content, citations and relationships.
6. An LLM synthesizes approved Level 2 documents into Level 3 documents.
7. A human reviews the Level 3 content and its provenance.
8. Approved changes are merged into the publishing branch.
9. GitHub Actions validates the repository and deploys the site to GitHub Pages.

Use GitHub Pull Requests as the review interface. They provide diffs, discussion, approvals, history and a way to revert incorrect generated content. The LLM workflow should create proposed changes or branches/PRs; it must not bypass the human approval process and publish directly to production.

## 8. Validation and dependency tracking

Validation should protect the integrity of the hierarchy and the published site.

At minimum, automated checks should detect:

- Duplicate or missing page IDs.
- Missing page files or invalid page paths.
- Invalid level values.
- Parent IDs that do not exist.
- Child IDs that do not exist.
- A child whose declared parent does not list it, or the reverse, if both directions are stored.
- A page with more than one parent at the next lower level.
- Relationships that skip a level when the model requires adjacent-level parent relationships.
- Cycles or otherwise invalid hierarchy relationships.
- Broken internal links and references.
- Invalid or missing required metadata.
- Documents marked as unapproved that would otherwise enter the published content.

Dependency tracking is an important planned capability. When an Arc source changes, the system should identify every Level page whose `derived_from` includes it and mark those pages for review or regeneration. Structural children are separate from provenance; a change must not silently overwrite approved content.

## 9. GitHub Pages deployment

The production site is a public static website hosted on GitHub Pages.

The deployment workflow should:

1. Run document and relationship validation.
2. Check internal links and required assets.
3. Build or assemble the static site if the project uses a build step.
4. Deploy only changes merged through the agreed review process.
5. Fail visibly when validation fails.

Account for the fact that a GitHub Pages project site may be hosted below a repository path rather than at the domain root. Internal links, asset paths and navigation must work in the configured GitHub Pages base path as well as in local development.

Do not assume that hiding links makes documents private. All published files are public.

## 10. Implementation roadmap

Build and verify the navigation model before investing in automated knowledge generation.

| Phase | Deliverable |
|---|---|
| 1 | Minimal static site with sample pages at three levels |
| 2 | Persistent level selector on every content page |
| 3 | Central JSON page registry and hierarchy relationships |
| 4 | Correct navigation up and down the hierarchy, including child selection |
| 5 | Validation scripts for metadata, relationships and references |
| 6 | GitHub Actions validation and GitHub Pages deployment |
| 7 | Human review workflow for LLM-generated content |
| 8 | Dependency tracking and regeneration/review indicators |
| 9 | Optional interactive visualization of the hierarchy |

Keep the knowledge repository independent of the website implementation. The content pipeline should produce structured documents and metadata; the frontend should consume them. This preserves the option to replace the hosting platform or frontend later.

## 11. Acceptance criteria for the first working prototype

The prototype is ready when all of the following work:

1. The site contains example pages at Levels 1, 2 and 3, corresponding to *The Prophecies Begin*, *The New Prophecy* and *Power of Three*, with no content revealing later arcs at a lower level.
2. Every content page displays the knowledge-level selector.
3. The visitor's selected level is saved in `localStorage` and restored after reload.
4. Changing the selected level navigates to the corresponding page, rather than only changing the menu.
5. Moving from a higher level to its unique lower-level parent works.
6. Moving from a lower level to a level with multiple child pages uses the previously visited child when available; otherwise, the visitor can choose.
7. Following a topic link preserves the visitor's selected level and resolves the destination at that level when possible.
8. Missing or ambiguous relationships are handled explicitly; the site does not silently choose an arbitrary page.
9. Page relationships come from the central JSON data and are not inferred from filenames.
10. Automated validation catches broken or inconsistent relationships.
11. The site works when deployed under the GitHub Pages project-site path.
12. A reviewed change merged to the publishing branch can be deployed through GitHub Actions.

## 12. Guidance for OpenCode

Use this README as the project overview and product/architecture baseline.

When implementing:

- Inspect the existing repository before changing files.
- Preserve the agreed static architecture unless a requirement demonstrably cannot be met with it.
- Prefer small, understandable modules and minimal dependencies.
- Treat the JSON hierarchy as the single source of truth for page relationships.
- Implement and test navigation edge cases before adding optional features.
- Do not invent page relationships or silently guess a destination.
- Keep all published content public; do not imply that the level selector provides security.
- Add validation alongside the data model, not as a late cleanup step.
- Keep generated content separate from application code.
- Do not publish unreviewed LLM-generated content.
- Document any necessary deviation from this README and explain why it is needed.

Before considering a task complete, run the available validation and tests, check the GitHub Pages base-path behavior, and summarize what changed and what remains incomplete.

## 13. Decisions and open questions

The conversation establishes the following decisions:

- The audience is public.
- Level selection is information filtering/presentation, not strict authorization.
- The knowledge generation process includes human review.
- GitHub Pages is the initial hosting target.
- The initial implementation uses static HTML, CSS, vanilla JavaScript and JSON.
- Each page above Level 1 has one parent at the next lower level.
- A page may have multiple children at the next higher level.
- The selected level persists across navigation using browser storage.

The following details should be settled during implementation rather than guessed:

- The final visual design and page layout.
- The exact JSON schema and whether child lists are stored or derived from parent references.
- The default behavior for a topic link when no page exists at the visitor's selected level.
- How long previously visited child-page choices should be remembered and how they are keyed by parent/topic.
- The exact content authoring conventions and required provenance fields.
- Whether the initial prototype needs a search feature or hierarchy visualization.

Until these are decided, choose the simplest behavior consistent with the requirements, document it, and keep it easy to change.

---

**In short:** build a public, static, multi-level knowledge wiki for Warrior Cats, where each level means how far the reader has read in the serial (its subseries/arc) and defines a spoiler boundary. Keep the hierarchy explicit in JSON, let visitors choose and retain their position in the series, navigate between corresponding pages using the parent/child relationships, and publish only human-reviewed content through GitHub.
