# Token Catalog — names, groups, types, scopes, baseline values

> Part of `figma-ds-foundation`. This is the source of truth for **what** to create and **how to name it**. Naming drives Figma grouping (`/` = group nesting). **Brand guidelines override any value here** for the same token.
>
> **Verify the drift-prone numeric sets by web-fetch before building** (marked ⟳). Use the baseline values below only as a fallback if the fetch fails — and tell the user you fell back.

## Collection & group layout

```
BrandPrimitives
  color/brand/*            COLOR    ← brand guidelines
  font/family/*            STRING   ← brand guidelines
  font/weight/*            FLOAT    ← brand guidelines (numeric weight)
  font/size/*              FLOAT    ← brand type scale (or proposed + confirmed)
  font/line-height/*       FLOAT    ← optional, if brand specifies
  font/letter-spacing/*    FLOAT    ← optional, if brand specifies

App-Primitives
  color/gray/*             COLOR    ← generated neutral ramp
  color/red|green|yellow|blue/*   COLOR  ← accessible ramps
  spacing/*                FLOAT  ⟳ ← Atlassian
  radius/*                 FLOAT  ⟳ ← Atlassian
  border/width/*           FLOAT  ⟳ ← Atlassian
  elevation/{level}/*      FLOAT+COLOR ⟳ ← Material 3 (decomposed)
  opacity/*                FLOAT    ← standard scale
  motion/duration/*        FLOAT  ⟳ ← Material 3
  motion/easing/*          STRING ⟳ ← Material 3 (cubic-bezier)
  z-index/*                FLOAT    ← standard layer stack
  breakpoint/*             FLOAT  ⟳ ← Tailwind
```

## Scope map (functional / usable-now)

| Token group | Type | Scopes |
|---|---|---|
| `color/*` (brand, gray, semantic) | COLOR | `["FRAME_FILL","SHAPE_FILL","TEXT_FILL","STROKE_COLOR"]` |
| `elevation/*/color` | COLOR | `["EFFECT_COLOR"]` |
| `font/family/*` | STRING | `["FONT_FAMILY"]` |
| `font/weight/*` | FLOAT | `["FONT_WEIGHT"]` |
| `font/size/*` | FLOAT | `["FONT_SIZE"]` |
| `font/line-height/*` | FLOAT | `["LINE_HEIGHT"]` |
| `font/letter-spacing/*` | FLOAT | `["LETTER_SPACING"]` |
| `spacing/*` | FLOAT | `["GAP","WIDTH_HEIGHT"]` |
| `radius/*` | FLOAT | `["CORNER_RADIUS"]` |
| `border/width/*` | FLOAT | `["STROKE_FLOAT"]` |
| `elevation/*/y|blur|spread` | FLOAT | `["EFFECT_FLOAT"]` |
| `opacity/*` | FLOAT | `["OPACITY"]` |
| `motion/duration/*` | FLOAT | `[]` (reference-only) |
| `motion/easing/*` | STRING | `[]` (reference-only) |
| `z-index/*` | FLOAT | `[]` (reference-only) |
| `breakpoint/*` | FLOAT | `[]` (reference-only) |

> Note to surface in the final report: these functional scopes make primitives bindable now. When a semantic layer is added later, re-scope primitives to `[]` so designers bind only semantic tokens.

---

## BrandPrimitives (from brand guidelines)

`color/brand/*` — one variable per brand color / brand ramp step, names taken from the guidelines (e.g. `color/brand/primary`, `color/brand/primary/600`, `color/brand/accent`). Values = sRGB hex → `hexToRgba`. If only a single brand hue is given, propose a 50–900 ramp built around it and confirm before creating.

**Typography** — from the brand type scale. If the brand names weights ("Semibold") but not numbers, propose the numeric mapping (Regular 400, Medium 500, Semibold 600, Bold 700) and confirm. If no type scale exists, propose a modular scale (e.g. 12/14/16/18/20/24/30/36/48) and confirm.

- `font/family/sans`, `font/family/mono` (+ `serif`/`display` if brand has them) — STRING, e.g. `"Inter"`.
- `font/weight/{regular|medium|semibold|bold|…}` — FLOAT (400/500/600/700).
- `font/size/{xs|sm|md|lg|xl|2xl|…}` — FLOAT px from the brand scale.
- `font/line-height/*`, `font/letter-spacing/*` — FLOAT, only if the brand specifies them.

---

## App-Primitives

