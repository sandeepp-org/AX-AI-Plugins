---
name: figma-ds-foundation
description: Build the Design System FOUNDATION (primitive tokens only) directly as Figma variables via the Figma MCP. Creates/updates exactly two variable collections — BrandPrimitives (brand colors + typography from brand guidelines) and App-Primitives (grays, semantic colors, spacing, radius, border, elevation/shadow, opacity, motion, z-index, breakpoints from best-in-class design systems) — grouped per best practice. Use when the user asks to "build design system foundation", "create primitive tokens", "set up brand primitives in Figma", "generate foundation variables", "seed the design system", "create BrandPrimitives / App-Primitives", or to turn brand guidelines into Figma variables. Foundation only — NOT semantic/component tokens, NOT a full design system. Accepts brand guidelines as Figma link, image, PDF, or plain text.
---

# Design System Foundation → Figma Variables

## In the Aatmix platform

When the Aatmix platform's UX Agent runs this skill, read every instruction below through this mapping. Everything else in the skill applies unchanged.

- **"The user"** is the UX Engineer, reached through the Exchange. Ask with `exchange_ask`: numbered replies, your recommendation marked, one round at a time — every open question of that round together, then end the turn. The run resumes with the answers. "Ask", "confirm", "get a go-ahead", "wait for approval" and "the interactive input tool" all mean such a round.
- **The Figma file** is the project's connected Figma file named in the run details. Only when none is connected, ask the UX Engineer for its URL.
- **The brand guidelines** are artifact `BRG`, already supplied by the project (run details name its version; read it with `artifacts_read`). Never ask for them.
- **The PRD** is the approved PRD artifact the run details name (`artifacts_read`), never a file.
- **Files the skill writes or reads under `ax-sdd-artifacts/`** live in the artifact store instead (`artifacts_list`, `artifacts_read`, `artifacts_put_draft`, then `artifacts_submit` for review as the task text says), under these kinds: direction spec → `visual_direction`; foundation report → `brand_primitives`; ds-core spec → `semantic_tokens` (its token half: the Semantics and ComponentTokens mapping and the styles) and `core_components` (its component half: the component inventory); VD contract → `vd_contract`; rollout record → `screen_rollout`; product-change report → the PRD change requests the task text names (never edit the PRD). A change report the skill writes beside a spec goes into the next version of that spec's artifact, as its own section.
- **Previews** (PNG or HTML of what you drew) go to `design_preview_put` against the project asset they belong to (`brand_primitives`, `visual_direction`, `semantic_tokens`, `core_components`, `vd_contract`), and to `screen_preview_put` against a screen's record.
- **Other skills**: `figma-ds-foundation`, `figma-design-direction`, `figma-ds-core`, `figma-vd-contract` and `figma-vd-rollout` are this plugin's skills; `figma-use`, `figma-generate-design` and `figma-generate-library` come from the Figma plugin.
- **In this skill**: the two required inputs are already in hand — the Figma file from the run details and the guidelines from `BRG`; ask only about the gaps Phase 1 detects and the Phase 2 build plan. The Phase 6 final report is the `brand_primitives` artifact: the summary, the token report and the Figma links.

Build the **primitive foundation** of a design system as Figma variables, in exactly **two collections** and nothing else:

| Collection | Holds |
|---|---|
| **BrandPrimitives** | Everything derived from the brand guidelines — brand color ramp(s) + typography (families, weights, sizes, line-heights, letter-spacing). |
| **App-Primitives** | Everything else, sourced from best-in-class systems — gray scale, semantic colors (red/green/yellow/blue), spacing, radius, border widths, elevation/shadow, opacity, motion, z-index, breakpoints. |

This is **foundation only**. Do NOT create semantic tokens, component tokens, aliases, styles, modes beyond one, or any other collection. **Touch only these two collections** — never modify or add anything else in the file.

## Hard guardrails (read first)

1. **Only ever create/modify `BrandPrimitives` and `App-Primitives`.** If a script would touch anything else (other collections, styles, nodes, pages, modes), stop — that is out of scope.
2. **Single mode.** Foundation primitives are raw values with one mode named `Value`. No light/dark, no aliasing here — so plan-based mode limits never apply.
3. **Idempotent, additive, skip-by-default.** If a collection exists, add only missing variables. If a variable already exists by name, **skip it and report** — do NOT overwrite. Only overwrite values when the user explicitly asks to update (`update mode`). Never rename an existing collection's mode.
4. **Brand guidelines win.** Any value specified in the brand guidelines takes precedence over the reference design systems for the same token.
5. **`use_figma` is the only write path.** Load the `figma-use` skill BEFORE any `use_figma` call, and include `figma-ds-foundation,figma-use` in the `skillNames` param. On any `use_figma` error, STOP and read it — scripts are atomic (nothing was written); fix, then retry.
6. **Confirm before writing.** Show the full build plan and get a go-ahead before the first write. Never invent a brand value silently — propose, then wait for confirmation.

## Required inputs — ask if missing

