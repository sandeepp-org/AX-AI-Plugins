# Design Direction Checklist

> Part of `figma-design-direction`. This is the quality bar for Phase 7. Run it in **two passes**: **A) per screen** on every frame, **B) per direction** once across the whole set before presenting. A design direction ships only when every item passes or a trade-off is explicitly accepted and recorded in the spec.

## How to run it (Phase 7 protocol)

1. `get_screenshot` each screen frame (and each state variant).
2. Walk the per-screen checks (A1–A8) against each screenshot **and** the frame's structure (auto-layout, bound variables). Prefer inspecting bound variables over eyeballing color — a value that "looks right" but is hardcoded fails A1.
3. Walk the per-direction checks (B9–B11), and the **motion checks (C12–C14)** against the prototype, across the full set.
4. Run the **WCAG AA contrast check** on every text/background and UI/background pair.
5. List every violation with the screen, the item, and the fix. Fix, re-screenshot, re-check. Iterate until clean.
6. Record the result (pass + any accepted trade-offs) in the spec and the final report. Report failures honestly — never claim a pass you didn't verify.

---

## A · Per-screen checks

### A1. Foundation fidelity — gate, non-negotiable
- [ ] Every fill, text color, spacing, padding, gap, radius, border, and shadow is **bound to an existing primitive variable**. Zero hardcoded values.
- [ ] No new variables, collections, or components were created.
- [ ] Any value the design needed but the foundation lacked was **flagged, not invented**.

### A2. Layout & spatial system
- [ ] The screen and each major section is an **auto-layout** frame; nothing is free-floating.
- [ ] Content sits on the stated column grid; shared edges align; tabular/numeric data aligns consistently.
- [ ] All spacing comes from **spacing tokens** (this is what enforces the 8-pt rhythm) — no eyeballed gaps.
- [ ] Density is consistent within the screen and matches the direction's stated density.

### A3. Visual hierarchy
- [ ] One unmistakable focal point, graspable in under 3 seconds.
- [ ] Exactly **one primary action** per screen, and it is the most prominent control; secondary/tertiary actions are clearly subordinate.
- [ ] Reading order flows by deliberate size / weight / color / spacing — not by accident.

### A4. Typography
- [ ] Only the **brand type scale** is used (sizes, weights, line-heights from tokens) — no off-scale sizes.
- [ ] A clear, few-step hierarchy (display / heading / body / caption) with obvious jumps between levels.
- [ ] Body measure is ~50–75 characters; line-height is comfortable for the size.
- [ ] Few distinct text styles per screen — restraint, not a ransom note.

### A5. Color & contrast
- [ ] Surfaces form a legible value hierarchy (page background < card/surface < raised/overlay) using neutral tokens.
- [ ] Body text ≥ **4.5:1**; large text and meaningful UI/icons ≥ **3:1** (WCAG AA).
- [ ] Status and meaning are **never color-only** — always paired with an icon, label, or shape.
- [ ] The same role uses the same token everywhere (every primary button is the same primary color).

### A6. Depth, iconography & finish
- [ ] Elevation uses the **shadow scale** to signal hierarchy (raised / overlay / modal), not decoration; flat where flat reads cleaner.
- [ ] Radii are consistent across like elements, drawn from the radius scale, and match the direction's personality.
- [ ] All icons come from **one family** at consistent size and stroke; labeled wherever meaning isn't obvious.
- [ ] No ornament that doesn't do a job — every element earns its place.
- [ ] (If glass/blur is used) only on floating overlays, and only where text over it still meets contrast.

### A7. Interaction states
- [ ] Core controls (buttons, inputs, nav items) define default, hover, focus-visible, active/pressed, disabled; inputs define error.
- [ ] Focus is **visibly** indicated (a real focus treatment, not just a color shift).
- [ ] Hit targets are comfortable: ≥ **44px** touch, ≥ **32–40px** pointer, with enough spacing to avoid mis-taps.
- [ ] The states that are animated in Phase 6.5 are realized as **page-local component variants** (unpublished) so Smart Animate can move between them.

### A8. Content realism & states
- [ ] Real, plausible content and data — **no lorem**, no "Title goes here." Realistic labels, names, numbers, dates.
- [ ] Data-driven screens define **empty, loading, and error** states (drawn on the complex flow; described elsewhere).
- [ ] Layout survives extremes: long strings, very short values, zero / one / many — with stated truncation or wrap behavior.

---

## B · Per-direction checks (once, across the set)

### B9. Concept coherence
- [ ] The direction has a **named, one-line thesis** (its personality), and every screen visibly expresses it.
- [ ] Any two screens read as the same product **and** the same direction.
- [ ] This alternative is **genuinely distinct** from the other directions in the file (on the other `Design_Direction_*` pages) — a different personality, not a recolor.

### B10. Navigation & information architecture
- [ ] The navigation model is explicit, **consistent on every screen**, and appropriate to the platform and app type.
- [ ] The user can always tell **where they are** and what the primary paths are.
- [ ] The main layout (nav + content regions) is consistent enough that a future screen would slot in without redesign.

### B11. Consistency & accessibility — final sign-off
- [ ] Patterns, spacing rhythm, and type styles repeat **identically** across all screens; a new screen would look native.
- [ ] Contrast, target sizes, visible focus, not-color-only, and text scaling all pass across the set.
- [ ] Palette is restrained across the whole direction — mostly neutral + brand primary; accents sparse and consistent.
- [ ] A clean **semantic mapping is derivable** (which primitive plays Primary / Secondary / Surface / Background / Text / Border / Success / Warning / Danger / Info) — captured in the spec for the rollout skill.

---

## C · Motion checks (the prototype)

The direction **demonstrates** motion as a Figma prototype (Phase 6.5), so it IS checked. Capture the key interaction states / transitions and verify:

### C12. Motion is present and purposeful
- [ ] The representative sample is animated: **button** (default → hover → pressed), a **navigation** interaction (hover + active, and screen-to-screen navigation), one **popup/modal** (overlay open/close), and the **complex journey's screen transitions**.
- [ ] Every animation communicates a **state change** (feedback, transition, or guidance) — none is decoration.
- [ ] Motion is **subtle**: small fade / slide / scale, not bounce / spin.

### C13. Motion is on-system and consistent
- [ ] Durations and easing come from the **`motion/duration/*` / `motion/easing/*` token values** (read and applied to the interactions).
- [ ] Most UI transitions land in **100–300 ms**; the same element type animates the same way everywhere.
- [ ] Transitions animate the **changing element** and preserve spatial continuity (things move from a logical origin); overlays / toasts / menus get priority.

### C14. Motion stays in scope
- [ ] Any components built for motion are **page-local and unpublished**, limited to the interactive elements the sample needs.
- [ ] The spec records the motion character (durations, easing, what animates) and notes that **reduced-motion** is honored downstream.

*(Reduced-motion enforcement, non-blocking / interruptible behavior, and the full component library are rollout / QA concerns — noted in the spec, not built here.)*
