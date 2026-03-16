---
name: project-code-map
description: Detailed map of project scripts, their roles, and interactions
metadata:
  tags: architecture, scripts, code-map, documentation
  source: docs/rules/rules_code_structure.md
---

# 项目脚本分析报告

**日期:** 2026-02-05 (最新更新)
**上次更新:** Code Review - UI & Map Data
**范围:** `Assets/Game/Scripts`

本报告详细说明了项目代码标准中各脚本的当前用途、功能和交互。

---

## 📂 核心层 (`Assets/Game/Scripts/Core`)

### `Interfaces/IInventoryService.cs`

* **目的**: 定义库存系统的契约（玩家道具/天赋检查）。
* **角色**: 解耦游戏系统（如地图）与具体的库存实现（EasySave/SQL）。
* **核心方法**:
  * `bool HasItem(string itemId)`
  * `bool HasTalent(string talentId)`
  * `void ConsumeItem(string itemId, int amount)`
* **交互**:
  * 由库存系统实现（待定）。
  * 由 `MapManager` 调用。

---

## 📂 数据层 (`Assets/Game/Scripts/Data`)

### `UIAssetDatabase.cs` 🟢 [NEW - 2026-02-05]

* **目的**: UI 资产的中央索引数据库，用于管理 AI 生成的 UI 切片。
* **角色**: 作为 UI 资产的"事实来源"(Single Source of Truth)，解耦 UI 逻辑与具体美术资源。
* **核心功能**:
  * **自动同步**: 扫描指定文件夹，自动发现并分类 Sprite 资源。
  * **智能分类**: 基于文件夹结构（优先）和文件名规则（次优先）自动判定类别（Button/Window/Icon/Border/Background/Misc）。
  * **CSV 导出**: 支持导出到 CSV 格式，便于团队协作和版本管理（包含自动备份机制）。
  * **Managed Text 生成**: 自动生成 Naninovel 多语言键值对，格式：`UI.{Category}.{AssetName}`。
  * **Odin Inspector 集成**:
    - `[Searchable]` 可搜索表格视图。
    - `[PreviewField]` Sprite 预览。
    - `[Button]` "应用到当前选择" 快捷操作。
  * **运行时接口**: 提供 `GetSprite(string)` 和 `GetSpritesByCategory(UIAssetCategory)` 方法。
* **架构特性**:
  * **Editor/Runtime 分离**:
    - 编辑器功能（同步、导出、按钮操作）包裹在 `#if UNITY_EDITOR` 中。
    - 运行时代码（查询方法、数据结构）保持轻量。
  * **UTF-8 BOM 编码**: CSV 导出使用 UTF-8 with BOM，确保 Excel 正确显示中文。
  * **备份机制**: 导出前自动创建带时间戳的备份文件（如 `UI_Assets_Backup_20260205_180000.csv`）。
* **交互**:
  * 被 `ChangeUISprite` (Naninovel Command) 调用以获取 Sprite。
  * 被 UI Manager 或其他运行时系统查询。
  * 导出的 CSV 可供策划在 Excel 中编辑，之后重新导入（未来扩展）。
* **命名规范**:
  * Sprite 命名：`UI_{Category}_{功能描述}_{变体}.png`
  * 示例：`UI_Btn_Confirm_Wasteland.png`（废土风格确认按钮）
* **注意事项**:
  * ScriptableObject 需放置于 `Assets/Resources/` 以便 Build 后 `ChangeUISprite` 能通过 `Resources.Load()` 加载。
  * 或使用 Addressables 标记该资产（推荐用于优化加载）。

### `MapGameState.cs`

