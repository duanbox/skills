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
5. For a named screen, read its current per-UI brief under `Docs/todo/demo_ui_polish/` when one exists. If no active brief exists, use the relevant `Docs/Knowledge/ui_scene_flow_acceptance_matrix.md` row, current Scene/Prefab, and latest real screenshot or user-provided reference as the visual authority before inspecting similar usages.

## Implementation Workflow

1. Fix the visual authority: approved mockup, `.pen`, per-UI brief, flow-matrix row, or current Scene/Prefab.
2. For `concept`, stop at the candidate image or mockup; do not write Unity, promote assets, run registry, or start tests.
3. For `visual trial`, make at most one focused inspection pass, then start real Scene/Prefab writing or report the concrete blocker within the first three tool batches after inspection. Do not spend those batches on tests, contracts, registry, documentation, broad audits, or repeated reads.
4. Before the first `visual trial` Scene/Prefab write, create a compact pre-lock in the working notes or user-visible update; do not create a long-lived document. Include only:
   - `visual authority priority`: the approved mockup, `.pen`, flow-matrix row, current screenshot, or user reference, in order.
   - `must preserve`: user-named existing content, layout, data regions, assets, and interactions that must not change.
   - `must change`: the specific visible target of this trial.
   - `forbidden moves`: user-named "do not" items, no self-directed alternative layouts, no unrelated screen edits, and no finalization-only work.
   - `stale exclusions`: obsolete, user-rejected, wrong, or unused assets / Prefabs / styles that must not be reused in this trial.
5. Run the ownership gate. Classify each affected element as shared primitive, screen-specific asset, container layout, dynamic zone, or temporary candidate.
6. For fullscreen menu work, confirm the whole screen contract before editing: background model, content density, detail and action zones, dynamic pools or data regions, reusable shell/header such as `PanelHeader.prefab`, and upper-right exit semantics. Do not treat "add a background" as sufficient when the request is to convert or repair the whole menu experience.
7. Inspect only the real active Scene hierarchy, inactive objects, Prefab references, controller bindings, current screenshot, and pre-lock exclusions needed for the selected stage before editing.
8. Author static hierarchy, RectTransform geometry, painterly components, list-item shells, layer order, and finalized sprites in Scene/Prefab serialization.
9. Keep runtime scripts limited to data binding, dynamic content, state, visibility, tooltips, animation, interaction, and reuse of Scene/Prefab-authored pools.
10. Reuse finalized shared primitives. Do not create a parallel screen-specific replacement without explicit approval.
11. Use `UIDataAsset` color/layout groups and `TMP_Text`. Do not add runtime `new Color(...)`, legacy `Text`, or hard-coded final styling.
12. Use LayoutGroups only for outer shells or genuinely dynamic containers. Do not let nested layouts or fixed preferred sizes overwrite painterly Scene-authored geometry.
13. For generated bitmaps, keep candidates under `ai/...` or `Temp/...`; promote only complete, provenance-recorded, QA-approved assets. Finish with a temporary-asset cleanup classification only when the selected stage reaches finalization or an asset rule requires it.

## Visual Trial Guardrails

- `concept` and `visual trial` use at most one owning skill. Do not spawn subagents, load multiple owning skills, update long-lived tests/contracts/docs, regenerate registry, run `strict-final`, collect a resolution matrix, or complete final asset-promotion bookkeeping before visual approval unless a hard safety or traceability rule makes that exact step necessary.
- A targeted screenshot test is allowed when it is the smallest way to obtain the real affected state. Do not turn that into the full harness.
- If the approved visual authority contains a distinct button, connector, panel, icon, or other screen-specific element, the first real Scene screenshot must use the corresponding reviewed asset. Do not substitute text glyphs, Unity primitives, unrelated existing buttons, diagnostic crops, or placeholder bitmaps and then validate the substitute.
- If the required asset is missing, create or process that asset first, or report that `visual trial` is blocked on the missing asset.
- Treat user corrections from the current turn and latest relevant turn as binding inputs to the pre-lock. If a correction says "not this", "keep this", "do not change", or names a required prefab / layout / density, cancel any older direction that conflicts with it before writing.
- Do not reuse a stale asset or Prefab merely because it is already referenced nearby or matches the object type. If the user flagged it as unused, wrong, old, rejected, "garbage", or unrelated, add it to `stale exclusions`; before any possible reuse, check the exact name or GUID against the current visual authority and active Scene/Prefab references. Presence on disk is not approval to reuse it.
- `visual trial` may mark stale exclusions and avoid them, but must not delete assets, remove `.meta` files, rewrite promotion manifests, or perform zero-reference cleanup unless the user explicitly asks for cleanup or the task has reached finalization.
- When the real screenshot is materially wrong, return to the visual authority immediately. Do not update tests, contracts, registry, or baselines to canonize the wrong implementation.
- If the same UI root receives two material visual corrections, or one correction says the implementation clearly missed the reference, stop the current direction. Re-state the visual authority, existing changed files or Scene roots, current screenshot evidence, and the single next corrective step before continuing.

## Forbidden Shortcuts

- Do not construct final painterly components or list items with runtime `new GameObject`.
- Do not normalize broken scale, anchors, parents, or Canvas state at runtime.
- Do not replace Scene/Prefab authority with a generator loop after visual signoff.
- Do not treat a flattened mock image, editor-only preview, or compile success as runtime proof.
- Do not use a generic web or generic Unity UI skill to override EndGods Scene/Prefab rules.

## Verification

Classify the verification lane under the selected stage and current `AGENTS.md` before editing. This skill does not add gates beyond the risk that actually entered scope.

- For a `FAST` visual-only Scene/Prefab adjustment, use exactly the minimum evidence currently required by `AGENTS.md`, including the real screenshot requirement. Do not add a formal/full parity report, Unity recompile, Test Runner, `strict-final`, registry regeneration, multi-resolution capture, or matrix update unless its escalation trigger entered scope.
- For `visual trial`, use the smallest evidence that makes the visual result reviewable: ownership classification, real write, affected-region screenshot, same-region visual comparison, and only the Console/rule scan/compile/safety checks required by the changed artifact. The same-region comparison must explicitly check user-named details such as connector shape, open or closed borders, icon scale, content density, and exit-control placement.
- When runtime or Editor code, interaction, visibility, binding, services, async timing, resources, data, or scene flow changes, run only the smallest checks required by the applicable `AGENTS.md` lane and relevant authority.
- Reserve registry refresh, `strict-final`, formal parity reports, long-lived test or matrix updates, multi-resolution coverage, asset cleanup, and promotion closure for `finalization` or for a specific authority trigger that entered scope.
- Do not use verification failure as permission to expand scope or restore an obsolete visual value.

## Handoff

Report the screen/state, visual authority, pre-lock summary when used, changed ownership or runtime contracts, asset classifications, stale exclusions that were avoided, and only the evidence required by the selected `AGENTS.md` lane. Include pass/fail for user-named visual details. A UI task is not complete while evidence required by that lane is missing or a user-named visual detail is still unaddressed.
