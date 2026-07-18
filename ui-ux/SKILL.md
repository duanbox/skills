---
name: ui-ux
description: Implement and verify EndGods UI under Scene and Prefab authority. Use when changing UI Scenes, Prefabs, generators, bindings, layout, styling, navigation, visibility, assets, or scene flow.
---

# UI & UX Design

This skill owns authorized EndGods UI implementation. Use `game-ui-designer` only when material player-experience, information-hierarchy, navigation, or onboarding decisions remain unresolved; a bounded task with an approved design authority may enter this workflow directly.

## Required Routing

1. Read `AGENTS.md` and the UI routes in `Docs/INDEX.md`.
2. Read `Docs/Pipeline/ui-vibe-coding-pipeline.md` first.
3. Run the stage gate before deeper reads or planning. Classify the current user intent as `concept`, `FAST`, `visual trial`, `behavior`, or `finalization`; leaving `FAST` only upgrades to the narrowest stage that satisfies the current request.
   - `concept`: deliver only candidate images or mockups plus minimal candidate QA.
   - `visual trial`: deliver the smallest reversible Scene/Prefab implementation, a real affected-region screenshot, and same-region comparison against the approved visual authority.
   - `behavior`: add the smallest runtime or flow evidence for interaction, visibility, binding, services, async timing, resources, data, or scene flow that entered scope.
   - `finalization`: run the one-time asset, contract, cleanup, registry, parity, matrix, and signoff work after the user accepts the real in-game visual result or explicitly asks to finalize.
4. Load only the task-relevant authorities:
   - UI path A/B and Scene ownership: `Docs/rules/rules_ui.md`
   - visual language and primitives: `Docs/rules/rules_ui_guide.md`
   - colors and tokens: `Docs/rules/rules_uicolor.md`
   - sorting and Canvas layers: `Docs/rules/rules_uilayer.md`
   - generators and patch ownership: `Docs/rules/rules_ui_generator.md`
   - bitmap/resource changes: `Docs/rules/rules_assets.md` and `Docs/rules/rules_resources.md`
   - validation: `Docs/rules/rules_testing.md` and `Docs/Knowledge/ui_scene_flow_acceptance_matrix.md`
5. For a named screen, read its current per-UI brief under `Docs/todo/demo_ui_polish/` and inspect two or three similar Scene/Prefab usages only when the selected stage needs that context.

## Implementation Workflow

1. Fix the visual authority: approved mockup, `.pen`, per-UI brief, or current Scene/Prefab.
2. For `concept`, stop at the candidate image or mockup; do not write Unity, promote assets, run registry, or start tests.
3. For `visual trial`, make at most one focused inspection pass, then start real Scene/Prefab writing or report the concrete blocker within the first three tool batches after inspection. Do not spend those batches on tests, contracts, registry, documentation, broad audits, or repeated reads.
4. Run the ownership gate. Classify each affected element as shared primitive, screen-specific asset, container layout, dynamic zone, or temporary candidate.
5. Inspect only the real active Scene hierarchy, inactive objects, Prefab references, controller bindings, and current screenshot needed for the selected stage before editing.
6. Author static hierarchy, RectTransform geometry, painterly components, list-item shells, layer order, and finalized sprites in Scene/Prefab serialization.
7. Keep runtime scripts limited to data binding, dynamic content, state, visibility, tooltips, animation, interaction, and reuse of Scene/Prefab-authored pools.
8. Reuse finalized shared primitives. Do not create a parallel screen-specific replacement without explicit approval.
9. Use `UIDataAsset` color/layout groups and `TMP_Text`. Do not add runtime `new Color(...)`, legacy `Text`, or hard-coded final styling.
10. Use LayoutGroups only for outer shells or genuinely dynamic containers. Do not let nested layouts or fixed preferred sizes overwrite painterly Scene-authored geometry.
11. For generated bitmaps, keep candidates under `ai/...` or `Temp/...`; promote only complete, provenance-recorded, QA-approved assets. Finish with a temporary-asset cleanup classification only when the selected stage reaches finalization or an asset rule requires it.

## Visual Trial Guardrails

- `concept` and `visual trial` use at most one owning skill. Do not spawn subagents, load multiple owning skills, update long-lived tests/contracts/docs, regenerate registry, run `strict-final`, collect a resolution matrix, or complete final asset-promotion bookkeeping before visual approval unless a hard safety or traceability rule makes that exact step necessary.
- A targeted screenshot test is allowed when it is the smallest way to obtain the real affected state. Do not turn that into the full harness.
- If the approved visual authority contains a distinct button, connector, panel, icon, or other screen-specific element, the first real Scene screenshot must use the corresponding reviewed asset. Do not substitute text glyphs, Unity primitives, unrelated existing buttons, diagnostic crops, or placeholder bitmaps and then validate the substitute.
- If the required asset is missing, create or process that asset first, or report that `visual trial` is blocked on the missing asset.
- When the real screenshot is materially wrong, return to the visual authority immediately. Do not update tests, contracts, registry, or baselines to canonize the wrong implementation.

## Forbidden Shortcuts

- Do not construct final painterly components or list items with runtime `new GameObject`.
- Do not normalize broken scale, anchors, parents, or Canvas state at runtime.
- Do not replace Scene/Prefab authority with a generator loop after visual signoff.
- Do not treat a flattened mock image, editor-only preview, or compile success as runtime proof.
- Do not use a generic web or generic Unity UI skill to override EndGods Scene/Prefab rules.

## Verification

Classify the verification lane under the selected stage and current `AGENTS.md` before editing. This skill does not add gates beyond the risk that actually entered scope.

- For a `FAST` visual-only Scene/Prefab adjustment, use exactly the minimum evidence currently required by `AGENTS.md`, including the real screenshot requirement. Do not add a formal/full parity report, Unity recompile, Test Runner, `strict-final`, registry regeneration, multi-resolution capture, or matrix update unless its escalation trigger entered scope.
- For `visual trial`, use the smallest evidence that makes the visual result reviewable: ownership classification, real write, affected-region screenshot, same-region visual comparison, and only the Console/rule scan/compile/safety checks required by the changed artifact.
- When runtime or Editor code, interaction, visibility, binding, services, async timing, resources, data, or scene flow changes, run only the smallest checks required by the applicable `AGENTS.md` lane and relevant authority.
- Reserve registry refresh, `strict-final`, formal parity reports, long-lived test or matrix updates, multi-resolution coverage, asset cleanup, and promotion closure for `finalization` or for a specific authority trigger that entered scope.
- Do not use verification failure as permission to expand scope or restore an obsolete visual value.

## Handoff

Report the screen/state, visual authority, changed ownership or runtime contracts, asset classifications, and only the evidence required by the selected `AGENTS.md` lane. A UI task is not complete while evidence required by that lane is missing.
