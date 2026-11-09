# Figma Scripts (`use_figma`) — idempotent build

> Part of `figma-ds-foundation`. All scripts run through the `use_figma` tool. **Load the `figma-use` skill first** and pass `skillNames: "figma-ds-foundation,figma-use"`. Write plain JS with top-level `await`/`return` — no IIFE, no `figma.closePlugin()`. On error: STOP (scripts are atomic, nothing was written), read the message, fix, retry. Only ever touch `BrandPrimitives` and `App-Primitives`.

## Shared helpers (prepend to any build script)

```javascript
// ---- helpers ----
const TAG_NS = 'ax-dsf';                 // shared-plugin-data namespace for idempotency
const UPDATE = false;                    // set true ONLY when user explicitly asks to update existing values

function hexToRgba(hex, alpha) {
  hex = String(hex).replace('#', '').trim();
  if (hex.length === 3) hex = hex.split('').map(c => c + c).join('');
  if (hex.length === 8) { alpha = parseInt(hex.slice(6, 8), 16) / 255; hex = hex.slice(0, 6); }
  return {
    r: parseInt(hex.slice(0, 2), 16) / 255,
    g: parseInt(hex.slice(2, 4), 16) / 255,
    b: parseInt(hex.slice(4, 6), 16) / 255,
    a: alpha === undefined ? 1 : alpha,
  };
}

// Find-or-create a collection. Returns {collection, modeId, created}.
// Only renames the mode to "Value" when WE created the collection (never mutate an existing one's mode).
async function ensureCollection(name) {
  const cols = await figma.variables.getLocalVariableCollectionsAsync();
  let c = cols.find(x => x.name === name);
  if (c) return { collection: c, modeId: c.modes[0].modeId, created: false };
  c = figma.variables.createVariableCollection(name);
  c.renameMode(c.modes[0].modeId, 'Value');
  return { collection: c, modeId: c.modes[0].modeId, created: true };
}

// Build a name->variable map of what already exists in a collection.
async function existingVarsIn(collection) {
  const all = await figma.variables.getLocalVariablesAsync();
  const map = {};
  for (const v of all) if (v.variableCollectionId === collection.id) map[v.name] = v;
  return map;
}

// Idempotent create. type: 'COLOR'|'FLOAT'|'STRING'. Skips if present (unless UPDATE).
function makeEnsureVar(collection, modeId, existing) {
  return function ensureVar(name, type, value, scopes) {
    let v = existing[name];
    if (v) {
      if (UPDATE) { v.setValueForMode(modeId, value); return { name, id: v.id, status: 'updated' }; }
      return { name, id: v.id, status: 'skipped' };
    }
    v = figma.variables.createVariable(name, collection, type);
    v.setValueForMode(modeId, value);
    if (scopes !== undefined) v.scopes = scopes;       // never leave default ALL_SCOPES
    v.setSharedPluginData(TAG_NS, 'key', name);         // idempotency tag
    existing[name] = v;
    return { name, id: v.id, status: 'created' };
  };
}
```

## Discover (Phase 3) — read-only

```javascript
const cols = await figma.variables.getLocalVariableCollectionsAsync();
const all = await figma.variables.getLocalVariablesAsync();
function report(name) {
  const c = cols.find(x => x.name === name);
  if (!c) return { name, exists: false, vars: [] };
  const vars = all.filter(v => v.variableCollectionId === c.id).map(v => v.name).sort();
  return { name, exists: true, id: c.id, modes: c.modes.map(m => m.name), vars };
}
return { brand: report('BrandPrimitives'), app: report('App-Primitives') };
```

Use the returned `vars` lists to compute the missing-only delta before building.

## BrandPrimitives (Phase 4)

Fill `BRAND` from the parsed + confirmed guidelines. Only include typography keys the brand actually defined (or that the user confirmed).

```javascript
// <helpers here>
const BRAND = {
  colors: [                                   // from brand guidelines (name, hex[, alpha])
    // { name: 'color/brand/primary', hex: '#4F46E5' },
    // { name: 'color/brand/primary/600', hex: '#4338CA' },
  ],
  families: [                                 // STRING
    // { name: 'font/family/sans', value: 'Inter' },
  ],
  weights: [                                  // FLOAT
    // { name: 'font/weight/regular', value: 400 },
  ],
  sizes: [                                    // FLOAT px
    // { name: 'font/size/md', value: 16 },
  ],
  lineHeights: [], letterSpacing: [],         // FLOAT, optional
};

const COLOR_SCOPES = ['FRAME_FILL', 'SHAPE_FILL', 'TEXT_FILL', 'STROKE_COLOR'];
const { collection, modeId, created } = await ensureCollection('BrandPrimitives');
const existing = await existingVarsIn(collection);
const ensureVar = makeEnsureVar(collection, modeId, existing);
const out = [];

for (const c of BRAND.colors)       out.push(ensureVar(c.name, 'COLOR', hexToRgba(c.hex, c.alpha), COLOR_SCOPES));
for (const f of BRAND.families)     out.push(ensureVar(f.name, 'STRING', f.value, ['FONT_FAMILY']));
for (const w of BRAND.weights)      out.push(ensureVar(w.name, 'FLOAT',  w.value, ['FONT_WEIGHT']));
for (const s of BRAND.sizes)        out.push(ensureVar(s.name, 'FLOAT',  s.value, ['FONT_SIZE']));
for (const l of BRAND.lineHeights)  out.push(ensureVar(l.name, 'FLOAT',  l.value, ['LINE_HEIGHT']));
for (const t of BRAND.letterSpacing)out.push(ensureVar(t.name, 'FLOAT',  t.value, ['LETTER_SPACING']));

return { collection: 'BrandPrimitives', collectionCreated: created, results: out,
  summary: { created: out.filter(o=>o.status==='created').length, skipped: out.filter(o=>o.status==='skipped').length } };
```

