---
name: figma-ds-core
description: Build the DESIGN SYSTEM CORE in Figma — the building blocks between the design direction and the screen rollout. Creates (1) an exhaustive SEMANTIC token layer (collection `Semantics`, every variable an alias to an existing primitive), (2) a lean COMPONENT-token layer (collection `ComponentTokens` for the high-theming-surface components), (3) semantic TEXT STYLES and elevation EFFECT STYLES, and (4) a full component library — atoms on page `DS_Core_Components`, patterns on `DS_Core_Patterns`, layouts/templates on `DS_Core_Templates` — every component styled by the chosen design direction and mappable 1:1 to a named base design system (Material, shadcn, …) so code wrappers are buildable. Driven by NORMATIVE manifests (token + component catalogs) with a validation gate after every phase — nothing may be silently skipped. Also writes a ds-core spec (markdown) as the contract for the downstream rollout skill. Use when the user asks to "build the design system core", "create semantic tokens / component tokens", "build the component library in Figma", "create components from the design direction", "ds core", or "prepare the design system for rollout". Requires: Figma file with BrandPrimitives + App-Primitives, a design direction spec + its Figma page, and a named base design system. Third step of the pipeline — after figma-ds-foundation and figma-design-direction, before the rollout skill.

---

# Design System Core → Semantic tokens + Component library in Figma

## In the Aatmix platform

When the Aatmix platform's UX Agent runs this skill, read every instruction below through this mapping. Everything else in the skill applies unchanged.

- **"The user"** is the UX Engineer, reached through the Exchange. Ask with `exchange_ask`: numbered replies, your recommendation marked, one round at a time — every open question of that round together, then end the turn. The run resumes with the answers. "Ask", "confirm", "get a go-ahead", "wait for approval" and "the interactive input tool" all mean such a round.
- **The Figma file** is the project's connected Figma file named in the run details. Only when none is connected, ask the UX Engineer for its URL.
- **The brand guidelines** are artifact `BRG`, already supplied by the project (run details name its version; read it with `artifacts_read`). Never ask for them.
- **The PRD** is the approved PRD artifact the run details name (`artifacts_read`), never a file.
- **Files the skill writes or reads under `ax-sdd-artifacts/`** live in the artifact store instead (`artifacts_list`, `artifacts_read`, `artifacts_put_draft`, then `artifacts_submit` for review as the task text says), under these kinds: direction spec → `visual_direction`; foundation report → `brand_primitives`; ds-core spec → `semantic_tokens` (its token half: the Semantics and ComponentTokens mapping and the styles) and `core_components` (its component half: the component inventory); VD contract → `vd_contract`; rollout record → `screen_rollout`; product-change report → the PRD change requests the task text names (never edit the PRD). A change report the skill writes beside a spec goes into the next version of that spec's artifact, as its own section.
- **Previews** (PNG or HTML of what you drew) go to `design_preview_put` against the project asset they belong to (`brand_primitives`, `visual_direction`, `semantic_tokens`, `core_components`, `vd_contract`), and to `screen_preview_put` against a screen's record.
- **Other skills**: `figma-ds-foundation`, `figma-design-direction`, `figma-ds-core`, `figma-vd-contract` and `figma-vd-rollout` are this plugin's skills; `figma-use`, `figma-generate-design` and `figma-generate-library` come from the Figma plugin.
- **In this skill**: the direction spec is the approved `visual_direction` artifact; the base design system comes from the task text or the project context, and only when neither names one is it a question. The Phase 10 spec is stored as two artifacts, each submitted and approved in turn: its token half (semantics, component tokens, styles) as `semantic_tokens`, then its component half (the component inventory with every link) as `core_components`; store a PNG of each component sheet with `design_preview_put` against the `core_components` draft.

Build the **core** of the design system: the reusable building blocks that let a downstream rollout AI design *any* new screen without inventing anything. This is the third stage of the pipeline:

```
ds-foundation (primitives) → design-direction (chosen look) → THIS skill (semantics + components + component tokens) → rollout skill (all screens)
```

The core = **semantic variables** + **component tokens** + **text/effect styles** + **components** (atoms, patterns, layouts/templates), all expressing the chosen design direction, all implementable as thin code wrappers over the base design system.

## What this is — and is NOT

