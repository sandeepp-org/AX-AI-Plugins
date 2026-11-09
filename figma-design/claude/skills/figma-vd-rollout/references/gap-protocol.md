# Gap protocol — extending the design system without corrupting it

> Part of `figma-vd-rollout`, Phase 3. A rollout is allowed to grow the design system. It is not allowed to grow a *second* design system on a screen page.

## The reuse ladder (always walk it in order)

1. **Use the existing component.** Read its variant properties before deciding it doesn't fit — most "missing" components are an unread variant.
2. **Compose existing components.** Many needs are a pattern, not a part: a card containing an existing table, a header containing existing buttons. Compose on the screen; only promote to a component if it repeats across screens.
3. **Add a variant to the existing set.** A new size, state, density, layout or emphasis of something that already exists is a **variant** — this is the preferred way to grow. Adding `Variant=Compact` beats creating `TableCompact`.
4. **Create a new component.** Only when the anatomy is genuinely new and no existing set can express it with a variant. State in the plan *why* step 3 failed.
5. **Stop and escalate.** If the need requires a value with no primitive behind it, stop and route the user to `figma-ds-foundation`. Never invent a raw value.

## Token gaps

- A needed **semantic** role that doesn't exist → create it in the semantic collection as an **alias to an existing primitive**. Never a raw value.
- A needed **component token** → alias to a semantic variable (one hop), following the existing naming convention exactly.
- No primitive exists to alias → **stop and escalate** to the foundation skill. A screen never carries an unbacked value.
- Match the existing naming, grouping, type and scopes of the collection you're adding to. A token that doesn't read like its neighbours is a future bug.

## Where additions live

Everything reusable is built **in the design system**, then instanced onto the screen:

| Addition | Home |
|---|---|
| New variant | The existing component set, in place |
| New atom | The atoms page (`DS_Core_Components` or equivalent) |
| New pattern (composes atoms) | The patterns page (`DS_Core_Patterns`) |
| New layout/template | The templates page (`DS_Core_Templates`) |
| New semantic / component token | The corresponding variable collection |

Never leave a reusable element as loose nodes on the rollout page.

## How to build an addition (non-negotiable conventions)

Match how `figma-ds-core` builds, so the library stays homogeneous:

- **Token-bound throughout** — zero raw values, zero primitive bindings.
- **Full variant matrix** — every state the component can be in (default/hover/focus/active/disabled, plus its own properties), named with the same property vocabulary the neighbouring sets use.
- **Auto-layout**, real labels, correct resizing behaviour.
- **Composed from instances** — a new pattern contains the real atom components, never redrawn copies.
- **Documentation frame** beside the set: name, what it's for, one usage note, and its base design system mapping if the library records one.
- **Tagged** with the library's plugin-data convention (`ax_dsc` in this pipeline: `node.setSharedPluginData('ax_dsc','key', node.name)`) so future ds-core runs see it as existing rather than missing.
- **Placed** in the page's existing grid/section rhythm, not dropped in empty canvas.

## The approval gate

Before building any addition:

1. Present the **complete gap list** in one table: need · why existing parts don't cover it · proposed resolution (variant / component / token) · where it will live · which screens consume it.
2. Get the user's go-ahead. Note anything they reject and design around it.
3. Build the additions **before** the screens that use them, so no screen is ever assembled from placeholders.

## After the run

Every addition must land in the written record (Phase 9), or the design system's spec becomes a lie:

- **Update the ds-core spec** — add the token rows to the token dictionary, the component to the component API tables with its variant matrix, and update the coverage counts.
- **Write a ds-core change report** — `ax-sdd-artifacts/ax-design-specs/ds-core-change-reports/<YYYYMMDD-HHMMSS>-<slug>.md`: one entry per addition with what, why, where it lives, which screens use it, and this run as provenance.
- **If a direction-level decision was extended** (a pattern, density or motion the direction never faced), append it to the direction spec's relevant section and flag it in the final report. Never rewrite the direction's settled decisions.
- **Never overwrite** an existing token, component or spec decision. Additions are additive; a change to something that exists needs the user's explicit instruction and belongs to `figma-ds-core`, not here.
