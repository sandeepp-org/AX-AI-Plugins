# Brand Guidelines — ingest, extract, fill gaps

> Part of `figma-ds-foundation`. Goal: turn whatever the user gives you into a clean set of **brand color** + **typography** tokens for `BrandPrimitives`, flag anything critical that's missing, and never use an invented value without explicit confirmation.

## Accept any input format

| Input | How to read it |
|---|---|
| **Figma link** | Load the `figma-use` skill and read the file: existing variables (`getLocalVariablesAsync`), paint/text styles, and any brand/logo/typography frames. Extract hex from fills and font family/size/weight from text styles. |
| **Image** (PNG/JPG of a brand sheet) | It's visible in context — read colors, hex codes, font names, and the type scale directly from it. |
| **PDF** | Use the pdf-reading skill / extract text + rasterize pages to read swatches, hex values, font specs. |
| **Plain text** | Parse directly for hex codes, color names, font families, weights, and sizes. |

Normalize everything to: a list of `{name, hex}` brand colors and a typography set (families, weights, sizes, optional line-heights/letter-spacing). Map hex → `{r,g,b,a}` later via `hexToRgba`.

## Naming the brand tokens

- Single flat brand colors → `color/brand/{role}` (e.g. `color/brand/primary`, `color/brand/secondary`, `color/brand/accent`).
- A brand ramp → `color/brand/{hue}/{step}` (e.g. `color/brand/primary/600`).
- Keep the guidelines' own names where sensible; otherwise use the roles above and show the mapping in the plan.

## Critical-gap checklist — detect, propose, CONFIRM

For each gap, present a **specific proposed value** and **wait for a yes** before using it. Do not proceed on assumptions.

1. **Colors not in sRGB hex** (only Pantone/CMYK/RGB-255): Figma COLOR needs 0–1 sRGB. Propose the hex conversion for each and confirm. (255 RGB → divide by 255; Pantone/CMYK → propose closest sRGB hex and flag it as approximate.)
2. **No neutral/gray defined**: gray lives in App-Primitives, but ask whether the neutral should be **pure** or **tinted toward the brand hue** (default: pure). Confirm.
3. **No type scale**: propose a modular scale (e.g. 12/14/16/18/20/24/30/36/48) and confirm before creating `font/size/*`.
4. **Weights named but not numeric** ("Semibold"): propose Regular 400 / Medium 500 / Semibold 600 / Bold 700 and confirm.
5. **Only one brand hue given**: propose a 50–900 ramp built around it (tints/shades) and confirm, or keep it as a single `color/brand/primary`.
6. **Font family not available in Figma**: note it; the STRING variable can still store the name, but flag that text binding needs the font installed.
7. **Alpha/overlay brand colors**: capture alpha into the `a` channel (`hexToRgba` handles `#RRGGBBAA`).

## Precedence rule

Whenever the brand guidelines specify a value that also appears in the reference systems (e.g. brand defines its own spacing or radius), **the brand value wins**. Note the override in the build plan so it's visible.

## Output of this phase

A confirmed structure ready to drop into the BrandPrimitives build script's `BRAND` object:
```
colors:        [{ name, hex[, alpha] }]
families:      [{ name: 'font/family/…', value }]
weights:       [{ name: 'font/weight/…', value }]
sizes:         [{ name: 'font/size/…',   value }]
lineHeights:   [{ name: 'font/line-height/…', value }]   // only if defined
letterSpacing: [{ name: 'font/letter-spacing/…', value }] // only if defined
```
Plus a short list of every gap that was filled and the confirmed value used.
