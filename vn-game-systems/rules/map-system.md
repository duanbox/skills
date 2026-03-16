---
name: map-system
description: World map system design, architecture, and visual mechanics
metadata:
  tags: map, system, architecture, visual-novel, 2d
  source: docs/rules/rules_map.md
---

# Unity 2D World Map System Design Specification

## Project: Cultivation Wasteland (Ink Wash Style)

---

## 1. Design Goal & Vision

To create a strictly **2D, Orthographic, scrollable** world map that simulates a "Three Realms" vertical structure (**Heaven, Earth, Underworld**) using **Parallax Scrolling** and **Visual Layering** techniques.

| Aspect          | Description                                                       |
| --------------- | ----------------------------------------------------------------- |
| **Style**       | Ink Wash Painting (Black/White) mixed with Neon/Spirit Qi effects |
| **Perspective** | Cavalier/Scatter Perspective (Orthographic)                       |
| **Navigation**  | Drag to move, Scroll to zoom                                      |

### Travel Modes

- **Local**: Dashed lines (Shapes plugin) between nodes within a region
- **Inter-Realm**: Teleportation (Camera Transition) between isolated regions (Kunlun, Wasteland, Fengdu)

---

## 2. Scene Architecture (The "Industrial Path" Model)

We will use a **Single Scene** architecture with a refined 3-layer vertical structure for maximum visual depth and performance.

### 2.1 Core Layer Logic & Blending Formula

We follow the **Multi-Level Blending** formula for deep visual integration:

$$FinalColor = (DetailTex \times PaperTint) \oplus (BrushMask \times MacroTerrain \times InkColor)$$

- **$\oplus$**: Multiply (正片叠底) or Darken blending logic.
- **Layer ROLES**:
  - **Detail Layer (B)**: 1024x1024 seamless fiber texture.
  - **Macro Layer (A)**: 2:1 or 4:1 landscape "Dragon Veins" distribution.
  - **Brush Mask**: Dynamic `RenderTexture` from the Painter tool.

### 2.2 Hierarchy Structure

```plaintext
MapScene_Root
├── [System] MapCamera (Orthographic)
│    └── MapCamController.cs
│
├── [Global Volume] PostProcessing
│
├── [Parallax_Root] (Script: ParallaxManager.cs)
│    │
│    ├── 1. Macro_Terrain_Layer (Speed Factor: 1.0)
│    │    └── Sprite_GroundMap
│    │
│    ├── 2. Vector_Infrastructure_Layer (Speed Factor: 1.0)
│    │    └── Rail/Canal Paths
│    │
│    ├── 3. Decoration_Layer (Speed Factor: 0.95) // Subtle Parallax
│    │    ├── Prefab_DeadTree_01
│    │    └── Prefab_Ruin_Stone_A
│    │
│    └── 4. Location_Prefabs_Layer (Speed Factor: 1.0)
│         └── Node_City_ChangAn
```

---

## 3. Visual Mechanics Implementation

### A. The Kunlun "Height" Illusion

**Technique**: "Floating Island"

**Setup**:

1. Place `Region_Kunlun` at a high Y coordinate (isolated)
2. In `Foreground_Layer`, place semi-transparent Cloud Sprites that cover the bottom 20% of the Kunlun buildings

**Result**: When camera pans, clouds move faster than buildings, creating a sensation that buildings are far behind/above the clouds.

---

### B. The Fengdu "Depth" Illusion (The Hole)

**Technique**: "Masking & Differential Scroll"

**Setup**:

1. **Wasteland Map (Sprite)**: Must have an `Alpha=0` (Transparent) area cut out where the "Abyss" is
2. **Fengdu Map (Sprite)**: Placed physically at the same X/Y as the hole, but slightly smaller or darker

**Z-Sorting**:

- Fengdu `SortingOrder = -5`
- Wasteland `SortingOrder = 0`

