# Prompt readme — HTML mockup → Figma screen (`figma-vd-rollout`)

For the case where **you have already ideated the screen as an HTML file** (a hand-built mockup, a v0/Lovable export, a coded prototype, a static page from the app) and you want that *same screen* rebuilt in Figma — inside the file that already holds your design-system variables, component library and approved design direction.

This is the companion to [PROMPT-TEMPLATE.md](PROMPT-TEMPLATE.md). Same skill, same phases, same two approval gates (design-system gap list → Screen Plan). The only difference is the **requirement source**: the HTML file replaces the wireframe *and* carries most of the requirement context, because the layout, IA, content and flow are already decided in it.

---

## The one rule that makes this work

The HTML is a **structure-and-content contract, not a style contract.**

```
design-system tokens / components   >   design direction   >   your HTML mockup
        (style always wins)              (look & feel wins)      (structure/content/flow wins)
```

- **Taken from the HTML, exactly:** regions and their order, information architecture, navigation placement, every label/column/field/row, copy, the data shown, the controls and what they do, the flow between screens.
- **Never taken from the HTML:** hex colours, `rgb()`/`hsl()` values, font families and sizes, `px` spacing/radius/border/shadow, Tailwind/Bootstrap utility classes, CSS transitions, browser chrome, and any framework's default look. All of that is re-derived from the `Semantics` / `ComponentTokens` variables and the design direction.
- **Never hand-drawn:** if the HTML has a table, a chip, a tab strip or a card, it becomes an **instance** of the DS component (existing → new variant → new component, in that order). A pixel-faithful redraw of your HTML that isn't made of DS instances is a failed run.

If a piece of the HTML can't be expressed by the design system, it goes through the **gap protocol** for your approval — it does not become a rectangle on the screen page.

---

## Template — full screen from an HTML mockup (copy this)

```text
Roll out the visual design in Figma using the figma-vd-rollout skill.
Source of truth for structure and content: the HTML mockup(s) below.

## Target  (REQUIRED)
<paste the Figma FILE link — the skill creates a new Rollout_<slug> page>
   …or…
<paste a Figma PAGE link — the skill builds on that page>

The file already contains: the design-system tokens as variables, the component
library, and the design direction page.

## HTML mockup — the ideated design  (REQUIRED)
File(s):
1. <path/to/screen.html>          → screen: <Screen name>
2. <path/to/other-screen.html>    → screen: <Screen name>
Assets/CSS it needs: <path to css/js/images, or "self-contained">
Render at: <viewport width, e.g. 1440×900 desktop; add 390 mobile if it must be shown>

READ the HTML, then RENDER it (headless browser) and screenshot it before designing,
so you design from what it actually looks like — not only from the markup. If it
cannot be rendered, say so and work from the markup + my description.

Treat the HTML as: exact structure, IA, regions, order, labels, copy, columns,
fields, controls and flow. Do NOT copy its colours, fonts, px spacing, radii,
shadows, utility classes or CSS transitions — those come from the design system
tokens and the design direction. Rebuild every element as a DS component instance:
existing component → new variant → new component, in that order.
Precedence on any conflict: DS tokens/components > design direction > my HTML.

## States inside the HTML  (REQUIRED — tell the skill how to reach them)
<e.g. "index.html shows the filled state; empty.html and error.html are the same
 screen in those states"  …or…  "toggle the .is-empty / .is-loading class on <main>"
 …or…  "the HTML only shows the filled state — derive the rest">
States to draw: <empty · loading · filled · invalid input · error · success · …
 or leave blank to let the skill walk the canonical set and affirm the N/A ones>

## Requirement context  (REQUIRED — one or more)
PRD / BRD: <path>
Requirements text: <what the screen is for, who uses it, the one primary action,
 the rules the HTML does not make visible: permissions, validation, async behaviour,
 data volumes, the extremes it must survive — longest label, 0 rows, 200 rows>
Ticket: <Jira/ADO ticket ID — the skill fetches it via MCP>

## Design direction  (REQUIRED)
Direction name: <exact <name> of the Design_Direction_<name> page>
Direction spec: <path, e.g. ax-sdd-artifacts/ax-design-specs/<app>-direction-<name>.md>

## ds-core spec  (optional but helpful)
<path, e.g. ax-sdd-artifacts/ax-design-specs/<app>-ds-core-<direction>.md>

## Deviations I expect you to make  (optional)
<where my HTML is knowingly rough, e.g. "the filter bar is a placeholder — use the
 DS Filter pattern", "ignore my table's inline styling", "the header is browser-only">

## Deviations you may NOT make  (optional)
<the parts of the HTML that are decided, e.g. "the 3-column split and the column
 order are fixed", "the step order in the wizard is fixed">

Notes: single light theme only (no dark mode). Bind only to semantic/component
tokens — never primitives, never raw values. Reuse the component library to the
maximum: existing component → new variant → new component, in that order, and put
anything reusable in the library, not on the screen page. Draw EVERY applicable
state as its own frame; affirm the ones that don't apply. Wire the motion as a real
Figma prototype. Anything in my HTML the design system cannot express goes through
the gap protocol for my approval BEFORE you draw. Flag any conflict between the
HTML, the requirements and the direction instead of designing over it.
Finish with: a mapping table (HTML region → Figma component instance), the frame
list with links, the state ledger, and every place you departed from my HTML and why.
```

