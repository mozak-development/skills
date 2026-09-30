---
name: review-overview
description: Create a template-based technical HTML dossier that explains a changeset before deep review.
disable-model-invocation: true
---

# Review overview

Create an evidence-backed **dossier** for understanding a changeset before reviewing it. The artifact explains intent and structure; evaluative findings belong to the later review.

## Process

### 1. Pin the changeset and its sources

Use the scope supplied after `/skill:review-overview`. With no argument, cover all tracked and untracked changes in the current worktree relative to `HEAD`.

Read:

- the complete diff and status, including untracked source files;
- the originating issue, PRD, or acceptance criteria;
- relevant context maps, domain terminology, and ADRs;
- the modified production classes and their callers/callees;
- the modified tests.

Use the repository's structural index before text search when one exists. Separate implemented facts from intended follow-up work.

**Complete when:** every modified production class is assigned a role in the dossier, every architectural relation shown is verified in source, and the primary behavioral claims trace to the diff plus a requirement or ADR.

### 2. Build the explanatory model

Identify the smallest building block that explains the change. Write the dossier around that block, using the project's domain language.

Include these views when the changeset supports them:

1. **Executive delta** — one-sentence thesis and a concise before/after comparison.
2. **Runtime flow** — ordered stages from input/capture through side effects and publication.
3. **Class diagram** — changed classes, key collaborators, inheritance/implementation, owned values, and labelled call/data relationships.
4. **Decision semantics** — variants, precedence, evidence, and failure boundaries.
5. **Component matrix** — each important implementation, its input, output, and execution mode.
6. **Drill-down index** — expandable notes mapping concepts to concrete classes, methods, and paths.
7. **Preserved invariants** — architecture or behavior the change must keep true.
8. **Verification map** — which tests pin down each important behavior.
9. **Review boundary** — neutral hotspots and explicitly deferred follow-up work, without performing the review.

Omit views that would be empty. Keep the opening high-level; place method names, paths, and edge cases in drill-down sections.

**Complete when:** a reader can explain the change's purpose, transition order, decision owner, side-effect boundary, and principal class relationships from the collapsed page, while every important topic has an in-page route to source-level detail.

### 3. Produce the technical dossier bundle from the template

Place the dossier beside the feature spec when one exists; otherwise use `.scratch/review-overview.html`. Honor an explicit output path.

Use the assets bundled with this skill, resolving these paths relative to `SKILL.md`:

- `template.html` — required HTML structure and placeholder vocabulary;
- `review-overview.css` — canonical dossier styling;
- `review-overview.js` — canonical diagram-to-drill-down interaction.

Copy all three files into the output directory. Rename `template.html` to the requested HTML filename and keep `review-overview.css` and `review-overview.js` beside it so the template's relative references continue to work locally. Replace every `{{PLACEHOLDER}}` in the copied HTML with source-backed content; remove unused optional sections rather than leaving empty placeholders.

Do not create a new visual direction or add page-specific CSS unless the requested dossier cannot be represented with the existing template classes. Prefer composing the existing cards, matrices, flows, notes, details, and diagram classes. If a genuinely necessary style is missing, add the smallest reusable rule to the canonical `review-overview.css`, not an inline `<style>` or `style` attribute, and copy the updated stylesheet to the output directory.

Keep CSS and JavaScript external. Do not place inline `<style>` or inline executable `<script>` content in the generated HTML. Keep the class diagram as inline SVG because its labels and relationships are dossier content. When Mermaid is more appropriate, validate it, render it to SVG, and inline only the resulting SVG.

The supplied styles already provide:

- a restrained technical-dossier aesthetic and responsive typography;
- semantic cards, tables, native `<details>` drill-downs, and print styling;
- keyboard-focusable diagram nodes that work with `review-overview.js` through `data-jump` targets;
- horizontally scrolling runtime flow and class-diagram containers at narrow widths;
- reduced-motion behavior and local, network-independent rendering.

**Complete when:** the HTML, CSS, and JavaScript files are colocated and locally openable, no template placeholders remain, the collapsed page provides the overview, and its interactions expose details without navigating away.

### 4. Validate truth and rendering

Validate:

- HTML structure, unique IDs, internal anchors, and absence of unresolved `{{PLACEHOLDER}}` tokens;
- that the HTML references colocated `review-overview.css` and `review-overview.js`, with no inline CSS or executable JavaScript;
- external JavaScript syntax with `node --check review-overview.js`;
- every diagram node's drill-down target;
- every class relation and transition stage against current source;
- the page in a local browser at a wide desktop viewport and a narrow viewport;
- headline spacing, diagram labels, class-name containment, flow readability, and internal horizontal scrolling;
- repository whitespace checks.

Use temporary screenshots for visual inspection; deliver only the HTML/CSS/JavaScript bundle unless the user explicitly needs another artifact.

**Complete when:** both viewport renders are readable with no text collisions or clipping, all links and interactions resolve, all technical claims remain source-backed, and the final response gives the exact HTML, CSS, and JavaScript paths.
