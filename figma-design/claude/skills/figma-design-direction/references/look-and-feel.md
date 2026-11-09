# Look & Feel — the design interview and mood → parameter mapping

> Part of `figma-design-direction`. Used in Phase 4. Goal: turn vague inputs ("make it modern and clean") into **concrete design parameters** the brief can commit to, and — for alternatives — pick a concept that genuinely diverges from prior directions. Ask only the questions that change the design; infer the rest and state your assumptions.

## The design interview (ask the sharp ones only)

Ask these like a design lead scoping a project. Skip any already answered by the brand guidelines, requirements, or reference inputs. Prefer offering 2–3 concrete options over open questions — it's easier to react than to invent.

1. **Audience & context.** Who uses this, how often, and where? (Power users in a data-dense admin all day vs. an occasional consumer on mobile changes everything: density, target size, hierarchy.)
2. **Personality — pick 3 adjectives.** e.g. *calm, editorial, trustworthy* / *bold, energetic, modern* / *warm, friendly, approachable* / *precise, technical, dense*. These become the thesis.
3. **Emotional goal.** What should a user feel in the first 5 seconds — *confident? impressed? at ease? in control?*
4. **References — liked and disliked.** Products/sites they admire, and (just as useful) ones they want to avoid. Ask *why* — it reveals the real preference.
5. **Differentiation.** Which competitors should this *not* look like?
6. **Density preference.** Airy and spacious, balanced, or compact and information-dense?
7. **Tone on the spectrum.** Playful ↔ serious. Expressive ↔ restrained. Decorative ↔ minimal.
8. **Shape language.** Sharp/architectural, softly rounded, or fully pill/organic?
9. **Brand color emphasis.** Brand-forward (color-led UI) or neutral-forward (brand used as a sparing accent)?
10. **Motion appetite.** Static-feeling and instant, subtle and functional, or lively and expressive? (Demonstrated as a prototype in Phase 6.5 and recorded in the spec.)

If the user can't answer, propose a default derived from the brand guidelines + product type, state it, and move on. Don't stall the direction on preferences the user doesn't have.

## Mood → concrete parameters

Translate the adjectives/answers into buildable settings. Everything still resolves to **existing primitives** — this table decides *which* primitives to lean on, never new values.

| Signal | Density / spacing | Radius | Type character | Color emphasis | Elevation | Motion |
|---|---|---|---|---|---|---|
| **Calm / editorial / premium** | Airy — larger spacing steps, wide margins, generous measure | Small–medium, even | Larger type, high contrast in size, generous line-height, restrained weights | Neutral-forward; brand as a quiet accent | Low, soft, sparing | Slow-ish (250–300 ms), gentle easing |
| **Bold / energetic / modern** | Balanced, confident blocks | Medium | Big headings, strong weight contrast | Brand-forward; primary carries the UI | Medium, crisp | Snappy (150–200 ms), decisive |
| **Warm / friendly / approachable** | Balanced, comfortable | Medium–large, rounded | Humanist feel, rounded forms, softer contrast | Brand + a friendly accent, used warmly | Soft, low | Gentle, springy-but-subtle |
| **Precise / technical / dense** | Compact — tight spacing, more per screen | Small / sharp | Smaller, tighter, tabular figures, clear labels | Neutral-forward; semantic colors do the talking | Minimal / flat, hairline borders | Instant–fast (100–150 ms) |
| **Trustworthy / institutional** | Balanced, orderly, strong grid | Small–medium | Classic hierarchy, sober weights | Neutral + brand primary only | Low, understated | Subtle, unshowy |

Read down the columns for the chosen adjectives and reconcile conflicts toward the top adjective (it's the thesis). Record the resulting parameters in the Design Decisions Brief.

## Divergence levers (for alternatives)

When prior directions exist on the page, this alternative must differ **structurally**, not just chromatically. Pick a distinct combination across these levers so the options are genuinely different choices:

- **Navigation pattern** — left sidebar · top bar · top bar + sub-nav · rail + drawer · (mobile) bottom tab bar · sectioned hub.
- **Composition** — centered/contained · full-bleed · asymmetric/editorial · split-pane · card-grid canvas.
- **Density** — airy · balanced · compact.
- **Type personality** — large-and-expressive · restrained-and-systematic · humanist · technical/tabular.
- **Shape** — sharp · softly rounded · pill/organic.
- **Color emphasis** — neutral-forward (brand as accent) · brand-forward (color-led surfaces or nav).
- **Elevation** — flat/bordered · softly layered · pronounced depth.
- **Motion character** — instant/functional · subtle/gentle · lively/expressive (short snappy durations vs. slower eased transitions). Motion is a real divergence lever — two directions can share a layout yet feel different purely through how they move.

Guidance:
- Change **at least the nav pattern + one of {composition, density, type personality}** between alternatives. That's what makes two directions feel like real alternatives rather than variants.
- Keep all of them **within the same primitives and brand** — divergence is in *arrangement and emphasis*, never in inventing off-brand values.
- Give each alternative a name and thesis that names its lever choices (e.g. "Direction B — Dense Command Center: left rail, compact grid, technical type, neutral-forward").

## Anti-patterns to avoid

- Chasing a trend (glass everywhere, heavy gradients, oversized shadows) that fights the brand — trends age; coherence doesn't.
- More than one primary accent competing for attention.
- Decoration with no job. If removing an element loses no meaning, remove it.
- Copying a reference pixel-for-pixel — translate its *principle* into this brand's primitives.
