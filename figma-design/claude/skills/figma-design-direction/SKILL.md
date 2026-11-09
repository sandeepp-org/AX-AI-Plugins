---
name: figma-design-direction
description: Build a UI DESIGN DIRECTION in Figma — complete, high-fidelity screens for a product's critical flows (landing/entry, dashboard, main navigation + layout, and one complex journey), drawn as flat frames bound to the existing design-system primitive variables, plus a motion pass that demonstrates state changes (buttons, navigation, popups, screen transitions) as a Figma prototype. Produces one named directional concept per run; run 3–4 times to generate distinct alternatives to choose from. Runs in TWO modes — (A) create a new direction, or (B) ADD more screens to an existing direction: on a later run the user names an existing direction and gives the new screens' features, and the skill designs those screens to conform to that direction's concept and places them on that direction's existing Figma page (never a new page, never a redesign). Also writes/updates a reusable design-direction spec (markdown) as a contract for downstream screen-rollout AI. Use when the user asks to "create a design direction", "build design direction in Figma", "design the key screens", "produce a design concept / look and feel", "make a direction alternative", "explore a UI direction", or "add screens to an existing direction / add more screens". Requires a Figma file whose primitive foundation (Brand_Primitives + App_Primitives) already exists, plus requirement context (BRD/PRD/requirements). This skill designs screens and their motion only — it creates NO variables or semantic tokens, and only minimal page-local components needed to demonstrate motion (the full component library belongs to a later rollout skill).
---

# UI Design Direction → Figma Screens

## In the Aatmix platform

When the Aatmix platform's UX Agent runs this skill, read every instruction below through this mapping. Everything else in the skill applies unchanged.

- **"The user"** is the UX Engineer, reached through the Exchange. Ask with `exchange_ask`: numbered replies, your recommendation marked, one round at a time — every open question of that round together, then end the turn. The run resumes with the answers. "Ask", "confirm", "get a go-ahead", "wait for approval" and "the interactive input tool" all mean such a round.
- **The Figma file** is the project's connected Figma file named in the run details. Only when none is connected, ask the UX Engineer for its URL.
- **The brand guidelines** are artifact `BRG`, already supplied by the project (run details name its version; read it with `artifacts_read`). Never ask for them.
- **The PRD** is the approved PRD artifact the run details name (`artifacts_read`), never a file.
- **Files the skill writes or reads under `ax-sdd-artifacts/`** live in the artifact store instead (`artifacts_list`, `artifacts_read`, `artifacts_put_draft`, then `artifacts_submit` for review as the task text says), under these kinds: direction spec → `visual_direction`; foundation report → `brand_primitives`; ds-core spec → `semantic_tokens` (its token half: the Semantics and ComponentTokens mapping and the styles) and `core_components` (its component half: the component inventory); VD contract → `vd_contract`; rollout record → `screen_rollout`; product-change report → the PRD change requests the task text names (never edit the PRD). A change report the skill writes beside a spec goes into the next version of that spec's artifact, as its own section.
- **Previews** (PNG or HTML of what you drew) go to `design_preview_put` against the project asset they belong to (`brand_primitives`, `visual_direction`, `semantic_tokens`, `core_components`, `vd_contract`), and to `screen_preview_put` against a screen's record.
- **Other skills**: `figma-ds-foundation`, `figma-design-direction`, `figma-ds-core`, `figma-vd-contract` and `figma-vd-rollout` are this plugin's skills; `figma-use`, `figma-generate-design` and `figma-generate-library` come from the Figma plugin.
- **In this skill**: the requirement context is the approved PRD. Each run of Mode A is one alternative on its own `Design_Direction_<name>` page; run it once per alternative the task text asks for, store a PNG of each direction's key screens with `design_preview_put` against the `visual_direction` draft, and let the UX Engineer choose among them (with feedback) in one Exchange round with the previews attached. The direction spec of Phase 8 is the `visual_direction` artifact: the alternatives, the chosen one and its revisions.

