---
name: storyboard-to-seedance
description: Create EndGods nine-shot production boards and traceable Seedance shot packages. Use when the user explicitly requests this production workflow, not for ordinary storyboard, CG/PV, cut-in, or image-to-video planning.
---

# EndGods Storyboard To Seedance

Produce real preproduction assets, not only a prompt draft. This is an explicit production workflow, not the default owner for generic storyboard, CG, PV, character-intro, cut-in, or image-to-video planning.

## Read authority first

1. Read `Docs/rules/rules_video.md` first.
2. Read `Docs/video/storyboard_to_seedance_workflow.md` and `Docs/video/video_manifest_template.md` for supporting workflow details.
3. When the older workflow document conflicts with `rules_video.md`, follow `rules_video.md` and report the conflict. The active contract is a nine-shot board.

## Paths

Use the correctly spelled project workspace:

```text
Docs/video/                 tracked workflow documentation
video/01_storyboards/       generated production boards
video/02_refs/              character, scene, prop, and upload references
video/03_seedance_raw/      Seedance raw takes
video/03_grok_raw/          optional Grok Direct test takes
video/04_selected/          selected takes
video/05_post/              postprocessed media
video/06_final/             local delivery candidates
video/manifests/            manifests and JSON sidecars
```

## Storyboard generation

1. Normalize the brief: title, purpose, duration, characters, scene, emotional arc, continuity anchors, references, and forbidden elements.
2. Audit references. A production board controls shot structure and space; separate character/environment references remain authoritative for face, costume, props, and location identity.
3. Use the built-in `imagegen` skill and image-generation tool. The EndGods model convention is `gpt-image-2`. Do not use Grok Imagine as the storyboard generator.
4. Generate one real 3840x2160, 16:9 Chinese production board with exactly nine numbered shots. Include readable shot purpose, framing, action, camera move, spatial continuity, lighting/color system, and start/end handoff anchors.
5. Put these constraints near the start of the image prompt:

```text
clean low-noise, readable, broad clear shapes, smooth atmospheric rendering,
no film grain, no speckling, no gritty random pixel noise, no compression artifacts
```

6. Keep labels short. Forbid random text, watermark, logo, camera equipment, unrelated UI, extra characters, face drift, costume drift, and prop morphing.
7. Save the board under `video/01_storyboards/` and visually inspect the actual image. Scripts, Pillow, HTML/CSS, SVG, or a layout mock may support diagnosis but cannot replace the final generated bitmap board.

## Board QA gate

Reject or regenerate when any of these fail:

- exactly nine legible, correctly numbered shots are present;
- each shot has a distinct narrative, composition, action, or emotional function;
- Chinese labels are short and readable at normal viewing size;
- faces, hairstyles, costumes, weapons, props, scene layout, palette, and lighting remain coherent;
- entrances, screen direction, spatial relationships, and shot handoffs are understandable;
- no film grain, random noise, watermark, stray text, equipment, extra character, cropped grid, or missing edge is visible.

## Seedance shot package

After the board passes QA:

1. Bind it as the main production-board reference and list every additional character, environment, or prop reference explicitly.
2. Write one prompt per selected shot unless the current platform contract and project rules justify a short timestamped sequence.
3. Give each shot one clear subject action and one compatible camera move. State duration, framing, motion delta, continuity invariants, style/LUT, lighting, and forbidden drift.
4. Respect the current Seedance/Grok limits from `Docs/rules/rules_video.md`; do not preserve stale platform numbers in this skill.

## Manifest and video QA

For every generated take, create or update the project manifest or same-name JSON sidecar with generation entry, model ID, request/job ID, exact shot prompt, reference paths, requested and actual duration/resolution, aspect ratio, audio presence, raw output path, selection status, and QA result.

Before selection or Unity delivery:

- run `ffprobe` on the actual MP4 for codec, dimensions, frame rate, duration, aspect ratio, audio stream, and size;
- extract beginning, middle, and end frames and build a contact sheet;
- visually inspect the contact sheet for identity drift, composition failure, malformed props, text, flicker, and handoff continuity;
- move only selected takes through `04_selected`, `05_post`, and `06_final`;
- promote to `Assets/Game/Art/Video/` only after the video rules' encoding, naming, final-frame, Naninovel/Inspector, and QA gates pass.

Report the board path, nine-shot QA, prompts, manifest path, raw/selected take paths, `ffprobe` evidence, contact sheet, and every skipped or blocked gate.
