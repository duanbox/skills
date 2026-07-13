---
name: game-ui-designer
description: Resolve EndGods player-facing UI decisions. Use when hierarchy, flow, navigation, accessibility, feedback, onboarding, or visible states remain unsettled; not as a gate for approved Unity implementation.
---

# Game UI Designer

## Ownership

This skill owns unresolved EndGods player-experience decisions: information hierarchy, player-visible states, navigation and focus, feedback, accessibility, onboarding, and economy clarity. It does not own Unity Scene/Prefab implementation, bitmap production, gameplay-system logic, or generic UI design education.

Do not require this skill as a serial prerequisite for `ui-ux`. If the target screen, visual authority, and player-visible behavior are already approved, enter the implementation workflow directly.

## Authority gate

1. Read `AGENTS.md`, then use the UI routes in `Docs/INDEX.md`.
2. Read `Docs/Pipeline/ui-vibe-coding-pipeline.md`, `Docs/rules/rules_ui.md`, and the affected screen brief only when the task needs those authorities.
3. When bitmap ownership, provenance, import, or promotion is in scope, also read `Docs/rules/rules_assets.md` and `Docs/rules/rules_resources.md`.
4. Inspect the current player-visible screen, flow, states, and available evidence before proposing a replacement.

Use the optional files under `references/` only when one directly answers the current task; do not load a generic HUD, menu, shop, or tutorial checklist by default.

## Design workflow

1. State the player outcome and the material decision that remains unresolved.
2. Map only the relevant entry, exit, empty, loading, locked, error, repeat-entry, and destructive-action states.
3. Decide information priority, navigation/focus order, feedback timing, and accessibility behavior from current EndGods authority and evidence.
4. Identify the data shown and its existing gameplay owner without inventing a new service, binding, asset, or runtime contract.
5. Resolve discoverable facts from the repository. State only assumptions that can materially change the design.
6. Keep the answer proportional: a bounded review may need a few decisions, not a mandatory multi-section design document.

## Review and handoff

- For a review, report only concrete player-facing findings in severity order, with exact screen, state, or component evidence where available.
- For a design decision, record only the states, navigation, feedback, accessibility, data-owner assumptions, and visual authority needed by the implementation.
- Do not force a separate handoff document or a second planning pass when the user has already approved those decisions.
- If implementation is requested and material design decisions are settled, route execution to `ui-ux`; this skill itself does not edit Unity Scenes, Prefabs, bindings, or production assets.
- A design recommendation never authorizes runtime construction of final painterly components, replacement of Scene/Prefab layout authority, or a parallel shared primitive. Classify a genuinely new visual component under the current project ownership gate.
- Keep the output concise and label unresolved material choices instead of padding it with generic HUD, menu, inventory, shop, or tutorial advice.
