# Prompt template — `figma-vd-contract`

Copy the block that matches your run, fill in the `<…>` placeholders, and paste it as your message. The first line triggers the skill. **One contract = one design direction** — the contract is bound to a single `Design_Direction_<name>` page and its spec.

The skill has **two modes**:

| Mode | When | Template |
|---|---|---|
| **A — Establish** | Right after a design direction is created and approved (`figma-design-direction`) — no contract exists yet for it | [Template A](#template-a--establish-a-new-contract-mode-a) |
| **B — Update · rollout sync** | A screen rollout just finished — record its Figma links and the states it drew | [Template B1](#template-b1--update-after-a-screen-rollout-mode-b) |
| **B — Update · new intents** | New business requirements extend the product (incremental / brownfield) | [Template B2](#template-b2--update-with-new-intents-mode-b) |

B1 and B2 can be combined in one run — paste both sections under a single "Update the VD contract…" first line.

---

## Template A — Establish a new contract (Mode A)

```text
Create the VD contract using the figma-vd-contract skill.

## Design Direction Figma file  (REQUIRED)
<paste the Figma file link — it must already contain the approved
 Design_Direction_<name> page this contract binds to>

## Design direction name  (REQUIRED if the file has more than one direction)
<the exact <name> of the Design_Direction_<name> page, e.g. "Sovereign Stage">

## Product model / PRD  (REQUIRED)
<path or link to the PRD — default: ax-sdd-artifacts/prd/product-prd.md>
<it must articulate user journeys with a Surface column and the UI surface
 requirements (FRs) tied to them; the skill validates this and stops if missing>

## Visual design reference  (optional)
Direction spec: <path — default ax-sdd-artifacts/ax-design-specs/<app>-direction-<name>.md>
Specific nodes: <any Figma node links worth reading first>

## Additional intents / context  (optional)
- <priority, constraint, or context that should steer the mapping>
- <e.g. "journeys UJ-019/020 are the v1 critical path — map those first">
- <e.g. "the Installation plane is out of scope for this contract">

Note: derive the sitemap from the Figma page itself (nav walk, popups,
sub-screens, prototype interactions) — not just the spec's screen list.
Present the mapping to me before writing; I'll correct it.
```

---

## Template B1 — Update after a screen rollout (Mode B)

Use this after **every** screen rollout. The rollout skill produces the per-screen Figma links; this run **records** them in the contract, flips rollout statuses, settles the states the rollout drew, and appends the rollout log + a change report.

```text
Update the VD contract after a screen rollout using the figma-vd-contract skill.

## Existing VD contract  (REQUIRED)
<path — e.g. ax-sdd-artifacts/ax-design-specs/<app>-vd-contract-<direction-slug>.md>

## Design Direction Figma file  (REQUIRED)
<the SAME Figma file containing the direction and the rolled-out screens>

## Rollout run output  (REQUIRED)
<path to the rollout run's report/spec, or paste the rolled-out screens below>
1. <VDS-00x or surface name> — Figma: <node link> — states rolled out: <default, empty, loading, …>
2. <VDS-00y or surface name> — Figma: <node link> — states rolled out: <…>

## Notes  (optional)
- <anything the rollout changed that the contract should know: a surface split,
  a state that turned out N/A, a new sub-screen that appeared>

Note: apply deltas only — never rewrite the contract. Preserve every VDS- ID,
append one Rollout Log row, and write the change report.
```

---

## Template B2 — Update with new intents (Mode B)

Use this when **new business requirements** extend the product and the contract must absorb them.

```text
Update the VD contract with new intents using the figma-vd-contract skill.

## Existing VD contract  (REQUIRED)
<path — e.g. ax-sdd-artifacts/ax-design-specs/<app>-vd-contract-<direction-slug>.md>

## Design Direction Figma file  (REQUIRED)
<the SAME Figma file containing the Design_Direction_<name> page>

## New intents / business requirements  (REQUIRED)
1. <intent — what the business now needs users to be able to do>
   PRD grounding (if already in the PRD): <UJ-00x / FR-00x>
2. <intent …>
   PRD grounding: <…>

## Product model / PRD  (REQUIRED)
<path — the PRD version these intents are grounded in;
 if the intents are NOT yet in the PRD, say so — the skill will map them
 best-effort and raise a product-change report rather than editing the PRD>

## New screens designed for these intents  (optional)
<if figma-design-direction Mode B already added screens for them, name the
 new frames so the sitemap derivation picks them up as Direction-designed>

## Notes  (optional)
- <constraints, priorities, or surfaces you already know these should land on>

Note: apply deltas only. New surfaces continue the VDS- sequence; existing IDs
never change. Present the new/changed mapping rows to me before writing.
```

---

## Filled example

```text
Create the VD contract using the figma-vd-contract skill.

## Design Direction Figma file
https://www.figma.com/design/2p1w8n65o4NhlESGC24drG/AX-AI-PLATFORM-V1

## Design direction name
Sovereign Stage

## Product model / PRD
ax-sdd-artifacts/prd/product-prd.md

## Visual design reference
Direction spec: ax-sdd-artifacts/ax-design-specs/aatmix-ai-direction-sovereign-stage.md

## Additional intents / context
- The board-centric operating model is settled: work-item surfaces are reached
  through the Work Item Details modal, not from navigation — map them that way.
- V2-tagged nav items (Guardrails, Token Economy, Evals, Recents, Favorites)
  are placeholders — list them, keep them out of the coverage gates.
- The unified HITL Inbox is newer than the rest of the direction; check its
  journey coverage carefully.

Note: derive the sitemap from the Figma page itself (nav walk, popups,
sub-screens, prototype interactions) — not just the spec's screen list.
Present the mapping to me before writing; I'll correct it.
```

---

## How each field maps to the skill

| Field | Drives |
|---|---|
| Design Direction Figma file | Phase 0 target resolution + Phase 2 sitemap derivation (read-only; the page is ground truth) |
| Design direction name | Which `Design_Direction_<name>` page the contract binds to — one contract per direction |
| Product model / PRD | Phase 0 input gate (validated for UI journeys + surface requirements) and Phase 3's requirement side of the mapping |
| Visual design reference | Optional Phase 1 read — accelerates derivation as a cross-check; never overrides the Figma page |
| Additional intents / context | Steers Phase 3 mapping priorities and Phase 6's coverage interrogation |
| Existing VD contract (B) | Phase 0 Mode B detection, `VDS-` high-water mark, and the delta base |
| Rollout run output (B1) | The per-screen Figma links and settled states recorded in §2/§5 + the §7 Rollout Log row |
| New intents (B2) | New/changed §3 mapping rows and any new `VDS-` surfaces and §5 blocks |
| New screens designed (B2) | Tells Phase 2 which new frames to expect as `Direction-designed` rather than `Nav-promised` |

## Notes

- **A design direction must exist first.** If there's no `Design_Direction_*` page in the file, run `figma-design-direction` before this. The skill verifies the page exists and stops if it doesn't.
- **The PRD gate is real.** The skill reads the PRD and validates it articulates user journeys (with a Surface column identifying UI journeys) and the UI surface requirements tied to them. If it doesn't, the run stops and tells you exactly what's missing — provide or fix the PRD, then re-run.
- **Expect a conversation.** Phase 4 presents the full requirement↔surface mapping and Phase 6 asks expert coverage questions (reachability, dead nav items, state-vs-screen carriers, cross-surface promises). Correct the rows — a mapping you confirmed is worth far more than one the skill guessed. The contract is written only after you confirm.
- **The UI/UX model is deprecated** and is not read or referenced. Surfaces carry contract-native `VDS-` IDs, and the contract itself now carries the per-screen state ledger (all canonical states, screen-wise).
- **Figma is read-only here.** This skill never writes to Figma. Per-screen Figma links for rolled-out screens are *produced* by the rollout skill and *recorded* here in Mode B — Establish mode leaves them blank on purpose.
- **Update runs are deltas, never rewrites.** `VDS-` IDs are immortal; retired or merged surfaces move to the contract's §6 register with a tombstone. A diff of the contract after a Mode B run should read as exactly that run's changes.
- **Change reports are separate files.** Every Mode B run writes `ax-sdd-artifacts/ax-design-specs/vd-contract-change-reports/<timestamp>-<direction-slug>.md`. Product-level divergences (a journey with no possible home, an FR the direction contradicts) go to `ax-sdd-artifacts/prd/product-change-reports/` — the skill never edits the PRD.
- **Run it after every rollout.** The contract is the rollout skill's work order (which surfaces exist, which states each still owes); letting it drift behind the rollout is what makes it untrustworthy.
