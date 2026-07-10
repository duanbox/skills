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
4. For UI art, also read `Docs/Pipeline/ui-vibe-coding-pipeline.md` and the affected screen brief.

When documents conflict, follow the more specific current project rule and surface the conflict.

## Workflow

1. Inspect the real repository state and search for an existing asset before generating anything.
2. Classify the requested asset as a shared primitive, screen-specific asset, or temporary candidate. Do not replace protected finalized icons or their `.meta` files without explicit approval.
3. Decide generation versus edit:
   - Default to a fresh redraw or regeneration for style unification, quality replacement, or Grok-era bitmap replacement.
   - Edit the original only when the user explicitly requests retouching, local adjustment, or preservation of the original composition.
4. Use the built-in `imagegen` skill and image-generation tool for bitmap work. The EndGods model convention is `gpt-image-2`. Do not substitute SVG, Pillow, HTML/CSS, canvas, or procedural drawings for a requested final bitmap.
5. Place every raw candidate under `ai/...`. Do not write an unreviewed generation directly into `Assets/Game/...`.
6. Use the deterministic post-processing tool selected by `Docs/rules/rules_assets.md`; do not invent one-off Python when a project tool already owns the operation.
7. Perform visual QA before promotion:
   - full silhouette and all borders/corners are present;
   - no clipped head, feet, weapon, ornament, frame, or shadow;
   - alpha edges are clean when transparency is required;
   - no random text, watermark, logo, baked mock UI, or unrequested characters;
   - style, palette, composition, intended small-size readability, and reference identity match the authority;
   - compare the real output visually, not only its dimensions or file existence.
8. Record provenance before promotion: source path, tool/model, prompt or source reference, intended component, QA status, and promotion reason.
9. Promote only approved outputs to `Assets/Game/...`, preserve required `.meta` files and serialized references, and verify the Unity importer or affected screen as required by the project rules.
10. Run a cleanup pass. Classify every generated file as promoted final, shared fallback, archived source, or deleted; remove unpromoted candidates from final asset folders together with their `.meta` files.

## Done condition

Report the generated and promoted files, provenance location, visual QA evidence, Unity or screenshot validation when required, cleanup result, and any skipped check. A prompt, a candidate image, or successful file conversion alone is not completion.
