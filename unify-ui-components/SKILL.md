---
name: unify-ui-components
description: Audit EndGods UI component families shared by at least two consumers and choose canonical primitive, Prefab Variant, screen-specific, or temporary ownership. Use when the user requests consolidation; read-only by default.
---

# Unify UI Components

Own cross-consumer component consistency and canonical-family decisions. Do not use this skill to redesign one screen, generate art, or implement Unity UI by itself.

## Scope gate

1. Require at least two concrete consumers. Name their screens, Scenes, Prefabs, or component instances.
2. If only one consumer is in scope, use `game-ui-designer` for player-experience decisions or `ui-ux` for authorized implementation.
3. Default to a read-only audit and proposal. Do not edit Scenes, Prefabs, assets, briefs, or code unless the user explicitly authorizes implementation.
4. Do not invent a new gameplay system, visual language, token, dimension, state, or breakpoint. Resolve them from current project authority.

## Read authority

1. Read `AGENTS.md` and the UI routes in `Docs/INDEX.md`.
2. Read `Docs/Pipeline/ui-vibe-coding-pipeline.md` and `Docs/rules/rules_ui.md`.
3. Load only the authorities needed for the family:
   - visual language and existing primitives: `Docs/rules/rules_ui_guide.md`
   - colors and style tokens: `Docs/rules/rules_uicolor.md`
   - bitmap, importer, provenance, and promotion: `Docs/rules/rules_assets.md` and `Docs/rules/rules_resources.md`
   - screen-specific intent: every affected per-UI brief
   - acceptance, only for authorized implementation or final signoff: `Docs/rules/rules_testing.md` and the applicable acceptance matrix
4. Inspect the real shared-asset inventory, every consumer's Scene or Prefab, bindings, current screenshots, and bitmap provenance before recommending consolidation.

Treat the current Scene or Prefab as implementation truth. Stitch, Pencil, generated mockups, and design explorations are upstream visual references and must not overwrite signed-off Scene geometry.

## Workflow

### 1. Inventory the family

Record for each consumer:

- exact Scene, Prefab, or asset path
- current component or sprite reference
- player-visible purpose and interaction contract
- states required by its current brief or runtime contract
- binding owner and fields that vary at runtime
- available real screenshot evidence
- bitmap source, provenance, and promotion status when applicable

Separate structural similarity from superficial visual similarity. Do not force unrelated semantics into one family merely because their frames look alike.

### 2. Choose the ownership model

Use the smallest valid ownership model:

| Decision | Use when | Required record |
| --- | --- | --- |
| Canonical shared primitive | At least two consumers share semantics, structure, state contract, and should evolve together | canonical source, consumers, public slots, allowed state variation |
| Base Prefab plus Prefab Variant | Consumers share a stable core but legitimately differ in composition, orientation, decoration, or default state | base-owned fields, variant-owned fields, prohibited overrides |
| Screen-specific component or asset | Interaction, state model, layout role, painterly silhouette, or ownership differs materially | reason sharing would create coupling or runtime patching |
| Temporary candidate | The item exists only for diagnosis, collage, slicing, or visual exploration | staging location, replacement or deletion condition; never treat it as canonical |

Reuse shared structure, not an unrelated finished Prefab patched at runtime. Preserve Scene-authored placement and keep runtime code limited to data, state, visibility, interaction, and reuse of authored pools.

### 3. Define the canonical contract

Describe the contract without copying numeric values into this skill:

- stable hierarchy and component identity
- public content and bitmap slots
- binding-owned versus Scene-owned fields
- state list taken from current briefs and runtime contracts
- typography, color, material, spacing, and motion authorities
- input, focus, accessibility, and reduced-motion behavior where applicable
- permitted Base, Variant, Scene-instance, and runtime overrides
- fallback, missing-asset, and deprecated-consumer handling

Prefer existing finalized primitives and tokens. Surface authority conflicts instead of averaging them.

### 4. Audit states and bitmaps

Compare only the states and bitmap properties required by the concrete consumers. Mark missing states, conflicting semantics, unsafe focus behavior, bitmap drift, clipped borders, alpha defects, import-setting differences, or untracked candidates. Use a compact table only when it materially clarifies the decision; a formal matrix is not required for a read-only ownership audit.

For bitmap work, route generation or redraw to `art-pipeline` and `imagegen`. Keep candidates in project staging, require provenance and visual QA, and promote only through the current asset rules. This skill decides family ownership; it does not generate or promote art.

### 5. Specify the contact sheet

Define or create a contact sheet only when the user explicitly requests it, authorized implementation is underway, or the family is entering final signoff. When used, show the canonical component, legitimate Variants, required states, and representative consumers at comparable scale, with source and evidence labels.

Do not use a contact sheet as final Unity proof. Skip this step for a bounded read-only ownership decision when it adds no material evidence.

### 6. Build the screenshot acceptance matrix

Create or update a formal screenshot acceptance matrix only when the user explicitly requests it, implementation is authorized and underway, or the family is entering final signoff. When required, record:

| Family | Consumer | State | Visual authority | Real Scene/GameView evidence | Parity result | Approved exception | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |

For implementation acceptance, follow the current `AGENTS.md` risk lane and its real screenshot requirements. Do not add a full parity report or matrix to a read-only proposal or `FAST` visual-only adjustment unless its escalation trigger entered scope.

### 7. Propose migration

Order work by canonical source first, then Variants, then consumers. Identify affected files, reference-preservation risks, asset cleanup, rollback boundary, and the smallest verifier. Stop at the proposal unless implementation is explicitly authorized.

## Route specialized work

- Use Stitch design-system capabilities, when available, for cross-screen exploration and design-language variants; keep outputs as upstream references.
- Use Pencil MCP for editable component boards and mockups according to the project Pencil workflow; `.pen` is not Unity authority.
- Use `product-design:ideate` for visual alternatives and `product-design:audit` for screenshot-based critique; neither authorizes Unity changes.
- Use Claude Design, when available, for alternate visual exploration; treat its output like any other upstream reference.
- Use `game-ui-designer` when information hierarchy, navigation, onboarding, or player decisions are unresolved.
- Use `art-pipeline` and `imagegen` for bitmap-family creation, redraw, provenance, promotion, and visual QA.
- After explicit implementation authorization, use `ui-ux` for Scene, Prefab, Variant, binding, and asset wiring changes.
- Use `unity-animation` for shared transition, state-motion, lifecycle, cancellation, replay, and reduced-motion contracts.
- Use `Verify` for fresh, risk-based Unity, screenshot, parity, and acceptance evidence.

Use only integrations available in the current environment. Missing Stitch, Pencil, Product Design, or Claude Design access must not block a repository-grounded audit.

## Output

Report:

1. family scope and concrete consumers
2. current inventory and evidence gaps
3. canonical ownership decision with rejected alternatives
4. material state or bitmap conflicts
5. migration order, affected scope, and explicit authorization boundary
6. contact-sheet or screenshot-matrix evidence only when that conditional gate applies
7. result as `Verified`, `Should work`, or `Skipped / blocked`

Do not claim a unified family is implemented until every authorized consumer has the required real screenshot evidence and applicable verification gates pass.
