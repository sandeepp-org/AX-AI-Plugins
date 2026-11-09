# States & motion

> Part of `figma-vd-rollout`. Phase 4 plans states from here, Phase 6 draws them, Phase 7 wires the motion.

## Part 1 · The canonical state set

Walk this list for **every** screen. Each state is either **drawn as its own full-screen frame** or written `N/A — affirmed — <reason>` in the plan and the spec. Never dropped silently.

| State | Applies when | What the frame must show |
|---|---|---|
| **Default / filled** | Always | The screen doing its job with real, representative data |
| **Empty — no data yet** | The screen shows a collection the user can create into | Why it's empty, what the thing is, and the primary way to create the first one. Never a bare "No data." |
| **Empty — no results** | The screen has search/filter | That the filter (not the data) caused it, which filter, and a one-click way to clear it |
| **Loading — initial** | Data is fetched | The product's single loading treatment (skeleton *or* spinner — one rule everywhere), with the shell already in place so the layout doesn't jump |
| **Loading — in-place / submitting** | An action is in flight | The affected control in its busy state, the rest of the screen still readable; destructive/irreversible actions disabled while in flight |
| **Invalid input** | Any form field | Field-level messages at the position the ds-core spec prescribes, on the offending fields, with the submit affordance's state made explicit |
| **Validation / business-rule error** | Rules beyond field format | The rule that failed in plain language, where it applies, and what the user can do about it |
| **Submission success** | A write completes | The confirmation (inline, toast or state change per the direction), the new truth reflected on screen, and the next step |
| **Submission failure** | A write fails | What failed, whether the data was kept, and a retry. Never a raw error code alone |
| **Permission denied / read-only** | Personas differ in access | Exactly what is denied, what the user *can* still do, and who to ask. Read-only variants show the data without the actions |
| **Partial / degraded** | A dependency can be unavailable | Which part is unavailable, that the rest still works, and whether the data shown is stale |
| **Long-running / background** | Work continues beyond the request | Progress or status, whether the user can leave, and how they'll be told when it's done |
| **Screen-specific** | Domain states the requirements imply | Anything the requirements name — blocked, expired, archived, over-quota, conflict, first-run, offline |

**Rules**
- A state frame is a **complete screen** — the full shell with the state applied to the affected region, never a cropped fragment.
- States are expressed through the components' own **state variants** (`disabled`, `error`, `loading`, `empty`), never by restyling a default instance.
- The same state looks the same on every screen. Empty, loading and error are product-wide patterns, not per-screen inventions.
- If a state's behaviour isn't knowable from the requirements, **ask** — it is a requirement gap, not a design choice.

## Part 2 · Motion

Motion is built as **Figma prototype reactions** via `use_figma` (`setReactionsAsync`) so it plays in Figma. Load the `figma-use` skill for the reactions API; add `figma-use-motion` only for in-node timeline micro-animations.

### What to wire (the required set)

1. **Screen-to-screen navigation** — from the nav component instance to each screen this run created, using the direction's page-transition character.
2. **Overlay open/close** — modals, drawers, menus, popovers, toasts as **overlay interactions** (`OPEN_OVERLAY` / `CLOSE_OVERLAY`), with the direction's entry/exit (typically dissolve or small slide + fade), plus the backdrop treatment.
3. **In-screen state transitions** — the state frames connected in the order a user experiences them: default → loading → filled; submit → loading → success | failure; empty → created.
4. **Interaction feedback** — the primary controls' hover / pressed / focus states, animated with **Smart Animate** between the component's own variants.

### How it should feel

- **Purpose only.** Every animation communicates a state change: feedback, transition, or guidance. Decoration is a defect.
- **Fast.** Most UI transitions **100–300 ms**; overlays and page transitions at the longer end, feedback at the shorter.
- **Eased, never linear.** Ease-out for entrances, ease-in-out for movement between states — taken from the `motion/easing/*` token values.
- **Tokenized.** Read the `motion/duration/*` and `motion/easing/*` variable values and apply those numbers. Two elements of the same type never animate at different speeds.
- **Subtle.** Fade, small slide, small scale. No bounce, spin or overshoot unless the direction explicitly established it.
- **Only the changing element moves.** Never animate the whole screen for a local change.
- **Spatially continuous.** Things enter from and exit to where they logically come from — an overlay from its trigger, a row into its detail. Smart Animate requires **matching layer names** across frames; keep names identical or it will cross-fade instead of moving.

### Reuse over invention

If the design direction already demonstrated a motion (its page-local motion components and prototype), reuse that exact treatment and those durations. A rollout extends the direction's motion language; it never starts a second one.

### What cannot be built here

Reduced-motion preferences and interruptible/non-blocking behaviour cannot be expressed in a Figma prototype. Record them as **handoff notes in the rollout spec** — every animation must be skippable under `prefers-reduced-motion`, and no animation may block input — and do not pretend the prototype covers them.
