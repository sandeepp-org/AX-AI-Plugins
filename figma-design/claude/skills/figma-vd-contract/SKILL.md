---
name: figma-vd-contract
description: Build and maintain the VISUAL DIRECTION CONTRACT (VD contract) — the markdown mapping layer that ties every UI surface requirement and journey in the PRD (the product model) to a concrete screen, screen state, or navigation item in an approved design direction's Figma page, and that carries the per-screen state ledger (all canonical experience states per screen). Runs in TWO modes — (A) ESTABLISH a new contract right after a design direction is created and approved via figma-design-direction, deriving the full sitemap from the direction (nav walk, popups, sub-screens, prototype interactions), mapping it to the PRD's UI surface requirements, and confirming the mapping with the user; (B) UPDATE an existing contract after every screen rollout (recording per-screen Figma links and settled states) or when new intents/business requirements extend the product incrementally (brownfield). One contract per design direction. Use when the user asks to "create the VD contract", "build the visual design contract", "map the PRD to the design direction", "update the VD contract after rollout", "sync the contract with the new screens", or "add new intents to the VD contract". Requires a Figma file with an existing Design_Direction_* page and a PRD with user journeys and UI surface requirements; Update mode additionally requires the existing contract (and the new intents, when extending). This skill writes the contract and change-report files only — it never writes to Figma and never edits the PRD.

---

# Visual Direction Contract → the PRD ↔ Design-Direction mapping layer

## In the Aatmix platform

When the Aatmix platform's UX Agent runs this skill, read every instruction below through this mapping. Everything else in the skill applies unchanged.

- **"The user"** is the UX Engineer, reached through the Exchange. Ask with `exchange_ask`: numbered replies, your recommendation marked, one round at a time — every open question of that round together, then end the turn. The run resumes with the answers. "Ask", "confirm", "get a go-ahead", "wait for approval" and "the interactive input tool" all mean such a round.
- **The Figma file** is the project's connected Figma file named in the run details. Only when none is connected, ask the UX Engineer for its URL.
- **The brand guidelines** are artifact `BRG`, already supplied by the project (run details name its version; read it with `artifacts_read`). Never ask for them.
- **The PRD** is the approved PRD artifact the run details name (`artifacts_read`), never a file.
- **Files the skill writes or reads under `ax-sdd-artifacts/`** live in the artifact store instead (`artifacts_list`, `artifacts_read`, `artifacts_put_draft`, then `artifacts_submit` for review as the task text says), under these kinds: direction spec → `visual_direction`; foundation report → `brand_primitives`; ds-core spec → `semantic_tokens` (its token half: the Semantics and ComponentTokens mapping and the styles) and `core_components` (its component half: the component inventory); VD contract → `vd_contract`; rollout record → `screen_rollout`; product-change report → the PRD change requests the task text names (never edit the PRD). A change report the skill writes beside a spec goes into the next version of that spec's artifact, as its own section.
- **Previews** (PNG or HTML of what you drew) go to `design_preview_put` against the project asset they belong to (`brand_primitives`, `visual_direction`, `semantic_tokens`, `core_components`, `vd_contract`), and to `screen_preview_put` against a screen's record.
- **Other skills**: `figma-ds-foundation`, `figma-design-direction`, `figma-ds-core`, `figma-vd-contract` and `figma-vd-rollout` are this plugin's skills; `figma-use`, `figma-generate-design` and `figma-generate-library` come from the Figma plugin.
- **In this skill**: the direction spec is the approved `visual_direction` artifact. Mode A (Establish) writes the `vd_contract` artifact; Mode B (Update) writes its next version, with the contract change report as its own section. The UI/UX model (`uiux_model`) stays deprecated.

Produce and maintain the **VD contract**: a markdown file that records, as settled facts, **where every UI surface requirement and journey in the PRD lands in the design direction** — which screen, which state of that screen, or which navigation item — plus the **complete per-screen state ledger**. The mapping is the core essence of the contract; the states are its second core duty.

```
ds-foundation → design-direction (approved) → THIS skill (VD contract) → screen rollout → THIS skill (Update mode, after every rollout)
```

The contract is the bridge downstream skills stand on: the rollout skill reads it as its work order (which surfaces exist, which states each owes), and every rollout run flows back into it through Update mode. The UI/UX model is deprecated — **do not read it, reference it, or depend on it**; the VD contract is now the single home of screen-inventory and screen-state truth.

## Two run modes — detect first

