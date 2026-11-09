# plugin-ax-ai-figma-design

Five Figma design-system skills for coding agents, run through the Figma MCP (`use_figma`, with the Figma plugin's
`figma-use`, `figma-generate-design` and `figma-generate-library` skills). Together they take a product from its
branding guideline to production screens: primitives, a chosen visual direction, the design-system core, the contract
that maps the PRD onto the direction, and the screen rollout.

One top-level folder per CLI (`claude/`, `codex/`), each shipping the same five skills (the two `skills/` trees are
identical). This is the layout the ax-ai-platform plugin fetcher expects (`paths` default: `claude/`, `codex/`).

## Pipeline

```
figma-ds-foundation → figma-design-direction (×3–4, one chosen) → figma-ds-core → figma-vd-contract (Establish)
                                                                                 → figma-vd-rollout (per screen) → figma-vd-contract (Update)
```

Each skill checks that its inputs exist in the Figma file and routes back to the earlier skill when they do not.

## Skills

| Skill | Builds | Key outputs |
|---|---|---|
| **figma-ds-foundation** | The primitive foundation from the brand guidelines | Variable collections `BrandPrimitives` (brand colour ramps, typography) and `App-Primitives` (grays, semantic ramps, spacing, radius, border, elevation, opacity, motion, z-index, breakpoints); a foundation report with the WCAG summary |
| **figma-design-direction** | One visual-direction alternative per run (Mode A), or more screens on an existing direction (Mode B) | A `Design_Direction_<name>` page of key screens bound to the primitives, a motion prototype, and the direction spec; run 3–4 times for distinct alternatives, then choose one |
| **figma-ds-core** | The design-system core from the chosen direction | `Semantics` and `ComponentTokens` (aliases only), `DS/*` text and effect styles, the component library on `DS_Core_Components`, `DS_Core_Patterns`, `DS_Core_Templates` with 1:1 base-design-system parity, and the ds-core spec |
| **figma-vd-contract** | The VD contract (read-only on Figma) | The PRD-to-direction mapping (every UI journey to a screen, state or nav item), the per-screen state ledger with immortal `VDS-` ids; Update mode records rollouts and new intents |
| **figma-vd-rollout** | Production screens | One frame per screen state, built from component instances and semantic tokens only, motion wired as a prototype, the rollout spec, design-system additions through the gap protocol |

Each skill folder holds its `SKILL.md`, a `PROMPT-TEMPLATE.md` to start a run by hand (not figma-ds-foundation;
figma-vd-rollout also has `PROMPT-README-HTML.md` for an HTML mockup as the source), and its `references/`.

## In the Aatmix platform

Every `SKILL.md` opens with an "In the Aatmix platform" section. When the platform's UX Agent runs a skill:

- "the user" is the UX Engineer, asked through the Exchange (`exchange_ask`, numbered replies, recommendation marked,
  one round at a time);
- the branding guideline is the project's artifact `BRG` (never asked for), the PRD is the approved PRD artifact;
- file outputs go to the artifact store: direction spec → `visual_direction`, foundation report → `brand_primitives`,
  ds-core spec → `semantic_tokens` (tokens and styles) and `core_components` (the component inventory), VD contract → `vd_contract`, rollout record → `screen_rollout`, product-change report →
  the PRD change requests the task names;
- previews go to `design_preview_put` (project assets) and `screen_preview_put` (screen records).

The platform's design-system item runs foundation, three directions (the UX Engineer chooses and gives feedback),
ds-core and, on a greenfield project, the VD contract; later items run the rollout and the contract update.

## Register in ax-ai-platform

Add to `aatmix/config/plugins.yaml` (and the committed `plugins.example.yaml` / `deploy/config/plugins.yaml`):

```yaml
  - id: figma-design
    url: https://github.com/chandra-pangea/plugin-ax-ai-figma-design
    ref: main
```

No `paths` needed — each CLI reads its own top-level folder. The fetcher downloads the plugin again when `main` moves.

## Conventions

- Every visual property is bound to a variable — zero hardcoded hex or px. Primitives hold raw values; everything
  above them is an alias.
- `use_figma` is the only write path; load the Figma skills first. Scripts are atomic — on error, fix and retry.
- Idempotent and additive: everything is tagged with shared plugin data; re-runs add only what is missing.
- One light theme; no dark mode or extra themes unless asked.
- The skills propose and the person decides: every build plan, brief and mapping is confirmed before it is written.
