---
name: voice-production
description: Create, review, and locally revise EndGods Chinese character dialogue audio while preserving saved voices and stable dialogue IDs. Use for new spoken lines, wrong delivery or pronunciation, voice auditions, and approved replacement takes; not BGM, SFX, or live voice chat.
---

# Character Voice Production

Own the production and listening-review loop. Read the active project's `Docs/design/voice.md` sections 0 and 7.10–7.13, then inspect the actual batch and current CLI help. Resolve paths from the real checkout; saved machine paths may be stale. Keep model-specific commands in that document, not a second pipeline here.

## Turn feedback into a bounded task

Accept ordinary language, for example: `羲和：“别急，天还没塌。”现在像在喊口号，希望是轻松安慰铜铃，带一点笑意。`

- Locate the exact character, script, stable line ID, source text, current take, and one or two surrounding exchanges. Ask only when the line or intended meaning remains ambiguous; users need not supply internal IDs.
- Distinguish mispronunciation/missing words, timing/stress, emotional delivery, character identity, and playback/mixing faults. A playback fault needs diagnosis, not a new voice design.
- Record a short performance direction: who is speaking to whom, intention, emotional intensity, pace, stresses/pauses, and what sounded wrong. This is a review brief, not proof the backend accepts those parameters.
- Preserve fixed reference WAVs, transcripts, versions and provenance. XiHe is male; teasing names such as “羲和姐” do not change his identity. Do not redesign a character because one delivery is wrong.

## Choose the route

**New dialogue:** use existing characters and the native export → prepare → plan → run workflow in §7.10. Preserve stable IDs and the complete script list; add new IDs without reordering old lines. New character voices require a versioned, permitted reference and representative auditions before broad production. Document-only dialogue remains material until a real game entry exists.

**Existing line, current Qwen Base:** its clone adapter rejects non-empty Qwen emotion/instruction controls. VoiceDesign `instruct` is not a per-line Base control. Changing a seed samples another delivery; it does not implement “more relaxed”. Never write direction text into spoken dialogue or invent `--utterance-id` / `--select-take` flags.

Use the original full manifest, change only the target line's seed, and inspect `plan` before generation. Pending IDs must match the intended scope. Unexpected changes, stale script sources, or missing caches must be resolved explicitly rather than triggering a full batch. Preserve the original seed and per-line signal repair/exception records before changes.

Generate a small comparison, normally two candidates and at most three in the initial round. Keep the original game WAV active while auditioning. Record candidate seed, selected attempt, exact WAV path/hash and feedback in a small `reviews/<case>/review.md` under the batch; the preview page does not persist user review decisions. Save candidates outside the regenerating preview selection when needed. Do not hand-edit request hashes or use unverified audio as a cached take.

If no candidate fits, report the control limitation and the next focused experiment. A new backend, emotional reference, or whole-character voice change is a separate choice with its own identity comparison; do not silently replace voices, upgrade dependencies, use a paid API, or endlessly sample seeds. Model choice remains open; check current official capabilities and model-weight licensing when recommending an alternative.

## Review and select

Run existing signal/ASR checks for truncation, silence, clipping, missing or wrong words. ASR scores do not prove correct delivery or voice identity. Do not report subjective listening as completed unless it occurred.

Provide the original and clearly labeled candidates, with the source line and intended change. Compare ordinary conversation first; do not make every mythic character declaim, whisper, or sound reverberant. Human judgment covers identity, emotion, implied meaning and continuity with neighboring lines.

When the user asks to choose among takes, retain candidates until that choice. If the user has already authorized agent selection and game integration, select within that authorization and continue, preserving any outstanding human-review status. Do not introduce a repeated approval gate. Existing Demo-use authorization is not final casting approval.

## Integrate only the chosen change

Restore the chosen seed and use the original batch's cache to refresh the report. Follow §7.13 rather than inventing a candidate-import command. Before `promote-demo --replace-owned --dry-run`, confirm source text, reference version and actual selected WAV still match the decision. A one-line repair normally means one replacement and all other files kept; an identical candidate can mean zero replacements.

Keep the existing `.meta` / GUID and owned-resource rollback history. Respect ownership/hash conflicts. Do not suppress QA or reuse stale repair evidence. After actual audio replacement, verify the affected line and its immediate transition in the existing Unity Editor through the verified CLI/MCP route; use a standalone Player only for a concrete Editor evidence gap.

Report separately: generated, technically checked, human-listened/selected, integrated, and game-playback verified. Close with `Verified`, `Should work`, or `Skipped / blocked` and the exact remaining limitation. An accepted preview alone is not verified game playback.