- **Mode A — Establish (default).** No contract exists yet for the named direction. Derive the sitemap from the direction, map it to the PRD, confirm the mapping with the user, and write a fresh contract from the template.
- **Mode B — Update.** A contract already exists. Two triggers, which may combine in one run:
  - **Rollout sync** — a screen rollout just completed: record the rolled-out screens' Figma links, flip their rollout status, settle any states the rollout drew, and append the rollout log.
  - **New intents** — new business requirements extend the product: map the new/changed UI requirements to surfaces (existing or newly derived), add `VDS-` rows/blocks for new surfaces, and re-run the coverage gates.

**Choose Mode B when** the user references an existing contract, asks to "update/sync" it, mentions a completed rollout, or supplies new intents for an existing direction. When ambiguous, ask. Mode B **never rewrites** the contract — it applies deltas, appends to the rollout log, and preserves every `VDS-` ID (IDs are immortal; retired surfaces move to the §6 register with a tombstone).

## What this is — and is NOT

| This skill DOES | This skill does NOT |
|---|---|
| Derive the full sitemap from the direction's Figma page (nav walk, modals, popups, sub-screens, prototype interactions) | Design screens or write anything to Figma (read-only) |
| Map every UI journey/FR to a screen, state, or nav item | Restate requirement text (link PRD IDs only) |
| Carry the per-screen state ledger (all canonical states, screen-wise) | Record visual decisions (that is the direction spec) |
| Confirm the mapping with the user, iterating to a perfect mapping | Invent or silently assume a mapping |
| Record per-screen rolled-out Figma links (Update mode) | Produce those links (the rollout skill does) |
| Raise change reports as separate artifact files | Edit the PRD (single writer: ax-skills-apply-to-prd) |

## Hard guardrails (read first)

1. **Figma is read-only.** Use only read tools (`get_metadata`, `get_screenshot`, `get_design_context`) to walk the direction page. Never call `use_figma` or any write path.
2. **Single writer, one contract per direction.** This skill is the only writer of `<app>-vd-contract-<direction-slug>.md`. A product with several direction alternatives gets one contract per direction taken forward. Never fork a contract inside a run; never let another skill's output be pasted in unverified.
3. **Never invent a mapping.** Every requirement→surface row is a best-effort *proposal* until the user confirms it in Phase 4. Where the mapping is unknowable, insert `[NEEDS CLARIFICATION: …]` and log it — never guess silently.
4. **States are never omitted.** Every screen contract block enumerates ALL canonical states (empty/no-data · loading · filled/valid · invalid-input · validation-error · submission-success · submission-failure · permission-denied) plus screen-specific ones; a non-applicable state is written `N/A — affirmed`, never dropped. This is the template's format — keep the states table screen-wise, exactly as templated.
5. **No UX-model dependency.** The UI/UX model (`ax-sdd-artifacts/ux/uiux-model.md`) is deprecated. Do not read it, map through it, or reuse its `SCR-` IDs. Surfaces carry contract-native immortal `VDS-` IDs.
6. **The PRD is read-only and its text stays in the PRD.** Reference journeys/FRs by ID (`UJ-00x`, `FR-00x`). A product-level divergence discovered while mapping (a journey step with no possible home, an FR contradicted by the direction) is raised in a **product-change report** file — never absorbed, never edited into the PRD.
7. **`VDS-` IDs are immortal.** Assigned in discovery order, never deleted, never reused. Merges/retirements go through the §6 register with a tombstone block; the survivor absorbs the requirement mappings.
8. **Gates are deterministic.** The contract cannot be reported as Approved-ready while any §3 row is `Gap` or unaffirmed, any non-placeholder surface lacks a complete states block, any orphan lacks a disposition, or any blocking clarification is open. Report gate failures honestly; never paper over them.
9. **Rollout links are recorded, not invented.** Per-screen Figma links enter the contract only in Update mode, from the rollout run's actual output. Establish mode leaves them `—`.

## Required inputs — validate first, ask if missing, do not proceed without

1. **Figma file link** — the file containing the design direction. Verify a `Design_Direction_<name>` page actually exists; if several exist and the user didn't name one, list them and ask which direction this contract binds to.
2. **Product model (PRD)** — default `ax-sdd-artifacts/prd/product-prd.md`, or a user-provided path. **Read it and validate** it articulates (a) user journeys with a Surface column (or equivalent) identifying UI journeys, and (b) UI surface requirements (FRs tied to those journeys). If the PRD is missing or lacks these, tell the user exactly what is missing and ask them to provide it — **stop** until it exists.
3. **[Mode B] Previously created VD contract** — the path to the existing contract for this direction. If the user asked for an update but no contract is found, list any `*-vd-contract-*.md` files in `ax-sdd-artifacts/ax-design-specs/` and ask; never silently fall back to Establish mode.
4. **[Mode B — new intents] The new intents** — the business requirements being added incrementally. Without them an intent-update run has nothing to map; ask and stop.

