# EndGods Map & UI Hierarchy and Invocation/Hiding Relationships

This document outlines the layer hierarchy, camera stacking behavior, and invocation/hiding relationships between different UI and game states in the EndGods project.

## 1. SortingOrder Hierarchy

The UI and game objects are layered using `sortingOrder` from bottom to top:

| Layer / Component      | Sorting Order                             | Render Mode               | Handled By                           |
| ---------------------- | ----------------------------------------- | ------------------------- | ------------------------------------ |
| **Map Lines**          | `0`                                       | World Space               | `MapViewController` (via Shapes)     |
| **Map Regions**        | `0` (or dynamic)                          | World Space               | `MapRegion` (via Shapes)             |
| **Map Nodes**          | `10`                                      | World Space               | `MapViewController` (SpriteRenderer) |
| **Player (Map)**       | `20`                                      | World Space               | `MapViewController` (SpriteRenderer) |
| **Dungeon UI**         | `100` (`SORTING_ORDER_DUNGEON_UI`)        | ScreenSpaceCamera         | `DungeonViewController`              |
| **Combat UI**          | `150` (`SORTING_ORDER_COMBAT_UI`)         | ScreenSpaceCamera         | `CombatViewController`               |
| **Main HUD (Dungeon)** | `200` (`SORTING_ORDER_HUD_ABOVE_DUNGEON`) | ScreenSpaceCamera         | `MainHUDController`                  |
| **Naninovel UI**       | `250` (`SORTING_ORDER_NANINOVEL_UI`)      | ScreenSpaceCamera/Overlay | `NaninovelBridge`                    |
| **Main HUD**           | `300` (`SORTING_ORDER_MAIN_HUD`)          | ScreenSpaceCamera         | `MainHUDController`                  |
| **Main HUD (Overlay)** | `1000` (`SORTING_ORDER_OVERLAY_TOP`)      | ScreenSpaceOverlay        | `MainHUDController`                  |

## 2. Camera Stacking Architecture (URP)

In `Universal Render Pipeline` (URP), camera stacking is used to manage dynamic transparent overlays in a multi-camera setup.

**Base Camera**:
- `MapCamera` (`CameraRenderType.Base`): Renders the 2D map objects (Nodes, Lines, Player token).

**Overlay Cameras (Dynamically Stacked)**:
When entering different modes, `Overlay` cameras are injected into the `MapCamera`'s `cameraStack`. The render order matches the insertion order:
1. `MapCamera` (Bottom - Renders the physical world)
2. `DungeonCamera` (Overlay) - Renders the `DungeonCanvas`. Appended *before* `UICamera`.
3. `Naninovel MainCamera` (Overlay) - Renders characters and backgrounds.
4. `Naninovel UICamera` (Overlay) - Renders the dialogue interface.
5. (Optional) `MainHUD Camera` (If placed on a custom UICamera, rendered last).

## 3. Invocation and Hiding Relationships

### A. Entering Map Mode (Default)
- **Invocation**: `NaninovelBridge.EnterMapMode()`
- **Action**: Restores the `MapCamera` as the sole renderer.
- **Hiding**: `ExitStoryMode()` cleans up all Naninovel actors, UI, and removes Naninovel cameras from the stack. The Map input is unlocked.

### B. Entering Dungeon Mode
- **Invocation**: `DungeonManager.OpenDungeon()` -> `DungeonViewController.OpenDungeon()`
- **Action**: 
  - Activates `DungeonCanvas` and `DungeonCamera` (Adds to stack).
  - Main HUD switches to `DungeonMode` (`MainHUDController.SetDungeonMode(true)`). It hides specific elements configured in `ElementsToHideInDungeon` (like menus) but keeps HP/Spirit visible.
- **Hiding**: Naninovel UI and input are explicitly disabled (`DisableNaninovelUI()`) to avoid overlapping menus.

### C. Exiting Dungeon Mode
- **Invocation**: `DungeonViewController.OnDungeonEnded()`
- **Action**: Removes `DungeonCamera` from the stack. Restores Game State. 
- **Restoration**: Main HUD restores hidden elements (`SetDungeonMode(false)`). Naninovel UI is restored (`RestoreNaninovelUI()`).

### D. Entering Combat Mode
- **Invocation**: `CombatManager.StartBattle()` -> `CombatViewController.InitializeCombatView()`
- **Action**: 
  - `CombatCanvas` is activated at Sorting Order `150`.
  - Main HUD is completely hidden to focus on combat (`MainHUDController.SetCombatMode(true)`).
- **Hiding**: Dungeon Input and Map Input are locked.

### E. Entering Novel/Story Mode
- **Invocation**: `NaninovelBridge.EnterNovelMode()`
- **Action**:
  - Map Interaction is locked.
  - Adds Naninovel cameras (`MainCamera`, `UICamera`) to the `MapCamera` stack as overlays.
  - Ensures Main HUD is visible (`EnsureMainHUDVisible()`), pushed to Sorting Order `250`+ to sit above the dialogue.
- **Handling World Visibility**: If `hideMapWorld = false`, Naninovel's black UI background and Background Characters are hidden so the Map shows transparently behind the dialogue UI.