**Parallax Logic (Crucial)**:

> [!IMPORTANT]
> Even though they are in the same Gameplay Group, for the **Visuals** of Fengdu (the background floor of hell), we treat it as `Background_Layer` (Factor 0.8).

- The `Node_GhostGate` (Interactable) must remain in `Gameplay_Layer` (Factor 1.0) to match mouse clicks, OR we use a unified Raycast approach.

**Simplified Approach**: Put Fengdu visuals in Background (0.8). Put Fengdu Buttons in Gameplay (1.0). Accept slight visual drift (it adds to the trippy effect).

---

### C. Path Visual Implementation (The Soul-Rail & Ink Canal)

Paths are treated as "living lines growing on rice paper" rather than static textures.

#### 1. Spiritual Rail (灵力铁轨)

- **Method**: `Draw.Polyline` with a custom shader.
- **Visuals**: Gold/Bronze rune lines with `DashOffset` to simulate energy flow.
- **Ink Blending**: Add a slight black ink bleeding effect at the edges to ground the neon lines into the paper texture.

#### 2. Ink Canal (灵能运河)

- **Method**: Thick Polyline with varying thickness.
- **Visuals**: A custom ink shader with **Distortion** to simulate flowing heavy ink.
- **Dynamics**: Can be color-shifted or "dried up" (broken alpha) via code based on game state.

---

### D. Depth Blending & Occlusion

To solve the "combining feel" between vector paths and macro terrain:

1. **Height Map**: Synchronously export a Depth/Height map during terrain generation.
2. **Depth Masking**:
   - When a rail passes through a "Mountain" area (high depth), the path shader darkens or fades to simulate a tunnel or occlusion.
   - When a canal passes through a "Plain", the ink disperses/fades; in "Valleys", it deepens.
3. **Ink Bleeding (Shader)**: Buildings and path nodes use a "Bleed-in" mask to soften the transition between prefabs and the base ink-wash texture.

---

### E. Map Decoration Layer (Ecological Detail)

Enhancing the world with procedural and dynamic elements to avoid the "sticker" look.

#### 1. Component-based Rendering

- All ruins, withered trees, and relics are independent **Prefabs** with a subtle Z-offset (0.05 - 0.1).
- **Parallax Depth**: Decorations move slightly slower (Speed Factor: 0.95) to give a "standing" 3D effect on 2D paper.

#### 2. Rule-based Generation (Map Decoration System)

- **Macro Mask Logic**: Reads the grayscale value of the Macro Terrain mask.
- **Rules**:
  - `Value > 0.6` (Mountains/Dark): Distribute **[High Ruins, Withered Tree Clusters]**.
  - `Value < 0.3` (Plains/Light): Distribute **[Small Relics, Desert Grass, Scraps]**.
- **Performance**: Uses **Object Pooling** to only instantiate objects within the camera viewport.

#### 3. Ambient & Interaction VFX

- **Ambient**: Tiny particles around ruins (Neon leaks, Ink dust).
- **Hover**: Objects use `DOTween` for subtle scaling and neon glow activation when hovered.

---

### F. Macro Terrain Logic (The Gray-scale Standard)

To automate placement and rendering, the Macro Mask texture must follow a strict grayscale convention:

| Value Range                   | Geography           | Logic / Action                                                                 |
| :---------------------------- | :------------------ | :----------------------------------------------------------------------------- |
| **#000000 - #333333** (Dark)  | **Mountains**       | Scatter: [High Ruins, Withered Trees]. Path Shader: Apply occlusion (tunnels). |
| **#666666 - #999999** (Grey)  | **Rivers/Lowlands** | Overlay: [Ink Mist, Water VFX]. Path: Dry up or distort.                       |
| **#CCCCCC - #FFFFFF** (White) | **Plains/Empty**    | Scatter: [Minimum debris]. Base: Clean paper fibers visible.                   |

