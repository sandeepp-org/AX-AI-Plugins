# Semantic + Component Token Catalog — NORMATIVE MANIFEST

This file is a **manifest, not a menu**. Every row below MUST exist in Figma at the end of Phase 4, either created this run or already present (skip+report). The validation script diffs the file against these tables; any MISSING item blocks progress unless the user waives it by name.

**Manifest totals (validation targets):**

| Collection / kind | Count |
|---|---|
| `Semantics` variables | **187** |
| `ComponentTokens` variables | **60** |
| Text styles | **12** |
| Effect styles | **3** |

Conventions:
- Both collections have exactly **one mode: `Default`**. Every variable is an **alias** (`setValueForMode` with `{type:'VARIABLE_ALIAS'}`) — never a raw value.
- **Alias target** column: a concrete primitive name = default target. **`DIRECTION`** = the design direction spec (§3 semantic mapping, §5–7) decides the target; the listed value in parentheses is the fallback if the spec is silent (state when you fall back). Primitive ramp steps (e.g. `gray/100`) refer to the actual foundation ramps — verify names in Phase 0 and adapt step numbers to the ramp that exists.
- **Scopes** are set explicitly per the Scope column. ComponentTokens inherit the same scope rules by type.
- Names use `/` grouping exactly as written.

## Collection `Semantics`

### color/surface — 7 · scope `["FRAME_FILL","SHAPE_FILL"]`

| Variable | Alias target |
|---|---|
| `color/surface/page` | DIRECTION (`color/gray/50` or white step) |
| `color/surface/raised` | DIRECTION (white / `gray/0`) |
| `color/surface/sunken` | DIRECTION (`color/gray/100`) |
| `color/surface/overlay` | same as raised unless DIRECTION says otherwise |
| `color/surface/inverse` | `color/gray/900` |
| `color/surface/brand-subtle` | DIRECTION (`color/brand/primary/50`) |
| `color/surface/disabled` | `color/gray/100` |

### color/text — 9 · scope `["TEXT_FILL"]`

| Variable | Alias target |
|---|---|
| `color/text/primary` | DIRECTION (`color/gray/900`) |
| `color/text/secondary` | `color/gray/700` |
| `color/text/tertiary` | `color/gray/500` |
| `color/text/disabled` | `color/gray/400` |
| `color/text/placeholder` | `color/gray/400` |
| `color/text/inverse` | `color/gray/50` |
| `color/text/on-brand` | DIRECTION (white/`gray/0` — must pass AA on action bg) |
| `color/text/link` | DIRECTION (`color/brand/primary/600`) |
| `color/text/link-hover` | one step darker than link |

### color/icon — 5 · scope `["SHAPE_FILL","TEXT_FILL"]`

`color/icon/default` → `gray/700` · `color/icon/subtle` → `gray/500` · `color/icon/disabled` → `gray/400` · `color/icon/inverse` → `gray/100` · `color/icon/brand` → DIRECTION (brand primary step)

### color/border — 6 · scope `["STROKE_COLOR"]`

`color/border/default` → `gray/300` · `color/border/subtle` → `gray/200` · `color/border/strong` → `gray/400` · `color/border/disabled` → `gray/200` · `color/border/focus` → DIRECTION (brand primary step, AA against page) · `color/border/inverse` → `gray/700`

### color/action — 21 · scope `["FRAME_FILL","SHAPE_FILL","TEXT_FILL","STROKE_COLOR"]`

| Group | Variables | Alias targets |
|---|---|---|
| `color/action/primary/` | `bg`, `bg-hover`, `bg-pressed`, `bg-disabled`, `text`, `text-disabled` | DIRECTION: brand step for `bg`; hover/pressed = ±1/±2 ramp steps; disabled bg → `gray/200`; text → `color/text/on-brand` target; text-disabled → `gray/400` |
| `color/action/secondary/` | `bg`, `bg-hover`, `bg-pressed`, `border`, `border-hover`, `text`, `text-disabled` | DIRECTION: bg = surface/raised target; hover/pressed = `gray/50`/`gray/100`; border = `gray/300`; text = `text/primary` target |
| `color/action/ghost/` | `bg-hover`, `bg-pressed`, `text`, `text-disabled` | hover `gray/100`, pressed `gray/200`, text = DIRECTION (brand or text/primary) |
| `color/action/danger/` | `bg`, `bg-hover`, `bg-pressed`, `text` | `red/600`, `red/700`, `red/800`, white step |