Produce a **design direction**: complete, presentation-grade screens for a product's most important flows, expressing one coherent visual personality, built entirely from the design system's existing **primitive variables**. This is the exploration stage of the pipeline:

```
ds-foundation (primitives)  →  THIS skill (design direction, ×3–4 alternatives)  →  rollout skill (semantics + components + all screens)
```

Each run yields **one named alternative** (e.g. "Direction A — Editorial & Calm"). The user runs the skill a few times to get distinct options, picks one, and a separate rollout skill turns the winner into semantic tokens, a component library, and production screens.

## Two run modes — detect first

Before anything else, determine which mode this run is:

- **Mode A — New direction (default).** Create a fresh, distinct directional concept on its **own new page** (`Design_Direction_<name>`). This is the standard behavior described throughout.
- **Mode B — Add screens to an existing direction.** The user wants **more screens** added to a direction that already exists. In this mode you do **not** invent a new concept, run divergence, or create a new page — you design the requested screens to **conform to the existing direction** and place them on **that direction's existing page**.

**Choose Mode B when** the user asks to "add screens / add more screens / extend a direction" or otherwise references an existing direction by name. For Mode B the user **must provide both**: (1) the **design direction name** of an existing `Design_Direction_<name>` page in the file, and (2) the **features/requirements of the new screen(s)** to design. If either is missing, ask for it (interactive input tool) and **stop**. Everything below is written for Mode A unless a step is explicitly marked **[Mode B]**.

## What this is — and is NOT

| This skill DOES | This skill does NOT |
|---|---|
| Design complete screens for the critical flows | Build every screen in the app |
| Draw flat, high-fidelity frames bound to primitive variables | Create variables, collections, or semantic tokens |
| Demonstrate motion / state-changes as a Figma prototype | Build or publish a reusable component library |
| Establish nav, layout, hierarchy, type, color, depth, states | Author a component system (only minimal, page-local motion components) |
| Produce a named, coherent, *distinct* alternative | Reskin a previous direction |
| Write a design-direction spec for downstream AI | Write production code or a full design system |
| Recommend a semantic mapping (as a handoff) | Create that semantic mapping in Figma |

## Hard guardrails (read first)

1. **Never deviate from the existing primitive variables.** Every fill, text color, spacing, padding, gap, radius, border, and shadow must be **bound to a primitive variable** already in the file. **Zero hardcoded values.** Brand consistency is critical. If a value you need doesn't exist in the primitives, **stop and flag it** — never invent it.
2. **Create screens + a motion prototype — nothing else.** Do NOT create variables, collections, semantic tokens, modes, or styles. Screens are **flat frames**. The one exception: you MAY create **minimal, page-local Figma components** when they're needed to demonstrate motion / interaction states (e.g. a button or nav-item variant set animated with Smart Animate). Those components must stay on this run's `Design_Direction_<direction name>` page, must **never be published** to a library, and must be kept to the few interactive elements the motion pass needs. The full, documented component library is a later rollout skill's job — never build a component system here.
3. **If wireframes/mockups are provided, do not deviate from them.** Match their structure and content; elevate the fidelity, don't redesign the flow. Resolve gaps with the user, don't paper over them.
4. **No dark mode or extra themes** unless the user explicitly asks. Design one theme (light) only.
5. **Alternatives must genuinely diverge.** This skill runs 3–4 times. Before building, detect prior directions (the existing `Design_Direction_*` pages in the file), summarize their concepts, and choose a **deliberately different** personality/nav/layout/type/shape — same primitives, same brand. Never produce a recolor of an existing direction.
6. **Confirm before building.** Get explicit go-ahead on (a) the platform and screen selection and (b) the Design Decisions Brief, before the first frame is drawn — propose, then wait. **Exception:** if the user already named the screens in their prompt/inputs, that list is **final** — don't run the selection heuristic and don't ask to confirm it; state it back and proceed (the Brief approval still applies).
7. **`use_figma` is the only write path.** Load the `figma-use` and `figma-generate-design` skills BEFORE any `use_figma` call. On any `use_figma` error, STOP and read it — scripts are atomic (nothing was written); fix, then retry.
8. **A new-direction run gets its own new page.** In **Mode A**, create a **new page named `Design_Direction_<direction name>`** (the `<direction name>` is exactly the one from the Design Decisions Brief and the spec title) and build everything on **that page only**. Never modify other pages, existing variables, styles, or components. Prior alternatives live on their own `Design_Direction_*` pages — read them for divergence, never edit them.
9. **[Mode B] Add screens onto the direction's existing page — never a new one.** When adding screens to an existing direction, find the existing `Design_Direction_<name>` page (name given by the user) and build the new screens **on that same page**, appended after the existing screens in flow order and numbered to continue the sequence. Do **not** create a new page, do **not** rename the page, do **not** touch the existing screens or the cover frame beyond appending. If no page matches the named direction, stop and ask the user to confirm the exact name (list the `Design_Direction_*` pages you found) rather than creating one. The new screens must be **indistinguishable in style** from the direction's existing screens (same nav, grid, density, type, color roles, radius, elevation, motion) — this is a conform pass, not a redesign.

