---
name: figma-vd-rollout
description: Roll out VISUAL DESIGNS in Figma — complete, production-grade screen designs for a set of functional requirements, built from an existing design system (semantic + component tokens, component library) and an approved design direction. Each screen is designed as a default frame plus one frame per applicable experience state (empty · loading · filled · invalid input · validation error · success · failure · permission-denied · screen-specific), with layout, navigation, information architecture, interaction states and motion decided and drawn, and the motion realized as a Figma prototype. Reuses the design system to the maximum — existing components first, then new variants of them, then genuinely new components — and creates any missing semantic/component tokens as aliases, reporting everything and updating the ds-core (and, if extended, design-direction) specs. Use when the user asks to "roll out the visual design", "design these screens in Figma", "create the UI for this requirement / ticket / PRD", "build the screens from the design system", "visual design rollout", or "design the screen and its states". Requires a Figma file that already contains the design-system tokens as Figma variables, the design-system component library, and a design direction (plus its spec), and requirement context (PRD/BRD/requirements text/ticket ID). Fourth step of the pipeline — after figma-ds-foundation, figma-design-direction and figma-ds-core.

---

# Visual Design Rollout → production screens in Figma

## In the Aatmix platform

When the Aatmix platform's UX Agent runs this skill, read every instruction below through this mapping. Everything else in the skill applies unchanged.

- **"The user"** is the UX Engineer, reached through the Exchange. Ask with `exchange_ask`: numbered replies, your recommendation marked, one round at a time — every open question of that round together, then end the turn. The run resumes with the answers. "Ask", "confirm", "get a go-ahead", "wait for approval" and "the interactive input tool" all mean such a round.
- **The Figma file** is the project's connected Figma file named in the run details. Only when none is connected, ask the UX Engineer for its URL.
- **The brand guidelines** are artifact `BRG`, already supplied by the project (run details name its version; read it with `artifacts_read`). Never ask for them.
- **The PRD** is the approved PRD artifact the run details name (`artifacts_read`), never a file.
- **Files the skill writes or reads under `ax-sdd-artifacts/`** live in the artifact store instead (`artifacts_list`, `artifacts_read`, `artifacts_put_draft`, then `artifacts_submit` for review as the task text says), under these kinds: direction spec → `visual_direction`; foundation report → `brand_primitives`; ds-core spec → `semantic_tokens` (its token half: the Semantics and ComponentTokens mapping and the styles) and `core_components` (its component half: the component inventory); VD contract → `vd_contract`; rollout record → `screen_rollout`; product-change report → the PRD change requests the task text names (never edit the PRD). A change report the skill writes beside a spec goes into the next version of that spec's artifact, as its own section.
- **Previews** (PNG or HTML of what you drew) go to `design_preview_put` against the project asset they belong to (`brand_primitives`, `visual_direction`, `semantic_tokens`, `core_components`, `vd_contract`), and to `screen_preview_put` against a screen's record.
- **Other skills**: `figma-ds-foundation`, `figma-design-direction`, `figma-ds-core`, `figma-vd-contract` and `figma-vd-rollout` are this plugin's skills; `figma-use`, `figma-generate-design` and `figma-generate-library` come from the Figma plugin.
- **In this skill**: the requirement context is the work item the task text names, with its PRD delta; the direction spec and the ds-core spec are the approved `visual_direction`, `semantic_tokens` and `core_components` artifacts. The rollout spec of Phase 9 is the screen's `screen_rollout` record, each drawn state's preview stored with `screen_preview_put`. Design-system additions go into the next `semantic_tokens` or `core_components` version, whichever half they belong to. The platform runs the `figma-vd-contract` Update step itself after the rollout: report the rolled-out screens, nothing more.

Turn functional requirements into **complete visual designs**: full screens with their layouts, navigation, information architecture, content, every applicable state, and motion — assembled from the design system that already exists in the file, in the visual language of the approved design direction.

```
ds-foundation (primitives) → design-direction (chosen look) → ds-core (semantics + components) → THIS skill (screens, ×N runs)
```

A visual design is **not** a pretty picture of the happy path. A screen is done when a developer could build it and a user could never reach a state that was never drawn.

