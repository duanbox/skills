---
name: generate-bgm
description: Generate and validate EndGods BGM through the maintained project CLI. Use when creating instrumental background music or long musical ambience, but not short SFX or voice.
---

# Generate EndGods BGM

Treat the project generator as the executable authority. Do not reconstruct its ComfyUI graph or copy model parameters into chat commands.

## Read authority first

- `Docs/Knowledge/comfyui.md`
- `Docs/design/audio.md`
- `Docs/rules/rules_assets.md` for staging and promotion gates
- `Tools/audio/bgm_generator.py` when exact IDs, arguments, or current parameters matter

## Workflow

1. Confirm the request is BGM or long musical ambience. Route short transient sounds to `generate-sfx`.
2. Inspect `Assets/Game/Audio/BGM/` and the generator list before creating or overwriting an ID. Do not overwrite a reviewed final without explicit approval.
3. Check that ComfyUI at `http://127.0.0.1:8188`, FFmpeg, the configured ACE-Step model, and the project venv are available. Report missing dependencies; do not install packages or change models without approval.
4. Use the maintained CLI:

```powershell
Tools/venv/Scripts/python.exe Tools/audio/bgm_generator.py --list
Tools/venv/Scripts/python.exe Tools/audio/bgm_generator.py --dry-run
Tools/venv/Scripts/python.exe Tools/audio/bgm_generator.py --id BGM_ID
```

Use `--all` only when the user explicitly requests batch generation. The script owns node IDs, steps, CFG, seed behavior, silence trimming, and the current output format.

5. Keep raw generations under `ai/Audio/BGM/`. The maintained generator currently promotes reviewed BGM to `Assets/Game/Audio/BGM/{ID}.flac`; verify the actual script and output rather than assuming a format.
6. Record the ID, prompt/tags, seed, duration, generator/model, source path, final path, and QA result. Avoid prompts that imitate a living artist or copyrighted track.

## QA gate

Before accepting a BGM:

- confirm the process exited successfully and the expected file exists;
- inspect codec, duration, channels, sample rate, and file size with `ffprobe`;
- check mean and peak levels with FFmpeg; use the current target in `Docs/design/audio.md` (normally about -15 to -18 dB for BGM) and reject clipping;
- inspect beginning, middle, and end for leading silence, mid-track dropout, truncated tails, or unexpected vocals;
- check fade and loop-seam behavior when the track will loop;
- listen to the final asset in context when audio review is available;
- verify Unity import/reference state when the asset is promoted.

If listening, Unity import, or another required check is unavailable, report it as skipped or blocked. File creation and `mean_volume` alone do not prove completion.