### color/status — 24 · scope `["FRAME_FILL","SHAPE_FILL","TEXT_FILL","STROKE_COLOR"]`

For each tone `{success→green, warning→yellow, danger→red, info→blue}` create 6:
`color/status/<tone>/bg-subtle` (ramp 50/100) · `text` (700+, AA on bg-subtle) · `border` (300/400) · `icon` (600) · `bg-bold` (600; warning may need 400/500 + dark text — check AA) · `text-on-bold` (white step, or `gray/900` on warning).

### color/selected — 4 · scope `["FRAME_FILL","SHAPE_FILL","TEXT_FILL","STROKE_COLOR"]`

`color/selected/bg` → DIRECTION (`brand/primary/50`) · `bg-hover` (one step darker) · `text` (brand 700) · `border` (brand 600)

### color/focus + color/overlay — 2

`color/focus/ring` → same target as `border/focus` · scope stroke ∪ effect-color. `color/overlay/scrim` → DIRECTION (`gray/900` — note: apply at `opacity/scrim` or use an alpha primitive if the foundation has one) · scope frame-fill.

### typography — 51 · scopes: family `["FONT_FAMILY"]`, size `["FONT_SIZE"]`, weight `["FONT_WEIGHT"]`, line-height `["LINE_HEIGHT"]`, letter-spacing `["LETTER_SPACING"]`

12 roles: `display`, `h1`, `h2`, `h3`, `h4`, `body-lg`, `body`, `body-sm`, `label`, `caption`, `overline`, `code`.
Each role gets 4 variables — `typography/<role>/family|size|weight|line-height` — aliasing `font/*` primitives per the DIRECTION's type pairing and scale usage (§5). Plus 3 letter-spacing variables: `typography/display/letter-spacing`, `typography/overline/letter-spacing`, `typography/caption/letter-spacing` (alias `font/letter-spacing/*`). `code/family` needs a mono primitive — if none exists, flag (guardrail 2). 12×4 + 3 = **51**.

### space — 17 · scope `["GAP","WIDTH_HEIGHT"]`

| Group | Variables | Default targets (`spacing/*`) |
|---|---|---|
| `space/inset/` | `xs, sm, md, lg, xl` | 4, 8, 12, 16, 24 — DIRECTION density may shift one step |
| `space/stack/` | `xs, sm, md, lg, xl` | 4, 8, 12, 16, 24 |
| `space/inline/` | `xs, sm, md, lg` | 4, 8, 12, 16 |
| `space/layout/` | `page-margin, gutter, section-gap` | DIRECTION (§4 grid) |

### radius — 5 · scope `["CORNER_RADIUS"]`

`radius/control-sm`, `radius/control`, `radius/surface`, `radius/overlay`, `radius/pill` — all DIRECTION (§7 radius personality) aliasing `radius/*` primitives (`pill` → the max/full primitive).

### border-width — 3 · scope `["STROKE_FLOAT"]`

`border-width/default` → `border/width` 1 · `border-width/strong` → 2 · `border-width/focus` → 2.

### elevation — 12 · scopes: y/blur/spread `["EFFECT_FLOAT"]`, color `["EFFECT_COLOR"]`

3 roles × 4: `elevation/raised/{y,blur,spread,color}`, `elevation/overlay/{…}`, `elevation/modal/{…}` — aliasing the DIRECTION's chosen primitive elevation levels (§7 elevation strategy; fallback level-1/level-3/level-5 equivalents).

### size — 6 · scope `["WIDTH_HEIGHT"]`

`size/control/sm|md|lg` → DIRECTION density (defaults 32, 40, 48 via `spacing/*`; if no matching primitive exists, flag) · `size/icon/sm|md|lg` → 16, 20, 24.

### motion — 7 · scope `[]` (reference-only)

`motion/duration/fast|base|slow|exit` and `motion/easing/standard|enter|exit` — aliasing the `motion/*` primitives the DIRECTION's §11 motion character uses.

### opacity — 2 · scope `["OPACITY"]`