## App-Primitives (Phase 5)

Ensure the collection **once**, then run the chunks below (they all re-`ensureCollection`, which is safe — it finds the existing one). Validate after each chunk. Fill numeric sets from the **verified** catalog values.

### Chunk A — colors (gray + semantic ramps)

```javascript
// <helpers here>
const COLOR_SCOPES = ['FRAME_FILL', 'SHAPE_FILL', 'TEXT_FILL', 'STROKE_COLOR'];
const COLORS = [
  // gray (baseline neutral — replace with confirmed ramp)
  { name: 'color/gray/50',  hex: '#FAFAFA' }, { name: 'color/gray/100', hex: '#F5F5F5' },
  { name: 'color/gray/200', hex: '#E5E5E5' }, { name: 'color/gray/300', hex: '#D4D4D4' },
  { name: 'color/gray/400', hex: '#A3A3A3' }, { name: 'color/gray/500', hex: '#737373' },
  { name: 'color/gray/600', hex: '#525252' }, { name: 'color/gray/700', hex: '#404040' },
  { name: 'color/gray/800', hex: '#262626' }, { name: 'color/gray/900', hex: '#171717' },
  { name: 'color/gray/950', hex: '#0A0A0A' },
  // semantic ramps 50..900 — generate the FULL ramp for red/green/yellow/blue from the chosen
  // accessible source (verified). Only a few steps shown; the real build lists every step.
  { name: 'color/red/500',    hex: '#EF4444' }, { name: 'color/green/500',  hex: '#22C55E' },
  { name: 'color/yellow/500', hex: '#EAB308' }, { name: 'color/blue/500',   hex: '#3B82F6' },
];
const { collection } = await ensureCollection('App-Primitives');
const modeId = collection.modes[0].modeId;
const existing = await existingVarsIn(collection);
const ensureVar = makeEnsureVar(collection, modeId, existing);
const out = COLORS.map(c => ensureVar(c.name, 'COLOR', hexToRgba(c.hex, c.alpha), COLOR_SCOPES));
return { chunk: 'colors', results: out };
```

### Chunk B — spacing / radius / border (verified values)

```javascript
// <helpers here>
const { collection } = await ensureCollection('App-Primitives');
const modeId = collection.modes[0].modeId;
const existing = await existingVarsIn(collection);
const ensureVar = makeEnsureVar(collection, modeId, existing);
const out = [];
const spacing = { '0':0,'25':2,'50':4,'75':6,'100':8,'150':12,'200':16,'250':20,'300':24,'400':32,'500':40,'600':48,'800':64,'1000':80 };
const radius  = { none:0, sm:2, md:4, lg:8, xl:12, '2xl':16, full:9999 };
const border  = { none:0, default:1, thick:2, thicker:3 };
for (const [k,v] of Object.entries(spacing)) out.push(ensureVar(`spacing/${k}`, 'FLOAT', v, ['GAP','WIDTH_HEIGHT']));
for (const [k,v] of Object.entries(radius))  out.push(ensureVar(`radius/${k}`,  'FLOAT', v, ['CORNER_RADIUS']));
for (const [k,v] of Object.entries(border))  out.push(ensureVar(`border/width/${k}`, 'FLOAT', v, ['STROKE_FLOAT']));
return { chunk: 'spacing/radius/border', results: out };
```

### Chunk C — elevation (decomposed shadow)

```javascript
// <helpers here>
const { collection } = await ensureCollection('App-Primitives');
const modeId = collection.modes[0].modeId;
const existing = await existingVarsIn(collection);
const ensureVar = makeEnsureVar(collection, modeId, existing);
const out = [];
// { y, blur, spread, color, alpha } per level (verify against M3). Level 0 = none, skip.
const elevation = {
  '1': { y:1, blur:3,  spread:1, color:'#000000', a:0.15 },
  '2': { y:2, blur:6,  spread:2, color:'#000000', a:0.15 },
  '3': { y:4, blur:8,  spread:3, color:'#000000', a:0.18 },
  '4': { y:6, blur:10, spread:4, color:'#000000', a:0.18 },
  '5': { y:8, blur:12, spread:6, color:'#000000', a:0.20 },
};
for (const [lvl, e] of Object.entries(elevation)) {
  out.push(ensureVar(`elevation/${lvl}/y`,      'FLOAT', e.y,      ['EFFECT_FLOAT']));
  out.push(ensureVar(`elevation/${lvl}/blur`,   'FLOAT', e.blur,   ['EFFECT_FLOAT']));
  out.push(ensureVar(`elevation/${lvl}/spread`, 'FLOAT', e.spread, ['EFFECT_FLOAT']));
  out.push(ensureVar(`elevation/${lvl}/color`,  'COLOR', hexToRgba(e.color, e.a), ['EFFECT_COLOR']));
}
return { chunk: 'elevation', results: out };
```

