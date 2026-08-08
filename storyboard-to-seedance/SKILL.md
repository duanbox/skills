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
2. Audit references. A production board controls shot structure and space; separate character/environment/prop references remain authoritative for face, costume, props, and location identity. Keep the reference set as small as the shot problem allows; extra unscreened images dilute weighting. If a character has no reliable lock reference, do not invent a clear face. Constrain depiction to silhouette, back view, distance, feet, clothing markers, or key props, and use the approved environment reference as the stronger authority.
3. Use the built-in `imagegen` skill and image-generation tool. The EndGods model convention is `gpt-image-2`. Do not use Grok Imagine as the storyboard generator.
4. Generate one real 3840x2160, 16:9 Chinese production board with exactly nine numbered shots. Include readable shot purpose, framing, action, camera move, spatial continuity, lighting/color system, and start/end handoff anchors.
5. Put these constraints near the start of the image prompt:

```text
clean low-noise, readable, broad clear shapes, smooth atmospheric rendering,
no film grain, no speckling, no gritty random pixel noise, no compression artifacts
```

6. Keep labels short. Forbid random text, watermark, logo, camera equipment, unrelated UI, extra characters, face drift, costume drift, and prop morphing.
7. Save the board under `video/01_storyboards/` and visually inspect the actual image. Scripts, Pillow, HTML/CSS, SVG, or a layout mock may support diagnosis but cannot replace the final generated bitmap board.

If built-in imagegen returns a network or transport error, report the exact error text. When the user specified a hard stop for that process, stop immediately: do not retry, revise the prompt, switch to Dreamina, use CLI/API or external backends, generate video, or reuse unrelated images. Mark storyboard and visual QA as blocked unless a real generated candidate already exists and is explicitly inspected under the current instruction.

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
2. Classify the delivery as either a `single-generation direction test` or a `precision editorial delivery` before compiling prompts.
3. Treat storyboard timecodes as editorial targets and rhythm cues, not as a guaranteed frame-accurate edit decision list. Never imply that decimal cut points will execute precisely.
4. For a dense single generation with several sub-second shots, such as nine shots in 6-8 seconds, compress the board into three or four macro narrative phases. Use coarse time ranges only when they help rhythm, keep them explicitly approximate, and let the board control the internal shot language and order. Do not demand nine exact micro-cuts in one generation.
5. For precision editorial delivery, write one prompt per selected shot, generate each shot at a platform-supported workable duration, and cut the strongest interval to the storyboard target in post. Give each shot one clear subject action and one compatible camera move. State editorial target duration separately from requested generation duration, framing, motion delta, continuity invariants, style/LUT, lighting, and forbidden drift.
6. When the user asks for one directly usable prompt, return the compact macro-phase prompt first rather than a director-style micro-timeline or shot execution table. If the request says to include the board and references, list upload order, each reference role, and file paths before the prompt. Keep reference bindings, narrative progression, start/end anchors, style lock, audio policy, and negative constraints in that single block.
7. Use first/last-frame references or separate shot generation when an exact reveal, cut, or handoff anchor matters more than single-pass continuity.
8. Respect the current Seedance/Grok limits from `Docs/rules/rules_video.md`; do not preserve stale platform numbers in this skill.
9. For every complete copyable Seedance prompt, count reference bindings, Chinese and English text, punctuation, spaces, and line breaks. The hard limit is 2000 characters; target 1800 or fewer unless the user explicitly accepts a tighter margin. Report the character count with the prompt.

When a repair changes an adjacent-segment handoff object, seal, ticket, causal line, tail environment, or first/last-frame anchor, update the whole affected package together: storyboard panels, reference bindings, direct prompt, manifest or sidecar, and previous/next segment handoff wording. Do not treat a one-line prompt edit as sufficient when the visual authority or downstream handoff semantics changed.

For single-generation QA, judge macro progression, motif order, visual scale, and start/end anchors. Do not call decimal cut drift a failure when the declared goal is only direction validation. If exact shot retention is required, switch to precision editorial delivery instead of adding more micro-timestamps.

## Manifest and video QA

For every generated take, create or update the project manifest or same-name JSON sidecar with generation entry, model ID, request/job ID, exact shot prompt, reference paths, editorial target duration, requested and actual generation duration/resolution, aspect ratio, audio presence, raw output path, selection status, and QA result.

Before selection or Unity delivery:

- run `ffprobe` on the actual MP4 for codec, dimensions, frame rate, duration, aspect ratio, audio stream, and size;
- extract beginning, middle, and end frames and build a contact sheet;
- visually inspect the contact sheet for identity drift, composition failure, malformed props, text, flicker, and handoff continuity;
- move only selected takes through `04_selected`, `05_post`, and `06_final`;
- promote to `Assets/Game/Art/Video/` only after the video rules' encoding, naming, final-frame, Naninovel/Inspector, and QA gates pass.

Report the board path, nine-shot QA, prompts, manifest path, raw/selected take paths, `ffprobe` evidence, contact sheet, and every skipped or blocked gate.