* **位置**: `Assets/Game/Scripts/Data/MapGameState.cs`
* **命名空间**: `Game.Systems.Map` (⚠️ 注意：命名空间与文件夹 `Data` 不一致，不符合架构规范。建议未来重构时修正为 `Game.Core.Data`)
* **目的**: 实现 Naninovel 状态系统的简单包装类。
* **角色**: 作为游戏 `WorldMapSaveData` 与 Naninovel 存取系统之间的桥梁。
* **关键接口**: 继承自 `GameState` (Naninovel)。
* **交互**:
  * Naninovel 在游戏存取期间用于序列化/反序列化地图状态。
  * 持有对 `WorldMapSaveData` 的引用。

### `MapDatabase.cs`

* **目的**: 静态地图数据的核心 ScriptableObject 数据库（运行时只读）。
* **角色**: 存储节点、连接、区域、移动模式和地下城步骤列表。作为地图布局的“事实来源”。
* **核心组件**:
  * `TravelModeData`: 移动模式定义（步行、飞行等）。
  * `MapNodeEntry`: 增强型节点定义，包括 `InteractionType`、`EntryConditions`（道具/天赋）和 `EnvironmentTags`。
  * `MapConnectionEntry`: 节点间的连接定义。
  * `MapRegionData`: 区域定义（迷雾、解锁条件）。
  * `DungeonStepData`: 地下城流程定义。
* **交互**:
  * 由 `MapDataImporter` 填充。
  * 由 `MapManager` 读取以在运行时构建地图图表。

---

## 📂 编辑器 (`Assets/Game/Scripts/Editor`)

### `MapDataImporter.cs`

* **目的**: 将 CSV 文件中的地图数据导入到 `MapDatabase` ScriptableObject 的编辑器工具。
* **角色**: 工作流工具，允许策划在 Excel/CSV 中编辑地图、区域、移动模式和地下城，并导入到 Unity。
* **核心功能**:
  * **全面导入**: 读取 `Assets/Game/Config/Tables` 下的 5 个 CSV：
    * `Map_Nodes.csv`, `Map_Connections.csv`, `WorldMap_Regions.csv`, `Map_TravelModes.csv`, `Dungeon_Flows.csv`。
  * **双向导入 (Split-Brain)**:
    * **数据**: 将功能数据（ID、坐标、条件、连接）写入 `MapDatabase.asset`。
    * **本地化**: 提取显示文本（名称、描述）并写入 Naninovel 托管文本 (`MapDatabase.txt`)。
  * **复杂解析**: 处理 `EnvironmentTags`、`EntryConditions`（道具/天赋）和 `DungeonStep` 流程。
* **交互**:
  * 写入 `MapDatabase` (SO)。
  * 生成 `Assets/Game/Story/Resources/Naninovel/Text/MapDatabase.txt`。

### `AntigravityLogger.cs`

* **目的**: 捕获 Unity 编辑器报错并输出到本地文件的工具。
* **角色**: 开发辅助工具，将报错信息同步给 AI 助手。
* **核心功能**:
  * 监听 `Application.logMessageReceived`。
  * 自动将 Error/Exception 写入项目根目录的 `LastUnityError.txt`。

---

## 📂 剧情层 (`Assets/Game/Scripts/Story`)

> [!NOTE]
> `DialogueManager.cs` 和 `AdventureMapCommands.cs` 已于 2026-01-21 删除，其功能已整合到 `NaninovelBridge.cs`。

### `Commands/ChangeUISprite.cs` 🟢 [NEW - 2026-02-05]

* **目的**: Naninovel 自定义命令，用于在剧本中动态切换 UI Sprite。
* **角色**: 连接 Naninovel 脚本层与 Unity UI 系统的桥梁。
* **核心功能**:
  * **命令别名**: `@changeUI`
  * **参数**:
    - `target` (必需): 场景中 UI GameObject 的名称。
    - `sprite` (必需): `UIAssetDatabase` 中的 Sprite 资源名称。
    - `applyNativeSize` (可选): 是否应用原始尺寸（布尔值，默认 `false`）。
    - `database` (可选): 自定义 `UIAssetDatabase` 路径（默认自动查找）。
  * **自动查找机制**:
    - Editor 环境：使用 `AssetDatabase.FindAssets("t:UIAssetDatabase")` 自动定位。
    - Build 环境：通过 `Resources.Load<UIAssetDatabase>("UIAssetDatabase")` 加载。
  * **静态缓存**: 使用 `static cachedDatabase` 避免重复加载（性能优化）。
