---
name: art-pipeline
description: Route EndGods bitmap creation, redraw, provenance, promotion, and visual QA. Use when working on character art, CGs, backgrounds, UI bitmaps, icons, map art, or VFX textures.
---

# EndGods Art Pipeline

Use this skill as a thin project router. Do not duplicate model guides, character lore, or prompt libraries here.

## Read authority first

1. Read `AGENTS.md`.
2. Read `Docs/rules/rules_assets.md` before touching art or asset tooling.
3. Read `Docs/rules/art_style_summary.md` and the nearest asset brief or subsystem pipeline.
4. Read `Docs/sop/ai_asset_promotion.md` before any AI bitmap enters or replaces a file under `Assets/Game/...`.
5. For map region, map node, town, or dungeon background art, also read the nearest map/dungeon authority, especially `Docs/sop/map_node_workflow.md`, `Docs/systemspec/map_locations_design.md`, and `Docs/rules/rules_map.md` when the work touches world-map art.
6. For UI art, also read `Docs/Pipeline/ui-vibe-coding-pipeline.md` and the affected screen brief.

When documents conflict, follow the more specific current project rule and surface the conflict.

## Workflow

1. Inspect the real repository state and search for an existing asset before generating anything.
2. Classify the requested asset as a shared primitive, screen-specific asset, or temporary candidate. Do not replace protected finalized icons or their `.meta` files without explicit approval.
3. Decide generation versus edit:
   - Default to a fresh redraw or regeneration for style unification, quality replacement, or Grok-era bitmap replacement.
   - Edit the original only when the user explicitly requests retouching, local adjustment, or preservation of the original composition.
   - For an authorized edit, inspect one explicit edit base, assign each supporting reference a role, and name the target region plus invariants. Compare changed and unchanged regions; a preservation prompt is not proof of pixel-identical output. Follow `Docs/sop/ai_asset_promotion.md` for drift or exact-compositing requirements.
   - For redraws or style-fix replacements, treat old images and stale candidates as references or counterexamples, not as style authority. Before generation, state what each reference may inherit and what it must exclude. If an asset name, region label, old brief, or user shorthand can mislead the model, such as treating "wasteland" as shabby poverty instead of the current mythic Flame Mountain direction, spell out the intended meaning and the forbidden interpretation in the prompt.
4. Use the built-in `imagegen` skill and image-generation tool for bitmap work. Follow the model-target and host-capability table in `Docs/rules/rules_assets.md`; do not hardcode the native backend or invent unexposed model, size, quality, or mask arguments. Do not substitute SVG, Pillow, HTML/CSS, canvas, or procedural drawings for a requested final bitmap. On a native-tool network error, report the exact error and stop; do not switch channels or request API keys without authorization.
5. Place every raw candidate under `ai/...`. Do not write an unreviewed generation directly into `Assets/Game/...`.
6. Decode source dimensions and alpha before selecting necessary processing from `Docs/rules/rules_assets.md`. Skip background removal when native alpha passes light/dark/checker-background QA, and skip super-resolution when the source can fit without enlargement. Keep intentional luma/chroma workflows intact. Do not invent one-off Python when a project tool already owns the operation.
   - For a circular or irregular progress fill that must sit inside an art groove, follow `Docs/Pipeline/ui-vibe-coding-pipeline.md` §4.5.1. Treat the final groove as the geometry authority; derive the mask and fill template from that exact source geometry, keep one coordinate space, and validate `0% / 1% / 50% / 99% / 100%`. Never generate, eyeball, or nudge the groove, mask, and fill independently.
7. Perform visual QA before promotion:
   - full silhouette and all borders/corners are present;
   - no clipped head, feet, weapon, ornament, frame, or shadow;
   - alpha edges are clean when transparency is required;
   - no random text, watermark, logo, baked mock UI, or unrequested characters;
   - style, palette, composition, intended small-size readability, and reference identity match the authority;
   - for AI bitmap outputs, inspect quiet surfaces and gradients at 100% and 200% for model-signature noise, isolated short strokes, pepper dots, stray ink fibers, smeared micro-lines, or repeated texture units;
   - for replacements, canvas size, aspect ratio, crop, pivot-sensitive silhouette, and target in-game footprint match the existing finalized asset unless the user explicitly approved a layout change;
   - compare the real output visually, not only its dimensions or file existence.
   - if the user rejects a candidate for style drift, mark that candidate's manifest as rejected or superseded, record the rejection reason in concrete visual terms, and carry those failed traits into the next prompt as exclusions before generating again. Never promote or reuse the rejected candidate as a fallback unless the user later explicitly revives it.
8. Record provenance in the existing brief/manifest per the promotion SOP: distinguish requested model from returned model (`unreported` if absent), source dimensions from processed dimensions, and native alpha from extraction. Include any edit base, reference roles, transforms, and preserved-region QA. Do not relabel historical assets or call an upscaled image native high resolution.
9. Promote only approved outputs to `Assets/Game/...`, preserve required `.meta` files and serialized references, and verify the Unity importer or affected screen as required by the project rules.
10. For map region/background replacements, verify the final in-engine display path, not only the source image:
   - capture the real Scene/GameView state the player or editor workflow depends on;
   - include current lock/unlock state, material/property-block or colorize branch, grayscale/desaturation overlays, SpriteRenderer/Prefab/Scene override references, and any atlas or importer behavior that can change the visible result;
   - do not treat a source PNG, generated candidate, isolated crop, or SceneView background artifact as final acceptance evidence.
11. If the same asset fails two consecutive iterations for the same class of issue, such as AI microartifact, blur, wrong proportion, style drift, or in-engine color loss, stop generating more candidates. Build a comparison sheet against the approved anchor and state the smallest next production-path decision before continuing.
12. Run a cleanup pass. Classify every generated file as promoted final, shared fallback, archived source, or deleted; remove unpromoted candidates from final asset folders together with their `.meta` files.

## Done condition

Report the generated and promoted files, provenance location, visual QA evidence, Unity or screenshot validation when required, cleanup result, and any skipped check.

Separate source/candidate QA, Unity importer/reference checks, and final in-game screenshot evidence. If final in-game visual proof is required but missing, report `Skipped / blocked` with the exact blocker; do not report the asset done from a prompt, candidate image, source PNG, isolated crop, successful conversion, GUID preservation, or Console-only evidence.
