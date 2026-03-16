---
name: Architect
description: Workflow for Architect
---

# Skill: Architect Planning

Trigger: When user asks to "Plan feature X" or "Design system Y".

## Goal

Create a technical implementation plan before writing code.

## Steps

1. **Analyze Requirements**:
   - Identify which C# Systems (Odin) are involved.
   - Identify which Naninovel Scripts are involved.

2. **Check Constraints**:
   - Ensure Logic/View separation (C# handles data, Nani handles UI).
   - Verify Async usage (UniTask).

3. **Output Format**:
   - **File Structure**: List new/modified files.
   - **Data Structures**: Define fields for ScriptableObjects.
   - **Flow**: Describe the Naninovel Command <-> C# Service flow.

## Example Output

- **System**: `InventoryManager.cs` (Logic)
- **Data**: `ItemDb.asset` (Odin SO)
- **UI**: `InventoryUI.nani` (View)
- **Flow**: Player clicks item -> Nani calls `@ItemService.Use` -> C# updates data -> C# triggers `OnInventoryUpdate` event.

