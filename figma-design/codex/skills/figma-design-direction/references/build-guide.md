# Build Guide — beautiful, flat, token-bound frames in Figma

> Part of `figma-design-direction`. Used in Phase 6. This guide adds **direction-specific** build patterns on top of the general Figma write skills. It does not restate the Plugin API — for authoritative, current `use_figma` syntax, **load `figma-use` and `figma-generate-design` first** and pass them in `skillNames`. Snippets here are **patterns to adapt**, not copy-paste truth.

## Prerequisites

- Load `figma-use` (how to call `use_figma`) and `figma-generate-design` (how to assemble a page section-by-section). Include `figma-design-direction,figma-use,figma-generate-design` in `skillNames`.
- Write plain JS with top-level `await`/`return`; no IIFE, no `figma.closePlugin()`.
- On any error: **STOP, read it, fix, retry** — scripts are atomic, nothing partial was written.
- **Only touch this run's `Design_Direction_<name>` page** (you create it in §Page setup). Never modify variables, styles, other pages, or components.

## The two golden rules

1. **Everything is an auto-layout frame.** No absolute-positioned children except deliberate overlays (menus, toasts, modals). Auto-layout is what makes the design token-driven, reflowable, and consistent.
2. **Everything visual is bound to a primitive variable.** Fills, strokes, radius, spacing, padding, type size/line-height, and shadows all reference variables — never raw numbers or hex. If you catch yourself typing a hex or a px, stop: find the primitive, or flag the gap.

## Page setup (a new page per run — except Mode B)

- **[Mode A — new direction] Create a new page named `Design_Direction_<direction name>`** — the `<direction name>` is exactly the one in the Design Decisions Brief and the spec title (e.g. `Design_Direction_Editorial & Calm`). One run → one page. Set it as the working page and build everything there.
- **[Mode B — add screens to an existing direction] Do NOT create a page.** Find the existing `Design_Direction_<name>` page (name given by the user), set it as the working page, and **append** the new screens after the existing ones in flow order, numbered to continue the sequence. Reuse the base frames, variable bindings, and page-local motion components already on that page so the additions are pixel-consistent; copy an existing shared element (nav bar, header, card) rather than rebuilding it. Leave existing screens untouched.
- Prior alternatives live on their own `Design_Direction_*` pages; read them for divergence, never edit them. If a page with this exact name already exists (a Mode A re-run of the same direction), ask the user whether to replace it or add a version suffix — or whether they actually meant Mode B (add screens to it).
- Start the page with a **cover / legend frame**:
  - Direction **name + one-line thesis**.
  - The key tokens this direction leans on (primary color, surface ramp, type family + scale, radius, elevation levels used, icon family).
  - Platform, date, and which screens/states follow.
- Then the screens in a readable row, in flow order, each a titled frame (`01 · Dashboard`, `02 · <Flow> — Step 1`, etc.).

## Frame sizes per platform

| Platform | Screen frame | Content max-width | Notes |
|---|---|---|---|
| Desktop | 1440 × (as tall as content) | ~1120–1280 centered | dashboards/admin; denser grids |
| Mobile | 390 × 844 (iPhone) or 360 × 800 (Android) | full-bleed with side padding from spacing tokens | single column, thumb-reachable primary actions |
| Tablet (if asked) | 834 × 1194 | ~720–800 | between the two |

Use a **column grid** on each screen frame (e.g. desktop 12-col, mobile 4-col) with margins/gutters from spacing tokens; state the grid in the brief and spec.

## Binding patterns (adapt via `figma-use`)

Resolve variables by name once, then reuse. Build a lookup from the foundation you inventoried in Phase 1.

```javascript
// Resolve primitives by name -> variable (build once)
const all = await figma.variables.getLocalVariablesAsync();
const V = {}; for (const v of all) V[v.name] = v;      // e.g. V['color/brand/primary'], V['spacing/300']
const byName = n => { const v = V[n]; if (!v) throw new Error('Missing primitive: ' + n); return v; };
```

**Fill / background (bind color):**
```javascript
const paint = { type: 'SOLID', color: { r: 0, g: 0, b: 0 } };      // color overwritten by the binding
node.fills = [ figma.variables.setBoundVariableForPaint(paint, 'color', byName('color/gray/50')) ];
```

