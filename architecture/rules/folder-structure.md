---
name: folder-structure
description: Project directory structure and file organization standards
metadata:
  tags: architecture, structure, organization, assets
  source: docs/rules/rules_structure.md
---

# Project Structure Standard (项目目录规范)

**项目名称**: EndGods
**适用版本**: Naninovel 1.20+ (UPM), Unity 2022+  
**最后更新**: 2026-02-05

---

## 1. 核心设计哲学 (Core Philosophy)

*   **物理隔离 (Isolation)**: `Assets/Game` 是唯一的开发目录。根目录下的其他文件夹属于"基础设施"，严禁存放业务逻辑。
*   **无损升级 (Safe Upgrade)**: Naninovel 和插件均通过 UPM 或 `Plugins` 目录管理。升级插件绝不会覆盖项目文件。
*   **地址化加载 (Addressables First)**: 既然是修仙+废土风格（大贴图），所有美术资源默认走 Addressables，严禁随意放入 `Resources`。
*   **数据驱动 (Data Driven)**: 逻辑代码 (Scripts) 不存储数据，数据存储在 Config (SO/CSV) 中。

---

## 2. 根目录结构 (Assets Root)

保持根目录极度精简，只允许存在以下文件夹：

| 📁 文件夹                  | 说明              | ⚠️ 操作规范                                    |
| :------------------------ | :---------------- | :-------------------------------------------- |
| **Game**                  | [核心] 开发主目录 | 所有代码、美术、剧本、配置的唯一归宿。        |
| **Plugins**               | 第三方插件库      | 存放 Odin, EasySave3, DOTween 等非 UPM 插件。 |
| **AddressableAssetsData** | 地址系统数据      | 系统自动生成，请勿手动修改结构。              |
| **NaninovelData**         | 引擎缓存数据      | 系统自动生成，存放资源引用缓存，请勿删除。    |
| **StreamingAssets**       | 流媒体资源        | 仅存放 Naninovel 的背景视频文件。             |

---

## 3. Game 目录详解 (Assets/Game)

这是日常工作的核心区域。

### 📂 Art (美术资源)
**策略**: 全面 Addressable 化。按"生命周期"或"章节"分组。
*   `Backgrounds/` (背景图资源)
*   `Characters/` (角色立绘 - 含差分和表情)
*   `CG/` (关键事件插画)
*   `Map/` (地图相关美术资源：节点图标、区域拼贴、装饰物)
*   `Materials/` (材质资源)
*   `UI/` (UI 界面资源：按钮、边框、图标等 AI 生成切片)
    *   `Slices/` (AI 生成的 UI 切片，按类别分文件夹)
        *   `Buttons/` (按钮)
        *   `Windows/` (窗口/面板)
        *   `Icons/` (图标)
        *   `Borders/` (边框)
*   `VFX/` (特效预制体和资源)

### 📂 Audio (音频资源)
*   `BGM/`
*   `SFX/`
*   `Voice/`

### 📂 Config (数据配置中心)
**策略**: Odin Inspector + CSV 工作流。
*   `Databases/` (存放 ScriptableObject 实例，运行时读取)
    *   `ItemDatabase.asset`
    *   `SkillDatabase.asset`
*   `Tables/` (存放 .csv 原始表格，通过 Editor 脚本导入)
*   `Settings/` (全局游戏设置，如 `GameConfig.asset`)

### 📂 Scripts (C# 源码)
**策略**: 命名空间严格对应文件夹，如 `Game.Systems.Map`。
*   `Core/` (架构核心：接口定义、服务定位器)
    *   `Interfaces/` (核心接口：如 `IInventoryService`)
*   `Data/` (数据定义：ScriptableObject 数据类)
    *   `MapDatabase.cs` (地图数据库)
    *   `UIAssetDatabase.cs` 🆕 (UI 资产数据库)
    *   `GameStateMap.cs` (地图状态包装)
*   `Editor/` (编辑器扩展工具)
    *   `MapDataImporter.cs` (CSV 导入/导出工具)
    *   `AntigravityLogger.cs` (错误日志捕获)
*   `Story/` (Naninovel 扩展)
    *   `Commands/` (自定义命令)
        *   `EnterDungeon.cs` (进入地下城命令)
        *   `ChangeUISprite.cs` 🆕 (动态切换 UI 命令)
    *   `Services/` (自定义服务)
