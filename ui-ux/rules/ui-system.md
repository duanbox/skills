---
name: ui-system
description: UI system design, asset management, and Naninovel integration
metadata:
  tags: ui, visual-novel, naninovel, assets
  source: docs/rules/rules_ui.md
---

# UI 系统设计规范

## 一、核心原则

### 1. 数据与逻辑分离
```
[Data Layer]          UIAssetDatabase.asset（中央资产库）
       ↓
[Service Layer]       UIManager.cs / Naninovel Commands
       ↓
[View Layer]          Unity UI / Naninovel UI
```

- **禁止**：在 MonoBehaviour 中硬编码 Sprite 引用
- **推荐**：所有 UI 资产通过 `UIAssetDatabase` 索引

### 2. AI 生成素材工作流

#### 命名规范
```
UI_{Category}_{功能描述}_{变体}.png

示例：
UI_Btn_Confirm_Wasteland.png     # 废土风格确认按钮
UI_Frame_Inventory_Neon.png      # 霓虹风格背包框架
UI_Icon_Item_SpiritOrb.png       # 灵珠道具图标
```

#### 文件夹结构
```
Assets/Art/UI/Slices/
├── Buttons/           # 按钮
├── Windows/           # 窗口/面板
├── Icons/             # 图标
├── Borders/           # 边框
└── Backgrounds/       # 背景
```

### 3. 自动化同步流程

1. **AI 生成切片** → 放入 `Assets/Art/UI/Slices/` 对应文件夹
2. **打开 UIAssetDatabase** → 点击"同步 AI 生成的 UI 资产"
3. **自动分类** → 基于文件夹和文件名智能分类
4. **导出 CSV** → （可选）用于版本管理和团队协作
5. **生成 Managed Text Keys** → 用于 Naninovel 多语言支持

---

## 二、UIAssetDatabase 使用指南

### 创建资产库
```
1. Project 窗口右键 → Create → Game/Data/UI Asset Database
2. 命名为 UIAssetDatabase.asset
3. 配置路径：
   - UI 切片路径: Assets/Art/UI/Slices
   - CSV 导出路径: Assets/Game/Config/Tables
   - Managed Text 路径: Assets/Game/Story/Resources/Naninovel/Text
```

### 同步工作流
```
[AI 生成 20 张新切图]
         ↓
[拖入对应文件夹]
         ↓
[UIAssetDatabase → 点击"同步"]
         ↓
[自动分类、生成预览、排序]
         ↓
[（可选）导出 CSV / 生成 Managed Text]
```

### 快速查找与应用
```
1. 在 Inspector 的搜索框输入关键词（如 "Btn"）
2. 找到目标 Sprite
3. 在 Hierarchy 选中 UI GameObject
4. 点击 "应用到当前选择" 按钮
5. 完成（自动调整为原始尺寸）
```

---

## 三、Naninovel 集成

### 命令用法
```nani
; 基础用法：替换 UI 精灵
@changeUI target:ButtonConfirm sprite:UI_Btn_Confirm_Wasteland

; 替换并应用原始尺寸
@changeUI target:MainPanel sprite:UI_Frame_Inventory applyNativeSize:true

; 场景示例：进入废土区域，UI 切换主题
@changeUI target:TopBar sprite:UI_Frame_TopBar_Wasteland
@changeUI target:BtnInventory sprite:UI_Btn_Inventory_Wasteland
@changeUI target:BtnMap sprite:UI_Btn_Map_Wasteland
```

### 动态主题切换示例
```nani
; 定义一个宏来批量切换主题
@macro theme:wasteland
    @changeUI target:TopBar sprite:UI_Frame_TopBar_Wasteland
    @changeUI target:BottomBar sprite:UI_Frame_BottomBar_Wasteland
    @changeUI target:BtnConfirm sprite:UI_Btn_Confirm_Wasteland
@endmacro

; 使用宏
@call theme:wasteland

; 切换到霓虹主题
@call theme:neon
```

