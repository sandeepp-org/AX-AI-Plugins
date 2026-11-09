# Visual Direction Contract (VD Contract)

<!--
═══════════════════════════════════════════════════════════════════════════
HOW TO USE THIS TEMPLATE
═══════════════════════════════════════════════════════════════════════════

BOUNDARY — the MAPPING layer, nothing else.
  This contract records, as settled facts, WHERE every UI surface requirement
  and journey in the PRD (the product model) lands in the design direction:
  which screen, which state of that screen, or which navigation item. It also
  records, per screen, the full set of experience states — the contract is the
  single home of screen-state truth for the product. It must NOT restate
  business requirements (journeys, FRs, rules — that is the PRD; link by ID,
  never duplicate) and must NOT contain visual decisions (colors, type,
  spacing, motion — that is the design direction spec). If you are describing
  WHY a capability exists, it belongs in the PRD. If you are describing how a
  surface LOOKS, it belongs in the direction spec.

SINGLE WRITER — figma-vd-contract.
  Established once after a design direction is created and approved; updated
  by the same skill after every screen rollout and whenever new intents extend
  the product. No other skill edits this file. The rollout skill PRODUCES
  per-screen Figma links in its own outputs; this skill's Update mode RECORDS
  them here.

SCOPE — one contract per design direction.
  The contract is bound to exactly one `Design_Direction_<name>` Figma page and
  its direction spec. A product exploring several direction alternatives has
  one contract per direction it takes forward.

ID SPINE — surfaces carry contract-native, immortal `VDS-` IDs.
  VDS-001, VDS-002, … assigned in §2 discovery order at establish time and
  appended for surfaces added later. IDs are never deleted or reused. A
  surface that is merged into another or retired moves out of the §2 sitemap
  into the §6 register; its §5 block is replaced by a one-line tombstone
  pointing at the survivor; the survivor absorbs its requirement mappings.

DESIGN STATUS vocabulary (per surface):
  Direction-designed  — a real frame exists on the direction's Figma page
  Nav-promised        — a navigation item exists in the direction; no frame yet
  Placeholder         — reserved nav placement only (e.g. V2); excluded from
                        coverage gates, listed for completeness
  Unplaced            — required by the PRD; no frame and no nav item  ← GAP

ROLLOUT STATUS vocabulary (per surface):
  Not started · Rolled out vX (per-screen Figma link recorded) · N/A (placeholder)

CANONICAL SCREEN STATES — every §5 screen block MUST enumerate ALL of:
  empty/no-data · loading · filled/valid · invalid-input · validation-error ·
  submission-success · submission-failure · permission-denied
  A state that does not apply is written `N/A — affirmed`, never omitted.
  A screen block is complete only when every canonical state is either
  specified or affirmed. Additional screen-specific states are appended below
  the canonical rows. Alongside the states table, each block records which
  states the direction has actually DRAWN and which states the rollout still
  OWES — the rollout skill's work order.

CLARIFICATIONS — Never invent a missing fact.
  Insert `[NEEDS CLARIFICATION: <question>]` inline wherever a mapping or
  state decision is unknown, and log it in §Open Clarifications. The contract
  CANNOT move to Approved while any BLOCKING clarification is open.

RETRIEVAL — This template is LAYERED on purpose.
  • DIGEST = front-matter + §1 Contract Summary + §2 Direction Sitemap +
    §3 Requirement ↔ Surface Map. Small, cheap to load; gives an agent the
    whole map of the contract.
  • DETAIL = §5, one self-contained block per surface. Retrieved on demand.
  Never split a table across sections; keep each one whole.

COVERAGE GATES — before this contract can be Approved:
  • Every PRD journey with Surface = UI has a §3 row mapping it to ≥1 surface,
    or an explicit `No surface — affirmed: <reason>`.
  • No §3 row has Coverage = Gap.
  • Every §2 surface (except Placeholder rows) has a §5 block with every
    canonical state either specified or affirmed.
  • Every §2 surface maps to ≥1 requirement in §3, or appears in §4 as an
    acknowledged orphan with a disposition.
  Non-UI journeys (Surface = API / System / None in the PRD) are out of scope
  by definition and are not restated here.
═══════════════════════════════════════════════════════════════════════════
-->

