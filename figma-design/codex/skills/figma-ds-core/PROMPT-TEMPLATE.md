# Prompt template — `figma-ds-core`

Copy the block below, fill in the `<…>` placeholders, and paste it as your message. The first line triggers the skill. **One run = the full core** (semantic tokens + component tokens + text/effect styles + atoms, patterns, templates on the three `DS_Core_*` pages) + one ds-core spec file — built for **one chosen design direction**, on top of the existing foundation.

The build is long and runs in confirmed phases (tokens → atoms → patterns → templates); expect several approval checkpoints. A later run with the same prompt is an **update run**: the skill detects what already exists and builds only the delta.

---

## Template (copy this)

```text
Build the design system core in Figma using the figma-ds-core skill.

## Target Figma file
<paste the Figma file link — must already contain the foundation
 (BrandPrimitives + App-Primitives) AND the design direction page>

## Design direction  (the chosen one — REQUIRED)
Direction name: <exact <name> of the Design_Direction_<name> page, e.g. "Sovereign Stage">
Direction spec: <path/attachment, e.g. ax-sdd-artifacts/ax-design-specs/<app>-direction-<name>.md>

## Base design system  (REQUIRED)
<Material 3 | shadcn/ui | Ant Design | Chakra | other — the code library the
 components will wrap 1:1. Add a docs link if it's not one of the well-known ones.>

## Additional intents  (optional)
- <e.g. density default: comfortable | compact>
- <e.g. our forms always use top-aligned labels>
- <e.g. prioritize table/data patterns — this is a data-heavy product>
- <any theme/mood/personality notes or references — applied WITHIN the direction,
  never overriding it>

## Previously created core  (optional — for update runs)
<link/note to the earlier run's spec or "core already exists in this file —
 extend it". Omit on the first run.>

Notes: single light theme only (no dark mode). Never deviate from the foundation
primitives or the direction — flag gaps instead of inventing values. Build the FULL
manifests (tokens + all three component tiers) with the validation gate after every
phase; nothing may be silently skipped, and any waiver needs my explicit per-item OK.
```

---

## Filled example

```text
Build the design system core in Figma using the figma-ds-core skill.

## Target Figma file
https://www.figma.com/design/XXXX/Aatmix-AI   (foundation + direction already in this file)

## Design direction
Direction name: Sovereign Stage
Direction spec: ax-sdd-artifacts/ax-design-specs/aatmix-ai-direction-sovereign-stage.md

## Base design system
shadcn/ui

## Additional intents
- Density default: comfortable, with a Compact option on tables.
- Forms use top-aligned labels, errors below the field.
- The product is workflow-heavy: make SidebarNav, Table, Stepper and Modal first-class.

Notes: single light theme only (no dark mode). Never deviate from the foundation
primitives or the direction — flag gaps instead of inventing values. Build the FULL
manifests (tokens + all three component tiers) with the validation gate after every
phase; nothing may be silently skipped, and any waiver needs my explicit per-item OK.
```

---

## How each field maps to the skill

| Field | Drives |
|---|---|
| Target Figma file | Phase 0 discovery — foundation inventory + direction page + existing-core detection |
| Direction name + spec | Phase 1 — the Direction Resolution Table (every `DIRECTION`-marked token target) and the component styling rules; the direction-delta patterns from spec §9 |
| Base design system | Phase 2 — docs review + the 1:1 parity table (Figma variants ↔ code props); gap protocol for unmappable components |
| Additional intents | Phase 3 expert questions (pre-answers them) — density, sizing, form/table ergonomics |
| Previously created core | Switches to update mode: `ax-dsc`-tagged items are skipped, only the delta is built, the same spec file is updated |

## What one full run produces

- **Collections:** `Semantics` (~187 alias variables) + `ComponentTokens` (60) — single `Default` mode, zero raw values.
- **Styles:** 12 `DS/*` text styles + 3 `DS/Elevation/*` effect styles, variable-bound.
- **Pages:** `DS_Core_Components` (26 atom sets), `DS_Core_Patterns` (23 pattern sets + direction delta), `DS_Core_Templates` (9 templates incl. the direction's AppShell).
- **Gated extras:** primitive re-scoping to `[]` (asked in Phase 8), optional library publish / code syntax (offered at the end).
- **Spec:** `ax-sdd-artifacts/ax-design-specs/<app>-ds-core-<direction>.md` — token dictionary, component APIs with base-DS mapping, composition rules, coverage table — the contract for the screen-rollout skill.

## Notes
- **Prerequisites:** foundation (`figma-ds-foundation`) and a chosen direction (`figma-design-direction`) must already exist in the file — the skill validates both and stops if missing.
- **The manifests are law.** The token and component catalogs inside the skill are normative; the run reports created / existing / MISSING counts after every phase and cannot finish with unexplained gaps.
- **Long build:** budget for multiple messages. If a phase pauses mid-way, just say "continue" — tagging makes every step resumable without duplicates.
- **Adding components later:** re-run with the *Previously created core* field filled and list the new needs under *Additional intents* (or let a future rollout run flag them); the skill appends to the same pages and updates the same spec.