---

## 四、代码层面的使用

### C# 获取 Sprite
```csharp
using Game.Core.Data;

public class InventoryUI : MonoBehaviour
{
    [SerializeField] private UIAssetDatabase uiDatabase;
    [SerializeField] private Image itemIconImage;

    public void DisplayItem(string itemId)
    {
        // 从数据库获取 Sprite
        Sprite itemSprite = uiDatabase.GetSprite($"UI_Icon_Item_{itemId}");
        
        if (itemSprite != null)
        {
            itemIconImage.sprite = itemSprite;
        }
    }
}
```

### 按类别批量加载
```csharp
// 获取所有按钮 Sprite
List<Sprite> allButtons = uiDatabase.GetSpritesByCategory(UIAssetCategory.Button);

// 根据主题过滤
var wastelandButtons = allButtons
    .Where(s => s.name.Contains("Wasteland"))
    .ToList();
```

---

## 五、CSV 导出与版本管理

### CSV 文件格式
```csv
AssetName,Category,AssetPath,Tags,AddressablePath
UI_Btn_Confirm,Button,Assets/Art/UI/Slices/Buttons/UI_Btn_Confirm.png,primary;action,UI/Buttons/Confirm
UI_Frame_Inventory,Window,Assets/Art/UI/Slices/Windows/UI_Frame_Inventory.png,inventory;main,UI/Windows/Inventory
```

### 用途
1. **团队协作**：CSV 易于 Git Diff，减少 Merge Conflict
2. **数据驱动**：策划可以在 Excel 中编辑 Tags、Addressable 路径
3. **批量操作**：通过脚本批量修改资产配置

### 导入流程（未来扩展）
```csharp
// 类似 MapDataImporter 的逻辑
[Button("从 CSV 导入")]
private void ImportFromCSV()
{
    // 读取 CSV
    // 更新 AssetList
    // 同步到 UIAssetDatabase
}
```

---

## 六、Managed Text Keys 生成

### 生成规则
```
UI.{Category}.{AssetName}: {Human-Readable Name}

示例：
UI.Button.UI_Btn_Confirm: UI Btn Confirm
UI.Window.UI_Frame_Inventory: UI Frame Inventory
UI.Icon.UI_Icon_Item_SpiritOrb: UI Icon Item Spirit Orb
```

### 用途
1. **本地化支持**：方便后期添加多语言描述
2. **工具提示**：在 UI Hover 时显示说明文字
3. **调试信息**：记录 UI 资产使用情况

---

## 七、Shapes 插件的高级应用

### 用途：水墨废土的"道化科技"视觉效果

#### 1. UI 连线（Polyline）
```csharp
using Shapes;
using DG.Tweening;

public class UITechLine : ImmediateModeShapeDrawer
{
    [SerializeField] private float lineThickness = 2f;
    [SerializeField] private Color neonColor = new Color(0.3f, 0.8f, 1f);
    [SerializeField] private float noiseStrength = 0.5f;

    public override void DrawShapes(Camera cam)
    {
        using (Draw.Command(cam))
        {
            Draw.LineThickness = lineThickness;
            Draw.Color = neonColor;

            // 绘制带噪声的连线（模拟水墨笔触）
            Vector3[] points = GetLinePoints();
            for (int i = 0; i < points.Length - 1; i++)
            {
                Vector3 jitter = Random.insideUnitSphere * noiseStrength;
                Draw.Line(points[i] + jitter, points[i + 1] + jitter);
            }
        }
    }
}
```

#### 2. 灵力流动动画（DOTween）
```csharp
public class SpiritFlowEffect : MonoBehaviour
{
    [SerializeField] private Image energyParticle;
    [SerializeField] private Vector3[] pathPoints;
    
    private void Start()
    {
        AnimateFlow();
    }

    private void AnimateFlow()
    {
        // 沿路径移动粒子
        energyParticle.transform.DOPath(pathPoints, 2f, PathType.CatmullRom)
            .SetEase(Ease.Linear)
            .SetLoops(-1); // 无限循环

        // 脉冲发光效果
        energyParticle.DOFade(0.3f, 0.5f)
            .SetLoops(-1, LoopType.Yoyo);
    }
}
```

