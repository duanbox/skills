---
name: dotween-sequencing
description: Sequencing patterns for DOTween - delay, chain, and nest animations
metadata:
  tags: dotween, sequence, chaining, timing
  adapted_from: Remotion sequencing.md
---

# DOTween Sequencing Patterns

**Adapted from**: [Remotion sequencing.md](https://github.com/remotion-dev/skills/blob/main/skills/remotion/rules/sequencing.md)

## Basic Sequencing

Use `Sequence()` to chain multiple animations together:

```csharp
using DG.Tweening;
using UnityEngine;

public class UIAnimator : MonoBehaviour
{
    [SerializeField] private RectTransform panel;
    [SerializeField] private CanvasGroup canvasGroup;
    
    public void ShowPanel()
    {
        Sequence sequence = DOTween.Sequence();
        
        // Fade in (duration: 0.3s)
        sequence.Append(canvasGroup.DOFade(1f, 0.3f));
        
        // Then slide in (duration: 0.5s)
        sequence.Append(panel.DOAnchorPos(Vector2.zero, 0.5f).SetEase(Ease.OutBack));
        
        // Play sequence
        sequence.Play();
    }
}
```

## Delayed Start (Remotion's `from` prop)

Use `.SetDelay()` or `.Insert()` for delayed starts:

```csharp
public void ShowTitleThenSubtitle()
{
    Sequence sequence = DOTween.Sequence();
    
    // Title appears at 0s
    sequence.Append(titleText.DOFade(1f, 0.5f));
    
    // Subtitle appears at 1s (1s delay)
    sequence.Insert(1f, subtitleText.DOFade(1f, 0.5f));
    
    sequence.Play();
}
```

## Series (One After Another)

**Remotion Pattern**:
```tsx
<Series>
  <Series.Sequence durationInFrames={45}><Intro /></Series.Sequence>
  <Series.Sequence durationInFrames={60}><Main /></Series.Sequence>
  <Series.Sequence durationInFrames={30}><Outro /></Series.Sequence>
</Series>
```

**Unity Equivalent**:
```csharp
public void PlaySeries()
{
    Sequence series = DOTween.Sequence();
    
    // Intro (0.75s)
    series.Append(introPanel.DOFade(1f, 0.75f));
    
    // Main (1.0s) - starts AFTER intro
    series.Append(mainPanel.DOFade(1f, 1.0f));
    
    // Outro (0.5s) - starts AFTER main
    series.Append(outroPanel.DOFade(1f, 0.5f));
    
    series.Play();
}
```

## Overlapping Sequences (Remotion's negative offset)

**Remotion Pattern**:
```tsx
<Series.Sequence offset={-15}>
  {/* Starts 15 frames before previous ends */}
</Series.Sequence>
```

**Unity Equivalent**:
```csharp
public void OverlappingTransitions()
{
    Sequence seq = DOTween.Sequence();
    
    // SceneA fades out (1.0s)
    seq.Append(sceneA.DOFade(0f, 1.0f));
    
    // SceneB fades in 0.25s BEFORE SceneA finishes (-0.25s offset)
    seq.Insert(0.75f, sceneB.DOFade(1f, 0.5f));
    
    seq.Play();
}
```

## Parallel Animations (Join vs Append)

```csharp
public void SimultaneousAnimations()
{
    Sequence seq = DOTween.Sequence();
    
    // Start together
    seq.Append(image.DOFade(1f, 0.5f));
    seq.Join(image.transform.DOScale(1.2f, 0.5f));  // Join = play at same time
    
    // Then next animation
    seq.Append(text.DOFade(1f, 0.3f));
    
    seq.Play();
}
```

## Nested Sequences

**Remotion Pattern**:
```tsx
<Sequence from={0}>
  <Background />
  <Sequence from={15} layout="none">
    <Title />
  </Sequence>
</Sequence>
```

**Unity Equivalent**:
```csharp
public async UniTask NestedSequences()
{
    // Parent sequence
    Sequence parent = DOTween.Sequence();
    
    // Background fades in
    parent.Append(background.DOFade(1f, 0.5f));
    
    // Create nested sequence for title
    Sequence titleSeq = DOTween.Sequence();
    titleSeq.Append(title.DOFade(1f, 0.3f));
    titleSeq.Join(title.transform.DOScale(1f, 0.3f).From(0.8f));
    
    // Insert nested sequence at 0.25s
    parent.Insert(0.25f, titleSeq);
    
    // Wait for completion
    await parent.Play().AsyncWaitForCompletion();
}
```

## Premounting (Prepare Before Play)

**Remotion Concept**: `premountFor={1 * fps}` loads component before it plays

**Unity Equivalent**: Initialize objects before animating

```csharp
public void PrepareAndAnimate()
{
    // Premount: Set initial state BEFORE animating
    panel.SetActive(true);
    canvasGroup.alpha = 0f;
    panel.anchoredPosition = new Vector2(0, -100);
    
    // Now animate
    Sequence seq = DOTween.Sequence();
    seq.Append(canvasGroup.DOFade(1f, 0.3f));
    seq.Join(panel.DOAnchorPos(Vector2.zero, 0.5f));
    seq.Play();
}
```

## Common Sequencing Patterns

### 1. Stagger Animation (Cascade)
```csharp
public void StaggerItems(List<RectTransform> items)
{
    Sequence seq = DOTween.Sequence();
    
    for (int i = 0; i < items.Count; i++)
    {
        float delay = i * 0.1f; // 0.1s stagger
        seq.Insert(delay, items[i].DOAnchorPosY(0, 0.5f)
            .From(new Vector2(0, -50))
            .SetEase(Ease.OutBack));
    }
    
    seq.Play();
}
```

### 2. Loop Within Sequence
```csharp
public void LoopingElement()
{
    Sequence seq = DOTween.Sequence();
    
    // Fade in once
    seq.Append(icon.DOFade(1f, 0.5f));
    
    // Then loop pulse
    seq.Append(icon.transform.DOScale(1.1f, 0.5f)
        .SetLoops(-1, LoopType.Yoyo)); // Infinite loop
    
    seq.Play();
}
```

### 3. Sequential UI Panel Flow
```csharp
public async UniTask ShowPanelFlow()
{
    Sequence seq = DOTween.Sequence();
    
    // 1. Header slides down (0.5s)
    seq.Append(header.DOAnchorPosY(0, 0.5f).From(new Vector2(0, 100)));
    
    // 2. Content fades in (0.3s) - starts after header
    seq.Append(content.DOFade(1f, 0.3f));
    
    // 3. Buttons pop in (0.4s) - starts after content
    seq.Append(buttons.transform.DOScale(1f, 0.4f)
        .From(0.8f)
        .SetEase(Ease.OutBack));
    
    await seq.Play().AsyncWaitForCompletion();
}
```

## Summary

| Remotion Concept | Unity/DOTween Equivalent |
|------------------|--------------------------|
| `<Sequence from={60}>` | `.Insert(1.0f, ...)` or `.SetDelay(1.0f)` |
| `<Series.Sequence>` | `.Append(...)` |
| `offset={-15}` (overlap) | `.Insert(earlierTime, ...)` |
| `premountFor` | Initialize before animating |
| `useCurrentFrame()` (local) | Use relative timing in sequences |
| `layout="none"` | Don't wrap (N/A in Unity) |

**Key Principle**: DOTween's `Sequence` is like Remotion's `<Series>` - it chains animations sequentially or with precise timing control using `.Insert()`.
