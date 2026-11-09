# Component Catalog — NORMATIVE MANIFEST

This is a **manifest, not a menu**. Every component below MUST exist as a Figma component/component set with its **full variant matrix** by the end of its phase — created this run, already present (skip+report), or waived by the user **by name**. The validation script diffs the pages against this list. Never build a "representative subset".

**Manifest totals (validation targets):**

| Tier | Page | Count |
|---|---|---|
| Tier 1 — Atoms | `DS_Core_Components` | **26 sets** |
| Tier 2 — Patterns | `DS_Core_Patterns` | **23 sets** (+ direction delta) |
| Tier 3 — Templates | `DS_Core_Templates` | **9 frames** |

Conventions for every entry:
- **Component set name** exactly as listed (e.g. `Button`). Variant properties use Title-case names and the exact values listed (`State=Default|Hover|Pressed|Focus|Disabled`).
- **Every** visual property bound to a `Semantics`/`ComponentTokens` variable or a `DS/*` style — zero hardcoded values (SKILL guardrail 2). The "Bind" line names the load-bearing bindings; anything not named still binds to the obvious semantic (audit catches leaks).
- **Auto-layout everywhere**; text uses the `DS/*` text styles; icons from the direction's icon family at `size/icon/*`.
- **Base-DS column** shows the wrapper mapping for Material 3 (M) / shadcn (S); other systems: resolve equivalent in Phase 2 parity table.
- Each set lives in a **doc frame**: title, base-DS mapping, one usage sentence, one do/don't.
- Sizes default to `md`; size variants only where listed (adding sizes everywhere bloats the matrix without value).

## Tier 1 — Atoms (page `DS_Core_Components`) — 26

| # | Set | Variant matrix | Bind highlights | Base DS (M / S) |
|---|---|---|---|---|
| 1 | `Button` | Tone=Primary\|Secondary\|Ghost\|Danger × Size=Sm\|Md\|Lg × State=Default\|Hover\|Pressed\|Focus\|Disabled\|Loading × Icon=None\|Left\|Right (Loading only for Tone=Primary\|Danger; Icon matrix only for Size=Md — 4×3×6 pruned ≈ 60–70 variants) | `button/height-*`, `button/pad-x-*`, `button/gap`, `button/radius`, `color/action/<tone>/*`, focus ring = `color/focus/ring` + `border-width/focus`, `DS/Label` | Button / button |
| 2 | `IconButton` | Tone=Primary\|Secondary\|Ghost\|Danger × Size=Sm\|Md\|Lg × State (5) | square `button/height-*`, `color/action/*`, `size/icon/*` | IconButton / button size=icon |
| 3 | `Link` | Kind=Inline\|Standalone × State=Default\|Hover\|Disabled | `color/text/link`, `link-hover`, underline per DIRECTION | — / — (anchor) |
| 4 | `Input` | State=Default\|Hover\|Focus\|Filled\|Error\|Disabled\|ReadOnly × Adornment=None\|IconLeft\|IconRight | all `input/*` tokens; error = `input/border-error` + `status/danger/*` | TextField / input |
| 5 | `Textarea` | State=Default\|Focus\|Error\|Disabled | `input/*` | TextField multiline / textarea |
| 6 | `Select` (closed trigger) | State=Default\|Hover\|Focus\|Error\|Disabled\|Filled | `input/*` + chevron `color/icon/subtle` | Select / select trigger |
| 7 | `Checkbox` | Checked=Off\|On\|Indeterminate × State=Default\|Hover\|Disabled\|Error | box `radius/control-sm`, on = `color/action/primary/bg`, check `text/on-brand` | Checkbox / checkbox |
| 8 | `Radio` | Checked=Off\|On × State=Default\|Hover\|Disabled | as Checkbox, circular | Radio / radio-group item |
| 9 | `Switch` | Checked=Off\|On × State=Default\|Hover\|Disabled | track on = `action/primary/bg`, off = `gray` via `border/strong`; thumb `surface/raised` | Switch / switch |
| 10 | `Slider` | State=Default\|Hover\|Drag\|Disabled | track `surface/sunken`, fill `action/primary/bg`, thumb `surface/raised`+`elevation/raised` | Slider / slider |
| 11 | `Badge` | Tone=Neutral\|Brand\|Success\|Warning\|Danger\|Info × Emphasis=Subtle\|Bold | `badge/*` + `color/status/<tone>/bg-subtle·text` or `bg-bold·text-on-bold` | Badge / badge |
| 12 | `Tag` | State=Default\|Hover\|Selected\|Disabled × Removable=No\|Yes | `selected/*`, `badge/radius`, close icon `icon/subtle` | Chip / — (composite) |
| 13 | `Avatar` | Size=Sm\|Md\|Lg × Content=Image\|Initials\|Icon | `radius/pill`, initials bg `surface/brand-subtle`, `DS/Label` | Avatar-ish / avatar |
| 14 | `Tooltip` | Placement=Top\|Bottom\|Left\|Right | `surface/inverse`, `text/inverse`, `radius/control-sm`, `space/inset/xs·sm` | Tooltip (plain) / tooltip |
| 15 | `Divider` | Orientation=Horizontal\|Vertical × Emphasis=Subtle\|Default | `border/subtle·default`, `border-width/default` | Divider / separator |
| 16 | `Spinner` | Size=Sm\|Md\|Lg | `color/icon/brand`, `size/icon/*` | CircularProgress / — |
| 17 | `ProgressBar` | Kind=Determinate\|Indeterminate | track `surface/sunken`, fill `action/primary/bg`, `radius/pill` | LinearProgress / progress |
| 18 | `Skeleton` | Shape=Text\|Rect\|Circle | `surface/sunken`, `radius/control-sm·pill` | — / skeleton |
| 19 | `Kbd` | (single) | `surface/sunken`, `border/default`, `DS/Code`, `radius/control-sm` | — / kbd (docs) |
| 20 | `FieldLabel` | Requirement=None\|Required\|Optional | `DS/Label`, `text/primary`, required mark `status/danger/text` | InputLabel / label |
| 21 | `HelperText` | Tone=Help\|Error\|Success | `DS/Caption`, `text/tertiary` \| `status/danger/text` \| `status/success/text` | FormHelperText / — |
| 22 | `SegmentedControl` | Segments=2\|3 × Selected=1st\|2nd\|3rd | container `surface/sunken` `radius/control`, selected `surface/raised`+`elevation/raised` (DIRECTION) | SegmentedButton / toggle-group |
| 23 | `NotificationDot` | Kind=Dot\|Count | `status/danger/bg-bold`, `text-on-bold`, `radius/pill` | Badge / — |
| 24 | `Icon` (frame wrapper) | Size=Sm\|Md\|Lg | `size/icon/*`, fill `color/icon/default` | Icon / lucide |
| 25 | `Logo` (slot) | Size=Sm\|Md | brand asset placeholder, height via `size/icon/lg`/`control/md` | — |
| 26 | `TextPair` (label+value) | Direction=Vertical\|Horizontal | `DS/Caption` `text/tertiary` + `DS/Body` `text/primary`, `space/stack/xs` | — (list primitive) |