> [!NOTE]
> All AI-generated macro masks must be strictly restricted to these geological features (No buildings/trees baked into the mask).

---

## 4. Required C# Systems

### 1. MapCameraController.cs

| Attribute        | Value                                 |
| ---------------- | ------------------------------------- |
| **Dependencies** | DOTween, Odin Inspector               |
| **Input**        | Mouse Drag (Pan), Scroll Wheel (Zoom) |

#### Pan Settings

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| **Pan Speed** | `float` | `1` | 拖动速度倍率（1 = 正常速度） |
| **Invert Pan** | `bool` | `false` | 反转拖动方向 |

#### Zoom Settings

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| **Zoom Speed** | `float` | `2` | 缩放速度 |
| **Auto Calculate Min Zoom** | `bool` | `true` | 自动计算最小缩放（看全地图）<br/>⚠️ **推荐关闭**：大部分情况下应手动设置 |
| **Zoom To Fit Padding** | `float` | `0.1` | 缩放到全景时的留白比例（0-0.5）<br/>仅在 Auto Calculate 开启时有效 |
| **Manual Min Zoom** | `float` | `2` | 手动设置最小缩放值（默认近景）<br/>⚠️ **核心参数**：决定玩家默认看到的区域大小<br/>- 值越小 = 看到的范围越小（近景）<br/>- 值越大 = 看到的范围越大（远景） |
| **Min Movement Margin** | `float` | `1` | 最小移动边距（0.5-5 单位）<br/>确保摄像机始终有移动空间，防止锁死<br/>- 值越小 = 移动范围越小，但更接近地图边缘<br/>- 值越大 = 移动自由度更高，但会提前遇到边界 |

> [!TIP]
> **推荐配置（标准玩法）**:
>
> - `Auto Calculate Min Zoom`: **关闭**
> - `Manual Min Zoom`: `2-5`（根据地图大小调整）
> - `Min Movement Margin`: `1`（平衡值）
>
> 这样玩家默认看到局部地图，可通过滚轮缩小到看全地图。

#### Bounds Settings

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| **Use Bounds** | `bool` | `true` | 启用边界限制 |
| **Background Sprite** | `SpriteRenderer` | - | 自动检测地图背景 Sprite 并计算边界 |
| **Map Resolution** | `Vector2` | `3840×2160` | 地图分辨率（像素）<br/>仅在 Background Sprite 为空时使用 |
| **Pixels Per Unit** | `float` | `100` | 像素到世界单位的转换比例 |
| **Bounds Padding** | `float` | `0` | 边界额外填充（世界单位） |
| **Encapsulate Regions** | `bool` | `false` | 是否包含 MapRegion 对象来扩展边界 |

#### 计算逻辑（Read Only）

| 参数 | 说明 |
|------|------|
| **Min Zoom** | 最小缩放值（能看到的最小范围） |
| **Max Zoom** | 最大缩放值（自动计算，确保永不显示黑边） |
| **Map Min/Max X/Y** | 当前地图的世界坐标边界 |

**约束规则**:

- `ClampPosition`: 严格限制摄像机在地图边界内，**绝不显示黑边**
- `ClampZoom`:
  - **MinZoom**: 由 `Manual Min Zoom` 或 Auto Calculate 决定
  - **MaxZoom**: 自动计算 = `(地图尺寸 - Min Movement Margin) / 2`，确保有移动空间

> [!IMPORTANT]
> **核心设计原则**：
>
> 1. **永不显示黑边**：MaxZoom 的计算保证摄像机视野不会超出地图范围
> 2. **始终可拖动**：Min Movement Margin 确保即使在极限缩放下也有移动空间
> 3. **分辨率自适应**：支持 1920×1080 到 3840×2160 的自动适配

---

### 2. ParallaxSystem.cs

**Logic**:

