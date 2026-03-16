---
name: namespace-structure
description: Namespace organization for Unity C# game code
metadata:
  tags: namespaces, organization, structure
---

# Namespace Structure

All game code should be organized under clear namespaces to maintain code organization and prevent naming conflicts.

## Core Structure

```csharp
Game.Core          // Core systems, shared utilities, game state
Game.Story         // Naninovel-related code, custom commands, bridges
Game.Systems       // Gameplay systems (Map, Inventory, Combat, etc.)
Game.Data          // ScriptableObject data definitions
Game.UI            // UI controllers and components
```

## Examples

### Core Systems
```csharp
namespace Game.Core
{
    public class GameStateManager : MonoBehaviour { }
    public enum GameState { MainMenu, MapExploration, Dialogue, Combat }
}
```

### Story/Naninovel Integration
```csharp
namespace Game.Story
{
    [CommandAlias("map")]
    public class NavigateToMapCommand : Command { }
    
    public class NaninovelBridge : MonoBehaviour { }
}
```

### Gameplay Systems
```csharp
namespace Game.Systems.Map
{
    public class MapCameraController : MonoBehaviour { }
    public class MapInteractionManager : MonoBehaviour { }
}

namespace Game.Systems.Inventory
{
    public interface IInventoryService { }
    public class InventoryManager : MonoBehaviour, IInventoryService { }
}
```

### Data Definitions
```csharp
namespace Game.Data
{
    [CreateAssetMenu(menuName = "Game/Item Database")]
    public class ItemDatabase : ScriptableObject { }
}
```

## Guidelines

1. **One namespace per feature area** - Don't over-granularize
2. **Match folder structure** - `Assets/Game/Scripts/Systems/Map` → `Game.Systems.Map`
3. **Use nested namespaces sparingly** - Maximum 3 levels deep
4. **Global usings** (in .csproj or IDE) for frequently used namespaces:
   - `UnityEngine`
   - `Cysharp.Threading.Tasks`
   - `Naninovel`
