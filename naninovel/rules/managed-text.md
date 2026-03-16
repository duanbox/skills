---
name: managed-text
description: Using Naninovel Managed Text for localization (NO I2 Localization)
metadata:
  tags: localization, managed-text, i18n, translation
---

# Naninovel Managed Text - The Only Localization System

**CRITICAL RULE**: ALL localization (English/Chinese) MUST use **Naninovel Managed Text**. DO NOT use I2 Localization.

## Why?

**"Split Brain" Problem**: Using both I2 Localization and Naninovel Managed Text creates:
- Duplicate translation workflows
- Inconsistent language switching
- Maintenance hell (2 systems to update)
- Potential synchronization bugs

## Architecture

```
Story Text   → Naninovel Auto-generated
UI Text      → Naninovel Managed Text
Data +Odin   → Naninovel Managed Text
```

## Usage in Code

### Step 1: Register Managed Text Keys

Create managed text records in Naninovel:
1. Open **Naninovel → Resources → Managed Text**
2. Add new records with keys like `Item_IronCaltrop`, `UI_Inventory_Title`
3. Provide translations for each language

### Step 2: Reference in C#

```csharp
using Naninovel;

namespace Game.Systems
{
    public class InventoryUI : MonoBehaviour
    {
        private ITextManager _textManager;

        private void Start()
        {
            _textManager = Engine.GetService<ITextManager>();
            
            // Retrieve localized text
            string title = _textManager.GetRecordValue("UI_Inventory_Title");
            titleText.text = title;
        }
    }
}
```

### Step 3: Use in Odin ScriptableObjects

```csharp
using Sirenix.OdinInspector;
using Naninovel;

[CreateAssetMenu(menuName = "Game/Item")]
public class ItemDefinition : ScriptableObject
{
    [Title("Localization")]
    [Tooltip("Managed Text key for item name, e.g., 'Item_IronCaltrop'")]
    public string NameKey;
    
    [Tooltip("Managed Text key for item description")]
    public string DescriptionKey;
    
    // Runtime accessor
    public string GetLocalizedName()
    {
        var textManager = Engine.GetService<ITextManager>();
        return textManager?.GetRecordValue(NameKey) ?? NameKey;
    }
}
```

## Naming Conventions

### UI Text
```
UI_[ScreenName]_[Element]
Examples:
- UI_Inventory_Title
- UI_Map_NodeSelected
- UI_Combat_AttackButton
```

### Data Text
```
[Category]_[ItemName]_[Field]
Examples:
- Item_IronCaltrop_Name
- Item_IronCaltrop_Desc
- Character_Nuwa_Name
- Skill_FlameStrike_Name
```

### System Messages
```
System_[Context]_[Message]
Examples:
- System_Save_Success
- System_Error_InvalidData
```

## Best Practices

1. **Store Keys, Not Text**: Store the **key** in your ScriptableObjects, retrieve value at runtime
2. **Consistent Naming**: Use the naming conventions above
3. **Null Safety**: Always check if `ITextManager` is available
4. **Preview in Editor**: Use Odin's `[ShowInInspector]` to display localized preview

## Example: Complete Item System

```csharp
[CreateAssetMenu(menuName = "Game/Item")]
public class ItemDefinition : SerializedScriptableObject
{
    [Title("Identity")]
    public string ItemID;
    
    [Title("Localization")]
    [InfoBox("Keys reference Naninovel Managed Text")]
    public string NameKey;
    public string DescriptionKey;
    
    [Title("Preview (Runtime Only)")]
    [ShowInInspector, ReadOnly, HideInEditorMode]
    private string LocalizedName => GetLocalizedName();
    
    [ShowInInspector, ReadOnly, HideInEditorMode]
    private string LocalizedDesc => GetLocalizedDescription();
    
    public string GetLocalizedName()
    {
        var textManager = Engine.GetService<ITextManager>();
        return textManager?.GetRecordValue(NameKey) ?? $"[Missing: {NameKey}]";
    }
    
    public string GetLocalizedDescription()
    {
        var textManager = Engine.GetService<ITextManager>();
        return textManager?.GetRecordValue(DescriptionKey) ?? $"[Missing: {DescriptionKey}]";
    }
}
```

## Language Switching

Language changes are handled **automatically** by Naninovel:

```csharp
// Switch language
var localizationManager = Engine.GetService<ILocalizationManager>();
await localizationManager.SelectLocaleAsync("zh-CN"); // or "en-US"

// All ITextManager.GetRecordValue() calls will now return Chinese text
```

---

## 官方文档新增特性（2026-02-19 校对）

### ManagedTextProvider 组件（无需代码绑定）

在 GameObject 上挂载 `ManagedTextProvider` 组件，无需写 C# 代码即可绑定文本：

- **Category**：文档名（如 `DefaultUI`）
- **Key**：记录名
- **OnValueChanged** 事件：连接 `TMP_Text.SetText` 等 UI 属性

运行 Managed Text 工具时会自动生成对应记录。

### [ManagedText] Attribute（静态字段绑定）

```csharp
// 静态字段自动从 Managed Text 文档初始化值，并在语言切换时更新
[ManagedText("MyCategory")]
public static string ButtonLabel = "默认按钮文字";

// 引擎初始化后，ButtonLabel 的值来自 "MyCategory" 文档中 "ButtonLabel" 条目
```

### 在 .nani 脚本中引用 Managed Text

使用 `T_` 前缀的键在脚本中引用 Managed Text 记录（文档名为 "Script"）：

```nani
; 在脚本中引用多个本地化文本，随机选一个
@print {Random(T_Greeting1,T_Greeting2,T_Greeting3)}

; 单独引用
@print {T_WelcomeMessage}
```

配置：
1. 创建名为 `Script` 的 Managed Text 文档
2. 添加 `T_Greeting1`、`T_Greeting2` 等键
3. 切换语言时自动拉取对应翻译

### 完整 ITextManager 编程访问 API

```csharp
var manager = Engine.GetService<ITextManager>();

// 1. 确保文档已加载（初始化后内置文档已自动加载）
await manager.DocumentLoader.Load("CustomCategory");

// 2. 获取文档
var document = manager.GetDocument("CustomCategory");

// 3. 获取记录
var record = document.Get("MyKey");
string value = record.Value;  // 当前语言下的文本值

// 注意：内置文档（DefaultUI 等）无需手动 Load
```

> **与现有项目代码的关系**：现有代码中的 `textManager.GetRecordValue(key)`
> 是项目的封装方法，底层使用上述 API。新代码建议直接使用标准 API。

---

## Summary

- ✅ **Use Naninovel Managed Text** for ALL localization
- ❌ **Never use I2 Localization** - creates split-brain problem
- 🔑 **Store keys** in ScriptableObjects, retrieve at runtime
- 📝 **Follow naming conventions** for consistency
- 🆕 **ManagedTextProvider** 组件：Inspector 中直接绑定，无需 C# 代码
- 🆕 **[ManagedText] attribute**：静态字段自动绑定
- 🆕 **`T_` 前缀**：在 `.nani` 脚本中直接引用本地化文本