---

## Template — one sub-section / tab from an HTML fragment (scoped run)

Use this when the parent screen already exists in Figma and your HTML covers only **one tab, panel, drawer or sub-view** inside it.

```text
Design ONE sub-section using the figma-vd-rollout skill — SCOPED run.
Source of truth for structure and content: the HTML fragment below.

## Scope lock  (READ FIRST — do not widen)
Work ONLY on the sub-section named below, inside the parent screen named below.
Do NOT redesign, restyle, re-lay-out, "improve", re-tokenize, renumber or re-audit:
- the parent screen's shell, header, navigation, sidebar, or any region outside
  the sub-section's own container
- any sibling tab / sub-section of the same screen
- any other screen, section, page, prototype flow or spec in this file
- the Design_Direction_* pages, the DS component library, or any variable
  (except a genuinely missing part, via the gap protocol, with my approval)
My HTML may include the surrounding shell for context — it is REFERENCE ONLY.
Copy the parent screen's existing shell from Figma unchanged; never rebuild the
shell from my HTML, even where the two differ.
If anything outside this scope looks wrong or blocks you, STOP and report it as a
finding at the end — do not fix it in this run.

## Target  (REQUIRED)
Figma page: <PAGE link that already holds the parent screen>
Parent screen: <exact section/frame name, e.g. "03 · Work Item Details">
Parent frame link: <link to the parent screen's default frame>
Where it renders: <the region inside the parent that owns this sub-section>

## HTML mockup of the sub-section  (REQUIRED)
File: <path/to/fragment.html>          Render at: <width>
The part that is in scope: <the selector/section, e.g. "everything inside
 <section id='artifacts'>" — everything else in the file is context only>
Structure, content and order come from it exactly; colour, type, spacing, radius,
shadow and motion come from the design system and direction. Every element becomes
a DS component instance.

## Sub-section  (REQUIRED)
Name (as the user sees it): <e.g. "Artifacts">
Description / primary action: <what it is for and the one thing the user does here>
Rules the HTML does not show: <permissions, empty conditions, validation, async>
Entry / exit: <how the user gets here and where they go next>
Requirement source (optional): <PRD path + FR ids | ticket ID>

## States to draw  (optional — else walk the canonical set and affirm the N/A ones)
<e.g. empty · loading · filled · error · permission-denied>
In every state frame ONLY the sub-section changes — the parent shell around it is
copied from the parent screen unchanged, never redrawn.

## Design direction  (REQUIRED)
Direction name: <exact <name> of the Design_Direction_<name> page>
Direction spec (optional): <path>
ds-core spec (optional): <path>

## Deliverable + naming  (REQUIRED)
Append the new frames INSIDE the parent screen's existing section, after its
existing frames, named:
  <parent NN>.<M> · <Screen> › <Sub-section>              (default)
  <parent NN>.<M> · <Screen> › <Sub-section> — <state>    (one per state)
Do not create a new page or a new section. Do not rename or renumber anything
that already exists.

Notes: bind only to semantic/component tokens (never primitives, never raw values).
Single light theme. Real content, no lorem. Anything my HTML needs that the design
system lacks goes through the gap protocol for my approval BEFORE you draw. Show me
the plan for this sub-section only, then build. Finish with: the HTML region →
Figma component mapping, the exact list of frames you created (links), the state
ledger (drawn / N/A-affirmed), and an explicit confirmation that nothing outside
the scope lock was modified.
```

---

## Filled example

```text
Roll out the visual design in Figma using the figma-vd-rollout skill.
Source of truth for structure and content: the HTML mockup(s) below.

## Target
https://www.figma.com/design/XXXX/Aatmix-AI      (create the rollout page)

## HTML mockup — the ideated design
File(s):
1. ax-sdd-artifacts/mockups/agent-board/index.html   → screen: Agent Board
2. ax-sdd-artifacts/mockups/agent-board/empty.html   → same screen, empty state
Assets/CSS it needs: ./styles.css + ./app.js in the same folder
Render at: 1440×900 desktop

READ the HTML, then RENDER it and screenshot it before designing.
Treat the HTML as: exact structure, IA, regions, order, labels, copy, columns,
fields, controls and flow. Do NOT copy its colours, fonts, px spacing, radii,
shadows, utility classes or CSS transitions. Rebuild every element as a DS
component instance. Precedence: DS tokens/components > design direction > my HTML.

## States inside the HTML
index.html is the filled state; empty.html is the same screen with no work items.
Loading, drag-in-progress, submission failure and permission-denied are not in the
HTML — derive them from the requirements.
States to draw: empty · loading · filled · drag-in-progress · submission failure ·
permission-denied (read-only viewer)

## Requirement context
PRD: ax-sdd-artifacts/prd/product-prd.md — FR-18…FR-24
Requirements text: the board is the kanban of work items per project; the one
primary action is moving an item between gate columns. Viewers see it read-only.
Gate status must be readable without relying on colour alone. Must survive a column
with 0 items and one with 60, and 90-character work-item titles.
Ticket: 214

## Design direction
Direction name: Sovereign Stage
Direction spec: ax-sdd-artifacts/ax-design-specs/aatmix-ai-direction-sovereign-stage.md

## ds-core spec
ax-sdd-artifacts/ax-design-specs/aatmix-ai-ds-core-sovereign-stage.md

## Deviations I expect you to make
- My card is hand-rolled — use the DS Card + Badge components.
- My top bar is a placeholder; use the direction's app shell / nav template.

## Deviations you may NOT make
- Five gate columns, in that order, horizontally scrollable — fixed.
- The card must keep title, agent avatar, gate badge and updated-at, in that order.

Notes: [keep the Notes block from the template verbatim]
```