## What this is — and is NOT

| This skill DOES | This skill does NOT |
|---|---|
| Design complete screens for the given requirements | Re-decide the visual language (the direction is binding) |
| Draw every applicable state of every screen as its own frame | Draw only the default/happy state |
| Compose screens from **instances** of the DS component library | Redraw or detach design-system components |
| Add variants to existing components when a need isn't covered | Invent a parallel component system on the screen page |
| Create missing semantic/component tokens as **aliases**, then report and update the specs | Hardcode a value, or bind a screen to a raw primitive |
| Wire motion + transitions as a real Figma prototype | Ship motion as prose in a spec |
| Decide navigation, IA and interaction — and defend the decisions | Ask the user to make routine design decisions |
| Interrogate the requirements and surface conflicts | Paper over a contradiction to keep building |

## Hard guardrails (read first)

1. **Semantic and component tokens only — never primitives, never raw values.** Every fill, text color, stroke, spacing, padding, gap, radius, border width, shadow and typographic property on every frame is bound to a variable in `Semantics` or `ComponentTokens` (or applied via a `DS/*` text/effect style). Binding a **primitive** (`BrandPrimitives` / `App-Primitives`) directly is a defect, exactly as a hardcoded hex is. Brand consistency is the point of the whole pipeline.
2. **Reuse before variant, variant before new.** In order: (a) use the existing component; (b) if it can't express the need, add a **variant** to the existing component set; (c) only if a variant is genuinely wrong, create a new component. Every DS element on a screen is a live **instance** — never a copy, never detached, overridden only through component properties. A screen made of rectangles and text nodes that *look* like the system is a failed rollout.
3. **New components and tokens live in the design system, not on the screen page.** Anything reusable you create is built on the `DS_Core_*` pages to ds-core conventions (token-bound, variant matrix, documentation frame, `ax_dsc` tag), then **instanced** onto the screen. Screen-page nodes are compositions and one-off content only.
4. **The design direction is binding.** Its navigation model, grid, density, type usage, color roles, elevation strategy, radius personality, icon family and motion character are settled decisions. Your screens must look **native** beside the direction's screens. You may extend the direction (a pattern it never faced); you may not reinterpret it.
5. **If wireframes/mockups are provided, do not deviate from their structure.** Precedence when they conflict: **design system tokens/components > design direction > wireframe**. A wireframe governs *structure, content and flow*; it never governs style. Elevate the fidelity, don't redesign the flow — and surface any gap between wireframe and requirements to the user instead of silently choosing.
6. **No dark mode or additional themes** unless the user explicitly asks. One theme, exactly as the direction and ds-core define it.
7. **Every applicable state is drawn.** For each screen, walk the canonical state set (§ `references/states-and-motion.md`); every state is either drawn as its own frame or recorded `N/A — affirmed` with a reason. Silently omitting a state is the single most common way this run fails.
8. **Motion is built, not described.** The required transitions and interactions are wired as Figma **prototype reactions** so they can be played in Figma — using the direction's motion character and the `motion/*` token values.
9. **`use_figma` is the only write path.** Load the `figma-use` and `figma-generate-design` skills BEFORE any `use_figma` call (add `figma-generate-library` when creating components). Scripts are atomic — on any error, STOP, read it, fix, retry. Build in small chunks and screenshot after each.
10. **Confirm before building.** Present the Screen Plan (§Phase 4) and get explicit approval before the first frame is drawn. Then build. Do not re-ask routine design questions you are qualified to answer — decide, and state the decision in the plan.
11. **Touch only your own surfaces**: the rollout page(s) you create or were given, plus — through the gap protocol only — the `DS_Core_*` pages and the two DS variable collections. Never modify the direction pages, primitive values, or another run's screens.
12. **Report honestly.** Checklist failures, waived states, invented components, unresolved conflicts — all named in the final report. A run that quietly meets 80% of its own bar is worse than one that says which 20% it missed.

## Required inputs — validate first, ask if missing, do not proceed without

1. **Figma file or page link** — where the rollout happens.
   - Link resolves to a **page** → build the screens on **that page**.
   - Link resolves to a **file** (or no page given) → **create a new page** for this rollout (`Rollout_<slug>`, e.g. `Rollout_Work Items`; suffix `_v2` if the name is taken).
