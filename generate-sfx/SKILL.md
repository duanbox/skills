---
name: generate-sfx
description: Create and validate EndGods sound effects with licensed sources and project tools. Use when generating, replacing, listing, or QA-checking SFX, but not BGM or voice.
---

# Generate EndGods SFX

Choose the source by sound type. Do not force every SFX through a music model.

## Read authority first

- `Docs/design/audio.md`
- `Docs/Knowledge/comfyui.md`
- `Docs/rules/rules_assets.md`
- the selected script under `Tools/audio/` when exact arguments or current IDs matter

## Source routing

1. Inspect `Assets/Game/Audio/SFX/`, `ai/Audio/SFX/`, source sidecars, and `Assets/Game/Audio/CREDITS.md`. Do not overwrite a reviewed final or protected source without explicit approval.
2. Classify the requested sound:
   - Real-world one-shot or natural sound: prefer a reviewed real recording through `Tools/audio/fetch_sfx.py` and `Tools/audio/install_sfx.py` when a suitable CC0 or CC-BY source exists.
   - Five-to-fifteen-second musical ambience or sustained texture: use ACE-Step through `Tools/audio/sfx_generator.py`.
   - Sub-four-second transient that cannot be sourced: use `Tools/audio/sfx_audioldm.py` only after confirming the model/code/output license is suitable for the intended distribution. AudioLDM2 must not be treated as an automatically commercial-safe default.
3. Never accept CC BY-NC or another noncommercial/incompatible source for the game. Record attribution for CC-BY material and update `Assets/Game/Audio/CREDITS.md` through the project workflow.
4. Do not print, copy, or expose local service tokens. Do not install packages or change the GPU environment without approval.

## Project CLI

For ACE-Step ambience, use the maintained script instead of rebuilding its graph:

```powershell
Tools/venv/Scripts/python.exe Tools/audio/sfx_generator.py --list
Tools/venv/Scripts/python.exe Tools/audio/sfx_generator.py --id SFX_ID
```

Use `--all` only for an explicitly requested batch. For recording installation or transient generation, inspect the selected script's current `--help` and project documentation before execution; do not guess flags.

Keep raw candidates under `ai/Audio/SFX/`. Promote only selected, processed outputs to `Assets/Game/Audio/SFX/`.

## QA gate

Before accepting an SFX:

- verify license/source URL, author, attribution requirement, prompt/model where applicable, and final path;
- inspect codec, duration, channels, sample rate, and file size;
- detect leading silence in short sounds and trim so the audible event begins at the intended trigger;
- apply the current loudness/true-peak targets from `Docs/design/audio.md`; reject clipping and avoid double normalization;
- inspect the full waveform or segmented RMS so a late burst does not hide behind a normal overall average;
- check loop seams for looping ambience;
- listen against the target BGM and check frequency masking, timing, and perceived role;
- verify Unity import and the actual Naninovel/runtime reference when promoted.

Report any skipped listening, mix, license, or Unity check. A generated OGG and a non-silent mean volume are not sufficient evidence.
