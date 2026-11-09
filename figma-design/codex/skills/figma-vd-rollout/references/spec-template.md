# Rollout spec template

> Part of `figma-vd-rollout`, Phase 9. Write to `ax-sdd-artifacts/ax-design-specs/rollouts/<app>-rollout-<slug>.md` (create the folder if missing). This spec is the developer handoff and the record of what was designed — it must let someone build the screens without opening a conversation, and let a later run extend them without guessing.
>
> Fill every section. A section with nothing to say says `None — affirmed`, never disappears.

---

```markdown
# <App> — Visual Design Rollout: <scope name>

---
- rollout_id: VDR-<nnn>-<slug>
- title: "<App> — Rollout: <scope>"
- status: Draft | In Review | Approved
- version: 0.1
- direction: "<direction name>"
- direction_spec: <path>
- ds_core_spec: <path or "n/a">
- figma_file: <url>
- figma_page: "<page name> (<node id>)"
- requirement_source: <PRD path / ticket ID / attachment name>
- platform: <e.g. desktop 1440 · light theme only>
- screens: <n> · state frames: <n>
- created: <YYYY-MM-DD>
- updated: <YYYY-MM-DD>
- open_questions: <n>
---

## 1. Scope & summary

- **What was rolled out** and why (the requirement in one paragraph).
- **Screens produced**, with counts of states drawn vs affirmed.
- **Design-system additions** made this run (one line; detail in §6).
- **Anything deliberately out of scope**, and why.

## 2. Screen index

| # | Screen | Purpose | Primary action | Reached via | States drawn | Figma link |
|---|--------|---------|----------------|-------------|--------------|------------|
| 1 | <name> | <one line> | <the one action> | <nav item / parent + action> | default · empty · loading · … | <frame url> |

## 3. Screen contracts

Repeat per screen.

### <#> · <Screen name>

- **Purpose / job to be done:**
- **Requirements covered:** <IDs or quoted criteria>
- **Entry / exit:** how the user arrives, where they go next.
- **Information architecture:** what the screen groups and in what order, and why that order.
- **Layout:** template used, regions, grid/columns, container width, density.
- **Components used:** <component · where · notable variants/properties>
- **Content rules:** what each field shows, its source, formatting, truncation/wrap, sort/default order.
- **Permissions/personas:** who sees what; read-only differences.
- **Edge cases:** zero / one / many, longest string, stale data, concurrent edit.

**States**

| State | Drawn? | Frame | Behaviour / what the user sees |
|-------|--------|-------|--------------------------------|
| Default / filled | ✅ | <link> | |
| Empty — no data | ✅ / N/A — affirmed — <reason> | | |
| Empty — no results | | | |
| Loading — initial | | | |
| Loading — in place | | | |
| Invalid input | | | |
| Validation / business-rule error | | | |
| Submission success | | | |
| Submission failure | | | |
| Permission denied / read-only | | | |
| Partial / degraded | | | |
| Long-running / background | | | |
| <screen-specific> | | | |

## 4. Navigation & IA changes

- Nav items added/changed, and what they now reach.
- New overlays and their host screens.
- Anything a screen promises that does not yet exist (and where it is tracked).

## 5. Motion wired

| Interaction | From → To | Type | Duration | Easing | Token source |
|---|---|---|---|---|---|
| <e.g. New project> | <screen> → <overlay> | Overlay open (dissolve) | 200 ms | ease-out | motion/duration/fast · motion/easing/standard |

Prototype entry frame: `<frame>`.

## 6. Design-system usage & additions

**Used:** components and token groups this rollout leaned on (brief).

**Added this run** — every item also landed in the ds-core spec + change report:

| Addition | Type | Where it lives | Why existing parts didn't cover it | Used by |
|---|---|---|---|---|
| | variant / component / semantic token / component token | | | |

Change report: `<path or "none — no additions">`

## 7. Handoff notes (things Figma can't express)

- **Responsive/breakpoint behaviour** at the direction's declared breakpoints — what reflows, what collapses, what is hidden.
- **Keyboard & focus order** per screen; shortcuts; what traps focus in overlays.
- **Reduced motion:** every animation in §5 must be skippable under `prefers-reduced-motion` (fall back to an instant state change).
- **Interruptibility:** no animation blocks input; in-flight actions remain cancellable where the requirements allow.
- **Screen-reader semantics** worth stating: headings order, live regions for async feedback, labels for icon-only controls.
- **Data/formatting rules** that the visuals imply (locale, timezone, rounding, units).

## 8. Quality record

- Checklist result (per-screen / per-set / motion): pass, with any **accepted trade-offs** named.
- WCAG AA: verified across all states — result, and any exception with justification.
- Audits: raw-value/primitive-binding = 0 · instance integrity = 0 (or the exceptions, named).
- Nativeness check vs the direction and prior rollouts: result.

## 9. Open questions & conflicts

| # | Question / conflict | Raised from | Status | Interim design decision |
|---|---|---|---|---|

## 10. Change log

| Date | Version | Run | Change |
|------|---------|-----|--------|
```