**Stroke / border (bind color + width):**
```javascript
const stroke = { type: 'SOLID', color: { r: 0, g: 0, b: 0 } };
node.strokes = [ figma.variables.setBoundVariableForPaint(stroke, 'color', byName('color/gray/200')) ];
node.setBoundVariable('strokeWeight', byName('border/width/default'));   // or strokeTopWeight/… per side
```

**Corner radius (bind each corner, or the uniform field your API version exposes):**
```javascript
for (const f of ['topLeftRadius','topRightRadius','bottomLeftRadius','bottomRightRadius'])
  node.setBoundVariable(f, byName('radius/lg'));
```

**Spacing — padding & gap (bind, never type numbers):**
```javascript
frame.layoutMode = 'VERTICAL';                       // or HORIZONTAL
frame.setBoundVariable('itemSpacing',   byName('spacing/200'));        // gap between children
frame.setBoundVariable('paddingTop',    byName('spacing/300'));
frame.setBoundVariable('paddingBottom', byName('spacing/300'));
frame.setBoundVariable('paddingLeft',   byName('spacing/400'));
frame.setBoundVariable('paddingRight',  byName('spacing/400'));
```

**Typography — load the font, then bind size/line-height (bind family/weight if supported):**
```javascript
await figma.loadFontAsync({ family: 'Inter', style: 'Semibold' });    // MUST load before characters/size
const t = figma.createText();
t.fontName = { family: 'Inter', style: 'Semibold' };
t.characters = 'Real heading text';
t.setBoundVariable('fontSize',   byName('font/size/xl'));
t.setBoundVariable('lineHeight', byName('font/line-height/tight'));    // if the foundation defines it
// Bind fontFamily/fontWeight to primitives too if your API version supports those fields (see figma-use).
t.fills = [ figma.variables.setBoundVariableForPaint({ type:'SOLID', color:{r:0,g:0,b:0} }, 'color', byName('color/gray/900')) ];
```

**Elevation — assemble a shadow from the DECOMPOSED elevation primitives** (the foundation stores `elevation/{level}/y|blur|spread|color` because Figma has no shadow variable type):
```javascript
let e = { type: 'DROP_SHADOW', visible: true, blendMode: 'NORMAL',
          color: {r:0,g:0,b:0,a:0.15}, offset: {x:0,y:0}, radius: 0, spread: 0 };
e = figma.variables.setBoundVariableForEffect(e, 'color',   byName('elevation/2/color'));
e = figma.variables.setBoundVariableForEffect(e, 'offsetY', byName('elevation/2/y'));
e = figma.variables.setBoundVariableForEffect(e, 'radius',  byName('elevation/2/blur'));
e = figma.variables.setBoundVariableForEffect(e, 'spread',  byName('elevation/2/spread'));
node.effects = [e];
```

Always `return` the created frame/node IDs from each script so you can screenshot and validate.

## Color roles without a semantic layer

There's no semantic collection yet, so **you** hold the mapping in your head and in the spec. Decide once, at the brief:
- **Background** → e.g. `color/gray/50` · **Surface/card** → `color/white` or `color/gray/50` · **Raised/overlay** → white + elevation.
- **Text primary** → `color/gray/900` · **secondary** → `color/gray/600` · **disabled** → `color/gray/400`.
- **Border** → `color/gray/200`.
- **Primary action** → `color/brand/primary` (+ its darker step for hover/press).
- **Success / Warning / Danger / Info** → the semantic ramps (`color/green|yellow|red|blue`).

Use these **identically on every screen**. Record the exact map in the spec's *Recommended semantic mapping* — that's the handoff to the rollout skill.

## Icons

- Pick **one icon family** (e.g. a consistent open set) and use it for the whole direction — consistent grid size and stroke weight.
- Import icons as vectors/components from a library or paste SVG; size them from spacing tokens; color them with a bound fill.
- Pair icons with labels wherever meaning isn't obvious.

## Real content

- Write realistic labels, names, numbers, dates, and states from the requirement context — never lorem, never "Button". Real content is a design tool: it exposes truncation, alignment, and density problems that placeholders hide.

## Consistency without components (mostly)