| This skill DOES | This skill does NOT |
|---|---|
| Create the `Semantics` + `ComponentTokens` variable collections (aliases only) | Create or modify primitive values |
| Create semantic text styles + elevation effect styles | Create dark mode or any extra mode/theme |
| Build the full component library (atoms / patterns / templates) on 3 dedicated pages | Design product screens (rollout skill's job) |
| Map every component 1:1 to the base design system | Invent component anatomy the base DS can't express |
| Re-scope primitives to `[]` once semantics exist (gated) | Touch the design-direction page or its screens |
| Write the ds-core spec for the rollout skill | Publish a Figma library (offer only; org-plan dependent) |

## Hard guardrails (read first)

1. **The catalogs are LAW, not inspiration.** `references/semantic-token-catalog.md` and `references/component-catalog.md` are **normative manifests**. Every listed token, style, component, variant, and state MUST be created — or explicitly waived by the user, item by item. Never trim, sample, or "representative subset" the manifests. After every build phase, run the manifest-diff validation (see `references/figma-build-guide.md` §Validate) and report **created / skipped(existing) / MISSING** counts against the manifest totals printed in each catalog. You may not proceed past a phase, and may not declare the run complete, while MISSING > 0 without a per-item user waiver recorded in the final report.
2. **Never deviate from the foundation and brand tokens.** Every semantic variable is an **alias to an existing primitive** — zero raw values in `Semantics` or `ComponentTokens`. Every component property is **bound to a semantic or component token** — zero hardcoded fills, text colors, spacing, padding, gaps, radii, borders, shadows, or sizes anywhere in the library. If a needed value has no primitive, **stop and flag it** (route to `figma-ds-foundation`); never invent one.
3. **The design direction is binding.** The direction spec's decisions (§3 semantic mapping, §4 layout, §5–7 type/color/depth, §8 nav, §9 component inventory, §11 motion) are **not reopenable**. Where the token catalog marks an alias target `DIRECTION`, resolve it from the direction spec; use the catalog default only when the spec is silent, and say so. Components must look **native** next to the direction's screens.
4. **1:1 base-DS parity.** Every component maps to a named component of the base design system (see `references/base-ds-review.md`). If the direction demands anatomy the base DS can't express, surface the conflict and resolve it with the user — options: composite wrapper, direction adjustment, or documented custom component. Never silently improvise.
5. **No dark mode or additional themes/modes** unless the user explicitly asks. Both new collections get exactly one mode, named `Default`.
6. **Confirm before each build phase.** Show the resolved plan (tokens phase, atoms phase, patterns phase, templates phase) and get a go-ahead before the first write of each. Present questions with the interactive input tool where available.
7. **`use_figma` is the only write path.** Load `figma-use` AND `figma-generate-library` before any `use_figma` call; include them in `skillNames`. On any error, STOP and read it — scripts are atomic; fix, then retry. Build in **chunks** (≤ ~15 variables or 1 component set per script) and validate after each chunk.
8. **Idempotent, additive, skip-by-default.** Tag everything with `setSharedPluginData('ax-dsc', …)`. On re-runs (or when a *previously created core* is given), add only what's missing; never overwrite or rename existing items without explicit user instruction. This is how the library grows across runs.
9. **Touch only your own surfaces**: the two new collections, the three `DS_Core_*` pages, the semantic styles, and (Phase 8 only, gated) primitive *scopes*. Never modify primitive values, direction pages, or anything else in the file.

## Required inputs — validate first; ask and STOP if missing

1. **Figma file link** — the target file. Variables are file-scoped.
2. **Design System Foundation present** — verify by **inventorying the actual variables**: `BrandPrimitives` + `App-Primitives` must exist with usable coverage (brand ramp, grays, semantic ramps, type, spacing, radius, border, elevation, motion). Never assume. Missing/sparse → stop, route to `figma-ds-foundation`.
3. **Design direction spec** — the markdown contract (default location `ax-sdd-artifacts/ax-design-specs/<app>-direction-<name>.md`). Read it fully.
4. **Design direction Figma page** — verify a `Design_Direction_<name>` page exists; screenshot its screens and absorb the direction (nav model, grid, density, color roles, radius, elevation, motion, icon family).
5. **Base design system** — a named system (Material 3, shadcn/ui, Ant, Chakra, …). Needed for wrapper parity (guardrail 4).

**Optional:** user prompt/intents (analyze for theme, mood, colors, personality, references — fold into questions, never override the direction); a **previously created design system core** (→ update/extend mode: discover what exists via `ax-dsc` tags, build only the delta, update the same spec).

## Workflow

### Phase 0 — Inputs, mode & discovery
Validate all 5 required inputs (ask + stop on any gap). State the target file back for confirmation. Detect **update mode** (existing `ax-dsc` tags / user-provided prior core). Inventory: primitive collections (full variable list), existing `Semantics`/`ComponentTokens`, existing `DS_Core_*` pages, existing tagged styles/components.

### Phase 1 — Absorb the direction (never skip)
Read the direction spec; screenshot the direction page. Extract into a working **Direction Resolution Table**: every `DIRECTION`-marked alias target in the token catalog → the primitive the direction assigns (e.g. `color/action/primary/bg` → `color/brand/primary/600`). Extract the component-styling rules (radius personality, elevation strategy, density, nav model, motion character) and the direction's §9 component inventory → these become **direction-specific additions** to the component manifest (see catalog §Direction delta).

### Phase 2 — Review the base design system
Follow `references/base-ds-review.md`: web-fetch the base DS docs; extract each manifest component's anatomy, variant/prop API, states, and sizes. Produce the **parity table** (Figma component ↔ base-DS component + props). Flag any manifest component the base DS lacks (resolve per guardrail 4).

### Phase 3 — Expert questions
Ask only what the direction spec doesn't answer, like a senior product designer: default density, control sizing defaults, table ergonomics (row heights, selection model), form patterns (label position, error placement), empty/error philosophy, icon-only vs labeled actions, destructive-action treatment. Also analyze any user design-input prompt. Fold answers into the Direction Resolution Table.

### Phase 4 — Semantic + component tokens (plan → confirm → build → validate)
Present the resolved token plan (catalog + Direction Resolution Table, with counts). On approval, build per `references/figma-build-guide.md`: collection `Semantics` (mode `Default`), all aliases; then `ComponentTokens` aliasing semantics; then **text styles** (one per type role, bound to the semantic type variables) and **effect styles** (one per elevation role, from the decomposed elevation semantics). Chunked, tagged, validated: run the manifest diff, report counts, resolve MISSING before proceeding.

### Phase 5 — Atoms (confirm → build → validate)
Page `DS_Core_Components` (create if absent; else append). Build every Tier-1 component from the catalog as a **component set** with the full variant matrix (variant property names/values exactly as specified), auto-layout, all properties bound to tokens, real labels. Each set sits in a documentation frame (name, base-DS mapping, usage note, do/don't). Build one set per script; screenshot + spot-check bindings; manifest-diff at the end of the phase.

### Phase 6 — Patterns (build → validate)
Page `DS_Core_Patterns`. Build Tier-2 components, **composing Tier-1 instances** (a FormField contains the real Input component; never redrawn). Include the direction-delta patterns from Phase 1. Same documentation, chunking, validation.

### Phase 7 — Layouts & templates (build → validate)
Page `DS_Core_Templates`. Build Tier-3 templates from real pattern instances at the direction's platform frame size: app shell (the direction's exact nav model), dashboard, list/table page, detail page, form page, settings, auth/entry, overlay-in-context, full-page empty/error. Slot/placeholder areas clearly marked for the rollout skill.

### Phase 8 — Re-scope primitives (gated)
With the semantic layer live, propose setting every primitive's scope to `[]` (reference-only) so designers/AI can bind **only** semantics — as anticipated by the foundation skill. Execute only on explicit confirmation. Never change primitive values or names.

### Phase 9 — Self-critique & conformance
Run `references/quality-checklist.md` in full: per-token, per-component (every variant screenshot-checked), per-system passes; **WCAG AA** on every interactive state; **zero-hardcoded-value audit** (script-scan all `DS_Core_*` nodes for unbound paint/number properties); **conformance check** — place sample components beside the direction's screens and verify they look native. Fix everything; do not present until clean. Report honestly.

### Phase 10 — Spec + final report
Write `ax-sdd-artifacts/ax-design-specs/<app>-ds-core-<direction>.md` per `references/spec-template.md` (update, don't fork, in update mode): token dictionary, component API tables with base-DS parity mapping, composition rules, direction deltas, waivers. Final report: manifest totals vs created/skipped/missing (must be 0 or waived), pages + Figma links, checklist/WCAG result, re-scope status, spec path. Offer (off by default): publish as library; set code syntax on semantic variables.

## Reference files
- `references/semantic-token-catalog.md` — **normative** semantic + component token manifest: exact names, types, scopes, alias targets, counts.
- `references/component-catalog.md` — **normative** component manifest: 3 tiers, exact variant matrices, states, required bindings, base-DS mapping, counts.
- `references/base-ds-review.md` — reviewing the base DS and building the parity table.
- `references/figma-build-guide.md` — `use_figma` patterns: alias creation, component sets, variable binding, styles, chunking, tagging, the manifest-diff validation script.
- `references/quality-checklist.md` — the Phase 9 checks.
- `references/spec-template.md` — the ds-core spec contract for the rollout skill.