## Required inputs — validate first, ask if missing, do not proceed without

1. **Figma file link** — the target file where the direction is created **and** where the primitive foundation already lives. Variables are file-scoped, so this identifies both.
2. **Design System Foundation present** — the file must already contain the primitive variables (`BrandPrimitives` + `App-Primitives`). **Verify this in Phase 1 by inventorying the actual variables** — do not assume. If primitives are absent or too sparse to design with, stop and tell the user to run `figma-ds-foundation` first.
3. **Requirement context** — a BRD/PRD, detailed user requirements, or any text describing the functional requirements. Needed to choose screens and design real content. **May be provided per screen** — each named screen can carry its own functional requirements (and its own mockup, below). Use a screen's own requirements first and fall back to the global context where a screen has none.

**[Mode B] additionally required** — do not proceed without both:
4. **Existing design direction name** — the exact `<name>` of the `Design_Direction_<name>` page to extend. Verify a page with that name exists in Phase 0; if not, list the `Design_Direction_*` pages found and ask the user to pick.
5. **New screen features / requirements** — for **each** new screen the user wants added: its purpose, primary action, and content-level requirements. These drive the new content; the existing direction (its spec + page) drives the styling.

If any required input is missing, ask for it (use the interactive input tool for a clean prompt) and **stop** until provided.