### color/gray/* — generated neutral ramp
Pure-neutral ramp (confirm brand-tinted vs pure with the user if unsure; default pure). Steps `50,100,200,300,400,500,600,700,800,900,950`. Baseline (neutral):
```
50 #FAFAFA  100 #F5F5F5  200 #E5E5E5  300 #D4D4D4  400 #A3A3A3
500 #737373 600 #525252  700 #404040  800 #262626  900 #171717  950 #0A0A0A
```

### color/{red|green|yellow|blue}/* — accessible semantic ramps
Steps `50…900`. Prefer a WCAG-checked source (Radix or Tailwind) fetched at build; run a contrast check (text on white/black). Baseline (Tailwind, verify):
```
red   500 #EF4444   600 #DC2626   700 #B91C1C   (…50–900)
green 500 #22C55E   600 #16A34A   700 #15803D
yellow500 #EAB308   600 #CA8A04   700 #A16207
blue  500 #3B82F6   600 #2563EB   700 #1D4ED8
```
Generate the full 50–900 for each from the chosen source; don't hand-pick only three steps in the real build.

### spacing/* ⟳ Atlassian
Base-8 scale with 2px quarter-step. Baseline:
```
0=0  25=2  50=4  75=6  100=8  150=12  200=16  250=20  300=24
400=32  500=40  600=48  800=64  1000=80
```
Verify: https://atlassian.design/foundations/spacing

### radius/* ⟳ Atlassian
Baseline:
```
none=0  sm=2  md=4  lg=8  xl=12  2xl=16  full=9999
```
Verify: https://atlassian.design/foundations/shape (border-radius tokens)

### border/width/* ⟳ Atlassian
Baseline:
```
none=0  default=1  thick=2  thicker=3
```
Verify: https://atlassian.design/foundations/shape (border-width tokens)

### elevation/* ⟳ Material 3 (DECOMPOSED — no shadow variable type exists)
Levels `0–5`. Level 0 = no shadow. For each level 1–5 create the decomposed primitives so a composite effect can be assembled later in the semantic layer:
```
elevation/{level}/y       FLOAT  (offset-Y, px)
elevation/{level}/blur     FLOAT  (px)
elevation/{level}/spread   FLOAT  (px)
elevation/{level}/color    COLOR  (with alpha, from hexToRgba)
```
M3 uses two shadow layers (key + ambient); if you keep both, suffix `/key/*` and `/ambient/*`. Baseline single-layer approximation (verify + refine from source):
```
level0: none
level1: y=1  blur=3  spread=1  color=#00000026 (0.15a)
level2: y=2  blur=6  spread=2  color=#00000026
level3: y=4  blur=8  spread=3  color=#0000002E (0.18a)
level4: y=6  blur=10 spread=4  color=#0000002E
level5: y=8  blur=12 spread=6  color=#00000033 (0.20a)
```
Verify: https://m3.material.io/styles/elevation/overview

### opacity/* — standard scale (FLOAT 0–1)
```
0=0  5=0.05  10=0.1  20=0.2  40=0.4  60=0.6  80=0.8  90=0.9  100=1
```

### motion/duration/* ⟳ Material 3 (FLOAT, ms)
```
short-1=50  short-2=100 short-3=150 short-4=200
medium-1=250 medium-2=300 medium-3=350 medium-4=400
long-1=450 long-2=500 long-3=550 long-4=600
extra-long-1=700 extra-long-2=800 extra-long-3=900 extra-long-4=1000
```
Verify: https://m3.material.io/styles/motion/easing-and-duration/tokens-specs

### motion/easing/* ⟳ Material 3 (STRING, cubic-bezier)
```
linear=cubic-bezier(0,0,1,1)
standard=cubic-bezier(0.2,0,0,1)
standard-decelerate=cubic-bezier(0,0,0,1)
standard-accelerate=cubic-bezier(0.3,0,1,1)
emphasized=cubic-bezier(0.2,0,0,1)          (approx — M3 emphasized is a 2-part curve; note the approximation)
emphasized-decelerate=cubic-bezier(0.05,0.7,0.1,1)
emphasized-accelerate=cubic-bezier(0.3,0,0.8,0.15)
```
Verify: same M3 tokens-specs page.

### z-index/* — standard layer stack (FLOAT, reference-only)
```
hide=-1  base=0  raised=10  dropdown=1000  sticky=1100  banner=1200
overlay=1300  modal=1400  popover=1500  skip-link=1600  toast=1700  tooltip=1800
```

### breakpoint/* ⟳ Tailwind (FLOAT px, reference-only)
```
sm=640  md=768  lg=1024  xl=1280  2xl=1536
```
Verify: https://tailwindcss.com/docs/responsive-design