*   `Systems/` (纯 C# 游戏逻辑)
    *   `Dungeon/` (地下城探索系统)
    *   `Map/` (世界地图系统)
        *   `MapManager.cs` (核心管理器)
        *   `MapViewController.cs` (视图控制器)
        *   `MapCameraController.cs` (摄像机控制)
        *   `MapRegion.cs` (区域拼贴)
    *   `Naninovel/` (Naninovel 集成桥接)
*   `Tools/` (通用工具类)
*   `UI/` (UI 视图层逻辑)

### 📂 Story (剧本与引擎配置)
**策略**: 这里的 Resources 是为了覆盖 Naninovel 默认配置。
*   `Scenarios/` (存放 .nani 剧本文件)
    *   `Chapter1/Main.nani`
*   `CustomUI/` (自定义的 Naninovel UI 预制体)
*   `Resources/` (⚠️ 特殊目录：用于接管配置)
    *   `Naninovel/Configuration/` (在此处存放生成的 `Configuration.asset`)
    *   `Text/` (Managed Text 翻译文件)
    *   `Localization/` (多语言配置)

### 📂 Prefabs (游戏对象)
*   `Systems/` (单例管理器)
*   `UI/` (非 AVG 界面)

### 📂 Scenes (场景文件)
*   `Boot.unity` (启动场景)
*   `MainGame.unity` (核心场景)

### 📂 Shaders (着色器) 🆕
**策略**: 支持水墨废土风格的自定义 Shader。
*   `ColorRegionTransparent.shader` (区域图 Chroma Key 去底)
*   `InkWashWater.shader` (水墨水面效果)
*   `InkWashFog.shader` (战争迷雾效果)
*   `UniversalNaninovel.shader` (Naninovel URP 兼容 Shader)

---

## 4. 关键工作流指南 (Workflows)

### 🟢 1. 如何修改 Naninovel 配置？
不要在 Project Settings 里改完就跑。
1. **位置**: 找到 `Assets/Game/Story/Resources/Naninovel/Configuration` 文件。
2. **操作**: 在 Inspector 窗口中直接修改该文件，Project Settings 会自动同步。
3. **目的**: 确保配置文件被 Git 版本控制，且不随插件更新丢失。

### 🟢 2. 如何添加新的美术资源？
1. 将图片导入 `Assets/Game/Art/...` 对应文件夹。
2. 在 Inspector 中勾选 **Addressable**。
3. 修改 Addressable Name 为简短名称 (例如: `XiHe/Angry`)。
4. 在 Addressables Groups 窗口中，将其归类到对应分组 (如 `Nani_Characters`)。

### 🟢 3. 如何添加新物品？
1. 在 `Assets/Game/Config/Tables` 中编辑 CSV。
2. 运行导入工具 (**Tools -> Import CSV**)。
3. 检查 `Assets/Game/Config/Databases` 下的 SO 是否更新。

### 🟢 4. 剧本路径引用
Naninovel 的 Script Loader 应配置为：
*   **Method**: Project (1.20 推荐) 或 Addressable。
*   **Path**: 指向 `Assets/Game/Story/Scenarios`。

---

## 5. 项目文档结构 (Docs Structure)

项目所有文档均存放在 `Docs/` 根目录下，严禁散落在 Desktop 或其他临时文件夹。

| 📁 文件夹        | 说明                                                                                                                           |
| :-------------- | :----------------------------------------------------------------------------------------------------------------------------- |
| **background**  | **世界观与设定**<br>含世界观(`worldview.md`)、种族(`race.md`)、地图(`map.md`)、美术风格(`artstyle.md`)、角色(`character.md`)等 |
| **pipeline**    | **制作管线规范**<br>含特效制作流程等工作流文档                                                                                 |
| **todo**        | **待办与计划**<br>短期任务、系统设计草案 (`combat_system_design.md`、`mapsystemplan.md`等)                                     |
| **walkthrough** | **功能演示文档** 🆕<br>各系统实现完成后的使用说明（如 `ui_system.md`）                                                          |
| **knowledge**   | **知识库**<br>沉淀下来的长期有效知识（跨版本通用的技术文档）                                                                   |

> [!NOTE]
> 原 `Program/` 文件夹已移除，程序架构文档现存放于 `docs/rules/rules_code_structure.md`。