Before doing anything, make sure you have both. If either is absent, ask for it (use the interactive input tool for a clean prompt) and stop until provided:

1. **Figma page/file link** — identifies the target file. Note to the user: variables are *file-scoped*, not page-scoped, so this just tells us which file to write into.
2. **Brand guidelines** — accept ANY form: a Figma link, an image, a PDF, or plain text. Parse whatever is given (see `references/brand-guidelines.md`).

## Workflow

Run these phases in order. Details for each live in the reference files.

### Phase 0 — Inputs & target file
- Confirm both required inputs are present; otherwise ask and stop.
- Resolve the Figma link to a target file and **state it back to the user for confirmation** ("I'll write variables into file **X**"). Variables land at file level regardless of the page in the link.

### Phase 1 — Ingest & parse brand guidelines
Follow `references/brand-guidelines.md`:
- Read the guidelines from whatever format was provided (Figma link → read variables/styles/screens; image/PDF → extract; text → parse).
- Extract brand tokens: color ramp(s) with hex, and typography (families, weights, type scale, line-heights, letter-spacing if given).
- **Detect critical gaps** (e.g. colors given only as Pantone/CMYK with no sRGB hex; no neutral defined; no type scale; weights named but not numeric). For each gap, present a **specific suggestion** and **wait for explicit confirmation** before using it. Do not proceed on assumed values.

### Phase 2 — Verify canonical values & assemble the build plan
Follow `references/token-catalog.md`:
- The catalog defines the naming/grouping, types, scopes, and baseline values for every token group.
- **Web-fetch to confirm the drift-prone numeric sets** before building (Atlassian spacing/radius/border, Material 3 elevation + motion, Tailwind breakpoints). Use the baseline values only as a fallback if a fetch fails, and say so.
- Generate the **gray ramp** (pure neutral) and **semantic ramps** (red/green/yellow/blue) from an accessible source; run a WCAG contrast check (report later).
- Produce a written **build plan**: for each collection, the groups and the exact variable names + values + types + scopes. Show it and **get confirmation** before writing.

### Phase 3 — Discover existing state (idempotency)
Run the discovery script from `references/figma-scripts.md` (§Discover). It returns whether `BrandPrimitives` / `App-Primitives` exist and the set of variable names already in each. Use this to compute the "missing only" delta.

### Phase 4 — Build BrandPrimitives
Run the BrandPrimitives build script (`references/figma-scripts.md` §BrandPrimitives). Create the collection if missing; add only missing variables; skip+report existing. Return created/skipped IDs.

### Phase 5 — Build App-Primitives
Ensure the App-Primitives collection exists (once), then add its variables. Because there are ~150+ variables, build in logical chunks and validate after each (colors → spacing/radius/border → elevation → opacity/motion → z-index/breakpoints). Scripts in `references/figma-scripts.md` §App-Primitives. Skip+report existing.

### Phase 6 — Validate & report
Run the validation script (`references/figma-scripts.md` §Validate) and produce a final report:
- Collections touched (must be only the two), variable counts created vs skipped.
- Groups created, per collection.
- **WCAG contrast summary** for grays + semantics.
- Any brand gaps that were filled (with the confirmed suggested values).
- Reminder: primitives are given functional scopes so they're usable now; when a semantic layer is added later, primitives should be re-scoped to `[]` so designers bind only semantics.

## Key technical rules (baked into the scripts)

- **Colors are `{r,g,b,a}` in 0–1 range** (with alpha), not 0–255. Use the `hexToRgba` helper.
- **Figma groups come from `/` in the variable name** — e.g. `color/brand/primary` → group `color` › `brand`. Naming = grouping. Follow the catalog's names exactly.
- **Types:** COLOR (colors), FLOAT (spacing, radius, border, sizes, opacity, durations, z-index, breakpoints, shadow offsets/blur/spread), STRING (font family, easing curves).
- **Shadow is decomposed** into FLOAT primitives (`elevation/{level}/y|blur|spread`) + a COLOR (`elevation/{level}/color` with alpha). Figma has no shadow variable type, and effect styles are out of scope for foundation.
- **Scopes are always set explicitly** (never `ALL_SCOPES`). Primitives get *functional* scopes (usable now). z-index, breakpoints, motion durations get `[]` (reference-only — no Figma binding surface).
- **Tag every created variable** with `setSharedPluginData('ax-dsf','key', <name>)` for reliable idempotency on re-runs.
- **Always `return` created/mutated node & variable IDs** from each `use_figma` script.
- Optional (offer, off by default): set `setVariableCodeSyntax('WEB', 'var(--…)')` for downstream code pipelines.

## Reference files
- `references/token-catalog.md` — naming/grouping, types, scopes, baseline values, and the source URLs to verify.
- `references/figma-scripts.md` — copy-paste `use_figma` scripts: helpers, discover, build, validate (all idempotent).
- `references/brand-guidelines.md` — parsing any input format, gap detection, and the propose-then-confirm protocol.