* **架构特性**:
  * **UniTask 集成**: 完全使用 `async UniTask` 替代 Coroutine（符合项目规范）。
  * **完整错误处理**: 覆盖数据库未找到、Sprite 不存在、GameObject 缺失、Image 组件缺失等场景。
  * **Editor/Build 兼容**: 使用 `#if UNITY_EDITOR` 区分编辑器和构建环境的资源加载策略。
* **用法示例**:
  ```nani
  ; 切换按钮样式（废土主题）
  @changeUI target:BtnConfirm sprite:UI_Btn_Confirm_Wasteland
  
  ; 切换面板并应用原始尺寸
  @changeUI target:MainPanel sprite:UI_Frame_Inventory applyNativeSize:true
  ```
* **交互**:
  * 查询 `UIAssetDatabase` 获取 Sprite。
  * 使用 `GameObject.Find()` 定位目标 UI GameObject。
  * 修改 `UnityEngine.UI.Image` 组件的 `sprite` 属性。
* **注意事项**:
  * 目标 GameObject 必须在场景的活动层级中（不支持未激活的对象）。
  * `GameObject.Find()` 仅搜索根级别和活动对象，建议 UI 命名唯一。
  * 未来可扩展支持 Transform 路径查找（如 `Canvas/Panel/Button`）以提高鲁棒性。

* **状态**: **已删除** (2026-01-25)。
* **原因**: 功能归于自动化。现在由 `MapManager` 自动监听剧情停止信号。

---

## 📂 玩法地图系统 (`Assets/Game/Scripts/Systems/Map`)

### 核心管理

#### `MapManager.cs` (核心系统)

* **目的**: 地图系统的核心逻辑控制器。单例。
* **角色**: 管理地图状态、玩家移动和判定条件。
* **核心功能**:
  * **架构**: 遵循 `世界 -> 区域 -> 节点` 层级结构。
  * **异步逻辑**: 使用 `UniTask` 处理线性的、非阻塞流程 (`TravelToNode`)。
  * `LoadRegion(string regionId)`: 设置当前上下文。
  * `TravelToNode(string nodeId)`: 验证连接性和**准入条件**。
  * **解耦逻辑**: 使用 `IInventoryService` 检查道具/天赋，避免直接依赖。
  * `HandleNodeArrival()`: 根据节点类型触发事件。将 `EnvironmentTags` 推送至 Naninovel。
* **交互**:
  * 读取 `MapDatabase`。
  * 控制 `MapViewController`。
  * 使用服务定位器或 `FindObject` 获取 `IInventoryService`。

#### `MapViewController.cs` (表现层)

* **目的**: 地图视觉呈现的总入口，协调各层渲染。
* **角色**: 视图层控制器。
* **核心功能**:
  * `RefreshView()`: 调度 `MapPathRenderer` 绘制矢量路径。
  * `MovePlayerVisual()`: 使用 `DOTween` 执行玩家位置同步。
  * **三层架构集成**: 统一处理地理坐标到世界坐标的比例转换。
* **交互**:
  * 拥有 `MapPathRenderer` 引用。
  * 监听 `MapManager` 事件。

#### `MapCameraController.cs`

* **目的**: 控制正交摄像机进行地图的平移和缩放。
* **角色**: 输入处理层，独立于地图逻辑。
* **核心功能**:
  * `HandlePan()`: 中键/左键拖拽平移，支持反转。
  * `HandleZoom()`: 滚轮缩放，限制 `MinZoom` / `MaxZoom`。
  * `ClampToBounds()`: 可选的边界限制。

### 拼贴系统 (Collage System) 🟢 [NEW]

