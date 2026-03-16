---
name: fundamental-fixes
description: Principle of seeking fundamental solutions instead of workarounds
metadata:
  tags: debugging, problem-solving, root-cause-analysis, workarounds
---

# Fundamental Fixes Over Workarounds

**Core Principle**: When encountering bugs, always seek the **fundamental root cause** rather than adding workarounds or defensive code.

## Why This Matters

Workarounds create technical debt:
- They add complexity without solving the real problem
- They can mask or interact with the actual issue
- They make the codebase harder to understand
- Future bugs may stem from the workaround itself

## Case Study: Camera Drift Bug

### ❌ The Workaround Path (What We Initially Did)

**Problem**: Camera position shifted after dialogue ended.

**Attempted Workarounds**:
1. Grace period delays (0.5s → 1.0s wait before recalculation)
2. Bounds caching (prevent dynamic recalculation)
3. Camera state cache/restore (save position before dialogue, restore after)

**Result**: None of these fixed the core issue. The camera still moved.

### ✅ The Fundamental Fix (What Actually Worked)

**Root Cause Investigation**:
- Added strategic debug logging
- Observed **actual behavior** via console logs
- Found camera jumped from `(-9.77, 2.76)` to `(-16.14, 0.00)` in a single frame
- Traced to `HandlePan()` using stale `_lastMousePosition` from before dialogue

**The Real Problem**:
```csharp
// HandlePan() was called after control regained
Vector3 delta = Input.mousePosition - _lastMousePosition;
// _lastMousePosition was FROZEN during dialogue
// Massive accumulated delta caused instant camera jump
```

**The Fix (3 Lines)**:
```csharp
if (canControl != _lastCanControl && canControl)
{
    _lastMousePosition = Input.mousePosition; // Reset to current
    _isDragging = false; // Clear dragging state
}
```

**Result**: Problem completely resolved.

## Debugging Methodology

### 1. Data Over Theory
❌ **Theorizing**: "Maybe Naninovel modifies the camera..."  
✅ **Observing**: Add logging, see what **actually** happens

### 2. Verify Assumptions
❌ **Assuming**: "The camera restoration logic prevents drift"  
✅ **Verifying**: Check if Naninovel even touches the camera (it doesn't - uses CustomCameraPrefab)

### 3. Empirical Debugging
When theoretical approaches fail:
- Add **strategic logging** at key decision points
- Log state **before and after** operations
- Track **exact values** (positions, timestamps, state transitions)
- Look for **unexpected changes** in logged data

### 4. Trace Execution Flow
- Don't trust documentation alone
- Follow the **actual code path**
- Check what runs **between** observed state changes

## Red Flags (When to Suspect You're Building a Workaround)

🚩 You're adding delays or "grace periods"  
🚩 You're caching and restoring state "just in case"  
🚩 You're adding defensive checks for problems you haven't confirmed exist  
🚩 The fix makes the code more complex but you're not sure why it works  
🚩 You're solving an "assumed" problem without verification

## Green Lights (Signs of a Fundamental Fix)

✅ The fix is simple (often just a few lines)  
✅ You can explain **exactly** why the bug occurred  
✅ You can explain **exactly** why your fix resolves it  
✅ The fix removes code rather than adds it  
✅ The fix makes the system's behavior more predictable  
✅ You verified the fix with test cases or logging

## Workflow

```
1. Encounter Bug
     ↓
2. Add Strategic Logging (observe actual behavior)
     ↓
3. Identify Root Cause (what actually happened vs what should happen)
     ↓
4. Form Hypothesis (why is this happening?)
     ↓
5. Verify Hypothesis (check assumptions, trace code path)
     ↓
6. Implement Minimal Fix (address root cause, not symptoms)
     ↓
7. Verify Fix (confirm bug is gone, no new issues)
     ↓
8. Clean Up Logging (remove temporary debug code)
```

## Examples from Our Codebase

### Example 1: Camera Drift
- **Symptom**: Camera shifts after dialogue
- **Workaround Attempts**: Grace periods, state caching
- **Root Cause**: Stale mouse position data
- **Fundamental Fix**: Reset mouse state on control regain

### Example 2: Green Background Persistence
- **Symptom**: Green background visible after returning to map
- **Workaround Attempt**: Manually hide specific backgrounds
- **Root Cause**: Naninovel actors not being hidden on mode switch
- **Fundamental Fix**: Iterate and hide all Background/Character actors

## Application to New Code

When writing new code that seems like it might prevent a problem:

**Ask yourself**:
1. Does this problem **actually** exist? (Verify, don't assume)
2. If it exists, what is the **root cause**?
3. Am I solving the cause or just the symptom?
4. Is there a simpler way that addresses the fundamental issue?

## Summary

> **"The best code is the code that doesn't need to exist."**
> **"Fix the disease, not the symptoms."**

Always prefer:
- **Understanding** over patching
- **Observation** over assumption
- **Simplicity** over complexity
- **Root causes** over symptoms
