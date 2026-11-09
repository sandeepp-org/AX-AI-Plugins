# Figma build guide — `use_figma` patterns for the DS core

Load `figma-use` AND `figma-generate-library` before any `use_figma` call and include `figma-ds-core,figma-use,figma-generate-library` in `skillNames`. Scripts are atomic — on error, nothing was written: read the error, fix, retry. **Always `return` created node/variable/style IDs.**

## Tagging (idempotency backbone)

Everything this skill creates is tagged so re-runs and validation can find it:

- Variables: `v.setSharedPluginData('ax-dsc','key', v.name)` and `('ax-dsc','kind','semantic'|'component-token')`
- Styles: `s.setSharedPluginData('ax-dsc','key', s.name)`
- Component sets / templates: `node.setSharedPluginData('ax-dsc','key', node.name)` and `('ax-dsc','tier','1'|'2'|'3')`

Discovery = enumerate collections/styles/pages and read these keys. **Skip-by-default**: if a key already exists, don't recreate or overwrite — count it as `existing`.

## Creating alias variables (Phase 4)

```js
const collections = await figma.variables.getLocalVariableCollectionsAsync();
const prim = Object.fromEntries(
  (await figma.variables.getLocalVariablesAsync()).map(v => [v.name + '|' + v.variableCollectionId, v]));
// resolve primitives by name within BrandPrimitives/App-Primitives; fail loudly if a target is missing
let sem = collections.find(c => c.name === 'Semantics')
       ?? figma.variables.createVariableCollection('Semantics');
sem.renameMode(sem.modes[0].modeId, 'Default');   // only if newly created — never rename existing modes
const mode = sem.modes[0].modeId;

function makeAlias(name, type, target, scopes) {
  // skip if exists (by name in this collection)
  const v = figma.variables.createVariable(name, sem, type);
  v.setValueForMode(mode, { type: 'VARIABLE_ALIAS', id: target.id });
  v.scopes = scopes;
  v.setSharedPluginData('ax-dsc','key', name);
  v.setSharedPluginData('ax-dsc','kind','semantic');
  return v.id;
}
```

- A missing primitive target is a **hard stop** (guardrail 2) — report the exact name and route to the foundation skill; never substitute a raw value.
- `ComponentTokens` variables alias **`Semantics` variables** (one hop), same pattern.
- Chunk ≤ ~15 variables per script; return `{created:[], existing:[], failed:[]}`.

## Text & effect styles (bound to variables)

```js
const ts = figma.createTextStyle(); ts.name = 'DS/H1';
await figma.loadFontAsync({family, style});      // resolve family/weight from the typography variables first
ts.setBoundVariable('fontFamily', famVar); ts.setBoundVariable('fontSize', sizeVar);
ts.setBoundVariable('fontWeight', weightVar); ts.setBoundVariable('lineHeight', lhVar);
```
Effect styles: `figma.createEffectStyle()`, one DROP_SHADOW effect; bind `offsetY/radius/spread/color` via `figma.variables.setBoundVariableForEffect`-style helpers (see figma-use skill for the exact current API; if effect binding is unavailable, set values read from the variables and note the styles must be regenerated if elevation changes).

## Building component sets (Phases 5–7)

Per set, one script:
1. Build **one variant frame per matrix cell** with auto-layout; name each `Prop1=Value, Prop2=Value` (exact catalog names/values).
2. Bind everything: fills/strokes `setBoundVariable('fills'|'strokes', …)` via `figma.variables.setBoundVariableForPaint`; numbers (`paddingLeft`, `itemSpacing`, `cornerRadius`, `strokeWeight`, `width/height` where fixed) via `setBoundVariable`; text nodes get `textStyleId = DS/<Role>` and fill bound to a color semantic.
3. `figma.combineAsVariants(frames, page)` → name the set (catalog name) → tag it → wrap in the **doc frame** (title, base-DS mapping line, usage sentence, do/don't).
4. Lay doc frames on a page grid: 64px gutters, one row per catalog section; section header text frames.
5. `return { setId, variantCount, docFrameId }` and **screenshot** the set for visual check before the next script.

Patterns/templates: **instances only** (`component.createInstance()`) for any atom/pattern they contain — check the instance swap works; never detach.

Interaction states that Figma can't express live as explicit variants (the matrix already encodes Hover/Focus/etc.) — do not wire prototype reactions here; that was the direction skill's job.

## §Validate — the manifest-diff gate (run after every phase)

One read-only script per phase:

```js
// returns { variables: [...names], styles: [...], components: [{page, name, variants}] } filtered to ax-dsc tags
```

Then diff **in the conversation, explicitly**, against the catalog tables:
1. Enumerate the catalog rows for the phase (print them).
2. Mark each: `created` (this run) / `existing` (prior run) / `MISSING`.
3. Print counts vs the manifest totals from the catalog headers.
4. `MISSING > 0` → build them now or get a named user waiver. Then re-run the gate. Only a clean (or fully-waived) gate unlocks the next phase.

Also run the **zero-hardcoded audit** (Phase 9): walk every `DS_Core_*` node; flag any solid paint not variable-bound, any text without a `DS/*` style, any corner radius / stroke weight / padding / gap with no bound variable (allow 0 values and slot placeholders explicitly marked `slot`). Fix all flags.

## Re-scoping primitives (Phase 8, gated)

On explicit confirmation only: for every variable in `BrandPrimitives`/`App-Primitives`, set `v.scopes = []`. Touch **scopes only** — never values, names, modes, or collection structure. Return the count re-scoped.