2. **Design system tokens** — the file must contain the semantic and component token collections as Figma **variables** (`Semantics`, `ComponentTokens`, or their equivalents). **Verify by inventorying the actual variables** — never assume. If only primitives exist, **stop** and route to `figma-ds-core`.
3. **Design system components** — the file must contain the component library (`DS_Core_Components` / `DS_Core_Patterns` / `DS_Core_Templates`, or their equivalents), including navigation and layout/template samples. **Verify by enumerating the actual component sets.** If absent, **stop** and route to `figma-ds-core`.
4. **Design direction** — the `Design_Direction_<name>` page in the file, for visual reference. Verify it exists; if several exist, ask which one this rollout follows.
5. **Design direction spec** — the markdown recording the direction's decisions (default `ax-sdd-artifacts/ax-design-specs/<app>-direction-<name>.md`). Read it fully.
6. **Requirement context** — a BRD/PRD, detailed user requirements, or any text describing the functional requirements to design. May be provided globally or **per screen**.

If any required input is missing, ask for it (use the interactive question tool for a clean prompt) and **stop** until it is provided. Do not substitute an assumption for a missing input.

### Optional inputs (use if given)

1. **Wireframes / mockups / sample designs** — Figma link or attachment, global or per screen (guardrail 5 applies).
2. **Design inputs** — notes on how the screen should look, reference sites, design systems, theme, mood, personality, feel.
3. **Ticket ID** (Jira / Azure DevOps / other) — fetch the ticket via the appropriate MCP server and treat its description, acceptance criteria and attachments as requirement context. If the MCP server isn't connected, say so and ask for the requirements directly.
4. **ds-core spec** — the component/token contract (default `ax-sdd-artifacts/ax-design-specs/<app>-ds-core-<direction>.md`). Read it when present: it is faster and more precise than re-deriving the component API from Figma. Figma remains ground truth where they disagree.
5. **Any other design artifact** the user supplies (a VD contract, a prior rollout's spec, an existing screen to extend) — extra requirement and consistency context.

## Workflow

Run in order. The details live in the reference files.

### Phase 0 — Inputs, target & discovery

- Validate the 6 required inputs; ask and **stop** on any gap.
- Resolve the link: **page given → that page is the target; file given → create the rollout page** (name it from the requirement scope; confirm the name with the user). State the target back before writing anything.
- If a **ticket ID** was given, fetch it now via the MCP server and fold it into the requirement context.
- Inventory the file: variable collections (all variables, with which are primitive vs semantic vs component tokens), text/effect styles, component pages and their sets, `Design_Direction_*` pages, and any existing rollout pages (screens already designed — your consistency reference and your idempotency check).

### Phase 1 — Absorb the system (never skip)

- **Tokens:** list the semantic + component tokens actually available, grouped by role (surface, text, border, action, feedback, spacing, radius, elevation, motion, typography). This list is your palette; nothing outside it may appear on a screen.
- **Components:** enumerate every component set with its variant properties and values — this is your parts bin. Note the navigation components and the layout/app-shell templates specifically; every screen starts from a template, not from an empty frame.
- **Direction:** read the spec and screenshot the direction's screens. Extract the binding rules: nav model, grid + container widths, density, type usage, color roles, elevation, radius, icon family, motion character, and the frame/naming conventions.
- Report coverage and **flag gaps that constrain design** *before* designing (e.g. no Table component for a data-heavy requirement, no Toast for async feedback). Gaps go through the gap protocol in Phase 3, not through improvisation in Phase 5.

### Phase 2 — Critically analyze the requirements

Read the requirement context (PRD/BRD, text, ticket, wireframes) as a senior product designer, not a transcriber:

- Derive, per screen: **purpose**, the **one primary action**, the user's entry and exit, the real data it shows, permissions/personas that see it differently, and the failure modes it must survive.
- **Hunt for conflicts** and put them to the user: requirements that contradict each other; a requirement contradicting the design direction or the design system; a wireframe diverging from the written requirement; an acceptance criterion with no possible surface; a flow step with no screen; data referenced but never sourced. Do **not** design over a conflict — surface it, propose a resolution, and get an answer.
- Note the questions only the user can answer (business rules, data volumes, terminology) and ask them in one batch, with your recommended answer for each. Decide the design questions yourself.
- **Screen set:** if the user named the screens, that list is final. If not, derive the minimum set of screens the requirements need and state it back.

### Phase 3 — Design-system gap analysis & gap protocol

For every screen, walk its planned anatomy against the parts bin and classify each need:

| Need | Resolution |
|---|---|
| Covered by an existing component | Use the instance |
| Covered by an existing component **with a new variant** | Add the variant to the existing set (preferred over a new component) |
| Not expressible by any existing component | New component in the library |
| Needs a token role that exists | Use it |
| Needs a token role that doesn't exist | New semantic/component token as an **alias** |
| Needs a value with no primitive behind it | **Stop** — route to `figma-ds-foundation` |

Follow **`references/gap-protocol.md`**: present the full gap list with your proposed resolution for each, get the user's go-ahead, then build the additions **in the design system** (tagged, documented, token-bound) *before* the screens that consume them. Every addition is recorded for the Phase 9 spec updates.

### Phase 4 — Screen Plan (approve before building)

One compact plan covering, per screen:

- **Name, purpose, primary action**, the template/app-shell it starts from, and where it sits in the navigation and IA (what nav item reaches it, what it reaches).
- **Layout**: regions, grid/columns, density, and the content hierarchy top to bottom — what the user must see first.
- **Content**: the real data it will show (no lorem), and the extremes it must survive.
- **States**: the full canonical walk — each state marked *drawn* or `N/A — affirmed` with a reason (`references/states-and-motion.md`).
- **Components**: which DS components each region uses, plus any Phase 3 additions.
- **Motion**: which transitions and interactions will be wired.
- Any design decision you made on the user's behalf, stated in one line each so they can object.

**Show the plan, get explicit approval, then build.** This is the cheapest place to be wrong.

### Phase 5 — Build the screens

Follow **`references/figma-build-guide.md`**:

- One **section per screen** on the rollout page, named for the screen; inside it the **default frame first**, then one frame per state, laid out left to right in a consistent order. Frame naming: `<NN> · <Screen>` and `<NN> · <Screen> — <state>`.
- Start from the DS **template/app-shell instance**, then compose with component **instances**; auto-layout everywhere; every property bound to a semantic or component token.
- **Real, plausible content** drawn from the requirements' domain — real labels, names, numbers, dates.
- Build **one screen at a time**: assemble → screenshot → validate bindings and instance integrity → fix → next. Never batch-build unverified frames.
- Repeats (rows, cards, list items) come from one verified instance, copied — never re-drawn per row.

### Phase 6 — Build the states

Every state frame from the plan is drawn as a **complete screen**, not a fragment: the full shell with the state applied to the affected region. Follow `references/states-and-motion.md` for what each canonical state must show (empty states carry a cause + a way out; error states carry the error, the reason and the recovery; loading uses one consistent treatment product-wide; permission-denied shows what is denied and who to ask). States reuse the same component instances with their state variants — a `disabled`/`error` variant is used, never a restyled default.

### Phase 7 — Motion & prototype

Follow `references/states-and-motion.md` (§Motion). Motion here means Figma **prototype reactions** written via `use_figma` (`setReactionsAsync`) — not code, not prose.

- Wire: **screen-to-screen navigation** (from the nav component), **overlay open/close** (modals, drawers, menus, toasts) as overlay interactions, **in-screen state transitions** from the state frames (default → loading → filled, submit → success/failure), and **interaction feedback** on the primary controls.
- Pull **durations and easing from the `motion/*` token values** and the direction's motion character; keep most UI transitions in **100–300 ms**, easing not linear, animating only the element that changes.
- Reuse the direction's existing motion components/patterns where they exist rather than inventing a second motion language.
- Reduced-motion and interruptibility can't be expressed in a Figma prototype — record them as handoff notes in the spec, don't fake them.

### Phase 8 — Self-critique, audits & accessibility

- Screenshot every frame and run **`references/rollout-checklist.md`** in full — per-screen (A), per-set (B), motion (C) — plus **WCAG AA** contrast on every text/background and UI/background pair **in every state**, including hover, disabled and error.
- Run the two scripted audits from `references/figma-build-guide.md`: **zero-raw-value / no-primitive-binding** and **instance integrity** (no detached instances, no hand-drawn lookalikes).
- Run the **nativeness check**: place a new screen beside a direction screen and a prior rollout screen — any visible seam is a defect.
- Fix everything and re-check. Do not present until it passes. Report the result honestly, including accepted trade-offs.

### Phase 9 — Spec, reports & handoff

- **Rollout spec** — write `ax-sdd-artifacts/ax-design-specs/rollouts/<app>-rollout-<slug>.md` from `references/spec-template.md`: screens with Figma links, per-screen purpose/IA/layout/content rules, the complete state ledger (drawn + affirmed), component and token usage, DS additions made, motion wired, handoff notes (responsive/breakpoint behavior, reduced motion, keyboard order), open questions.
- **Design-system updates (guardrail 3's other half):** if this run added components, variants or tokens, **update the ds-core spec** (`<app>-ds-core-<direction>.md`) — token dictionary, component API tables, coverage counts — and write `ax-sdd-artifacts/ax-design-specs/ds-core-change-reports/<YYYYMMDD-HHMMSS>-<slug>.md` recording each addition, why it was needed and where it is used. If a run also extended a **direction-level** decision (a pattern or motion the direction never faced), append it to the direction spec's relevant section and note it in the report. Never rewrite either spec's settled decisions — append and update counts.
- **Product-change report** — if designing surfaced a divergence at the **product WHAT/WHY** level (a flow step with no home, an FR whose behavior must change, a missing dependency), write `ax-sdd-artifacts/prd/product-change-reports/<YYYYMMDD-HHMMSS>-rollout-<slug>.md` per the `ax-skills-apply-to-prd` contract: WHAT/WHY only, grounded entity, proposed change, provenance. **Never edit the PRD.** If nothing diverged, say so.
- **If the user maintains a VD contract** for this direction, list the rolled-out screens with their Figma links and states settled, and tell them to run `figma-vd-contract` Update mode. This skill never edits the contract.
- **Final report:** screens + state frames created (with links), the target page, checklist/WCAG/audit results, DS additions made, spec paths, unresolved conflicts and open questions.

## The craft — what separates this from generic output

- **Design the states, and the screen designs itself.** Most bad product UI is a beautiful happy path with an ugly reality. The empty state is where a product is won.
- **One primary action per screen.** If two things compete for primacy, the hierarchy is broken — fix the screen, don't add a third color.
- **Type carries hierarchy; boxes and lines are a last resort.** Reach for size, weight and space before borders and fills.
- **Space is a feature.** Generous, consistent whitespace reads as premium; crowding reads as cheap. Density is the direction's decision, not a per-screen mood.
- **Restraint with color.** Neutral surfaces, one primary, rare and purposeful accents. Semantic color carries meaning — never decoration.
- **Real content, real truth.** Realistic names, numbers, dates, and the long ones. Lorem hides exactly the layout problems you are being paid to find.
- **Reuse is a design decision, not a shortcut.** A screen assembled from the system is consistent by construction; one assembled by hand will drift within a week.
- **Every element earns its place.** If you can delete it and lose nothing, it was decoration.
- **A new screen should look like it was always there.** Nativeness beats novelty in a rollout — the exploration already happened, upstream.

## Reference files

- `references/gap-protocol.md` — the reuse ladder, and exactly how to add a variant, a component or a token without corrupting the design system.
- `references/states-and-motion.md` — the canonical state set with what each state must show, and the motion/prototype patterns for Phase 7.
- `references/figma-build-guide.md` — `use_figma` patterns: page/section/frame conventions, template-first assembly, instance + property overrides, token binding, tagging/idempotency, and the two audit scripts.
- `references/rollout-checklist.md` — the Phase 8 quality bar (per-screen, per-set, motion) and the review protocol.
- `references/spec-template.md` — the rollout spec contract, including the state ledger and handoff notes.