## Tier 2 — Patterns (page `DS_Core_Patterns`) — 23 + direction delta

Patterns **compose Tier-1 instances** — never redraw an atom inside a pattern.

| # | Set | Variant matrix / contents | Bind highlights | Base DS (M / S) |
|---|---|---|---|---|
| 1 | `FormField` | Control=Input\|Textarea\|Select × State=Default\|Error\|Disabled | `FieldLabel` + control + `HelperText`, gaps `input/gap-label·gap-help` | FormControl / form field |
| 2 | `SearchBar` | State=Default\|Focus\|Filled | `Input` with search icon + optional Kbd shortcut | SearchBar / composite |
| 3 | `MenuItem` | State=Default\|Hover\|Selected\|Disabled × Kind=Default\|Danger\|WithIcon\|WithShortcut | `space/inset/sm`, hover `action/ghost/bg-hover`, selected `selected/*`, danger `status/danger/text` | MenuItem / dropdown-menu item |
| 4 | `Menu` | Size=Sm\|Md | `surface/overlay`, `radius/control` (or `radius/overlay` per DIRECTION), `DS/Elevation/Overlay`, composed `MenuItem`s + divider | Menu / dropdown-menu content |
| 5 | `Card` | Kind=Default\|Header\|HeaderFooter × Interactive=No\|Hover\|Selected | all `card/*`; interactive hover = `DS/Elevation/Raised` or border shift (DIRECTION) | Card / card |
| 6 | `StatTile` | Trend=None\|Up\|Down | `Card` base + `DS/Display|H2` value + `TextPair`; trend `status/success·danger/text` | — / composite |
| 7 | `TableHeaderCell` | Sort=None\|Asc\|Desc | `table/header-bg·header-text`, `DS/Label`, sort icon `icon/subtle` | TableHead / th |
| 8 | `TableCell` | Content=Text\|Secondary\|Badge\|Actions\|Checkbox | `table/cell-pad-x`, `DS/Body-sm` | TableCell / td |
| 9 | `TableRow` | State=Default\|Hover\|Selected × Density=Default\|Compact | `table/row-*` | TableRow / tr |
| 10 | `Table` | (assembled sample: header + 4 rows + footer w/ `Pagination`) | composed | Table / table |
| 11 | `TabItem` | State=Default\|Hover\|Active\|Disabled | `tabs/*`, indicator `tabs/indicator-*` (style per DIRECTION: underline/contained) | Tab / tabs trigger |
| 12 | `Tabs` | (bar of TabItems, first active) | `tabs/gap`, bottom `border/subtle` | Tabs / tabs list |
| 13 | `Breadcrumb` | Depth=2\|3 | `DS/Body-sm`, links `text/link`, current `text/primary`, sep `icon/subtle` | Breadcrumbs / breadcrumb |
| 14 | `Pagination` | State=First\|Middle\|Last | `IconButton Ghost` + page `DS/Body-sm`, current `selected/*` | Pagination / pagination |
| 15 | `Accordion` | State=Collapsed\|Expanded | header `DS/H4`, chevron, `border/subtle`, `space/inset/md` | Accordion / accordion |
| 16 | `Alert` | Tone=Info\|Success\|Warning\|Danger × Dismissible=No\|Yes | `status/<tone>/bg-subtle·border·icon·text`, `radius/surface` | Alert / alert |
| 17 | `Toast` | Tone=Neutral\|Success\|Danger × Action=No\|Yes | `surface/inverse`+`text/inverse` or per DIRECTION, `DS/Elevation/Overlay` | Snackbar / sonner |
| 18 | `Modal` | Size=Sm\|Md\|Lg × Kind=Default\|Confirm\|Destructive | `modal/*`, `DS/Elevation/Modal`, footer = real `Button`s (Destructive → `Button Danger`) | Dialog / dialog |
| 19 | `Drawer` | Side=Right\|Left | `surface/overlay`, `DS/Elevation/Modal`, width DIRECTION, header + close `IconButton` | Drawer / sheet |
| 20 | `Popover` | (single, w/ arrow optional per DIRECTION) | `surface/overlay`, `radius/control`, `DS/Elevation/Overlay`, `space/inset/md` | Popover / popover |
| 21 | `EmptyState` | Kind=Default\|Error\|NoResults | icon/illustration slot, `DS/H3` + `DS/Body` `text/secondary`, CTA `Button` | — / composite |
| 22 | `NavItem` + `SidebarNav` | NavItem: State=Default\|Hover\|Selected\|Disabled × Badge=No\|Yes; SidebarNav: grouped sections + `Logo` + user row | all `nav/*` tokens | NavigationDrawer / composite |
| 23 | `TopBar` | Kind=Default\|WithSearch\|WithActions | height `size/control/lg`, `surface` per DIRECTION, composed `SearchBar`/`IconButton`/`Avatar` | TopAppBar / composite |