#### 3. 示例场景：废土 UI + 霓虹线缆
```
[UI_Frame_Inventory]（水墨边框）
         ↓ 
    UITechLine（霓虹连线，带噪声抖动）
         ↓
    SpiritFlowEffect（灵力粒子流动）
         ↓
[UI_Icon_SpiritOrb]（灵珠图标）
```

---

## 八、性能优化建议

### 1. Addressables 延迟加载
```csharp
// 不要在 Awake/Start 加载所有 UI 资产
// 改用 Addressables 按需加载

public async UniTask<Sprite> LoadUIAssetAsync(string assetName)
{
    var handle = Addressables.LoadAssetAsync<Sprite>($"UI/{assetName}");
    return await handle.Task;
}
```

### 2. 对象池（UI 复用）
```csharp
// 对于频繁创建/销毁的 UI（如道具图标）
// 使用对象池而不是 Instantiate/Destroy

ObjectPool<ItemIconUI> iconPool = new ObjectPool<ItemIconUI>(
    createFunc: () => Instantiate(iconPrefab),
    actionOnGet: (icon) => icon.gameObject.SetActive(true),
    actionOnRelease: (icon) => icon.gameObject.SetActive(false)
);
```

### 3. 图集打包（Sprite Atlas）
```
将同主题的 UI 切片打包到同一个 Atlas：
- Wasteland_UI_Atlas.spriteatlas
- Neon_UI_Atlas.spriteatlas

减少 Draw Call，提升渲染性能
```

---

## 九、团队协作规范

### Git 工作流
```
1. UI 美术：只修改 Assets/Art/UI/Slices/ 下的图片
2. 程序：
   - 同步 UIAssetDatabase.asset
   - 导出 CSV 到 Git
   - 不直接修改 Scene 中的 UI Sprite 引用
3. 策划：
   - 在 Excel 编辑 UI_Assets.csv
   - 提交 CSV，程序导入并同步
```

### Merge Conflict 处理
```
[Conflict] UIAssetDatabase.asset
解决方案：
1. 保留你的版本
2. 重新执行"同步 AI 生成的 UI 资产"
3. 导出 CSV，对比差异
4. 手动合并 Tags/Addressable 路径
```

---

## 十、故障排查

### 问题 1：同步后找不到 Sprite
**原因**：路径配置错误，或 Sprite 不在指定文件夹。  
**解决**：检查 `uiSpritesPath` 配置，确保路径正确。

### 问题 2：Naninovel 命令报错 "Sprite not found"
**原因**：拼写错误，或 Sprite 名称不匹配。  
**解决**：在 UIAssetDatabase Inspector 搜索框验证名称。

### 问题 3：Apply to Selected 无效
**原因**：未选中 GameObject，或 GameObject 没有 Image 组件。  
**解决**：先在 Hierarchy 选中目标 UI，确保有 Image 组件。

### 问题 4：Build 后 ChangeUISprite 失败
**原因**：UIAssetDatabase 不在 Resources 文件夹。  
**解决**：将 UIAssetDatabase.asset 放到 `Assets/Resources/`，或使用 Addressables。

---

## 十一、未来扩展方向

1. **主题系统**：支持一键切换整套 UI 主题（废土/霓虑/古典）
2. **动态生成**：根据 AI Prompt 实时生成 UI 样式
3. **AB 测试**：随机切换 UI 样式，收集玩家偏好数据
4. **Mod 支持**：允许玩家自定义 UI 资产

---

**记住**：UI 数据化的核心是**让策划和美术独立工作，程序只维护数据结构和逻辑，减少沟通成本和 Merge Conflict**。