---

## How to prepare the HTML so this run goes well

| Do | Why |
|---|---|
| **One screen per file** (or one clearly-marked section per screen) | The skill maps files → screens 1:1; a mega-page forces it to guess boundaries |
| **Ship the states as separate files** (`index.html`, `empty.html`, `error.html`) or name the exact class/attribute toggle | Otherwise only the default state is visible and every other state is derived, not matched |
| **Use real content** — real names, the longest label, a realistic row count | Lorem hides the layout problems the rollout is supposed to find |
| **Make it self-contained or list its assets** | The skill renders it headless; a broken CSS path means it designs from markup alone |
| **Annotate intent in comments** — `<!-- primary action -->`, `<!-- optional, cut if crowded -->` | Comments are read; they resolve exactly the questions that would otherwise become a blocking question |
| **Don't polish the styling** | It is discarded on purpose. Time spent on your CSS colours is time wasted |
| **Say what's rough vs what's decided** (the two "Deviations" blocks) | This is the single biggest driver of a first-pass-correct Screen Plan |

---

## How each field maps to the skill

| Field | Drives |
|---|---|
| Target (file vs page) | Phase 0 — build on the given page, or create `Rollout_<slug>` |
| HTML file(s) + render instruction | Phase 2 — read as requirement + wireframe in one; screenshot becomes the structural reference |
| HTML → screen mapping | Phase 2 — the screen set is final, no derivation needed |
| States inside the HTML | Phases 4 + 6 — the states your HTML shows are matched; the rest are derived from the canonical walk and affirmed |
| Requirement context / ticket | Phase 2 — purpose, primary action, permissions, failure modes, conflict hunt (the things markup cannot tell it) |
| Direction name + spec | Phase 1 — the binding visual language; Phase 8 nativeness check |
| ds-core spec | Phase 1 — component API and token dictionary without re-deriving from Figma |
| Deviations expected / not allowed | Phase 4 — where it may re-decide and where your HTML is law |
| Precedence line | Guardrail 5 — your HTML is treated exactly as a wireframe: structure yes, style never |

---

## What one run produces

- **A rollout page** — one section per screen: the default frame plus one frame per applicable state, matching your HTML's structure and content, composed from DS component instances and bound to semantic/component tokens.
- **A mapping table** — every HTML region → the Figma component instance that now represents it, plus every departure from your HTML with its reason.
- **A working prototype** — navigation, overlay open/close, state transitions and control feedback at the direction's durations and easing.
- **Design-system additions** (if any) — variants/components/tokens built *in the library* via the gap protocol, with the ds-core spec updated and a change report written.
- **A rollout spec** — `ax-sdd-artifacts/ax-design-specs/rollouts/<app>-rollout-<slug>.md`: screen contracts, full state ledger, motion table, DS additions, handoff notes, quality record, open questions.

---

## Notes

- **Prerequisites are unchanged:** tokens (`figma-ds-foundation` → `figma-ds-core`), the component library (`figma-ds-core`) and a direction (`figma-design-direction`) must already exist in the Figma file. The skill verifies each and stops if any is missing. An HTML mockup does **not** substitute for any of them.
- **Your HTML will not be reproduced pixel-for-pixel — that is the point.** It arrives in the design system's colour, type, spacing, radius, elevation and motion. Expect it to look better and different; expect the structure to be recognisably yours.
- **Where your HTML and the requirements disagree, the run stops and asks.** It will not silently design over a contradiction.
- **Two approval gates** still apply: the design-system gap list, then the Screen Plan. Everything after that is build + audit.
- **Long build:** budget for several messages — one screen at a time, screenshot-verified. If it pauses, say "continue"; tagging makes it resumable without duplicates.
- **Adding screens later:** re-run with the same page link and the new HTML files. Existing screens are skipped; new ones continue the numbering and must look native beside them.
- **Scope lock is the whole point of the scoped template — keep the block verbatim,** including the "my HTML may include the shell — reference only" clause. Without it a fragment that carries a `<header>` will get the parent screen re-laid-out.
- **If you maintain a VD contract**, run `figma-vd-contract` Update mode afterwards with this run's screen links — this skill never edits the contract.