---
- vdc_id: VDC-000-slug
- title: ""
- status: Draft                  # Draft | In Review | Approved
- version: 0.1
- author: Aatmix AI
- direction: ""                  # design direction name, e.g. "Sovereign Stage"
- direction_spec: ax-sdd-artifacts/ax-design-specs/<app>-direction-<name>.md
- figma_file: ""                 # Figma file link
- figma_page: ""                 # Design_Direction_<name>
- prd_ref: ax-sdd-artifacts/prd/product-prd.md
- prd_version: ""                # PRD version this contract was last synced to
- rollout_progress: "0 / 0"      # rolled-out / total surfaces (excl. placeholders)
- created: YYYY-MM-DD
- updated: YYYY-MM-DD
- open_clarifications: 0         # GATE — must be 0 (blocking) before status can become Approved
---

## 1. Contract Summary

<!-- The whole contract in ~100 words. Always retrieved first. -->

- **Direction & platform:** <direction name · desktop/mobile/both · theme>
- **Coverage:** <N of M UI journeys fully mapped · X gaps · Y nav-promised
  surfaces awaiting design · Z orphan surfaces>
- **Rollout:** <N of M surfaces rolled out · last rollout run + date>

## 2. Direction Sitemap (derived)

<!-- The COMPLETE surface inventory derived by walking the design direction
     itself: every screen frame, modal, drawer, popup, sub-screen, contextual
     surface AND every navigation item — including nav items with no frame yet
     (Nav-promised) and reserved placeholders. One subsection per navigation
     area, in the direction's nav order; child surfaces (modals, drawers,
     popups opened from a parent) are indented under their parent using `└─`.
     Every surface appears exactly ONCE — a surface reachable from several
     places lives under its primary home and is cross-referenced in other
     surfaces' §5 reached-via lines.
     "Reached via" makes nesting explicit: a nav item, a parent surface +
     action, or a prototype interaction.
     "States drawn" lists the states the direction has actually drawn as
     frames/variants (usually `default`, plus any explicitly designed states).
     "Figma (rolled-out)" stays `—` until the rollout skill produces the
     screen and Update mode records the link. -->

### <Navigation area — e.g. Plane 3 · Project — Agents> <!-- nav order 1 -->

| ID | Surface (direction name) | Type | Reached via | Design status | States drawn | Rollout status | Figma (rolled-out) |
|---------|--------------------------|------|-------------|---------------|--------------|----------------|--------------------|
| VDS-001 | <e.g. 04 · Agent Board> | Page | nav item "<…>" | Direction-designed | default | Not started | — |
| VDS-002 | └─ <e.g. overlay · Work Item Details> | Modal | VDS-001 <action> | Direction-designed | default | Not started | — |
| VDS-003 | <nav item with no frame> | Page | nav item "<…>" | Nav-promised | — | Not started | — |

<!-- repeat one subsection per navigation area, in the direction's nav order -->

## 3. Requirement ↔ Surface Map   <!-- THE CORE of the contract -->

