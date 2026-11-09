# Rollout quality checklist

> Part of `figma-vd-rollout`. This is the Phase 8 bar. Run it in three passes: **A) per screen** on every frame (default *and* every state frame), **B) per set** once across everything this run produced, **C) motion** against the prototype. A rollout ships only when every item passes or a trade-off is explicitly accepted and recorded in the spec and the final report.
>
> Design-language questions (is the palette right? is the mood right? is it modern?) are **not** on this list. They were settled by the design direction and the ds-core. Re-litigating them here is out of scope; *conforming* to them is item B10.

## How to run it (Phase 8 protocol)

1. `get_screenshot` every frame — default and every state.
2. Walk **A1–A9** against each screenshot **and** the frame's structure (auto-layout, instances, bound variables). Prefer inspecting bindings over eyeballing: a color that "looks right" but is hardcoded fails A1.
3. Run the two scripted audits (`figma-build-guide.md` §Audits): raw-value/primitive-binding, and instance integrity. These are gates, not opinions.
4. Walk **B10–B14** once across the whole run, and **C15–C17** against the prototype.
5. Run the **WCAG AA contrast check** on every text/background and UI/background pair, in **every state** — hover, focus, disabled and error included.
6. List every violation as *frame · item · fix*. Fix, re-screenshot, re-check. Iterate until clean.
7. Record the result (pass + accepted trade-offs) in the spec and the final report. Never claim a pass you didn't verify.

---

## A · Per-screen checks (every frame, including every state frame)

### A1. System fidelity — gate, non-negotiable
- [ ] Every fill, text color, stroke, spacing, padding, gap, radius, border width and shadow is bound to a **semantic or component token**, or applied via a `DS/*` text/effect style.
- [ ] **No binding to a primitive** (`BrandPrimitives` / `App-Primitives`) and **no raw value** anywhere.
- [ ] Every design-system element is a live **instance** — zero detached instances, zero hand-drawn lookalikes of an existing component.
- [ ] Instance customization is via **component properties/variants** only — no overridden fills, radii or spacing on an instance.
- [ ] Anything reusable created this run lives in the **component library**, not on the screen page.

### A2. Layout & structure
- [ ] The screen starts from the design system's **template / app-shell**; nav and shell regions are instances, identical to every other screen.
- [ ] Every frame is **auto-layout**; nothing is free-floating or absolutely positioned except deliberate overlays.
- [ ] Content respects the direction's **grid, column count and container max-width**; shared edges align; tabular and numeric data align consistently.
- [ ] All spacing comes from **spacing tokens** — no eyeballed gaps. (Token binding is what enforces the spacing rhythm; do not hand-check pixel arithmetic.)
- [ ] Density matches the direction's stated density, and is consistent within and across screens.
- [ ] The screen conforms to the direction's declared **platform and breakpoints** — do not invent responsive behavior the direction never declared; record intended behavior at the declared breakpoints in the spec.

### A3. Visual hierarchy
- [ ] One unmistakable focal point — what the screen is *for* is graspable in under 3 seconds.
- [ ] Exactly **one primary action**, and it is the most prominent control; secondary and tertiary actions are clearly subordinate.
- [ ] Reading order flows by deliberate size / weight / color / space — not by accident of source order.
- [ ] Related elements are visually grouped (proximity, shared container, shared background) and unrelated ones are separated; no group relies on a border where space would do.

### A4. Cognitive load & choice architecture
- [ ] Nothing on screen asks the user to think more than the task requires — no unexplained jargon, no raw IDs where a name exists, no computation left to the user.
- [ ] No more than ~7 ungrouped sibling choices in any one control cluster or menu; beyond that, group, prioritize or progressively disclose.
- [ ] Advanced/rare options are disclosed progressively, not competing with the primary path.
- [ ] Every field, column and control on screen is required by a requirement — if you can delete it and lose nothing, delete it.

### A5. Typography
- [ ] Only the design system's **text styles / type tokens** are used — no off-scale sizes, weights or line-heights.
- [ ] A clear, few-step hierarchy with obvious jumps between levels; few distinct text styles per screen.
- [ ] Body measure is ~50–75 characters; long-form text is not full-bleed across a wide container.
- [ ] Text truncation/wrap behavior is decided and shown wherever content can overflow.

### A6. Color, contrast & meaning
- [ ] The same role uses the **same token everywhere** (every primary button, every error, every muted label).
- [ ] Surfaces form a legible value hierarchy — page background < surface < raised/overlay — using the surface tokens, not ad-hoc greys.
- [ ] Body text ≥ **4.5:1**; large text, icons and meaningful UI boundaries ≥ **3:1** — verified in **every state**, including hover, disabled and error.
- [ ] Status and meaning are **never color-only** — always paired with an icon, label or shape.
- [ ] Semantic color is reserved for meaning (success/warning/danger/info); no decorative use of a feedback color.

