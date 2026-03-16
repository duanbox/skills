---
name: unity-animation-patterns
description: Animation best practices for Unity using DOTween Pro
metadata:
  tags: dotween, animation, ui, transitions, sequencing
---

## When to use

Use this skill whenever you are creating animations in Unity, especially UI animations, transitions, or sequenced effects using DOTween Pro.

## How to use

Read individual rule files for detailed explanations and code examples:

- [rules/dotween-sequencing.md](rules/dotween-sequencing.md) - **Adapted from Remotion**: Sequence and chain animations
- [rules/ui-transitions.md](rules/ui-transitions.md) - **Adapted from Remotion**: UI panel transitions and scene changes
- [rules/easing-timing.md](rules/easing-timing.md) - **Adapted from Remotion**: Easing curves and timing patterns
- [rules/text-effects.md](rules/text-effects.md) - Text animation patterns with Febucci

## Core Principles (from Remotion)

1. **All animations are time-based** - Use duration in seconds, not frames
2. **Sequences for complex timing** - Chain multiple animations with delays
3. **Avoid CSS-style transitions** - Use code-driven animations for consistency
4. **Premount/Prepare** - Initialize objects before animating