### Chunk D — opacity / motion / z-index / breakpoints

```javascript
// <helpers here>
const { collection } = await ensureCollection('App-Primitives');
const modeId = collection.modes[0].modeId;
const existing = await existingVarsIn(collection);
const ensureVar = makeEnsureVar(collection, modeId, existing);
const out = [];
const opacity = { '0':0,'5':0.05,'10':0.1,'20':0.2,'40':0.4,'60':0.6,'80':0.8,'90':0.9,'100':1 };
const duration = { 'short-1':50,'short-2':100,'short-3':150,'short-4':200,'medium-1':250,'medium-2':300,'medium-3':350,'medium-4':400,'long-1':450,'long-2':500,'long-3':550,'long-4':600,'extra-long-1':700,'extra-long-2':800,'extra-long-3':900,'extra-long-4':1000 };
const easing = { linear:'cubic-bezier(0,0,1,1)', standard:'cubic-bezier(0.2,0,0,1)', 'standard-decelerate':'cubic-bezier(0,0,0,1)', 'standard-accelerate':'cubic-bezier(0.3,0,1,1)', emphasized:'cubic-bezier(0.2,0,0,1)', 'emphasized-decelerate':'cubic-bezier(0.05,0.7,0.1,1)', 'emphasized-accelerate':'cubic-bezier(0.3,0,0.8,0.15)' };
const zindex = { hide:-1, base:0, raised:10, dropdown:1000, sticky:1100, banner:1200, overlay:1300, modal:1400, popover:1500, 'skip-link':1600, toast:1700, tooltip:1800 };
const bp = { sm:640, md:768, lg:1024, xl:1280, '2xl':1536 };
for (const [k,v] of Object.entries(opacity))  out.push(ensureVar(`opacity/${k}`, 'FLOAT', v, ['OPACITY']));
for (const [k,v] of Object.entries(duration)) out.push(ensureVar(`motion/duration/${k}`, 'FLOAT', v, []));
for (const [k,v] of Object.entries(easing))   out.push(ensureVar(`motion/easing/${k}`, 'STRING', v, []));
for (const [k,v] of Object.entries(zindex))   out.push(ensureVar(`z-index/${k}`, 'FLOAT', v, []));
for (const [k,v] of Object.entries(bp))       out.push(ensureVar(`breakpoint/${k}`, 'FLOAT', v, []));
return { chunk: 'opacity/motion/zindex/breakpoints', results: out };
```

## Validate (Phase 6) — read-only

```javascript
const cols = await figma.variables.getLocalVariableCollectionsAsync();
const all = await figma.variables.getLocalVariablesAsync();
function summarize(name) {
  const c = cols.find(x => x.name === name);
  if (!c) return { name, exists: false };
  const vars = all.filter(v => v.variableCollectionId === c.id);
  const groups = [...new Set(vars.map(v => v.name.split('/').slice(0, -1).join('/')))].sort();
  const badScope = vars.filter(v => (v.scopes || []).includes('ALL_SCOPES')).map(v => v.name);
  const byType = vars.reduce((a, v) => (a[v.resolvedType] = (a[v.resolvedType]||0)+1, a), {});
  return { name, exists: true, count: vars.length, groups, byType, allScopesLeak: badScope };
}
return {
  brand: summarize('BrandPrimitives'),
  app: summarize('App-Primitives'),
  otherCollectionsUntouched: cols.map(c => c.name).filter(n => n !== 'BrandPrimitives' && n !== 'App-Primitives'),
};
```

`allScopesLeak` should be empty (every variable got explicit scopes). `otherCollectionsUntouched` just lists what else exists — confirm you neither created nor modified any of them.

## Optional — code syntax (offer, off by default)

```javascript
// Run after a build chunk if the user opts in. Derives a CSS var name from the token name.
const all = await figma.variables.getLocalVariablesAsync();
const cols = await figma.variables.getLocalVariableCollectionsAsync();
const ids = new Set(cols.filter(c => ['BrandPrimitives','App-Primitives'].includes(c.name)).map(c => c.id));
let n = 0;
for (const v of all) {
  if (!ids.has(v.variableCollectionId)) continue;
  const css = `--${v.name.replace(/[\s\/]+/g, '-').toLowerCase()}`;
  v.setVariableCodeSyntax('WEB', `var(${css})`);
  n++;
}
return { codeSyntaxSet: n };
```