### Optional inputs (use if given)
1. **Wireframes / mockups / sample designs** — Figma link or attachment, **global or per screen**. If a mockup is provided for a screen, guardrail #3 applies to that screen (match it, don't redesign it).
2. **Design inputs** — reference sites, design systems, theme, mood, personality, or the feel of the design.
3. **Previously created design direction spec + screens** — used to diverge (guardrail #5).

## Workflow

Run in order. Details live in the reference files.

### Phase 0 — Mode, inputs & target file
- **Detect the mode** (see *Two run modes*): is the user creating a **new direction (Mode A)** or **adding screens to an existing direction (Mode B)**? When ambiguous, ask.
- Confirm the required inputs are present — the 3 base inputs, **plus for Mode B** the existing direction name and the new-screen features; otherwise ask and stop.
- Resolve the Figma link to the target **file** and state it back for confirmation.
- Scan the file for existing **`Design_Direction_*` pages**.
  - **[Mode A]** Note their concepts — this feeds Phase 4 divergence. Don't create this run's page yet; it's named after the concept, which is set in Phase 5.
  - **[Mode B]** Locate the page matching the named direction. If none matches, list the pages found and ask the user to confirm the exact name — do **not** create a page. Once matched, this is the working page for the whole run.

### Phase 1 — Read the real foundation (never skip)
- Inventory the **actual** primitive variables in the file: brand colors, gray + semantic ramps, type scale (families/weights/sizes/line-heights), spacing, radius, border, elevation, opacity, motion, breakpoints. Reuse the discovery approach from `figma-ds-foundation` (read local variable collections/variables).
- Report what's available. **Flag gaps that constrain design** (e.g. no elevation primitives → no soft shadows; no defined type scale → limited hierarchy).
- If the foundation is missing/insufficient, **stop** and route the user to the foundation skill.

### Phase 1.5 [Mode B only] — Absorb the existing direction (never skip)
- Read the direction's **spec** (`../ax-sdd-artifacts/ax-design-specs/<app>-direction-<name>.md`) if it exists, and **screenshot the existing screens** on its page. Extract the binding concept: navigation model, grid + container widths, density, type pairing & scale usage, color roles (which primitive carries which role), elevation strategy, radius personality, icon family, motion character, and the numbering/naming convention of existing frames.
- This captured concept is the **contract** the new screens must match. If the spec is missing, reconstruct the decisions from the screens themselves and state your reading back to the user.
- Note the highest existing frame number so new screens continue the sequence.

### Phase 2 — Screen selection + platform
- **[Mode B]** The screens are exactly the **new screens the user named** — that list is **final**. Skip the heuristic and skip divergence. The platform matches the existing direction's platform (don't re-ask); only ask if the user wants a new screen on a different platform.
- **[Mode A] If the user already specified the screens** (in the prompt or inputs), use that list **as final** — skip the heuristic and skip the confirmation. State it back for transparency and move on.
- **[Mode A] Otherwise**, choose the **critical screens** — adaptive to the product type, defaulting to: **entry/landing, dashboard/home, main navigation + main layout, and one complex journey** (3–5 screens). Drop "landing" for internal tools; add the flow that carries the product's core value. **Confirm this proposed list** with the user before proceeding.
- **[Mode A] Platform:** if the user specified it, use it; otherwise **ask: desktop, mobile, or both** (desktop → dashboard/admin density; mobile → thumb-reachable, single-column). See `references/build-guide.md` for frame sizes.

### Phase 3 — Requirement & gap analysis (per screen)
- For **each** chosen screen, analyze its purpose, primary action, and real content from its **screen-specific requirements** if provided, else the global BRD/PRD.
- **If a mockup was provided for a screen** (global or per-screen), compare it against that screen's requirements. Where they diverge or a requirement is unmet, **surface the gap and resolve it with the user** (guardrail #3 — match the mockup, don't redesign it). Skip the comparison for screens with no mockup.
- Ask any requirement-clarifying questions needed to design real content (not lorem). You may still clarify a *specified* screen's content even though its selection is final.

### Phase 4 — Mood synthesis & divergence
- **[Mode B] Skip divergence entirely.** There is no new concept to synthesize — reuse the existing direction's parameters captured in Phase 1.5 verbatim. Do not ask look-and-feel questions; only ask content questions needed to design the new screens' real content. Go to Phase 6.
- **[Mode A]** Parse the design inputs (references, mood, personality). Translate them into concrete parameters using `references/look-and-feel.md` (density, contrast, radius personality, type character, color emphasis, elevation, motion).
- **[Mode A]** Ask the **expert look-and-feel questions** from that file — only the ones that change the design.
- **[Mode A]** If **prior directions** exist (other `Design_Direction_*` pages in the file), summarize them and deliberately pick a **distinct concept** on the divergence levers (nav pattern, density, type personality, shape, color emphasis, composition).

### Phase 5 — Design Decisions Brief (approve before building)
Produce a short, named brief — this alternative's **concept**:
- **Name + one-line thesis** (its personality).
- The critical decisions: **navigation model**, layout **grid** + container widths, **density**, **type pairing & scale usage**, **color emphasis** (which brand/neutral primitives carry which roles), **elevation strategy**, **radius personality**, **icon family**, **motion character** (durations/easing from the motion tokens, and what animates on buttons, nav, popups, and screen transitions — realized as a prototype in Phase 6.5).
- The chosen platform and the screens to be drawn (with which states).