Static repeats rely on **discipline**, not components. The only components in a direction are the minimal page-local ones you build for motion (see §Motion & prototyping).
- Build each repeated element (button, input, card, nav item, table row) **once, perfectly, bound**. Then **copy that base frame** for every repeat — never rebuild from scratch.
- Keep repeats **pixel-identical**: same tokens, same padding, same radius. If you change the base, propagate to all copies.
- Keep a short running note of each element's exact tokens (this becomes the spec's informal component inventory).
- For an element you'll animate (button, nav item, modal), build it **once as a component with variants** for its states instead of copies — that's what Smart Animate needs.

## States to draw

- **Every screen:** its default, populated state.
- **The one complex journey only:** also draw **empty, loading, and error** states, and show the **interaction states** of the core controls (default / hover / focus-visible / active / disabled; input error). A compact "states" strip near the flow is enough — it establishes the visual language without exploding scope.
- The interaction states you draw here double as the **variants** you animate in §Motion & prototyping.

## Motion & prototyping (Phase 6.5)

A design direction must **show how it moves**, not just how it looks. Demonstrate the motion language as a Figma prototype on a **representative sample** — don't animate everything. This is **Figma Prototype** motion — each interaction is a node **`reaction`** (trigger + action + transition) that you set through `use_figma` (`setReactionsAsync`); the **`figma-use`** skill has the exact `reactions` shape. `figma-use-motion` is only for optional in-node timeline micro-animations; `figma-implement-motion` (design → code) is **not** used here.

**What to animate (the sample):**
- **Button** — default → hover → pressed.
- **Navigation** — nav-item hover + active state, and screen-to-screen navigation.
- **Popup / modal** — overlay open and close.
- **Screen transitions** — the steps of the complex journey.

**How:**
- Build each interactive element **once as a component with variants** (one variant per state), on this run's `Design_Direction_<name>` page. Keep them minimal and **never publish** them.
- Animate between variants with **Smart Animate** (`While hovering` → hover variant, `On press` → pressed variant, etc.).
- For popups / modals, use an **overlay** interaction (`On click` → Open overlay, move-in / dissolve; `On click outside` → Close).
- For screen transitions, connect frames with `On click` → **Navigate to** → **Smart Animate** (or Move-in / Dissolve to match the personality).
- Each of these is a **`reaction`** on the node (trigger → action → transition, with duration + easing); write them via `use_figma` (`setReactionsAsync`). They show up in Figma's **Prototype** panel and play in Present mode.

**Timing — stay on-system:**
- Read values from the **motion tokens** (`motion/duration/*`, `motion/easing/*`) and apply them to each interaction. These tokens are **reference-only** (scope `[]`) — Figma can't *bind* a transition to a variable, so you copy the value in. Same element type → same duration / easing everywhere.
- Most UI transitions: **100–300 ms**. Keep motion **subtle** (fade / slide / small scale), never bounce / spin. Every animation must communicate a state change.

**Scope guardrails:**
- Page-local, unpublished components only, limited to the sample above. No component system, no library, no publishing — the rollout skill does that.
- Record the motion character (durations, easing, what animates, reduced-motion note) in the spec §11.

## Incremental assembly

Build one section at a time (nav → header → primary content block → secondary → footer). After each section: `get_screenshot`, eyeball against the brief, and confirm bindings resolved. Don't build a whole screen blind and validate at the end.

## Divergence between alternatives

Before building, re-read `look-and-feel.md`'s **divergence levers**. This alternative must differ from prior ones in **at least the nav pattern + one of {composition, density, type personality}** — realized through arrangement and emphasis of the **same** primitives, never off-brand values. The page's cover frame should name the lever choices so the alternatives are easy to compare.

## Common failure modes to avoid

- Hardcoded hex/px sneaking in "just this once" — it breaks the whole premise. Bind or flag.
- Absolute positioning instead of auto-layout — the design won't hold together or reflow.
- Building repeats independently and letting them drift.
- Skipping font loading (text creation fails or falls back to a wrong font).
- Over-decorating to look "designed" — restraint reads as more premium than ornament.
- Motion scope-creep — animating every element or building a component library. Keep motion to the representative sample; components page-local and unpublished.
