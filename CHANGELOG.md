# mattpocock-skills

## 1.2.0

### Minor Changes

- [`c0f0e83`](https://github.com/mattpocock/skills/commit/c0f0e8344020b8ffda74b02f29d2b0cd69c221c0) Thanks [@mattpocock](https://github.com/mattpocock)! - Bring the **`ask-matt`** router up to date with the full skill set. It now maps five skills it was missing: **`tdd`** (woven into the main flow as the red-green engine `implement` drives), **`diagnosing-bugs`** (a new "Something's broken" on-ramp — there was previously no route for a bug), **`domain-modeling`** and **`codebase-design`** (a new "Vocabulary underneath" section), and **`grilling`** (the shared interview primitive). `prototype` is fleshed out as a standalone and the description broadens from "user-invoked skills" to "the skills". A maintenance rule is added to `CLAUDE.md` so any future skill add/rename/remove or flow change triggers an `ask-matt` re-check, beside the existing docs-page re-sync rule.

- [`64072dd`](https://github.com/mattpocock/skills/commit/64072dd3db1211337e7ef22c760913e3ca650191) Thanks [@mattpocock](https://github.com/mattpocock)! - Rename the in-progress **`review`** skill to **`code-review`** and promote it from `in-progress/` to the `engineering/` bucket. It now ships in the plugin, is listed in the top-level and Engineering READMEs (Model-invoked), and has a human-facing docs page at `docs/engineering/code-review.md`. The `/implement` skill and docs now point at `/code-review`.

- [`ded883a`](https://github.com/mattpocock/skills/commit/ded883ad7e9a3c2794d3c93997b004e2e4638bd2) Thanks [@mattpocock](https://github.com/mattpocock)! - Add a fourth **Task** ticket type to the **`decision-mapping`** skill. Some blockers are neither a decision, a prototype, nor research — just literal manual work that has to happen before the discussion can move forward (moving data, signing up for a third-party service, provisioning access). The agent automates it where it can, otherwise hands the human a precise checklist, and records any resulting facts later tickets depend on.

- [`e499863`](https://github.com/mattpocock/skills/commit/e499863cbf9edca856c87e98558198c1cc99a0c4) Thanks [@mattpocock](https://github.com/mattpocock)! - Add a confirmation gate to **`grilling`**: the agent won't enact the plan until you confirm the shared understanding has been reached, turning the skill's existing "shared understanding" completion criterion into an explicit stop-gate. The `description` also recruits the pretrained **`grill`** leading word ("Grill the user relentlessly") to sharpen invocation, and the docs page is re-synced with the new gate behaviour.

- [`0d340a5`](https://github.com/mattpocock/skills/commit/0d340a5bfc9e64eb118b7aacbd454ebebc623728) Thanks [@mattpocock](https://github.com/mattpocock)! - Make the **`prototype`** skill model-invoked, so the agent can reach for it autonomously (and other skills can too). Its description is rewritten around the leading word _prototype_ — throwaway code that answers a design question — with one trigger per branch (state/logic sanity-check, or UI exploration).

- [#488](https://github.com/mattpocock/skills/pull/488) [`cdec9f6`](https://github.com/mattpocock/skills/commit/cdec9f6eb24dbfe606e3ad9b3eb457ba09210b85) Thanks [@mattpocock](https://github.com/mattpocock)! - Reword how the **`prototype`** skill handles its artifacts around a single idea: **the prototype is a primary source**. Rather than being deleted once it's answered its question, the prototype is captured as runnable evidence on a throwaway branch (`prototype/<name>`) out of main, with a context pointer to it left on the implementation issue — so the main branch keeps only the validated decision while the exploration stays findable. The answer (verdict + question) is still captured durably in an issue/ADR/commit.

- [`24e133c`](https://github.com/mattpocock/skills/commit/24e133c784d38eaead7746796603db626a757ab1) Thanks [@mattpocock](https://github.com/mattpocock)! - Add the **`research`** skill — a small, model-invoked skill that spins up a **background agent** to investigate a question against **primary sources** (official docs, source code, specs, first-party APIs), then leaves a single cited Markdown file wherever the repo keeps such notes. It's delegable reading legwork: you keep working while it reads, and get back a document to grill, plan, or design against. Listed in the top-level and Engineering READMEs (Model-invoked), added to `.claude-plugin/plugin.json`, given a docs page at `docs/engineering/research.md`, and routed as a Standalone in `ask-matt`.

- [`efc2b9c`](https://github.com/mattpocock/skills/commit/efc2b9c020099d5774ba020a558f3f41c430c713) Thanks [@mattpocock](https://github.com/mattpocock)! - Change **`wayfinder`**'s claim mechanism from a label to an assignee.

  A session now **claims** a ticket by assigning it to the dev driving the map, rather than setting a `wayfinder:claimed` label. The assignee _is_ the claim — an open, unassigned ticket is unclaimed — which reads more naturally in GitHub's own UI and frees the label vocabulary to `wayfinder:<type>` alone. The _claim_ leading word and its "first, before any work" rationale are unchanged; only the physical expression moved.

- [`2a14e35`](https://github.com/mattpocock/skills/commit/2a14e355da3047b6c5399763b97ca33d31e26b82) Thanks [@mattpocock](https://github.com/mattpocock)! - Make **`wayfinder`** collaborative by moving the map off a local Markdown file and onto the repo's issue tracker.

  The map is now a single `wayfinder:map` issue whose tickets are its child issues — one shared URL the whole team can watch and comment on. Blocking, claiming (`wayfinder:claimed`), and the frontier query all use native tracker semantics, so a session loads the map at low resolution (Notes + one context pointer per closed ticket + Fog prose) and zooms into individual tickets on demand, instead of loading the whole map every time.

  Wayfinder stays tracker-agnostic: the per-tracker mechanics live behind a pointer in `docs/agents/issue-tracker.md`, so `setup-matt-pocock-skills` now seeds a "Wayfinding operations" section for GitHub, GitLab, and local-markdown. Absent that doc, Wayfinder defaults to local-markdown.

- [`7b43403`](https://github.com/mattpocock/skills/commit/7b43403fda768da9b22cfc5e931f7593aaed62eb) Thanks [@mattpocock](https://github.com/mattpocock)! - Give **`wayfinder`** a first-class notion of **out of scope**, separate from fog.

  Fog and out-of-scope were conflated under one `## Fog` map section, gated by different things: fog by _knowledge_ (can't specify it yet — in scope, unripe, graduates as the frontier advances), out-of-scope by _scope_ (beyond the destination — never graduates). Cramming both under "Fog" made out-of-scope work read as takeable frontier (an unblocked, unclaimed item is indistinguishable from a live ticket).

  Now the map body has two plainly-named sections — **`## Not yet specified`** and **`## Out of scope`** — and the skill's prose splits to match: the **Fog of war** section teaches only the not-yet-specified bucket and keeps the two-way _fog-or-ticket_ sharpness test, while a new **Out of scope** section owns the scope axis (beyond the destination, closed not graduating, returns only as a fresh effort if the destination is redrawn). Charting and working-the-map now rule a beyond-destination ticket out of scope — close it, one line in _Out of scope_ — rather than leaving it on the frontier or logging it in _Decisions so far_.

  The **fog of war** leading word is retained: it names the concept and drives the graduate-the-fog behavior in the prose; only the human-facing map headings go to plain language.

- [`2e39c74`](https://github.com/mattpocock/skills/commit/2e39c743940e9a3f3b547f0a331c3447f962de8a) Thanks [@mattpocock](https://github.com/mattpocock)! - Sharpen **`wayfinder`**'s top-level purpose around **destination** as the leading word: wayfinding finds the _way_ to a destination, it doesn't charge at building it.

  The opening now states that the destination varies per effort — a spec to hand off and iterate, a decision to lock before planning, or a change made in place like a data-structure migration — and that naming it is the first act of charting, because it shapes every ticket. The map body gains a `## Destination` field that every session orients to before choosing a ticket, and triage's first step now pins the destination before any ticket exists. The stray `goal` mentions in the description and Fog section are unified onto `destination`.

- [`4b9d1e1`](https://github.com/mattpocock/skills/commit/4b9d1e18ccb2b58b962462cb95dab1dcbf4981c1) Thanks [@mattpocock](https://github.com/mattpocock)! - Make **`wayfinder`**'s no-duplication contract explicit: the map is an **index**, not a store.

  Adopting "index" as the leading word for the map's role fixes two duplication risks. The map now states up front that a decision lives in exactly one place — its ticket — so it only ever gists and links, never restates the answer (previously this rule was implied inside an HTML comment in the map-body template). And graduating fog into a ticket now clears the graduated patch from the Fog, so a suspected question can't linger in both places at once.

- [`163bb90`](https://github.com/mattpocock/skills/commit/163bb909b21ca11ab41c612b9c342cfe6e2b82ce) Thanks [@mattpocock](https://github.com/mattpocock)! - Reframe **`wayfinder`**'s description around the work it's for: planning a huge chunk of work, more than one agent session can hold.

  The description does the skill's invocation work, so it now leads with the trigger that actually reaches for Wayfinder — a big effort you need to break down — instead of the softer "chart a route through a foggy problem". The skill body and fog-of-war mechanic are unchanged.

- [`d7a9ca1`](https://github.com/mattpocock/skills/commit/d7a9ca1a1d34bae91d6bb8b00f2b7081899a8e8c) Thanks [@mattpocock](https://github.com/mattpocock)! - Rename the **`decision-mapping`** skill to **`wayfinder`**, invoked as `/wayfinder`.

  "Decision map" was jargony and inaccurate — only one of the skill's four ticket types (Grilling) is actually a decision. The reframe charts a route through a foggy problem, resolving investigation tickets one at a time until the way to the goal is clear. This makes one coherent leading-word frame (fog of war / frontier / the map) instead of mixing an invented term on top of it.

  Also a pruning pass: unified `node`→`ticket`, bound "the frontier" to the unblocked tickets, dropped the duplicated "one question at a time" (owned by `/grilling`), and trimmed intro no-ops.

### Patch Changes

- [#502](https://github.com/mattpocock/skills/pull/502) [`44eed54`](https://github.com/mattpocock/skills/commit/44eed545186ffd0263e8004867750b80cfddd215) Thanks [@mattpocock](https://github.com/mattpocock)! - Make `/setup-matt-pocock-skills` friendlier and align the local-markdown tracker with the current spec.

  - **Triage labels** are now asked about only when the `triage` skill is installed, and then as a single recommended-yes question ("keep the default triage labels?") instead of an override interrogation. When `triage` isn't installed, the section — and `docs/agents/triage-labels.md` — are skipped.
  - **External PRs as a request surface** is no longer a setup question. The GitHub/GitLab templates still carry the flag, defaulted off; a user can flip it in `docs/agents/issue-tracker.md` later.
  - **Domain docs** default to single-context without asking; multi-context is only offered when the repo shows monorepo signals.
  - **Local-markdown tickets** are now one file per ticket under `.scratch/<feature>/issues/<NN>-<slug>.md` — never a single combined `tickets.md`. `/to-tickets` and the local issue-tracker template now agree, and the spec file is `spec.md` (not `PRD.md`) to match `/to-spec`.

  Docs pages for `setup-matt-pocock-skills` and `to-tickets` re-synced.

- [`478b734`](https://github.com/mattpocock/skills/commit/478b7342c2c14efacf4f8cb7edaa9851a6ffba44) Thanks [@mattpocock](https://github.com/mattpocock)! - Give the in-progress **`code-review`** skill an always-on Fowler smell baseline on its Standards axis. A curated ~12 high-signal "Bad Smells in Code" (Mysterious Name, Duplicated Code, Feature Envy, Data Clumps, Primitive Obsession, Repeated Switches, Shotgun Surgery, Divergent Change, Speculative Generality, Message Chains, Middle Man, Refused Bequest) are inlined into `SKILL.md` as a fixed baseline alongside whatever the repo documents — not a new third axis. Two binding rules keep it safe: a documented repo standard overrides the baseline, and every smell is reported as a judgement call, never a hard violation.

- [`3c7f812`](https://github.com/mattpocock/skills/commit/3c7f812b56f0ba3247104db80bdc3fb78c24d6cd) Thanks [@mattpocock](https://github.com/mattpocock)! - Reshape the `tdd` skill into reference-only. The red → green → refactor loop is anchored by leading words the model already holds, so the step-by-step Workflow was largely restating the loop and duplicating the horizontal-slicing anti-pattern. Dropped the Workflow and per-cycle checklist; folded their one durable idea — vertical slices / tracer bullets — into the Anti-patterns section and a short Rules-of-the-loop list. Introduced **seam** as the leading word for where tests go, collapsing the old Philosophy "public interfaces" prose and the Planning "confirm interface / behaviors" handshake into one rule: test only at pre-agreed seams, confirmed with the user before any test is written.

  Also dropped the refactor stage — TDD is now red → green, not red → green → refactor. Refactoring belongs to the review stage, not the implementation loop, so the refactor rule and `refactoring.md` were removed (its home is the `review` skill).

- [`c57c8dd`](https://github.com/mattpocock/skills/commit/c57c8ddaf614b990cc718797cbe9e1c184cd93a4) Thanks [@mattpocock](https://github.com/mattpocock)! - Add the **tautological test** anti-pattern to the `tdd` skill. Tests whose assertion is recomputed the way the code computes it pass by construction and give zero confidence — distinct from the implementation-coupling anti-pattern already covered. Added as a peer at the same three sites: a Philosophy principle (expected values must come from an independent source of truth), a per-cycle checklist gate, and a BAD/GOOD example pair in `tests.md`.

- [`90c633c`](https://github.com/mattpocock/skills/commit/90c633c4a8f238df2da229b1c475ef3096a780be) Thanks [@mattpocock](https://github.com/mattpocock)! - Sharpen Wayfinder's blocking rule to prefer the tracker's native dependency relationship, and update the GitHub and GitLab issue-tracker templates to match.

  Native blocking is essential rather than cosmetic: it renders the frontier visually in the tracker's own UI, so the human sees what's takeable at a glance without opening the map. `wayfinder`'s SKILL.md now states that preference and rationale; the GitHub template spells out the native issue-dependencies recipe (`gh api .../dependencies/blocked_by`, frontier query on `issue_dependencies_summary.blocked_by`), and the GitLab template names the native `/blocked_by` blocking link (Premium/Ultimate) with the body-convention fallback. Both keep the body fallback for trackers that lack native blocking.

## 1.1.0

### Minor Changes

- [#406](https://github.com/mattpocock/skills/pull/406) [`930a450`](https://github.com/mattpocock/skills/commit/930a450089f77a49af09001d955db8452a4b867d) Thanks [@mattpocock](https://github.com/mattpocock)! - Bring the **`ask-matt`** router up to date with the full skill set. It now maps five skills it was missing: **`tdd`** (woven into the main flow as the red-green engine `implement` drives), **`diagnosing-bugs`** (a new "Something's broken" on-ramp — there was previously no route for a bug), **`domain-modeling`** and **`codebase-design`** (a new "Vocabulary underneath" section), and **`grilling`** (the shared interview primitive). `prototype` is fleshed out as a standalone and the description broadens from "user-invoked skills" to "the skills". A maintenance rule is added to `CLAUDE.md` so any future skill add/rename/remove or flow change triggers an `ask-matt` re-check, beside the existing docs-page re-sync rule.

- [#464](https://github.com/mattpocock/skills/pull/464) [`639df6e`](https://github.com/mattpocock/skills/commit/639df6e7386dfddc739b2aecdeff37a876f2483b) Thanks [@mattpocock](https://github.com/mattpocock)! - Promote and harden **`code-review`**. The in-progress **`review`** skill is renamed to **`code-review`** and moved from `in-progress/` into `engineering/`: it now ships in the plugin, is listed in the top-level and Engineering READMEs (Model-invoked), and has a docs page at `docs/engineering/code-review.md`. The `/implement` skill and docs point at `/code-review`.

  It also gains an always-on **Fowler smell baseline** on its Standards axis — a curated ~12 high-signal "Bad Smells in Code" (Mysterious Name, Duplicated Code, Feature Envy, Data Clumps, Primitive Obsession, Repeated Switches, Shotgun Surgery, Divergent Change, Speculative Generality, Message Chains, Middle Man, Refused Bequest) inlined into `SKILL.md` as a fixed baseline alongside whatever the repo documents, not a new third axis. Two binding rules keep it safe: a documented repo standard overrides the baseline, and every smell is reported as a judgement call, never a hard violation.

- [#464](https://github.com/mattpocock/skills/pull/464) [`639df6e`](https://github.com/mattpocock/skills/commit/639df6e7386dfddc739b2aecdeff37a876f2483b) Thanks [@mattpocock](https://github.com/mattpocock)! - Sharpen **`grilling`** on two fronts.

  **A confirmation gate.** The agent won't enact the plan until you confirm the shared understanding has been reached — turning the skill's existing "shared understanding" completion criterion into an explicit stop-gate. The `description` also recruits the pretrained **`grill`** leading word ("Grill the user relentlessly") to sharpen invocation, and the docs page is re-synced.

  **Facts vs. decisions.** Grilling now splits _facts_ (look them up — explore the codebase) from _decisions_ (put each one to the human and wait for their answer). The old blanket line — "if a question can be answered by exploring the codebase, explore the codebase instead" — was written for the live-human case, but once another skill runs grilling inside a resolve-the-ticket frame it read as license to answer _decisions_ autonomously too. Separating the two keeps a grilling agent from racing ahead and answering its own questions.

- [#463](https://github.com/mattpocock/skills/pull/463) [`af6d692`](https://github.com/mattpocock/skills/commit/af6d6922c3e2b5288eef155346cbe319e4ed3bd0) Thanks [@mattpocock](https://github.com/mattpocock)! - Add two adjacent Steering failure modes to **`writing-great-skills`**, both about how language you think of as "off" still steers the agent. **Negation** — the _elephant_ — is steering by prohibition: naming what _not_ to do drags the forbidden behaviour into context and makes it _more_ available, not less (_don't think of an elephant_), so the cure is to prompt the **positive**. **Negative Space** — the void — is blindness to the steering done by what you leave _out_: every decision a skill declines is delegated to the agent's priors rather than left neutral, so the cure is to read a draft for its silences and decide each omission deliberately (fill it, or leave it open as a real **branch**). Kept as two entries, not one — they carry different diagnostics and different cures — each a full `GLOSSARY.md` entry plus a `SKILL.md` failure-mode bullet, matching how every other failure mode is carried.

- [`850873c`](https://github.com/mattpocock/skills/commit/850873cd73d5f81826ebf512ad35d2b1e113001f) Thanks [@mattpocock](https://github.com/mattpocock)! - Make the **`prototype`** skill model-invoked, so the agent can reach for it autonomously (and other skills can too). Its description is rewritten around the leading word _prototype_ — throwaway code that answers a design question — with one trigger per branch (state/logic sanity-check, or UI exploration).

- [#409](https://github.com/mattpocock/skills/pull/409) [`0d74d01`](https://github.com/mattpocock/skills/commit/0d74d01cbc64ca27778a49b38599f70c534e76a0) Thanks [@mattpocock](https://github.com/mattpocock)! - Add the **`research`** skill — a small, model-invoked skill that spins up a **background agent** to investigate a question against **primary sources** (official docs, source code, specs, first-party APIs), then leaves a single cited Markdown file wherever the repo keeps such notes. It's delegable reading legwork: you keep working while it reads, and get back a document to grill, plan, or design against. Listed in the top-level and Engineering READMEs (Model-invoked), added to `.claude-plugin/plugin.json`, given a docs page at `docs/engineering/research.md`, and routed as a Standalone in `ask-matt`.

- [#469](https://github.com/mattpocock/skills/pull/469) [`a0329ba`](https://github.com/mattpocock/skills/commit/a0329ba95751f58566ed7ab484475917a68f1629) Thanks [@mattpocock](https://github.com/mattpocock)! - Split the **`to-issues`** skill into a lean **Process** and a **Reference** section, and teach it to handle a **wide refactor** — a single mechanical change (like renaming a column) whose **blast radius** fans across the whole codebase, breaking thousands of call sites at once so no vertical slice can land green. The drafting step now points at two co-located reference blocks: the **Vertical slice rules** for ordinary tracer bullets, and **Wide refactors**, which slices the change by **expand–contract** (expand the new form beside the old, migrate call sites in batches sized by blast radius, then contract the old form away) so CI stays green batch to batch — or, when it can't, only at a final integrate-and-verify issue. The issue body template moves into Reference too.

- [#464](https://github.com/mattpocock/skills/pull/464) [`386d4ff`](https://github.com/mattpocock/skills/commit/386d4ff719a7c420ad1454232d0436b01f1b8c17) Thanks [@mattpocock](https://github.com/mattpocock)! - Unify the planning skills. **`to-prd` is renamed to `to-spec`** — "spec" is now the single through-line term (it still opens with "you may know this document as a PRD" for discoverability). **`to-plan` and `to-issues` are merged into one `to-tickets` skill, and `to-issues` is deleted.**

  `to-tickets` breaks a plan, spec, or conversation into a set of **tickets** — tracer-bullet vertical slices, each declaring its **blocking edges**. That one artifact reads two ways depending on the tracker `/setup-matt-pocock-skills` configured: a **local file** (`tickets.md`) writes the edges as text and you work it top-to-bottom by hand; a **real tracker** writes them as native blocking links, so any ticket whose blockers are done is on the frontier and several agents can run at once. The edges live in the ticket either way — the medium only decides whether anything acts on them in parallel.

  Publishing prefers the tracker's **native sub-issues** for parent → slice and **native blocking edges** for `Blocked by` where the tracker supports them, keeping the `## Parent` / `## Blocked by` body sections as the fallback. The "What to build" template points at where a `/prototype`'s code lives rather than inlining a snippet from it.

  `ask-matt`'s main flow now routes `idea → /to-spec → /to-tickets → /implement`, and there are human-facing docs pages at `docs/engineering/to-spec.md` and `docs/engineering/to-tickets.md`.

- [#464](https://github.com/mattpocock/skills/pull/464) [`0557d57`](https://github.com/mattpocock/skills/commit/0557d57579d9b3d39839fdaf8d4a6542b17539ce) Thanks [@mattpocock](https://github.com/mattpocock)! - Settle wayfinder's place in the docs as a **situational on-ramp**, not the new main entry flow — the grill-led _idea → ship_ chain stays the front door (crowning wayfinder as the default spine is a v2-sized move, not a 1.1). The **`ask-matt`** router now names wayfinder's concrete triggers — a greenfield project or a huge feature build, too big for one session — and the two grill front doors (**`grill-me`**, **`grill-with-docs`**) signpost _up_ to wayfinder for the effort that's too big to hold in one session, so the on-ramp is discoverable from where a reader actually starts.

- [#464](https://github.com/mattpocock/skills/pull/464) [`639df6e`](https://github.com/mattpocock/skills/commit/639df6e7386dfddc739b2aecdeff37a876f2483b) Thanks [@mattpocock](https://github.com/mattpocock)! - Graduate and reframe **`wayfinder`** — the skill for planning a huge chunk of work, more than one agent session can hold. It moves out of `in-progress/` into `engineering/` (plugin entry, top-level + Engineering READMEs under **User-invoked**, a docs page at `docs/engineering/wayfinder.md`, and a route in `ask-matt`), landing as a mature skill. The rename and reframe that got it there:

  - **`decision-mapping` is renamed to `wayfinder`**, invoked as `/wayfinder`. "Decision map" was jargony and inaccurate — only one ticket type is actually a decision. The reframe charts a route through a foggy problem instead, giving one coherent leading-word frame — **fog of war**, **frontier**, **the map** — rather than an invented term layered on top.
  - **Destination as the leading word.** Wayfinding finds the _way_ to a destination; it doesn't charge at building it. Naming the destination is the first act of charting — it fixes the scope and shapes every ticket — so the map gains a `## Destination` field every session orients to, and triage pins it before any ticket exists.
  - **Plan, don't do.** The map produces **decisions, not deliverables**; it's done when nothing is left to decide before someone builds the thing. An effort can override this in its Notes.
  - **The map is an index, not a store.** A decision lives in exactly one place — its ticket — so the map only gists and links, never restates; graduating fog into a ticket clears the graduated patch so nothing lingers in two places.
  - **Collaborative by default.** The map moves off a local Markdown file onto the repo's issue tracker: a single `wayfinder:map` issue whose tickets are its child issues — one shared URL the team can watch. Sessions load the map at low resolution and zoom into tickets on demand. Wayfinder stays tracker-agnostic (GitHub, GitLab, local-markdown) behind a pointer in `docs/agents/issue-tracker.md`, and `setup-matt-pocock-skills` seeds the "Wayfinding operations" section.
  - **Claim by assignment, not a label.** A session claims a ticket by assigning it to the driving dev — the assignee _is_ the claim — freeing the label vocabulary to `wayfinder:<type>` alone.
  - **Native blocking.** Blocking prefers the tracker's native dependency relationship, which renders the frontier visually in the tracker's own UI so the human sees what's takeable without opening the map. GitHub and GitLab templates spell out the native recipe, with a body-convention fallback.
  - **Fog vs. out of scope, split.** Two plainly-named map sections — `## Not yet specified` (in-scope fog that graduates as the frontier advances) and `## Out of scope` (work ruled beyond the destination, closed, never graduating) — so beyond-destination work no longer reads as takeable frontier.
  - **A fourth `task` ticket type.** For literal manual work that blocks a decision (provisioning access, moving data, signing up for a service) — the one type that _does_ rather than decides, earning its place by unblocking a decision.
  - **HITL / AFK ticket classification.** Every ticket type is **HITL** (human in the loop — grilling, prototype) or **AFK** (agent alone — research; task is either). A HITL ticket only resolves through the live exchange, so "wait for the human" falls out of the label — a grilling agent that answers its own questions has, by definition, broken HITL. (This fixes students' reports of `/wayfinder` grilling _itself_ instead of the human.)
  - **No-fog early exit restored.** If the opening breadth-first grilling surfaces no fog, the journey is small enough for one session — so it stops and asks how you'd like to proceed rather than building a map nobody needs.

### Patch Changes

- [#464](https://github.com/mattpocock/skills/pull/464) [`639df6e`](https://github.com/mattpocock/skills/commit/639df6e7386dfddc739b2aecdeff37a876f2483b) Thanks [@mattpocock](https://github.com/mattpocock)! - Reshape **`tdd`** into a reference-only skill and add a missing anti-pattern.

  **Reference-only.** The red → green → refactor loop is anchored by leading words the model already holds, so the step-by-step Workflow was largely restating the loop. Dropped the Workflow and per-cycle checklist; folded their one durable idea — vertical slices / tracer bullets — into the Anti-patterns section and a short Rules-of-the-loop list. Introduced **seam** as the leading word for where tests go: test only at pre-agreed seams, confirmed with the user before any test is written. Also dropped the refactor stage — TDD is now red → green; refactoring belongs to the review stage, so the refactor rule and `refactoring.md` moved out (its home is `code-review`).

  **Tautological tests.** Added the tautological-test anti-pattern: a test whose assertion is recomputed the way the code computes it passes by construction and gives zero confidence — distinct from the implementation-coupling anti-pattern already covered. Added as a peer at the same sites: a Philosophy principle (expected values must come from an independent source of truth), a checklist gate, and a BAD/GOOD example pair in `tests.md`.

- [`e00eadb`](https://github.com/mattpocock/skills/commit/e00eadb4bb32c3d5a631ead1a5ed5d6a7c5f74e2) Thanks [@mattpocock](https://github.com/mattpocock)! - Extend the **`triage`** skill to triage external pull requests, treating a PR as an issue with attached code that runs through the same roles and state machine. PRs flow inline alongside issues (gated by a per-repo setup toggle), discovery surfaces only external PRs, the bug-only "reproduce" step is generalized into a single "verify the claim" step, and a redundancy check resolves already-implemented requests to `wontfix` without polluting the out-of-scope knowledge base. `setup-matt-pocock-skills` gains the PRs-as-a-request-surface toggle for GitHub/GitLab.

- [#472](https://github.com/mattpocock/skills/pull/472) [`d869d45`](https://github.com/mattpocock/skills/commit/d869d45afc32beab1c2d1350f8de5e81589512cd) Thanks [@mattpocock](https://github.com/mattpocock)! - Fix **`wayfinder`** hardcoding the issue-tracker doc path, which broke the indirection the rest of the suite relies on.

  `to-issues`, `to-prd`, and `triage` never name a path — they resolve the tracker through the `### Issue tracker` block that `setup-matt-pocock-skills` writes into `CLAUDE.md` / `AGENTS.md`, which points at the tracker doc wherever it lives. Wayfinder instead pinned the literal `docs/agents/issue-tracker.md`, so in a repo that keeps its agent docs elsewhere it silently fell back to the local-markdown tracker — even one whose `CLAUDE.md` clearly declares GitHub issues. It now resolves the doc via that same pointer and reads its "Wayfinding operations" section by name, keeping the indirection consistent across the suite.

## 1.0.1

### Patch Changes

- [`d20ee26`](https://github.com/mattpocock/skills/commit/d20ee2684e2a9442698ac3c1e0f2c5b68c4cf296) Thanks [@mattpocock](https://github.com/mattpocock)! - Make the **`teach`** skill reuse-first. Lessons are now built from reusable **components** in `./assets/` — stylesheets, quiz widgets, simulators, diagram helpers. Reuse is the default: the agent reads `./assets/` before authoring a lesson, builds from what's there, and extracts anything new and reusable into a component rather than inlining it.

## 1.0.0

### Major Changes

- [`47bde84`](https://github.com/mattpocock/skills/commit/47bde84da032afb2e5058f997f3bbca47d321dbd) Thanks [@mattpocock](https://github.com/mattpocock)! - Add the **`ask-matt`** skill — a user-invoked router that points you at the right skill or flow for your situation.

  **Breaking:** `ask-matt` routes over the other user-invoked skills in this repo, so it expects them to be installed.

- [`47bde84`](https://github.com/mattpocock/skills/commit/47bde84da032afb2e5058f997f3bbca47d321dbd) Thanks [@mattpocock](https://github.com/mattpocock)! - Add the shared design skills and rewire existing skills onto them.

  - New **`codebase-design`** skill — the deep-module vocabulary (module, interface, depth, seam, adapter) and the principles for putting a lot of behaviour behind a small interface. The language that previously lived in `improve-codebase-architecture/LANGUAGE.md` now lives here, generalized for reuse across skills.
  - New **`domain-modeling`** skill — actively build and sharpen a project's domain model, stress-testing terms against the glossary and keeping `CONTEXT.md` and ADRs current.
  - `improve-codebase-architecture` now draws its architecture vocabulary from `/codebase-design` and its domain model from `/domain-modeling`.
  - `tdd` now leans on `/codebase-design` for interface-design guidance — its inline `deep-modules.md` / `interface-design.md` notes were removed in favour of the shared skill.
  - `grill-with-docs` now builds the domain model inline via `/domain-modeling`.

  **Breaking:** these skills now depend on the new `codebase-design` / `domain-modeling` skills, so you must install them too.

- [`47bde84`](https://github.com/mattpocock/skills/commit/47bde84da032afb2e5058f997f3bbca47d321dbd) Thanks [@mattpocock](https://github.com/mattpocock)! - Remove the **`caveman`** and **`zoom-out`** skills.

  - `caveman` was a duplicate of another skill I was testing and was never meant to be public.
  - `zoom-out` went unused in practice, so it's been removed from the repo.

  **Breaking:** both skills have been removed.

- [`47bde84`](https://github.com/mattpocock/skills/commit/47bde84da032afb2e5058f997f3bbca47d321dbd) Thanks [@mattpocock](https://github.com/mattpocock)! - Rename the **`diagnose`** skill to **`diagnosing-bugs`**.

  **Breaking:** invoke it as `/diagnosing-bugs` — the old `/diagnose` name no longer exists.

- [`47bde84`](https://github.com/mattpocock/skills/commit/47bde84da032afb2e5058f997f3bbca47d321dbd) Thanks [@mattpocock](https://github.com/mattpocock)! - Replace **`write-a-skill`** with **`writing-great-skills`**.

  - Removed `write-a-skill`.
  - Added `writing-great-skills` (plus its `GLOSSARY.md`) — a reference for writing and editing skills well: the vocabulary and principles that make a skill predictable, hunting no-ops down to the sentence level.
  - Exposed `grilling` as a model-invoked skill — the reusable interview loop behind `grill-me` and `grill-with-docs`.

  **Breaking:** `write-a-skill` has been removed; use `writing-great-skills` instead.

### Minor Changes

- [`47bde84`](https://github.com/mattpocock/skills/commit/47bde84da032afb2e5058f997f3bbca47d321dbd) Thanks [@mattpocock](https://github.com/mattpocock)! - Add the **`resolving-merge-conflicts`** skill — a loop for resolving an in-progress git merge or rebase conflict. Standalone, with no dependencies on other skills.

- [`47bde84`](https://github.com/mattpocock/skills/commit/47bde84da032afb2e5058f997f3bbca47d321dbd) Thanks [@mattpocock](https://github.com/mattpocock)! - Rename the skill taxonomy from **Commands / Skills** to **User-invoked / Model-invoked** across the docs, and add `docs/invocation.md` defining the split: user-invoked skills are reachable only when you type them and exist to orchestrate; model-invoked skills can also be reached automatically when the task fits. A user-invoked skill may invoke model-invoked skills, but never another user-invoked one.

### Patch Changes

- [`47bde84`](https://github.com/mattpocock/skills/commit/47bde84da032afb2e5058f997f3bbca47d321dbd) Thanks [@mattpocock](https://github.com/mattpocock)! - Tighten the **`review`** skill: fail-fast ref check, single-sourced rules, and no-op cuts.