**Show the brief and get explicit approval before drawing anything.** This is where a great direction is won or lost.

**[Mode B]** There is no new concept to approve. Instead, show a **short conformance brief**: the existing direction's key decisions (from Phase 1.5) that the new screens will inherit, plus the list of new screens with their layout/content plan and which states each will include. Get approval on that plan, then build.

### Phase 6 — Build the screens
Follow `references/build-guide.md`:
- Build flat, high-fidelity **auto-layout** frames, everything **bound to primitive variables**, at the chosen platform size.
- **Default state for every screen; plus key states (empty / loading / error + interaction states) on the one complex journey.**
- Use **real, plausible content** — no lorem.
- Maintain consistency: for static repeats, define each element's treatment once and copy a base frame (keep repeats pixel-identical). The interactive elements you'll animate in Phase 6.5 are instead built once as **page-local components with variants** (guardrail #2).
- Assemble incrementally, section-by-section; screenshot and validate after each.
- **[Mode A] Create a new page named `Design_Direction_<direction name>`** (the same name as the spec) and build all screens + motion on **that page**. Start it with a cover/legend frame stating the thesis and key tokens. Never touch other pages. If a page with that exact name already exists (a re-run of the same direction), confirm with the user whether to replace it or add a version suffix.
- **[Mode B] Build on the existing direction's page** (matched in Phase 0) — **do not create a page**. Reuse the same variable bindings, base frames, and page-local motion components already on that page so the new screens are pixel-consistent with the existing ones; where the new screen reuses a pattern (nav bar, card, header), **copy the existing element** rather than rebuilding it. Append the new frames after the existing ones in flow order, numbered to continue the sequence (`07 · <Screen>`, …). Reference each new screen against neighboring existing screens as you go. Leave the cover frame in place; you may append the new screens to its "screens that follow" list.

### Phase 6.5 — Motion & prototype
Follow `references/build-guide.md` (§Motion & prototyping). Motion here means **Figma Prototype interactions** — created by writing node **`reactions`** via `use_figma` (`setReactionsAsync`), not by exporting code. Load the **`figma-use`** skill for the reactions API; use `figma-use-motion` only if you add optional in-node timeline micro-animations.
- Demonstrate the direction's **motion language** on a **representative sample**, not every element: the core **button** (default → hover → pressed), a **navigation** interaction (hover + active, and screen-to-screen navigation), one **popup/modal** (overlay open/close), and the **complex journey's screen transitions**.
- Build the interactive elements **once as page-local components** (one variant per state) and animate between states with **Smart Animate**; wire screen and overlay changes as **prototype interactions**.
- Pull **durations and easing from the `motion/duration/*` and `motion/easing/*` primitives** — these are reference-only tokens, so read their values and apply them to the interactions for consistency. Keep motion **subtle and purposeful** (100–300 ms for most UI); every animation communicates a state change.
- Never publish a component; keep everything on this run's `Design_Direction_<direction name>` page, labeled 'direction-only'.
- **[Mode B]** Only add motion where a **new screen introduces a new interaction** not already demonstrated on the page — reuse the direction's existing motion components and pull the **same durations/easing** the existing prototype uses. Wire the new screens into the existing prototype flow (e.g. navigation to/from them) so they feel part of the same direction.

### Phase 7 — Self-critique & accessibility pass
- Screenshot each screen (and capture the key interaction states / transitions from the prototype) and run **`references/design-checklist.md`** — all three passes (per-screen, per-direction, and the motion checks) — plus a **WCAG AA** contrast check.
- Fix every violation and re-check. Do not present until it passes. Report the checklist result honestly, including any accepted trade-offs.
- **[Mode B] Conformance check:** screenshot the new screens **beside the existing ones** and confirm they look native to the direction — same nav, grid, density, type, color roles, radius, elevation, spacing rhythm, and motion. Any visible seam is a violation; fix it so the additions are indistinguishable from the original screens.

### Phase 8 — Write / update the design-direction spec
- **[Mode A]** Write `../ax-sdd-artifacts/ax-design-specs/<app>-direction-<name>.md` (create the folder if missing) using `references/spec-template.md`.
- The spec is a **contract for downstream AI**: concept, foundation used, **recommended semantic mapping** (the handoff gift), layout system, type/color/depth/icon rules, nav & IA, informal component inventory, interaction states, motion character (and the prototype interactions built), states/edge-cases, per-screen notes (with each screen's source requirements/mockup), do's & don'ts.
- **[Mode B] Update the existing direction's spec, don't fork it.** Open the same `<app>-direction-<name>.md`, **append per-screen notes for the new screens** (source requirements, states, any new interaction), extend the component inventory / interaction-states / motion sections only if the new screens added something, and leave the concept and foundation sections unchanged. If the spec doesn't exist, create it from the direction you reconstructed in Phase 1.5.
- **Product-change report → PRD (both modes):** if designing the screens surfaced a divergence at the **product WHAT/WHY** level — a journey step with no home, an FR whose behaviour must change, a mis-stated outcome, a missing/incorrect dependency — this is a *product* issue, not an experience one. Write `../ax-sdd-artifacts/prd/product-change-reports/<YYYYMMDD-HHMMSS>-<direction-name>.md` following the contract in the `ax-skills-apply-to-prd` skill: one entry per divergence (WHAT/WHY only, no visual/experience detail), the entity it grounds in, what should change, why, and this run as provenance. **Never edit the PRD itself** — its single writer is `ax-skills-apply-to-prd`, whose Sync mode consumes this report and gates it (refinements apply after PO confirmation; genuinely new decisions or PRD contradictions are refused and routed to product discovery). Keep experience-WHAT out of this report (that is the design-change report above) and pixels out entirely (those are the spec). If nothing diverged at the product level, write no report and say so.
- Final report: screens created (with Figma links), checklist/WCAG result, the spec path, the product-change report path (or "no PRD divergence"), and **[Mode A]** a one-line summary of how this alternative differs from the others, or **[Mode B]** which direction the screens were added to and that they conform to it.

## The craft — rules that make it better than generic output

- **Restraint.** Mostly neutral surfaces + one brand primary. Accents are rare and purposeful. A confident direction uses *few* colors well.
- **Space is a feature.** Generous whitespace, a breathing grid, and consistent density read as premium. Crowding reads as cheap.
- **Type carries the hierarchy.** Let the type scale — not boxes and lines — do most of the hierarchy work.
- **Every decision ladders to the thesis.** If a choice doesn't express the concept, cut it.
- **Real content, real truth.** Realistic labels, names, numbers, dates. Real content exposes layout problems that lorem hides.
- **Bind, don't hardcode.** The direction is only credible if it's actually achievable from the foundation — binding proves it.
- **Consistency is non-negotiable.** The same role looks identical on every screen. A new screen added later should look native.

## Reference files
- `references/design-checklist.md` — the per-screen + per-direction quality checklist and the review protocol for Phase 7.
- `references/look-and-feel.md` — the expert design-interview questions and the mood → concrete-parameter mapping; how to derive divergent concepts for alternatives.
- `references/build-guide.md` — how to build beautiful, flat, token-bound frames via `use_figma`: per-run page setup, variable binding patterns, elevation from decomposed shadow primitives, fonts, icons, incremental assembly, the divergence levers, and the **motion & prototyping** patterns (interactions, Smart Animate, overlays, transitions, page-local motion components).
- `references/spec-template.md` — the design-direction spec contract to fill in for downstream rollout AI.