<!-- The traceability spine: one row per PRD journey with Surface = UI, in the
     PRD's order. "Mapped surface(s)" names the VDS- surface(s); where a
     specific STATE is the carrier, name it (e.g. "VDS-004 · creation-blocked
     state"); where only a nav item carries it, say so. A UI journey with no
     surface carries an explicit affirmation, never a blank.
     Coverage: Full — every step lands on a designed surface · Partial —
     name what is missing · Promised — carried only by a nav item, frame not
     yet designed · Gap — no surface and no nav item · No surface — affirmed:
     <reason>. -->

| Journey | Journey name | Related FRs | Mapped surface(s) — screen / state / nav item | Coverage |
|---------|--------------|-------------|-----------------------------------------------|----------|
| UJ-00x | | FR-00x | VDS-00x → VDS-00y <state/nav notes> | Full / Partial: <missing> / Promised / Gap / No surface — affirmed: <reason> |

### FR-level exceptions

<!-- ONLY FRs whose surface differs from their journey's mapping — exceptions,
     not repetition. `None — affirmed` is a valid body for this table. -->

| FR | Behaviour (short) | Journey | Surface that carries it | Status |
|--------|-------------------|---------|-------------------------|--------|
| FR-00x | | UJ-00x | VDS-00x <state/nav notes> | |

## 4. Orphan Surfaces & Divergences

<!-- The reverse check: surfaces present in the direction with no grounding
     requirement, or requirement-level contradictions discovered while
     mapping. Each row carries a disposition — mapped after clarification /
     raised in product-change report <path> / affirmed: <reason>.
     `None — affirmed` is a valid body for this table. -->

| Surface / finding | Where found | Issue | Disposition |
|-------------------|-------------|-------|-------------|
| | | | |

<!-- ═══════════════ DETAIL LAYER — retrieved on demand, self-contained ═══════════════ -->

## 5. Screen Contracts

<!-- Repeat this block for every §2 surface (Placeholder rows excepted — they
     get no block until taken up). Each block must stand alone. A merged or
     retired surface keeps a one-line TOMBSTONE in place of its block, so its
     VDS- ID still resolves:
     `### VDS-0xx — <name> · Merged into VDS-0yy / Retired · <date> · see §6` -->

### VDS-001 — <Surface name>

- **Type / reached via:** <Page / Modal / Drawer / Panel> · <nav item "…" /
  VDS-00x via <action> / prototype interaction> — cross-ref parents/children
- **Direction source:** frame `<frame name>` on `<figma_page>` <node link if resolvable>
- **Rolled-out Figma:** — <recorded by Update mode after rollout; per-state links welcome>
- **Purpose:** <what the user accomplishes here — one or two sentences>
- **Requirements served:** UJ-00x · FR-00x <IDs only — the mapping lives in §3;
  never restate requirement text here>
- **Nav obligations:** <what must be reachable from here; badges / counts /
  indicators the direction promises on this surface's nav item>
- **States drawn in direction:** <e.g. default + empty + loading>
- **Rollout owes:** <the states and interaction coverage rollout must design —
  e.g. "invalid-input, validation-error, permission-denied; hover/focus for
  the new card type">

#### States  <!-- ALL canonical states — specify or affirm, never omit -->

| State | Applies | Behaviour (observable) | Source |
|-------|---------|------------------------|--------|
| Empty / no data | Yes / N/A — affirmed | | establish / <run-slug> / PRD UJ-00x |
| Loading | Yes / N/A — affirmed | | |
| Filled / valid | Yes / N/A — affirmed | | |
| Invalid input | Yes / N/A — affirmed | | |
| Validation error | Yes / N/A — affirmed | | |
| Submission success | Yes / N/A — affirmed | | |
| Submission failure | Yes / N/A — affirmed | | |
| Permission denied | Yes / N/A — affirmed | | |
| <screen-specific state> | Yes | | |

- **Open clarifications:** CLR-00x (if any)
- **Last changed:** v0.x · <run slug> · <YYYY-MM-DD>

## 6. Retired & Merged Surfaces

<!-- Register of surfaces no longer in the sitemap. IDs are never deleted or
     reused; every historical reference resolves here. The survivor of a merge
     carries the absorbed surface's requirement mappings and notes it in its
     own §5 block. -->

| ID | Surface | Disposition | Date | Reason | Source |
|---------|---------|-------------|------|--------|--------|
| VDS-0xx | | Merged into VDS-0yy / Retired | YYYY-MM-DD | | <run ref> |

## 7. Rollout Log

<!-- Append-only; one row per Update-mode run (rollout sync or new intents). -->

| Date | Run / provenance | Surfaces affected | Contract changes | Change report |
|------|------------------|-------------------|------------------|---------------|
| YYYY-MM-DD | <rollout run / intent slug> | VDS-00x, VDS-00y | <links recorded, states settled, rows added> | <path> |

<!-- ═══════════════════════════ HOUSEKEEPING ═══════════════════════════ -->

## References

- PRD: <prd_ref + version> · Direction spec: <direction_spec> · Figma: <file + page>
- Change reports raised by contract runs: `ax-sdd-artifacts/ax-design-specs/vd-contract-change-reports/`
- Product-change reports raised: `ax-sdd-artifacts/prd/product-change-reports/`

## Open Clarifications  <!-- GATE -->

<!-- Aggregates every [NEEDS CLARIFICATION] in this contract. Keep the
     front-matter `open_clarifications` count of BLOCKING + Open rows in sync. -->

| ID | Question | Raised by | Blocking? | Status | Resolution |
|---------|----------|-----------|-----------|--------|------------|
| CLR-001 | | | Yes / No | Open / Resolved | |

> **Approval gate:** `status` cannot become **Approved** while any row here is
> *Blocking = Yes* and *Status = Open*, while any §3 row is `Gap` or lacks
> either a mapped surface or an explicit `No surface — affirmed` entry, while
> any §2 non-placeholder surface lacks a §5 block with every canonical state
> specified or affirmed, or while any §4 orphan lacks a disposition.
