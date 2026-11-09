# Prompt template — `figma-design-direction`

Copy the block below, fill in the `<…>` placeholders, and paste it as your message. The first line triggers the skill. **One run = one direction alternative** on its own `Design_Direction_<name>` page + one spec file. Run it 3–4 times (changing the direction name + design expectations) to get distinct alternatives.

The skill has **two modes**: use the first template to **create a new direction (Mode A)**, and the **"ADD screens to an existing direction" (Mode B)** template further down to add more screens to a direction you already built — those screens conform to the named direction and land on its existing page.

---

## Template (copy this)

```text
Create a design direction in Figma using the figma-design-direction skill.

## Target Figma file
<paste the Figma file link — this is BOTH where the direction gets created
 and where the design-system foundation (primitive variables) already lives>

## Platform
<desktop | mobile | both>

## Direction name  (optional)
<a name for this alternative, e.g. "Vivid Canvas". It becomes the page name
 Design_Direction_<name> and the spec title. If omitted, propose one.>

## Screens — FINAL, use exactly these (do not re-select or ask me to confirm)
Match each mockup; elevate fidelity, don't redesign the flow.
1. <Screen name> — mockup: <Figma link>  — notes: <optional per-screen requirements>
2. <Screen name> — mockup: <Figma link>
3. <Screen name> — mockup: <Figma link>
4. <Screen name> — mockup: <Figma link>
5. <Screen name> — mockup: <Figma link>
6. <Screen name> — mockup: <Figma link>
Complex journey (optional): <which screens form the multi-step flow — states + motion get demonstrated here>

## Requirements
BRD: <attach the BRD file, or give its path/link>
<any global functional context not covered by the BRD>

## Design expectations (look & feel)
- <expectation 1>
- <expectation 2>
- <expectation 3>
Motion feel (optional): <instant / subtle & functional / lively & expressive>
References to draw from / avoid (optional): <sites, products, adjectives, competitors to differ from>

Note: everything must be achievable from the foundation's existing tokens —
flag anything that isn't rather than inventing values. One theme (light) only.
```

---

## Template — ADD screens to an existing direction (Mode B)

Use this on a **later run** to add more screens to a direction you already created. The new screens are built to **conform to that direction** and are placed on its **existing `Design_Direction_<name>` page** — no new page, no redesign. Both the direction name and the new screens' features are **required**.

```text
Add screens to an existing design direction using the figma-design-direction skill.

## Target Figma file
<the SAME Figma file that contains the existing Design_Direction_<name> page>

## Existing direction name  (REQUIRED)
<the exact <name> of the Design_Direction_<name> page to add the screens to,
 e.g. "Vivid Canvas">

## New screens to add  (REQUIRED — features per screen)
1. <Screen name> — features/requirements: <what it does, primary action, key content>
   mockup (optional): <Figma link>
2. <Screen name> — features/requirements: <…>
Complex journey (optional): <if the new screens form/extend a multi-step flow>

## Requirements  (optional — falls back to the direction's existing spec/BRD)
<any functional context specific to the new screens>

Note: match the existing direction exactly — same nav, grid, density, type, colour
roles, radius, elevation, and motion. Bind to the foundation's existing tokens only;
flag anything that isn't achievable rather than inventing values.
```

---

## Filled example

```text
Create a design direction in Figma using the figma-design-direction skill.

## Target Figma file
https://www.figma.com/design/XXXX/OEd-CKD   (foundation already created in this file)

## Platform
Desktop

## Direction name
Vivid Canvas

## Screens — FINAL, use exactly these
1. Sign-in            — mockup: https://figma.com/…/signin
2. Dashboard / Home   — mockup: https://figma.com/…/dashboard
3. Exams list + nav   — mockup: https://figma.com/…/exams
4. Take-exam · Step 1 — mockup: https://figma.com/…/exam-1
5. Take-exam · Step 2 — mockup: https://figma.com/…/exam-2
6. Result / report    — mockup: https://figma.com/…/result
Complex journey: screens 4 → 5 → 6 (Take-exam through Result)

## Requirements
BRD: <attached> OEd-CKD-BRD.pdf

## Design expectations (look & feel)
- Inverted background: use a bright brand colour from the foundation as the page
  background, with cards/containers as the light-coloured foreground surfaces.
- Use illustrations to carry empty states and hero moments (supportive, not clutter).
- SVG iconography from a single family, coloured to suit the theme — readable on both
  the bright background and the light cards.
Motion feel: subtle & functional
```

---

## How each field maps to the skill

| Field | Drives |
|---|---|
| Target Figma file | Phase 0 target + Phase 1 foundation inventory (must already contain the primitives) |
| Platform | Skips the desktop/mobile question; sets frame sizes |
| Direction name | The `Design_Direction_<name>` page (created in Phase 6) and the spec title |
| Screens — FINAL | Skips screen selection/confirmation and takes the list as final |
| Screen mockups | Per-screen gap analysis in Phase 3 (match the mockup, don't redesign) |
| Screen notes | Per-screen functional requirements (fall back to the BRD where absent) |
| Complex journey | Where empty/loading/error states + the richer motion pass are shown |
| BRD | Global requirement context for real content |
| Design expectations + Motion feel | The Design Decisions Brief (Phase 5) and the motion prototype (Phase 6.5) |

## Notes
- **Foundation must exist first.** If the primitives aren't in the file, run `figma-ds-foundation` before this.
- **Don't-deviate guardrails still apply:** the skill binds to existing primitive tokens (no hardcoded values), builds one light theme, and won't invent off-brand values — it flags gaps instead. Design expectations are realized *within* the available tokens.
- **Alternatives:** re-run with a new *Direction name* and different *Design expectations*; the skill detects the earlier `Design_Direction_*` pages and deliberately diverges.
- **Adding screens later (Mode B):** use the "ADD screens to an existing direction" template. Give the exact existing *direction name* and the *features* of each new screen. The skill reads that direction's spec + screens, designs the new ones to match, appends them to the **same page** (continuing the frame numbering), and updates that direction's spec — it does **not** create a new page or a new concept.
