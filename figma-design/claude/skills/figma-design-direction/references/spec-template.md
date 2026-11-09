# Design Direction Spec — template

> Part of `figma-design-direction`. Fill this in during Phase 8 and write it to `../ax-design-specs/<app>-direction-<name>.md` (create the folder if missing). This spec is a **contract for downstream AI** — the rollout skill and future screen designers read it to build semantic tokens, components, and new screens that stay on-brand and on-concept. Be specific and prescriptive: state exact token names, not adjectives. Everything here must reflect what was actually built in Figma. Delete guidance in parentheses as you fill it.

---

# <App Name> — Design Direction: "<Direction Name>"

- **Thesis:** <one line — the personality this direction expresses>
- **Status:** Draft direction / alternative (not yet approved)
- **Platform:** <desktop / mobile / both> · **Theme:** Light only
- **Figma:** <file link> › page `Design_Direction_<Direction Name>`
- **Foundation file:** <same file — primitives in BrandPrimitives + App-Primitives>
- **Date:** <YYYY-MM-DD> · **Author:** AI (figma-design-direction)
- **Screens included:** <list> · **States drawn:** default (all) + empty/loading/error + interaction states (complex flow) · **Motion:** prototype (buttons, nav, popups, transitions)

## 1. Concept & personality
- **Adjectives:** <3 words>
- **Emotional goal:** <what a user should feel>
- **Audience & context:** <who, how often, where>
- **North-star / references translated:** <what principle was borrowed and how it maps to this brand>
- **How this alternative diverges from the others:** <nav pattern + the other lever(s) that make it distinct>

## 2. Foundation used
(Which primitive groups exist and were used. Note any gaps you flagged and how you worked around them — never invent values.)
- Brand colors: <names> · Neutrals: <ramp> · Semantic ramps: <red/green/yellow/blue>
- Type: <families / weights / sizes / line-heights used>
- Spacing / radius / border / elevation / motion: <which steps/levels used>
- **Gaps flagged:** <e.g. "no elevation primitives → used bordered surfaces instead">

## 3. Recommended semantic mapping (handoff to the rollout skill)
(THE key handoff. Map each semantic role to the exact primitive it should alias. The rollout skill will create these as real tokens.)

| Semantic role | Primitive | Notes / usage |
|---|---|---|
| Background | `color/…` | page canvas |
| Surface | `color/…` | cards, panels |
| Surface / raised | `color/…` + `elevation/…` | overlays, menus |
| Text / primary | `color/…` | |
| Text / secondary | `color/…` | |
| Text / disabled | `color/…` | |
| Border / default | `color/…` + `border/width/…` | |
| Primary | `color/brand/…` | primary actions; hover/press = `color/brand/…` |
| Secondary | `color/…` | secondary actions |
| Success / Warning / Danger / Info | `color/green|yellow|red|blue/…` | status, always with icon/label |

## 4. Layout system
- **Grid:** <cols / margin / gutter per breakpoint> · **Container max-width:** <px>
- **Spacing rhythm:** <base unit + the spacing steps in active use>
- **Density:** <airy / balanced / compact> — <what that means concretely>
- **Responsive rules:** <how the layout reflows for the platform(s); what collapses/stacks>

## 5. Typography
| Role | Token(s) | Usage rule |
|---|---|---|
| Display | `font/size/…` + `font/weight/…` | <where> |
| Heading (H1–H3) | … | |
| Body | … | measure ~50–75ch |
| Caption / label | … | |
- **Rules:** <max distinct styles per screen; number formatting; casing>

## 6. Color usage
- **Surface value hierarchy:** background → surface → raised (which tokens).
- **Accent rules:** <how sparingly the primary is used; where accents are allowed>
- **State colors:** <how success/warning/danger/info appear; never color-only>.

## 7. Depth, shape & iconography
- **Elevation:** which levels appear where (`elevation/2` = cards, `elevation/4` = modals, etc.).
- **Radius personality:** <which radius token on which elements>.
- **Icon family:** <name> · size <px> · stroke <px>.

## 8. Navigation & information architecture
- **Nav model:** <sidebar / top bar / bottom tabs / …> — structure and items.
- **Current-location indication:** <how "where am I" is shown>.
- **Main layout regions:** <nav / header / content / aside / footer> and their behavior.

## 9. Component inventory (informal — for the rollout skill to formalize)
(Each recurring element built in the direction, with its anatomy and exact tokens. Mostly flat-frame build lists; the interactive ones — button, nav item, modal — were built as **page-local components** for the motion prototype. The rollout skill formalizes all of them.)
- **Button** — variants <primary/secondary/…>; padding `spacing/…`; radius `radius/…`; text `font/…`; states default/hover/focus/active/disabled.
- **Input** — …; states incl. error.
- **Card** — …
- **Nav item** — …
- **Table row / list item** — …
- **<others>** — …

## 10. Interaction states
| Element | Default | Hover | Focus-visible | Active/Pressed | Disabled | Error |
|---|---|---|---|---|---|---|
| Button/primary | | | | | | — |
| Input | | | | | | |
| Nav item | | | | | — | — |
(State the token change for each cell, e.g. "bg → `color/brand/primary-dark`".)

## 11. Motion character (demonstrated as a prototype)
- **Typical durations:** <100–300 ms> from `motion/duration/…` · **Easing:** `motion/easing/…`.
- **Prototyped interactions:** <what was actually built — button hover/press, nav hover/active + screen navigation, popup/modal overlay, complex-journey screen transitions>.
- **Key transitions:** <what animates on nav change, dialog open, toast, etc. — and from where>.
- **Page-local motion components:** <the interactive elements built as variants for the prototype — to be rebuilt properly by the rollout skill>.
- **Reduced motion:** honored downstream — <what falls back to instant>.

## 12. States & edge cases
- **Empty / loading / error patterns:** <the visual treatment for each>.
- **Content extremes:** <truncation vs. wrap rules; zero/one/many behavior; overflow>.

## 13. Screen-by-screen notes
For each screen: **purpose · primary action · source requirements & mockup (if screen-specific) · key layout decisions · states shown · motion shown · anything non-obvious.**
- **<Screen 1>** — …
- **<Screen 2>** — …
- **<Complex flow, Step 1…n>** — …

## 14. Do's & Don'ts for future screens
- ✅ <e.g. "Use `color/brand/primary` only for the single primary action per view.">
- ✅ <e.g. "Cards on `elevation/2`; page uses bordered sections, not shadows.">
- ❌ <e.g. "Never introduce a second accent color.">
- ❌ <e.g. "No off-scale type sizes or eyeballed spacing.">

## 15. Open questions & assumptions
- <requirement gaps, unresolved decisions, or assumptions made that a human should confirm>
