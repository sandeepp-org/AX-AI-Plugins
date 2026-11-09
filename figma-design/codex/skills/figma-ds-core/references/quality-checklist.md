# Quality checklist (Phase 9) — all passes required before presenting

Screenshot as you check. Fix every violation and re-check; report results honestly, including accepted trade-offs.

## A. Token pass

- [ ] Manifest-diff gates for `Semantics`, `ComponentTokens`, text styles, effect styles are clean (0 MISSING or named waivers).
- [ ] Every variable in both collections is an **alias** — zero raw values (script-check `valuesByMode` types).
- [ ] No alias chain broken (resolve each to a primitive; flag dangling IDs).
- [ ] Scopes match the catalog; both collections have exactly one mode `Default`.
- [ ] Direction Resolution Table decisions all applied (spot-check 10 DIRECTION-marked tokens against the direction spec).
- [ ] **WCAG AA**: text/primary·secondary on every surface/*; action text on action bg (all states); status text on status bg-subtle and text-on-bold on bg-bold; link on page; selected/text on selected/bg. Record the ratio table.

## B. Per-component pass (every set, every variant)

- [ ] Full variant matrix present per catalog (count matches; variant property names/values exact).
- [ ] Zero-hardcoded audit clean: all paints variable-bound, all text on `DS/*` styles, all radii/strokes/paddings/gaps bound.
- [ ] Auto-layout correct: resizes sanely when text lengthens (test one long-label variant per set).
- [ ] States are visually distinct AND consistent (all Hover states shift the same way; Focus always shows `color/focus/ring` at `border-width/focus`; Disabled always uses the disabled tokens, never opacity-faked unless the catalog says so).
- [ ] Touch/click targets ≥ direction minimum (default 40px for md controls; icons inside have padding, not tiny frames).
- [ ] Patterns contain **instances** of atoms (no detached copies, no redrawn lookalikes).
- [ ] Doc frame present: name, base-DS mapping, usage line, do/don't.
- [ ] Real content everywhere — no lorem, no "Label".

## C. System pass

- [ ] **Direction conformance**: place Button, Card, Input, NavItem, Modal beside the direction page's screens — indistinguishable in personality (radius, density, color roles, elevation, type). Any seam = violation.
- [ ] Cross-component consistency: one control height per size across Button/Input/Select; one radius per role; identical focus treatment; icon family uniform.
- [ ] Base-DS parity table has a row for every built component, with gaps resolved (not blank).
- [ ] Templates use the direction's exact nav model and grid; slots clearly marked.
- [ ] Pages are only `DS_Core_Components|Patterns|Templates`; nothing else in the file was modified (diff the touched-node list).
- [ ] If Phase 8 ran: all primitives scoped `[]`, values untouched.

## D. Report

Produce: manifest totals vs created/existing/MISSING/waived per tier · WCAG ratio table · conformance screenshots · parity gaps resolved · waivers/deferrals with reasons.
