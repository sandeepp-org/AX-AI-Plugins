# Prompt template — `figma-vd-rollout`

Copy the block below, fill in the `<…>` placeholders, and paste it as your message. The first line triggers the skill. **One run = one batch of screens** — each drawn as a default frame plus a frame per applicable state, on one rollout page, with the motion wired as a Figma prototype, plus one rollout spec.

The run has two approval gates (the design-system gap list, then the Screen Plan) before anything is drawn. Re-running on the same page **extends** it — existing screens are detected and skipped, not overwritten.

---

## Template (copy this)

```text
Roll out the visual design in Figma using the figma-vd-rollout skill.

## Target  (REQUIRED)
<paste the Figma FILE link — the skill creates a new Rollout_<slug> page>
   …or…
<paste a Figma PAGE link — the skill builds on that page>

The file already contains: the design-system tokens as variables, the component
library, and the design direction page.

## Design direction  (REQUIRED)
Direction name: <exact <name> of the Design_Direction_<name> page>
Direction spec: <path, e.g. ax-sdd-artifacts/ax-design-specs/<app>-direction-<name>.md>

## Requirement context  (REQUIRED — one or more of these)
PRD / BRD: <path or attachment>
Requirements text: <paste the functional requirements, or per screen below>
Ticket: <Jira/ADO ticket ID — the skill fetches it via MCP>

## Screens to design  (optional — if omitted the skill derives them and states them back)
1. <Screen name> — <purpose / requirements specific to this screen>
2. <Screen name> — <…>

## Wireframes / mockups  (optional)
<Figma link or attachment, global or per screen. If given, the skill matches their
 structure and content and never redesigns the flow — style still comes from the
 design system and direction.>

## Design inputs  (optional)
<notes on how the screens should look/feel, reference sites, emphasis, priorities —
 applied WITHIN the direction, never overriding it>

## ds-core spec  (optional but helpful)
<path, e.g. ax-sdd-artifacts/ax-design-specs/<app>-ds-core-<direction>.md>

Notes: single light theme only (no dark mode). Bind only to semantic/component
tokens — never primitives, never raw values. Reuse the component library to the
maximum: existing component → new variant → new component, in that order, and put
anything reusable in the library, not on the screen page. Draw EVERY applicable
state as its own frame; affirm the ones that don't apply. Wire the motion as a real
Figma prototype. Flag any requirement conflict instead of designing over it.
```

---

## Filled example

```text
Roll out the visual design in Figma using the figma-vd-rollout skill.

## Target
https://www.figma.com/design/XXXX/Aatmix-AI      (create the rollout page)

## Design direction
Direction name: Sovereign Stage
Direction spec: ax-sdd-artifacts/ax-design-specs/aatmix-ai-direction-sovereign-stage.md

## Requirement context
PRD: ax-sdd-artifacts/prd/product-prd.md


## Azure DevOps project
https://dev.azure.com/aatmix/ax-ai-platform
ax-ai-platform
Ticket ID: 272 from the ADO project

## Screens to design
1. Agent Board — the kanban of work items for a project; drag between gate columns,
   open a work item, create a new one.
2. Work Item Details — full detail overlay: artifacts, gate status, comments.

## Design inputs
- The board is the product's centre of gravity — it should feel dense but calm.
- Gate status must be readable at a glance without relying on colour alone.

## ds-core spec
ax-sdd-artifacts/ax-design-specs/aatmix-ai-ds-core-sovereign-stage.md

Notes: single light theme only (no dark mode). Bind only to semantic/component
tokens — never primitives, never raw values. Reuse the component library to the
maximum: existing component → new variant → new component, in that order, and put
anything reusable in the library, not on the screen page. Draw EVERY applicable
state as its own frame; affirm the ones that don't apply. Wire the motion as a real
Figma prototype. Flag any requirement conflict instead of designing over it.
```

---

## How each field maps to the skill

| Field | Drives |
|---|---|
| Target (file vs page) | Phase 0 — build on the given page, or create `Rollout_<slug>` |
| Direction name + spec | Phase 1 — the binding visual language; Phase 8 nativeness check |
| Requirement context / ticket | Phase 2 — purpose, primary action, real content, states, conflict hunt |
| Screens to design | Phase 2 — if given, the list is final; if not, derived and stated back |
| Wireframes / mockups | Structure and content are matched exactly; style still comes from the DS |
| Design inputs | Phase 4 emphasis decisions — applied within the direction |
| ds-core spec | Phase 1 — component API and token dictionary without re-deriving from Figma |

## What one run produces

- **A rollout page** — one section per screen: the default frame plus one frame per applicable state, all composed from design-system component instances and bound to semantic/component tokens.
- **A working prototype** — navigation, overlay open/close, state transitions and control feedback, at the direction's durations and easing.
- **Design-system additions** (if any) — variants/components/tokens built *in the library*, tagged and documented, with the ds-core spec updated and a change report written.
- **A rollout spec** — `ax-sdd-artifacts/ax-design-specs/rollouts/<app>-rollout-<slug>.md`: screen contracts, the full state ledger, motion table, DS additions, handoff notes (responsive, keyboard, reduced motion), quality record, open questions.
- **A product-change report** if the design surfaced a product-level divergence (never an edit to the PRD).

## Notes

- **Prerequisites:** tokens (`figma-ds-foundation` → `figma-ds-core`), the component library (`figma-ds-core`) and a direction (`figma-design-direction`) must already exist in the file. The skill verifies each by inventorying Figma and stops if any is missing.
- **Two approval gates:** the design-system gap list and the Screen Plan. Everything after that is build + audit.
- **Long build:** budget for several messages — one screen at a time, screenshot-verified. If it pauses, say "continue"; tagging makes it resumable without duplicates.
- **Adding screens later:** re-run with the same page link and the new requirements. Existing screens are skipped; new ones continue the numbering and must look native beside them.
- **If you maintain a VD contract**, run `figma-vd-contract` Update mode afterwards with this run's screen links — this skill never edits the contract.