If any required input is missing, ask for it (use the interactive question tool for a clean prompt) and **stop** until provided.

### Optional inputs (use if given)

1. **User prompt / additional intents** — extra context, priorities, or constraints for the mapping.
2. **A reference to the visual design** — the direction spec (default `ax-sdd-artifacts/ax-design-specs/<app>-direction-<name>.md`) and/or specific Figma node links. The spec accelerates Phase 2 (its screen list, per-screen notes, and nav IA are a cross-check) — but the **Figma page is the ground truth**; where spec and page disagree, trust the page and note the discrepancy.

## Workflow

Run in order. The template lives at **`references/vd-contract-template.md`** (in this skill's folder) — it is the normative structure; review it in Phase 1 and never deviate from its sections, vocabularies, or gates.

### Phase 0 — Mode, inputs & target

- Detect the mode (see *Two run modes*); when ambiguous, ask.
- Validate the required inputs for that mode — otherwise ask and stop.
- Resolve the Figma link to the file and the `Design_Direction_<name>` page; state both back.
- **[Mode B]** Read the existing contract in full. Note its `VDS-` high-water mark (new surfaces continue the sequence), its version, and its rollout log.

### Phase 1 — Review the template and the inputs

- Read `references/vd-contract-template.md` end-to-end — the HOW-TO-USE header defines the boundary, ID rules, status vocabularies, state canon, and coverage gates this run must enforce.
- From the template, derive the information this run must collect: the surface inventory, reached-via topology, design/rollout statuses, the requirement↔surface mapping, per-screen states, orphans, clarifications.
- Read the PRD **digest**: the journey index filtered to `Surface = UI` (with priorities and related FRs) and the FR index. Pull detailed journey blocks on demand when mapping needs step-level understanding.
- Read the direction spec if available (optional input #2).

### Phase 2 — Derive the sitemap from the direction (never skip, never trust lists alone)

Derive the **entire sitemap** from the direction's Figma page itself:

- Enumerate every top-level frame on the `Design_Direction_*` page via `get_metadata`; classify screens vs. overlays/modals vs. cover/legend/interaction-strip frames (excluded from the sitemap).
- **Walk the navigation, item by item**, on every screen that shows nav (screenshot + design context): every nav item, group, and plane is part of the sitemap — including items with **no drawn frame** (Design status `Nav-promised`) and items marked V2/reserved (`Placeholder`). Note badges/counts/indicators a nav item promises — they become §5 nav obligations.
- **Follow the prototype**: read interactions/reactions to discover popups, overlays, and sub-screens nested inside screens, and record each surface's *reached-via* (nav item / parent + action / prototype interaction).
- Catch non-nav surfaces: modals opened from buttons, drawers, contextual surfaces reached from within another surface's content.
- Cross-check against the direction spec's screen list; the page is ground truth, discrepancies are noted.
- Assign `VDS-` IDs in discovery order (**[Mode B]** continue from the high-water mark; existing surfaces keep their IDs), organize the §2 sitemap by navigation area in the direction's nav order, with `└─` nesting under parents, every surface exactly once. Record per surface: type, reached-via, design status, and **which states the direction actually drew**.

### Phase 3 — Best-effort requirement ↔ surface mapping

- For every PRD journey with `Surface = UI` (in PRD order), propose the mapped surface(s): the `VDS-` screen(s) in journey-step order, naming a **specific state** where a state is the carrier (e.g. "creation-blocked state") and naming a **nav item** where only navigation carries it.
- Classify coverage per the template vocabulary: `Full` / `Partial: <missing>` / `Promised` (nav item only) / `Gap` / `No surface — affirmed`.
- Map FR-level exceptions only — FRs whose surface differs from their journey's mapping. Never re-map every FR; that duplicates the PRD.
- Run the reverse check: direction surfaces with **no grounding requirement** become §4 orphan candidates.

### Phase 4 — Present the mapping and iterate with the user (the heart of the run)

- Present the full §3 table and the §4 orphan candidates in a readable form, flagging every row where confidence is low and every proposed affirmation.
- **Engage the user as a peer**: ask them to correct rows, resolve orphans, and confirm affirmations from their product knowledge. Iterate — present, absorb corrections, re-present the changed rows — until the user confirms the mapping. The confirmed mapping is what gets written; a mapping the user has not confirmed is a proposal, not a contract.
- Where a mapping question is really a **requirement ambiguity** (the PRD doesn't settle it), ask the clarifying question explicitly; unresolved ones become `[NEEDS CLARIFICATION]` entries in the contract's clarification register.

### Phase 5 — State analysis (screen-wise, per surface)

- For every non-placeholder surface, build its full states table: walk the canonical set and any screen-specific states, writing the **observable behaviour** of each applicable state (grounded in the PRD's journey edges/exceptions and the direction's drawn states) and affirming the rest (`N/A — affirmed`).
- Record per surface which states the **direction drew** and which the **rollout owes** (states plus interaction coverage not yet designed) — the rollout skill's work order.
- Where a state's behaviour is unknowable from the inputs, ask the user; unresolved → `[NEEDS CLARIFICATION]`.

### Phase 6 — Expert coverage interrogation

Question the direction's navigation against the PRD like an expert UX engineer, and put the sharp findings to the user:

- Is every UI journey **reachable** — does a user starting at the entry surface actually get to every mapped surface through drawn navigation, or does a step dead-end?
- Do any nav items lead nowhere (promised, never designed) or duplicate one another?
- Is any requirement carried **only by a state** where the journey's weight deserves a surface (or vice versa)?
- Are cross-surface promises consistent (badges/counts on nav items vs. the surfaces that own the data; a modal's host surface vs. the journeys that need the modal)?
- Do persona/permission expectations in the PRD have a home (permission-denied states, read-only variants)?

Resolutions update the mapping and states; genuine product-level findings go to a product-change report; the rest become clarifications.

### Phase 7 — Write the contract / apply the update, then gate and report

- **[Mode A]** Write `ax-sdd-artifacts/ax-design-specs/<app>-vd-contract-<direction-slug>.md` from the template — every section, the confirmed mapping, the full state ledger, orphan dispositions, clarifications.
- **[Mode B]** Apply the delta to the existing contract: record rolled-out Figma links and flip rollout statuses; settle states a rollout drew; add rows/blocks for new surfaces from new intents; update `rollout_progress`, `prd_version`, `version`, `updated`; append one §7 Rollout Log row for this run. Untouched sections stay byte-identical.
- **Change reports (separate files, both modes):**
  - **Contract change report** — every **Mode B** run writes `ax-sdd-artifacts/ax-design-specs/vd-contract-change-reports/<YYYYMMDD-HHMMSS>-<direction-slug>.md`: what changed in the contract (surfaces added/retired, links recorded, mappings changed, states settled), why, and the run's provenance; the §7 log row links to it. Mode A needs none (the contract itself is the record).
  - **Product-change report** — if mapping surfaced a divergence at the **product WHAT/WHY** level (a journey step with no possible home, an FR the direction contradicts, a missing dependency), write `ax-sdd-artifacts/prd/product-change-reports/<YYYYMMDD-HHMMSS>-vd-contract-<direction-slug>.md` following the `ax-skills-apply-to-prd` contract: one entry per divergence, WHAT/WHY only, grounded entity, proposed change, provenance. **Never edit the PRD.** If nothing diverged, write no report and say so.
- **Run the coverage gates** from the template and report the result honestly: which gates pass, which fail and why (each `Gap`, each incomplete state block, each open blocking clarification).
- **Final report:** contract path (created/updated + version), coverage summary (UI journeys full/partial/promised/gap, orphans and dispositions), rollout progress, gate status, clarifications opened/resolved, and the change-report paths (or "none needed").

## The craft — rules that make the contract trustworthy

- **The mapping is the product.** A row the user corrected is worth ten the agent guessed. Spend the conversation budget in Phase 4 and 6, not on prose.
- **Ground truth over documents.** The Figma page beats the spec's screen list; the PRD's journey index beats anyone's memory of it. Derive, then cross-check, then ask.
- **Name the state, not just the screen.** "UJ-019 → VDS-004" is weak; "UJ-019 → VDS-004 via the New Work Item modal, blocked-submission state when no product model is confirmed" is a contract.
- **Nav items are promises.** A nav item with no frame is a real surface in the sitemap (`Nav-promised`) — losing it silently loses a requirement's only home.
- **Every gap is loud.** `Gap`, orphan, and `[NEEDS CLARIFICATION]` rows are the contract doing its job; hiding them is the only true failure.
- **Deltas, never rewrites.** In Update mode, a diff of the contract should read as exactly the run's changes and nothing else.

## Reference files

- `references/vd-contract-template.md` — the normative VD-contract template: section structure, ID rules, status vocabularies, canonical state set, and the approval/coverage gates this skill enforces.
