# Figma build guide — `use_figma` patterns for the rollout

Load `figma-use` AND `figma-generate-design` before any `use_figma` call (add `figma-generate-library` when creating components), and include them in `skillNames`. Scripts are **atomic** — on error nothing was written: read the error, fix, retry. Always `return` created node IDs and **screenshot after every chunk**.

## Target page

- **User gave a page link** → resolve to that page and build there. Do not create a page.
- **User gave a file link (or no page)** → create a new page `Rollout_<slug>` from the requirement scope (e.g. `Rollout_Work Items`). If the name exists, confirm with the user: append to it, or create `Rollout_<slug>_v2`.
- Never build on a `Design_Direction_*` or `DS_Core_*` page. Those are read-only to this skill except through the gap protocol.

## Page structure (deterministic, so re-runs and reports are quotable)

```
Rollout_<slug>                    (page)
├─ 00 · Cover                     (frame: scope, requirement source, direction, date, screen index)
├─ Section: <Screen name>
│   ├─ 01 · <Screen>              (default — always first)
│   ├─ 01 · <Screen> — empty
│   ├─ 01 · <Screen> — loading
│   ├─ 01 · <Screen> — error: <what>
│   └─ …one frame per applicable state, in the canonical order
└─ Section: <Next screen>  …
```

- One **section per screen**, named for the screen; default frame first, states left to right in the order they appear in `states-and-motion.md`.
- Frame numbering continues across runs on the same page — read the highest existing number first.
- Frame size = the direction's declared platform size. Do not introduce a size the direction never declared.

## Assembly order (per screen — never batch unverified work)

1. **Start from the template.** Instance the DS app-shell/layout template; set the nav's active state via its component properties. Never rebuild a shell.
2. **Fill regions with instances** — one region at a time, using component properties for text/icon/state overrides.
3. **Bind every remaining property** (the composition's own frames: padding, gap, radius, fills) to semantic/component tokens.
4. **Screenshot** and check: layout, hierarchy, bindings, instance integrity.
5. Only then move to the next region, then the next screen.
6. **Repeats** (rows, cards, list items): build one, verify it, then copy — never re-draw per row, never let two rows differ.

## Binding patterns

```js
// paints (fills / strokes) — semantic or component tokens ONLY
const v = (await figma.variables.getLocalVariablesAsync())
  .find(x => x.name === 'color/surface/raised');           // must resolve from Semantics/ComponentTokens
node.fills = [figma.variables.setBoundVariableForPaint(
  { type: 'SOLID', color: {r:0,g:0,b:0} }, 'color', v)];

// numbers
node.setBoundVariable('paddingLeft', spacingVar);
node.setBoundVariable('itemSpacing', gapVar);
node.setBoundVariable('topLeftRadius', radiusVar);

// text — always via the DS text style, plus a bound color
textNode.setTextStyleIdAsync(styleId);                      // 'DS/Body' etc.
```

- If the variable you need resolves to a **primitive collection**, that is a defect — go to the gap protocol, don't bind it.
- Elevation comes from the DS **effect styles**; don't hand-build shadows.

## Instances & overrides

```js
const comp = await figma.importComponentByKeyAsync(key);    // or find the set on the DS page
const inst = comp.createInstance();
inst.setProperties({ 'State': 'Disabled', 'Label#1:0': 'Create project' });
```

- Change appearance **only** through `setProperties` / variant selection. Overriding an instance's fill, radius or padding is a defect (checklist A1).
- Pick the correct **state variant** for state frames — never restyle a default.
- `detachInstance()` is forbidden in this skill.

## Prototype reactions (Phase 7)

```js
await node.setReactionsAsync([{
  trigger: { type: 'ON_CLICK' },
  actions: [{
    type: 'NODE',                                   // NAVIGATE / OPEN_OVERLAY / CLOSE_OVERLAY
    navigation: 'NAVIGATE',
    destinationId: targetFrame.id,
    transition: { type: 'SMART_ANIMATE', easing: { type: 'EASE_OUT' }, duration: 0.2 },
    preserveScrollPosition: false
  }]
}]);
```

- Read `motion/duration/*` and `motion/easing/*` variable **values** and use those numbers (`duration` is in seconds).
- Overlays use `navigation: 'OVERLAY'` / `'CLOSE'`, not a page swap.
- Smart Animate matches by **layer name** — keep names identical across the frames you're animating between.
- Set the page's prototype **starting frame** to the default screen so the flow plays.

## Tagging & idempotency

Tag everything this skill creates so re-runs extend rather than duplicate:

```js
node.setSharedPluginData('ax_vdr','key', node.name);        // hyphens are illegal in the namespace
node.setSharedPluginData('ax_vdr','screen', screenName);
node.setSharedPluginData('ax_vdr','state', stateName);      // 'default' for the default frame
node.setSharedPluginData('ax_vdr','run', runSlug);
```

Discovery on a re-run = enumerate the page's frames and read these keys. **Skip-by-default**: an existing screen+state is reported as `existing`, never silently overwritten. Rebuilding one requires the user to say so.

Library additions made through the gap protocol are tagged with the **library's** namespace (`ax_dsc`), not this one — they belong to the design system now.

## Audits (Phase 8 gates)

Run both as read-only scripts over every node on the rollout page and fix every flag.

**1. Raw-value / primitive-binding audit** — flag any node where:
- a solid paint (fill or stroke) has no bound variable;
- a bound variable resolves to a **primitive** collection rather than the semantic/component collections;
- a corner radius, stroke weight, padding, gap or fixed size has no bound variable (allow `0` and nodes explicitly named `slot`/`spacer`);
- a text node has no `DS/*` text style, or an unbound text color.

**2. Instance integrity audit** — flag:
- any node whose name matches a DS component but is a `FRAME`/`GROUP` rather than an `INSTANCE` (a hand-drawn lookalike);
- any `INSTANCE` with local paint/radius/padding overrides (`overrides` touching style properties rather than content);
- any detached instance (a former instance now a frame);
- any instance whose main component is missing.

Return `{node, page, frame, issue}` rows, print the counts, fix, re-run until both return zero.

## Chunking

One script per: page/section setup · one screen's shell · one screen region · one state frame · one batch of reactions. Keep each script small enough that a failure is diagnosable, and screenshot between them. A long silent script that half-succeeds is not possible (atomicity), but a long script that fails wastes the whole build.