> [!NOTE]
> 2026-01-29 架构变更：从“笔刷绘制”转向“区域拼贴”。旧系统 (InkDecoration) 已移除。

#### `MapTerrainController.cs`

* **目的**: 控制地图最底层的“宣纸”背景表现。
* **角色**: 背景管理器。
* **核心功能** (v2.0):
  * **单图/平铺**: 支持 `BackgroundDrawMode`，应对完整大图插画 (Simple) 或平铺纹理 (Tiled)。
  * **Mesh管理**: 简化的 Sprite 渲染，不再涉及复杂的高度图/墨迹遮罩。

#### `MapRegion.cs` (新增)

* **目的**: 定义地图上的独立区域图块。
* **角色**: 拼贴单元。
* **核心功能**:
  * **区域绑定**: 存储 `RegionID`，与逻辑层的区域对应。
  * **迷雾控制**: `SetLocked(bool)`，通过控制子物体的 `FogRenderer` 实现区域迷雾遮挡。

#### `MapPathRenderer.cs`

* **目的**: 实时渲染铁轨、运河等线性结构。
* **角色**: 视觉效果渲染器。
* **核心功能**:
  * **矢量渲染**: 利用 `Shapes` 库 (Polyline) 绘制连线。
  * **风格化**: 支持墨迹笔触或流光特效。

### 辅助逻辑

#### `MapInteractionFeedback.cs`

* **目的**: 处理单个地图物体的交互动效（如节点悬停高亮）。
* **核心功能**: `DOTween` 缩放，材质高亮。

#### `MapActionDatabase.cs`

* **目的**: 定义节点自交互动作（探索、对话、进入副本等）的 SO 数据库。

#### `WorldMapSaveData.cs`

* **目的**: 整个地图系统的可序列化数据类（解锁节点、当前位置）。

---

## 📂 玩法地下城系统 (`Assets/Game/Scripts/Systems/Dungeon`)

### `DungeonManager.cs`

* **目的**: 管理地下城探索（Expedition）模式的生命周期。单例。
* **角色**: 处理路径跳转、事件执行，以及在世界地图与地下城模式之间切换。
* **核心功能**:
  * `EnterDungeon(string dungeonId)`: 加载 `DungeonGraph` 并隐藏地图视图。
  * `MoveToStep(string stepId)`: 节点推进逻辑。
  * `ExecuteStep()`: 处理步骤类型（战斗、事件、首领）并运行 `NaninovelScriptName`。
* **交互**:
  * 通过 `Resources/Dungeons/` 加载。
  * 暂停 `MapManager`。

### `DungeonGraph.cs`

* **目的**: 定义特定地下城布局的 ScriptableObject（Rance 10 风格流程图）。
* **角色**: 步骤和连接的数据容器。
* **核心组件**:
  * `DungeonStep`: 包含类型、位置和后续步骤 ID。
* **交互**:
  * 在编辑器中创建，由 `DungeonManager` 消耗。

---

##  着色器层 (`Assets/Game/Shaders`) 🟢 [NEW]

本次重构核心新增的一组 Shader，用于支持“拼贴 (Collage)”美术风格。之前的 `InkWashTerrain` 已废弃。

### 1. `ColorRegionTransparent` (核心)

* **用途**: 区域图 (Region Viewer)。
* **特性**:
  * **Chroma Key**: 支持吸管吸取背景色并剔除 (Threshold/Softness)，支持 URP Sprite。
  * **Paper Blend**: 支持与底图的正片叠底混合，模拟“画在纸上”。

### 2. `InkWashWater`

* **用途**: 水面装饰。
* **特性**: 模拟深色墨水，带有动态流动噪点和高光闪烁 (Sparkle)。

### 3. `InkWashFog`

* **用途**: 区域遮罩 (Fog of War)。
* **特性**: 流动的云雾噪点，用于遮盖未解锁区域。

---

##  更新与近期重构 (的历史记录)
>(History content is preserved)
