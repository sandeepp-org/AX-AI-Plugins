# DS-Core Spec — template

Write to `ax-sdd-artifacts/ax-design-specs/<app>-ds-core-<direction>.md` (folder-create if missing). In update mode, update this same file — never fork. This spec is the **contract for the rollout skill / any downstream screen-designing AI**: with the Figma file plus this document, it must be able to design any new screen using only these tokens and components.

---

# <App Name> — Design System Core (Direction: "<Direction Name>")

> Built <date> by figma-ds-core · Figma file: <link> · Base DS: <name + version> · Direction spec: <path>
> Pages: `DS_Core_Components` · `DS_Core_Patterns` · `DS_Core_Templates`

## 1. How to use this core (rules for downstream AI)
- Bind ONLY `Semantics` / `ComponentTokens` variables and `DS/*` styles — primitives are scoped out <if Phase 8 ran>.
- Compose screens from Tier-3 templates first, Tier-2 patterns second, Tier-1 atoms last; never redraw a component that exists.
- Never introduce a new color/size/radius/shadow; if a screen needs a missing token or component, flag it for a ds-core update run instead of improvising.

## 2. Token dictionary
### 2.1 Semantics (<n> variables) — table: name · type · aliases → primitive · role/usage note
### 2.2 ComponentTokens (<n>) — per component group, same table
### 2.3 Styles — 12 text styles (role → family/size/weight/lh) · 3 effect styles
### 2.4 Direction Resolution Table — every DIRECTION-decided alias, its resolved primitive, and the spec section that decided it

## 3. Component API (one subsection per component, all tiers)
For each: **Figma set name** · variant properties + values · states · anatomy/slots · token bindings (the load-bearing ones) · **base-DS mapping** (component + prop map + wrapper notes/gaps) · usage do/don't · Figma node link.

## 4. Composition rules
Grid + page margins per template · which template fits which screen archetype · density rules (when Compact) · form layout rules (label position, error placement, footer actions) · overlay rules (Modal vs Drawer vs Popover) · empty/error state rules · motion tokens to apply per interaction (from direction §11).

## 5. Coverage & status
The full manifest table with per-row status (created / existed / waived / deferred + reason) and counts. WCAG summary. Known gaps and the intended follow-up run.

## 6. Change log
One line per run: date · what was added/changed · by which mode.