1. Store `lastCameraPosition`
2. In `LateUpdate`, calculate `delta = cam.pos - lastCamPos`
3. Apply `delta * (parallaxFactor - 1)` to the local position of Background/Foreground layers

---

### 3. MapManager.cs (Teleportation Logic)

**Function**: `TravelToRegion(RegionType target)`

**Flow**:

1. Block User Input
2. Fade UI to Black (or Ink dispersion effect)
3. `camera.transform.position = targetRegion.centerPosition`
4. Switch BGM / PostProcessing Profile
5. Fade UI to Clear
6. Unlock Input

---

### 4. MapPathRenderer.cs (Vector Drawing)

Handles the real-time rendering of rails, canals, and cables using **Shapes**.

```csharp
using Shapes;
using UnityEngine;

namespace Game.Systems.Map
{
    public class MapPathRenderer : ImmediateModeShapeDrawer
    {
        [Header("Path Config")]
        public PolylinePath RailPath; 
        public Color RailNeonColor = new Color(0, 1, 1, 1); // Neon Cyan

        public override void DrawShapes(Camera cam)
        {
            using (Draw.Command(cam))
            {
                Draw.LineGeometry = LineGeometry.Flat2D;
                
                // 1. Draw Base Ink (The "Shadow/Bleed")
                Draw.Polyline(RailPath, closed: false, thickness: 0.1f, color: Color.black);
                
                // 2. Draw Neon Pulse (The "Energy Line")
                float offset = Time.time * 0.5f;
                Draw.Polyline(RailPath, closed: false, thickness: 0.04f, 
                              color: RailNeonColor, dashStyle: DashStyle.Default(offset));
            }
        }
    }
}
```

**Key Features**:

- **Logical Dynamicism**: Real-time state updates (broken connections, dimming lights).
- **Scale Independence**: Vector-based lines remain sharp at any zoom level.
- **Connectivity**: Visually "locks" nodes together into a cohesive geography.

---

## 5. IDE Action Items (Step-by-Step)

> [!TIP]
> Follow these steps to implement the system incrementally.

### Step 1: Folder Structure

Create the folder structure `Game/Systems/Map`.

### Step 2: Camera Controller

Generate the `MapCameraController` script with Odin Inspector attributes for tuning speed/bounds.

### Step 3: Parallax System

Generate the `ParallaxSystem` script supporting multiple layer lists.

### Step 4: Scene Setup

Create a Scene named `Map_World` and set up the Hierarchy exactly as defined in Section 2.

### Step 5: Parallax Testing

Create dummy Sprites (Square placeholders) to test the "Hole in Ground" effect:

- Green Square with hole (Wasteland)
- Red Square behind it (Fengdu)
- **Test Parallax**: When moving camera, Red Square should move slightly slower than Green Square

---

## 6. AI Implementation Notes

> [!NOTE]
> Extra prompts for AI code generation.

When implementing the C# scripts:

- Use **UniTask** for async operations (teleport sequence)
- Use **DOTween** for camera smoothing
- Expose all data fields using **Odin Inspector** attributes like `[Title]`, `[BoxGroup]`
- For Parallax, keep it simple: move the **container Transform**, not individual sprites

---

## 7. Checklist

- [ ] Camera Projection 设为 Orthographic
- [ ] 场景结构按照 "4-Layer Industrial" 模型搭建 (Terrain, Infrastructure, Decorations, Prefabs)
- [ ] 实现 Component Blending Shader (让组件色调与宣纸底色完美融合)
- [ ] 实现基于 Macro Mask 的装饰物自动分布系统 (Map Decoration System)
- [ ] 使用 Shapes 插件实现跨区域线性结构 (Rails, Canals)
- [ ] 实现 Depth Mask 以后期处理或 Shader 方式进行高度感官融合
- [ ] MapPathRenderer 支持动态流光和状态切换
- [ ] ParallaxSystem 支持 4 层架构的基础移动
- [ ] 区域传送的异步 Fade 逻辑完成