**Also required (counted inside the 23):** none — but the following are **conditional additions**, appended to the manifest in Phase 1 as the **Direction delta**:

- Every entry in the direction spec **§9 Component inventory** not already covered above (e.g. a command palette, stage/canvas chrome, wizard/stepper header, file-upload dropzone, calendar/date field) becomes a named manifest row with a variant matrix you define from the direction's screens, and is validated like any other row. If the direction's screens show a pattern §9 forgot, add it too and note the addition in the spec.
- `Stepper` and `FileUpload` and `DateField`+`Calendar` are REQUIRED rows whenever the direction/PRD involves multi-step flows, uploads, or dates respectively; otherwise they may be deferred to a later run (record as deferred, not waived silently).

## Tier 3 — Layouts & templates (page `DS_Core_Templates`) — 9

Templates are **frames assembled from pattern/atom instances** at the direction's platform size, with clearly labeled slot placeholders (`surface/sunken` dashed regions) for the rollout skill.

| # | Template | Contents |
|---|---|---|
| 1 | `Template/AppShell` | The direction's exact nav model: `SidebarNav` + `TopBar` + content slot on `surface/page`, grid per direction §4 |
| 2 | `Template/Dashboard` | AppShell + `StatTile` row + 2 `Card` content modules + `Table` preview |
| 3 | `Template/ListPage` | AppShell + page header (title `DS/H1`, actions) + filter row (`SearchBar`, `Select`, `Tag`) + full `Table` + `Pagination` |
| 4 | `Template/DetailPage` | AppShell + `Breadcrumb` + header (avatar/title/badges/actions) + `Tabs` + two-column content (main + meta sidebar of `TextPair`s) |
| 5 | `Template/FormPage` | AppShell + form column at direction's form width: `FormField` stack, section dividers, sticky footer `Button`s |
| 6 | `Template/SettingsPage` | AppShell + secondary nav (vertical `NavItem`s) + sectioned `Card`s of `FormField`s |
| 7 | `Template/AuthEntry` | The direction's entry-screen composition: `Logo`, `FormField`s, primary `Button`, link row — centered/split per direction |
| 8 | `Template/OverlayInContext` | `Template/ListPage` dimmed by `color/overlay/scrim` @ `opacity/scrim` with `Modal Md` open |
| 9 | `Template/EmptyErrorPage` | AppShell + full-page `EmptyState` (one Default, one Error side by side) |

## Anti-shortcut protocol (binding on the executor)

1. Before Phase 5/6/7, print the tier's manifest rows as a checklist with the variant count you will build for each.
2. Build **one component set per `use_figma` script**; after each, record its node ID against the manifest row.
3. At phase end, run the validation script (build-guide §Validate): enumerate tagged components on the page, diff against the manifest, print `created / existing / MISSING`.
4. `MISSING > 0` → build the missing rows now, or obtain an explicit per-item user waiver. "Ran long" is not a waiver — pause and continue in the next message instead of dropping items.
5. The final report and the ds-core spec both include the full manifest table with per-row status (✓ created / ✓ existed / waived-by-user / deferred+reason).
