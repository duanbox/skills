---
name: easing-timing
description: Easing curves and timing patterns for DOTween
metadata:
  tags: easing, timing, curves, dotween
  adapted_from: Remotion timing.md
---

# Easing and Timing Patterns

**Adapted from**: [Remotion timing.md](https://github.com/remotion-dev/skills/blob/main/skills/remotion/rules/timing.md)

## Basic Easing

DOTween provides comprehensive easing curves via `SetEase()`:

```csharp
using DG.Tweening;

// Linear (default)
transform.DOMoveX(5f, 1f).SetEase(Ease.Linear);

// Ease In (starts slow, accelerates)
transform.DOMoveX(5f, 1f).SetEase(Ease.InQuad);

// Ease Out (starts fast, decelerates)
transform.DOMoveX(5f, 1f).SetEase(Ease.OutQuad);

// Ease InOut (smooth start and end)
transform.DOMoveX(5f, 1f).SetEase(Ease.InOutQuad);
```

## Common Ease Types (Sorted by Intensity)

**From Remotion**:
> Curves sorted from most linear to most curved:
> - `Easing.quad`
> - `Easing.sin`
> - `Easing.exp`
> - `Easing.circle`

**Unity/DOTween Equivalent**:

```csharp
// Subtle curves
Ease.InQuad / Ease.OutQuad / Ease.InOutQuad      // Gentle acceleration/deceleration

// Medium curves
Ease.InSine / Ease.OutSine / Ease.InOutSine      // Smooth, natural motion
Ease.InCubic / Ease.OutCubic / Ease.InOutCubic  // More pronounced

// Strong curves
Ease.InExpo / Ease.OutExpo / Ease.InOutExpo      // Dramatic acceleration
Ease.InCirc / Ease.OutCirc / Ease.InOutCirc      // Sharp curves
```

## Recommended Easing for Common Scenarios

**From Remotion**: `{ damping: 200 }` for smooth motion without bounce

**Unity Equivalent**:

```csharp
// UI Reveals (subtle, smooth)
panel.DOFade(1f, 0.3f).SetEase(Ease.OutQuad);

// Button Press (snappy)
button.transform.DOScale(0.9f, 0.1f).SetEase(Ease.InOutQuad);

// Panel Slide In (bouncy entrance)
panel.DOAnchorPos(Vector2.zero, 0.5f).SetEase(Ease.OutBack);

// Heavy Element (slow, weighty)
heavyObject.DOMoveY(0f, 1.2f).SetEase(Ease.OutExpo);

// Elastic Pop (playful)
icon.transform.DOScale(1f, 0.6f).SetEase(Ease.OutElastic);
```

## Custom Bezier Curves

**Remotion Pattern**:
```ts
easing: Easing.bezier(0.8, 0.22, 0.96, 0.65)
```

**Unity Equivalent**:
```csharp
// DOTween supports AnimationCurve
AnimationCurve customCurve = AnimationCurve.EaseInOut(0f, 0f, 1f, 1f);
transform.DOMoveX(5f, 1f).SetEase(customCurve);

// Or use built-in curves
transform.DOMoveX(5f, 1f).SetEase(Ease.INTERNAL_Custom);
```

## Elastic and Bounce

DOTween has unique easing types not in Remotion:

```csharp
// Elastic (overshoots then settles)
transform.DOScale(1f, 0.6f).SetEase(Ease.OutElastic);

// Bounce (bounces at the end)
transform.DOAnchorPosY(0f, 0.5f).SetEase(Ease.OutBounce);

// Back (slight overshoot)
transform.DOScale(1f, 0.4f).SetEase(Ease.OutBack);
```

## Timing Presets (Remotion-inspired)

```csharp
public static class AnimationPresets
{
    // From Remotion: { damping: 200 } - Smooth, no bounce
    public static Ease Smooth = Ease.OutQuad;
    
    // From Remotion: { damping: 20, stiffness: 200 } - Snappy, minimal bounce
    public static Ease Snappy = Ease.OutBack;
    
    // From Remotion: { damping: 8 } - Bouncy entrance
    public static Ease Bouncy = Ease.OutElastic;
    
    // From Remotion: { damping: 15, stiffness: 80, mass: 2 } - Heavy, slow
    public static Ease Heavy = Ease.OutExpo;
}

// Usage
panel.DOFade(1f, 0.3f).SetEase(AnimationPresets.Smooth);
button.transform.DOScale(1f, 0.4f).SetEase(AnimationPresets.Snappy);
```

## Duration Best Practices

**From Remotion**: Write animations in seconds

**Unity**: DOTween accepts seconds by default

```csharp
// Standard UI durations
float fadeTime = 0.3f;      // Quick fade
float slideTime = 0.5f;     // Panel slide
float elasticTime = 0.6f;   // Elastic pop

panel.DOFade(1f, fadeTime).SetEase(Ease.OutQuad);
panel.DOAnchorPos(Vector2.zero, slideTime).SetEase(Ease.OutBack);
icon.transform.DOScale(1f, elasticTime).SetEase(Ease.OutElastic);
```

## Combining Easing with Sequences

```csharp
public void ComplexAnimation()
{
    Sequence seq = DOTween.Sequence();
    
    // Fast entrance
    seq.Append(panel.DOFade(1f, 0.2f).SetEase(Ease.InQuad));
    
    // Bouncy scale
    seq.Join(panel.transform.DOScale(1f, 0.5f)
        .From(0.8f)
        .SetEase(Ease.OutBack));
    
    // Slow, smooth exit
    seq.AppendInterval(2f); // Wait
    seq.Append(panel.DOFade(0f, 0.8f).SetEase(Ease.OutExpo));
    
    seq.Play();
}
```

## Summary Table

| Use Case | Recommended Easing | Duration |
|----------|-------------------|----------|
| UI Fade In/Out | `Ease.OutQuad` | 0.3s |
| Panel Slide | `Ease.OutBack` | 0.5s |
| Button Press | `Ease.InOutQuad` | 0.1s |
| Icon Pop | `Ease.OutElastic` | 0.6s |
| Heavy Object Drop | `Ease.OutExpo` | 1.0s+ |
| Text Reveal | `Ease.OutQuad` | 0.4s |
| Bounce Effect | `Ease.OutBounce` | 0.5s |

**Key Principle**: Match easing to the physical feeling you want - light elements use subtle easing (Quad), playful elements use elastic/bounce, heavy elements use exponential.
