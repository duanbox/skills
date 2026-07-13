---
name: unity-animation
description: Implement lifecycle-safe EndGods DOTween and UniTask animation. Use when changing ownership, await, cancellation, teardown, replay, time scale, or reduced motion; not for static layout or generic visual design.
---

# Unity Animation

Use the installed source and current project patterns as authority. The current audited baseline is DOTween `1.2.790` and UniTask `2.5.10`; re-check the local installation before relying on version-specific APIs.

## Required Routing

- Read `Docs/rules/rules_tech_stack.md` and two or three nearby animation implementations.
- For UI motion, also read `Docs/Pipeline/ui-vibe-coding-pipeline.md` and the affected UI brief.
- For text effects, read `Docs/Knowledge/textanimator.md`; Naninovel text tags are not DOTween effects.
- For feedback/juice, inspect the existing MMFeedbacks pattern before adding a parallel DOTween effect.
- For validation ownership, read `Docs/rules/rules_testing.md`.
- When the change depends on a version-specific API or await extension, verify it against `Assets/Plugins/Demigiant/DOTween/`, `Assets/Plugins/UniTask/package.json`, and the installed UniTask DOTween extensions rather than external examples.

## Workflow

1. Define the animation owner, target, start/end state, time-scale policy, replay/re-entry behavior, cancellation owner, and cleanup point.
2. Preserve Scene/Prefab-authored static geometry. Animate state transitions; do not use tweens to normalize broken authoring data.
3. Kill or complete the previous owned tween before replacing it. Avoid multiple Sequences fighting over the same target.
4. Bind lifetime with `SetLink` or explicit lifecycle cleanup as appropriate. Do not rely on SafeMode to hide destroyed-target bugs.
5. If a tween must replay after completion, choose and document `SetAutoKill(false)` plus rewind/restart semantics; otherwise discard stale handles after kill.
6. Before awaiting a tween, inspect the exact local extension return type and nearby project usage. Prefer an existing UniTask-compatible adapter: use `ToUniTask` or the project's custom `AsyncWaitForCompletion` when that concrete extension returns `UniTask`. Do not introduce a `System.Threading.Tasks.Task` path or assume an API's return type from its method name.
7. Keep infinite idle loops separate from awaited finite Sequences. An infinite child prevents completion and can deadlock a flow waiting on the parent.
8. Decide explicitly whether pause/menu transitions use scaled or unscaled time. Respect reduced-motion requirements and never delay player agency for decorative motion.
9. Put reusable timing/easing values in existing serialized configuration, `UIDataAsset`, `GameConstants`, or the nearest established project owner; do not scatter magic durations.

## Sequence Rules

- `Append` is sequential, `Join` is parallel with the preceding step, and `Insert` uses absolute Sequence time.
- Prepare the initial visual state before playing; avoid a one-frame flash of the final state.
- Keep callbacks idempotent and unsubscribe or invalidate them with the owning lifecycle.
- Do not append an infinite loop before steps that must execute or before an awaited completion.
- Avoid bounce/elastic easing on high-frequency navigation or critical confirmations unless the UI brief explicitly calls for it.

## Verification

Classify verification under the current `AGENTS.md` risk lane and exercise only states whose lifecycle contract changed or presents a credible regression risk.

- Runtime C# change: project rule scan, Unity recompile, and Console check as required by `AGENTS.md`.
- Behavior, visibility, async, or scene-flow change: smallest relevant test or gameplay flow for the changed contract.
- Visible UI animation: real GameView/PlayMode evidence of the affected state as required by the UI lane.
- Check repeated open/close, target disable/destroy, cancellation, replay, reduced motion, or pause/time-scale only when the implementation touches that state. Do not require the full list for every tween edit.

Report the lifetime owner, changed cancellation/re-entry decision, verification evidence, and any skipped validation. Report installed versions only when a version-specific API or extension was material to the change.
