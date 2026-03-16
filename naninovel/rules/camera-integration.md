---
name: camera-integration
description: Camera stacking with Naninovel's CustomCameraPrefab
metadata:
  tags: camera, urp, camera-stacking, integration
---

# Camera Integration with Naninovel

**Key Insight**: Naninovel uses its **own camera** (CustomCameraPrefab) and does NOT modify your game cameras.

## Architecture

```
Map Camera (Base)
  ↓ Stack
  ├── Naninovel MainCamera (Overlay) - Renders backgrounds/characters
  └── Naninovel UICamera (Overlay) - Renders dialogue UI
```

## Configuration

### CameraConfiguration.asset
```yaml
CustomCameraPrefab: YourNaninovelCameraPrefab
# This is Naninovel's OWN camera, separate from game cameras
```

### Camera Stacking Setup

```csharp
// In your MapCameraController or similar
using UnityEngine.Rendering.Universal;
using Naninovel;

public class MapCameraController : MonoBehaviour
{
    private Camera _mapCamera;
    
    private void SetupCameraStacking()
    {
        _mapCamera = GetComponent<Camera>();
        var mapCamData = _mapCamera.GetUniversalAdditionalCameraData();
        mapCamData.renderType = CameraRenderType.Base;
        
        // Get Naninovel cameras
        var cameraManager = Engine.GetService<ICameraManager>();
        Camera naniMainCam = cameraManager.Camera; // Background/Character camera
        Camera naniUICam = cameraManager.UICamera; // UI camera
        
        // Configure Naninovel cameras as overlays
        var naniMainData = naniMainCam.GetUniversalAdditionalCameraData();
        naniMainData.renderType = CameraRenderType.Overlay;
        naniMainCam.clearFlags = CameraClearFlags.Depth;
        
        var naniUIData = naniUICam.GetUniversalAdditionalCameraData();
        naniUIData.renderType = CameraRenderType.Overlay;
        naniUICam.clearFlags = CameraClearFlags.Depth;
        
        // Add to stack (order matters!)
        mapCamData.cameraStack.Clear();
        mapCamData.cameraStack.Add(naniMainCam); // First
        mapCamData.cameraStack.Add(naniUICam);   // Second
    }
}
```

## Critical Rule: Don't Cache/Restore Camera State

❌ **Anti-Pattern** (from our camera drift bug):
```csharp
// DON'T DO THIS
private Vector3 _cachedCameraPosition;
private float _cachedCameraZoom;

private void EnterNovelMode()
{
    _cachedCameraPosition = mapCamera.transform.position; // ❌
    _cachedCameraZoom = mapCamera.orthographicSize;      // ❌
}

private void EnterMapMode()
{
    mapCamera.transform.position = _cachedCameraPosition; // ❌ Causes drift!
    mapCamera.orthographicSize = _cachedCameraZoom;       // ❌ Unnecessary!
}
```

✅ **Correct Approach**:
```csharp
// Map Camera transform is NEVER modified by Naninovel
// No need to cache or restore anything
private void EnterNovelMode()
{
    // Just show Naninovel UI, hide Map UI
    // Camera Transform is untouched
}

private void EnterMapMode()
{
    // Just hide Naninovel UI, show Map UI
    // Camera Transform is untouched
}
```

## Why Caching is Wrong

1. **Naninovel uses `CustomCameraPrefab`** - It has its own camera
2. **Map Camera is never modified** - Its Transform stays constant
3. **"Restoring" position actually MOVES it** - Creating bugs out of thin air

## MainCameraLookContainer

Naninovel's `MainCameraLookContainer` is a parent GameObject that contains Naninovel's cameras. You may need to show/hide it:

```csharp
private void SetMainCameraLookContainerActive(bool active)
{
    var lookContainer = GameObject.Find("MainCameraLookContainer");
    if (lookContainer != null)
    {
        lookContainer.SetActive(active);
    }
}

// Call when entering dialogue mode
SetMainCameraLookContainerActive(true);

// Call when returning to map
SetMainCameraLookContainerActive(false);
```

## Summary

- ✅ Naninovel has its **own camera** (CustomCameraPrefab)
- ✅ Use **URP Camera Stacking** to layer cameras
- ❌ **Never cache/restore** map camera state
- ✅ Map Camera Transform is **never modified** by Naninovel
- ✅ Use `MainCameraLookContainer.SetActive()` to show/hide Naninovel rendering
