# Reviewing the base design system (Phase 2)

Every Figma component in this core will be implemented in code as a **thin wrapper over the base design system** the user named. This review guarantees that mapping exists *before* anything is drawn.

## 1. Fetch the real docs — never work from memory

Web-fetch the component index and the per-component pages for every Tier-1/Tier-2 manifest component:

| Base DS | Where |
|---|---|
| shadcn/ui | https://ui.shadcn.com/docs/components (per-component pages) |
| Material 3 | https://m3.material.io/components (specs tabs: anatomy, states, measurements) |
| Ant Design | https://ant.design/components/overview |
| Chakra UI | https://chakra-ui.com/docs/components |
| Other | Ask the user for the docs root, then fetch |

If fetching fails, say so and fall back to the catalog's M/S columns — but flag reduced confidence in the parity table.

## 2. Extract, per manifest component

- **Anatomy** — the parts the base DS defines (container, label, leading icon, supporting text…). The Figma layer structure should mirror these part names so Code Connect / wrappers are trivial.
- **Variant & prop API** — the real prop names and values (`variant="outline"`, `size="sm"`). Figma variant properties should use the same *concepts* (they may keep the catalog's Title-case names, but the parity table records the exact prop mapping).
- **States** — which states the base DS implements natively (hover, focus-visible, disabled, loading…). Don't design a state the base DS can't produce without noting the wrapper work.
- **Sizes & metrics** — control heights, paddings. Where the direction's density differs, the token layer absorbs it (that's what wrappers override); note any metric the base DS hardcodes.

## 3. Produce the parity table (goes into the ds-core spec)

One row per manifest component:

| Figma set | Base-DS component | Prop mapping | Gaps / wrapper notes |
|---|---|---|---|
| `Button` | shadcn `button` | Tone→variant (Primary→default, Secondary→outline, Ghost→ghost, Danger→destructive); Size→size (Sm→sm…) | Loading state needs wrapper spinner |

## 4. Gap protocol (guardrail 4)

When a manifest/direction component has **no base-DS equivalent**, choose with the user, per component:
1. **Composite wrapper** — build from base-DS primitives (preferred; note the recipe),
2. **Adjust the design** — only if the direction spec permits,
3. **Documented custom component** — last resort; record the full build cost in the spec.

Never silently design something un-implementable, and never drop the component to avoid the question.
