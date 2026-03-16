---
name: composition-over-inheritance
description: Favor composition patterns over deep inheritance hierarchies
metadata:
  tags: composition, inheritance, design-patterns
---

# Composition Over Inheritance

**Principle**: Prefer composing objects from smaller components rather than building deep inheritance hierarchies.

## Why?

**Problems with Deep Inheritance**:
- Tight coupling between base and derived classes
- Changes to base class ripple through all derived classes
- Difficult to reuse specific behaviors
- "Gorilla/Banana" problem: You wanted a banana but got a gorilla holding a banana

**Benefits of Composition**:
- Loose coupling
- Flexible behavior mixing
- Easier testing
- Better encapsulation

## Unity-Specific Patterns

### ❌ Inheritance Anti-Pattern
```csharp
public class Character : MonoBehaviour { }
public class Player : Character { }
public class Enemy : Character { }
public class Boss : Enemy { } // Deep hierarchy
```

### ✅ Composition Pattern
```csharp
public class CharacterController : MonoBehaviour
{
    [SerializeField] private Health health;
    [SerializeField] private Movement movement;
    [SerializeField] private Inventory inventory;
    [SerializeField] private AIBrain ai; // Null for player
}

public class Health : MonoBehaviour { }
public class Movement : MonoBehaviour { }
public class Inventory : MonoBehaviour { }
public class AIBrain : MonoBehaviour { }
```

## Component-Based Architecture

Unity's GameObject/Component system **naturally encourages** composition:

```csharp
// MapNode example
GameObject node;
node.AddComponent<MapNode>();           // Core behavior
node.AddComponent<MapNodeVisuals>();    // Visual representation
node.AddComponent<MapNodeInteraction>(); // Click handling
```

## Interface-Based Composition

Use interfaces to define capabilities:

```csharp
public interface IDamageable
{
    void TakeDamage(int amount);
    int CurrentHealth { get; }
}

public interface IInteractable
{
    void Interact(GameObject interactor);
    string GetInteractionPrompt();
}

// Character can implement multiple interfaces
public class CharacterController : MonoBehaviour, IDamageable, IInteractable
{
    // Implementation
}
```

## When Inheritance IS Appropriate

✅ Shallow hierarchies (1-2 levels)  
✅ Clear "is-a" relationships  
✅ Sharing common Unity lifecycle methods

Example:
```csharp
public abstract class BaseUIPanel : MonoBehaviour
{
    public abstract void Show();
    public abstract void Hide();
}

public class DialoguePanel : BaseUIPanel { }
public class InventoryPanel : BaseUIPanel { }
```

## Summary

- **Default to composition** - Use components and interfaces
- **Inheritance is not evil** - Use it for shallow hierarchies
- **Unity Components** - Leverage GameObject/Component architecture
- **Mix both** - Use inheritance for base behavior, composition for features