`opacity/disabled` → `opacity/*` ≈0.4 · `opacity/scrim` → `opacity/*` ≈0.5.

### z — 6 · scope `[]` (reference-only)

`z/dropdown`, `z/sticky`, `z/overlay`, `z/modal`, `z/popover`, `z/toast` → matching `z-index/*` primitives.

> **Count check:** 7+9+5+6+21+24+4+2+51+17+5+3+12+6+7+2+6 = **187**. Validation MUST recompute by enumerating every row above (not trust this arithmetic) and compare that enumeration against the variables actually present in the file.

## Collection `ComponentTokens` — 60 · aliases into `Semantics` only

Per confirmed scope decision (Option B): component tokens exist **only** for the 8 high-theming-surface components. Everything else binds semantics directly.

| Group | Variables (count) |
|---|---|
| `button/` (8) | `height-sm`→`size/control/sm`, `height-md`, `height-lg`, `pad-x-sm`→`space/inset/sm`, `pad-x-md`, `pad-x-lg`, `gap`→`space/inline/sm`, `radius`→`radius/control` |
| `input/` (12) | `height`→`size/control/md`, `pad-x`→`space/inset/md`, `radius`→`radius/control`, `bg`→`surface/raised`, `border`→`border/default`, `border-hover`→`border/strong`, `border-focus`→`border/focus`, `border-error`→`status/danger/border`, `text`→`text/primary`, `placeholder`→`text/placeholder`, `gap-label`→`space/stack/xs`, `gap-help`→`space/stack/xs` |
| `card/` (5) | `bg`→`surface/raised`, `border`→`border/subtle`, `radius`→`radius/surface`, `inset`→`space/inset/lg`, `gap`→`space/stack/md` |
| `nav/` (11) | `width`→DIRECTION sidebar width (via spacing/size), `item-height`→`size/control/md`, `item-radius`→`radius/control`, `item-gap`→`space/stack/xs`, `bg`→DIRECTION (surface/page|raised|inverse), `item-text`→`text/secondary`, `item-text-active`→DIRECTION (`selected/text` or `text/primary`), `item-bg-hover`→`action/ghost/bg-hover`, `item-bg-selected`→`selected/bg`, `item-indicator`→`icon/brand`, `inset`→`space/inset/sm` |
| `table/` (8) | `header-bg`→`surface/sunken` (DIRECTION), `header-text`→`text/secondary`, `row-height-compact`→`size/control/sm`, `row-height-default`→`size/control/md`, `row-border`→`border/subtle`, `row-bg-hover`→`action/ghost/bg-hover`, `row-bg-selected`→`selected/bg`, `cell-pad-x`→`space/inset/md` |
| `modal/` (7) | `bg`→`surface/overlay`, `radius`→`radius/overlay`, `inset`→`space/inset/xl`, `max-w-sm`→`breakpoint`-derived or flagged, `max-w-md`, `max-w-lg`, `scrim`→`color/overlay/scrim` |
| `badge/` (3) | `height` (20 via spacing), `pad-x`→`space/inset/xs`, `radius`→DIRECTION (`radius/pill` or `control-sm`) |
| `tabs/` (6) | `height`→`size/control/md`, `gap`→`space/inline/lg`, `indicator-color`→`icon/brand` (DIRECTION), `indicator-thickness`→`border-width/strong`, `text`→`text/secondary`, `text-active`→`text/primary` |

`modal/max-w-*`: Figma FLOAT aliases must point at primitives — use `breakpoint/*` or flag for a foundation addition; never hardcode.

## Styles (created from semantics)

- **12 text styles** — `DS/<Role>` (`DS/Display`, `DS/H1` … `DS/Code`), each with family/size/weight/line-height (and letter-spacing where defined) **bound to the `typography/<role>/*` variables**.
- **3 effect styles** — `DS/Elevation/Raised|Overlay|Modal`, drop shadows composed from the `elevation/<role>/*` variables (y/blur/spread/color bound).

## Direction Resolution Table (produced in Phase 1, approved in Phase 4)

Before building, output a table with one row per `DIRECTION`-marked target above: variable → resolved primitive → source (spec §, or "fallback"). This table is part of the build plan the user approves and is copied into the ds-core spec.
