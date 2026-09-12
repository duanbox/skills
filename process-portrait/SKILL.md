---
name: process-portrait
description: Process an approved EndGods character PNG into a 2000x3000 RGBA portrait with the project CLI. Use when preparing staged transparent portraits or optional avatars before Unity promotion.
---

# Process EndGods Portraits

Use the maintained portrait tool. Do not replace it with an in-place Pillow resize or cover crop.

## Authority and boundaries

1. Read `Docs/rules/rules_assets.md`, especially the portrait and Python-environment sections.
2. Require an explicit staged input under `ai/...`. Default the processed output to `ai/Character/Processed/{Character}/`; do not write directly to `Assets/Game/...` during processing.
3. Confirm source provenance and visual approval before processing.
4. Inspect decoded dimensions and actual alpha. Keep usable native transparency; only route missing or failed alpha through approved background removal/edge repair. An RGBA mode or painted checkerboard is not evidence of usable transparency.
5. If a staged or final output already exists, inspect it and obtain explicit approval before replacement. Preserve a final asset's `.meta` file unless a deliberate asset replacement requires otherwise.

## Execute

For a transparent source large enough to contain-fit into 2000x3000 without enlargement, use the ordinary environment with strict direct fit:

```powershell
Tools/venv/Scripts/python.exe Tools/image/process_character_portrait.py `
  --input ai/Character/.../Character_alpha.png `
  --portrait-out ai/Character/Processed/Character/Portrait.png `
  --skip-upscale
```

For a smaller source, use the configured GPU environment. The same tool automatically invokes the existing Real-ESRGAN path only when enlargement is required; do not install dependencies or switch environments implicitly:

```powershell
Tools/venv_gpu/Scripts/python.exe Tools/image/process_character_portrait.py `
  --input ai/Character/.../Character_alpha.png `
  --portrait-out ai/Character/Processed/Character/Portrait.png
```

Add `--avatar-out ai/Character/Processed/Character/Avatar.png` and the documented avatar crop arguments only when an avatar is requested.

The project tool owns contain-fit behavior, the transparent 2000x3000 canvas, conditional upscaling, and optional avatar generation. `--skip-upscale` rejects undersized sources; even a super-resolved result may not be enlarged with ordinary resize. Fully opaque or empty-alpha PNGs are rejected. Never switch to `ratio=max(...)`, crop the full-body silhouette to fill the canvas, or save over the source file.

Record original and processed dimensions plus the actual processing route. `direct-fit` means this invocation skipped super-resolution; it does not prove native generation size or a backend model ID. Keep model provenance from the source, using `unreported` when the generation tool did not return it.

## QA gate

Inspect the actual output image and verify:

- `Portrait.png` is exactly 2000x3000 RGBA;
- head, feet, hands, weapon, hair, ribbons, costume edges, and shadows are complete and not clipped;
- the character is centered and scaled appropriately, with intentional transparent padding;
- alpha edges have no opaque box, colored halo, or hidden residue on light, dark, and checker backgrounds; preserve hair, ink fringes, translucent cloth, and intended shadows;
- identity, proportions, colors, and source composition remain unchanged by deterministic processing;
- an optional avatar has the intended shoulder/head crop and transparent circular edge;
- the staged files have passed visual QA before any Unity destination is touched.

## Promotion gate

Only after the user explicitly approves the staged result may it be promoted to `Assets/Game/Art/Characters/{Character}/`. During promotion:

- preserve the existing `.meta` file and serialized references when replacing an established asset;
- verify the imported `Portrait.png` uses Unity `maxTextureSize=2048` unless a documented, approved exception applies;
- verify the optional avatar importer and the affected Unity screen or character presentation;
- keep the approved staged source under `ai/Character/Processed/` for provenance or classify it during cleanup.

Report source, staged output, dimensions/mode, visual QA, promotion approval, final output when promoted, `.meta` status, `maxTextureSize=2048` verification, and Unity validation. Dimension-only PASS is insufficient.