### A7. Interactive elements & accessibility
- [ ] Every interactive element shows its state via the component's **state variants**: default, hover, focus-visible, active/pressed, disabled; inputs additionally show error.
- [ ] **Focus is visibly indicated** with a real focus treatment (ring/outline), not just a color shift.
- [ ] Hit targets: ≥ **44px** touch, ≥ **32px** pointer, with enough separation to avoid mis-taps.
- [ ] **Destructive actions** are visually distinct, never adjacent to the primary action, and confirmed before they commit.
- [ ] Disabled controls are explained — the user can tell *why* and what would enable them.
- [ ] The intended **keyboard/focus order** is coherent with the visual order and recorded in the spec.
- [ ] **No orphan interaction**: every control either leads somewhere in the prototype or is explicitly marked non-functional-in-this-run.

### A8. Content realism & resilience
- [ ] Real, plausible domain content — **no lorem**, no "Title goes here". Realistic names, numbers, dates, statuses.
- [ ] Numbers, dates, currency and units are formatted consistently and plausibly for the product's locale.
- [ ] Layout survives extremes: longest realistic string, shortest value, zero / one / many rows — with stated truncation or wrap behavior.
- [ ] Copy is written, not placeholdered: labels, empty-state text, error messages and button verbs say something true and specific.

### A9. State completeness (per screen)
- [ ] Every canonical state is **drawn as its own full-screen frame** or recorded `N/A — affirmed` with a reason — none silently dropped.
- [ ] Each state frame is a complete screen (full shell), not a cropped fragment.
- [ ] Empty states name the cause and offer the way out; error states name the error, the reason and the recovery; permission-denied says what is denied and who grants it.
- [ ] The loading treatment (skeleton vs spinner vs progressive) follows **one rule across the whole product**.
- [ ] State frames reuse the component's own state variants — no restyled default standing in for a real state.

---

## B · Per-set checks (once, across everything this run produced)

### B10. Direction conformance
- [ ] Placed beside a direction screen, a new screen shows **no seam**: same nav, grid, density, type usage, color roles, radius, elevation and icon family.
- [ ] All icons come from the **one icon family** the direction uses, at consistent size and stroke, and are labeled wherever meaning isn't obvious.
- [ ] Elevation is used to signal hierarchy from the elevation scale (raised / overlay / modal) — not decoration; flat where flat reads cleaner.
- [ ] Any surface effect the direction uses (blur/glass/gradient) appears only where the direction prescribes it, and text over it still meets contrast.

### B11. Cross-screen consistency
- [ ] The same pattern is solved the same way on every screen (page headers, toolbars, tables, forms, empty states, confirmations).
- [ ] Terminology is identical across screens for the same concept — one name per thing.
- [ ] Where this run extends a prior rollout, the new screens are indistinguishable in treatment from the existing ones.

### B12. Navigation & information architecture
- [ ] The navigation model is the direction's, present and consistent on every screen; the user can always tell **where they are**.
- [ ] Every screen this run created is **reachable** — from a nav item, a parent screen's action, or a documented deep link — and every outbound path leads to something that exists or is explicitly out of scope.
- [ ] Information architecture matches the user's mental model of the domain, not the database schema.
- [ ] Overlays vs pages is a deliberate choice: transient/focused tasks are overlays; addressable, shareable, deep contexts are pages.

### B13. Design-system integrity
- [ ] Every DS addition this run made (variant, component, token) followed the gap protocol: built in the library, token-bound, documented, tagged.
- [ ] No addition duplicates something the system already had under another name.
- [ ] Additions are reflected in the ds-core spec and the change report.

### B14. Requirement coverage
- [ ] Every functional requirement in scope has a surface, and each is traceable to a screen (and state) in the spec.
- [ ] Every acceptance criterion in scope is satisfiable on the drawn screens.
- [ ] Every conflict found in Phase 2 is resolved or recorded as an open question — none silently designed over.

---

## C · Motion checks (the prototype)

### C15. Motion is present and purposeful
- [ ] Every animation communicates a **state change** — feedback, transition or guidance. None is decoration.
- [ ] The required set is wired: screen-to-screen navigation, overlay open/close, in-screen state transitions (default → loading → filled; submit → success/failure), and feedback on the primary controls.
- [ ] Motion is **subtle**: fade / small slide / small scale — not bounce, spin or attention-seeking.
- [ ] Only the changing element animates; the rest of the screen holds still.

### C16. Motion is on-system
- [ ] Durations and easing come from the **`motion/*` token values** and the direction's motion character; most UI transitions land in **100–300 ms**.
- [ ] Easing is used everywhere — never linear.
- [ ] The same element type animates the same way on every screen.
- [ ] Spatial continuity holds: elements enter from and exit to logical origins; Smart Animate pairs have matching layer names.

### C17. Prototype integrity
- [ ] Overlays are wired as **overlay interactions**, not page swaps.
- [ ] The prototype actually plays end to end for the flows in scope; no dead hotspot, no trigger pointing at a deleted frame.
- [ ] Reduced-motion and interruptibility (which Figma can't express) are recorded as **handoff notes in the spec** — not faked in the prototype.
