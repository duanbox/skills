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
4. If the source lacks a usable alpha channel, stop and route background removal through the project-approved tool first.
5. If a staged or final output already exists, inspect it and obtain explicit approval before replacement. Preserve a final asset's `.meta` file unless a deliberate asset replacement requires otherwise.

## Execute

Use the GPU environment because the authoritative tool can invoke Real-ESRGAN:

```powershell
Tools/venv_gpu/Scripts/python.exe Tools/image/process_character_portrait.py `
  --input ai/Character/.../Character_alpha.png `
  --portrait-out ai/Character/Processed/Character/Portrait.png
```

Add `--avatar-out ai/Character/Processed/Character/Avatar.png` and the documented avatar crop arguments only when an avatar is requested.

The project tool owns contain-fit behavior, the transparent 2000x3000 canvas, upscaling, and optional avatar generation. Never switch to `ratio=max(...)`, crop the full-body silhouette to fill the canvas, or save over the source file.

## QA gate

Inspect the actual output image and verify:

- `Portrait.png` is exactly 2000x3000 RGBA;
- head, feet, hands, weapon, hair, ribbons, costume edges, and shadows are complete and not clipped;
- the character is centered and scaled appropriately, with intentional transparent padding;
- alpha edges have no opaque box, colored halo, or hidden residue problem;
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
