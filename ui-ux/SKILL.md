---
name: UI & UX Design
description: Implement and verify EndGods UI under Scene and Prefab authority. Use when changing UI Scenes, Prefabs, generators, bindings, layout, styling, navigation, visibility, assets, or scene flow.
---

# UI & UX Design

This skill owns EndGods UI implementation. Use `game-ui-designer` first when the task is still deciding player experience, information hierarchy, navigation, or onboarding; begin this workflow once the design authority and target screen/state are known.

## Required Routing

1. Read `AGENTS.md` and the UI routes in `Docs/INDEX.md`.
2. Read `Docs/Pipeline/ui-vibe-coding-pipeline.md` first.
3. Load only the task-relevant authorities:
   - UI path A/B and Scene ownership: `Docs/rules/rules_ui.md`
   - visual language and primitives: `Docs/rules/rules_ui_guide.md`
   - colors and tokens: `Docs/rules/rules_uicolor.md`
   - sorting and Canvas layers: `Docs/rules/rules_uilayer.md`
   - generators and patch ownership: `Docs/rules/rules_ui_generator.md`
   - bitmap/resource changes: `Docs/rules/rules_assets.md` and `Docs/rules/rules_resources.md`
   - validation: `Docs/rules/rules_testing.md` and `Docs/Knowledge/ui_scene_flow_acceptance_matrix.md`
4. For a named screen, read its current per-UI brief under `Docs/todo/demo_ui_polish/` and inspect two or three similar Scene/Prefab usages.

## Implementation Workflow

1. Fix the visual authority: approved mockup, `.pen`, per-UI brief, or current Scene/Prefab.
2. Run the ownership gate. Classify each affected element as shared primitive, screen-specific asset, container layout, dynamic zone, or temporary candidate.
3. Inspect the real active Scene hierarchy, inactive objects, Prefab references, controller bindings, and current screenshot before editing.
4. Author static hierarchy, RectTransform geometry, painterly components, list-item shells, layer order, and finalized sprites in Scene/Prefab serialization.
5. Keep runtime scripts limited to data binding, dynamic content, state, visibility, tooltips, animation, interaction, and reuse of Scene/Prefab-authored pools.
6. Reuse finalized shared primitives. Do not create a parallel screen-specific replacement without explicit approval.
7. Use `UIDataAsset` color/layout groups and `TMP_Text`. Do not add runtime `new Color(...)`, legacy `Text`, or hard-coded final styling.
8. Use LayoutGroups only for outer shells or genuinely dynamic containers. Do not let nested layouts or fixed preferred sizes overwrite painterly Scene-authored geometry.
9. For generated bitmaps, keep candidates under `ai/...` or `Temp/...`; promote only complete, provenance-recorded, QA-approved assets. Finish with a temporary-asset cleanup classification.

## Forbidden Shortcuts

- Do not construct final painterly components or list items with runtime `new GameObject`.
- Do not normalize broken scale, anchors, parents, or Canvas state at runtime.
- Do not replace Scene/Prefab authority with a generator loop after visual signoff.
- Do not treat a flattened mock image, editor-only preview, or compile success as runtime proof.
- Do not use a generic web or generic Unity UI skill to override EndGods Scene/Prefab rules.

## Verification

Every UI change requires:

1. ownership gate and confirmed Scene/Prefab write
2. real Scene/GameView or PlayMode screenshot of every changed state
3. parity review against the selected visual authority
4. project rule scan
5. Unity recompile and Console check

For visual-only Scene Preview work, report `Skipped / visual-only Scene preview change`; do not run Test Runner only to obtain a screenshot. When entry, interaction, visibility, binding, services, async timing, resources, or scene flow changes, run the smallest relevant EditMode/PlayMode/screenshot test and update the required acceptance matrix.

## Handoff

Report the screen/state, visual authority, Scene/Prefab ownership decisions, runtime binding changes, asset classifications, screenshot evidence, parity result, and exact verification status. A UI task is not complete while required screenshot evidence is missing.
