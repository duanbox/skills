---
name: background-system
description: Architecture and workflow for Naninovel backgrounds and resource providers
metadata:
  tags: naninovel, backgrounds, resources, addressables
  source: docs/rules/rule_nani_bg.md
---

# Naninovel 引擎背景子系统架构深度解析与全流程操作手册

## 1. 绪论：Naninovel 背景系统的设计哲学与架构定位

在现代视觉小说（Visual Novel）与互动叙事游戏的开发管线中，背景（Backgrounds）系统不仅承担着营造环境氛围的视觉职能，更是场景状态管理、渲染层级控制以及资源动态调度的核心枢纽。Naninovel 引擎通过一套高度抽象化的 `IActor` 接口体系，将背景定义为具备独立生命周期、可编程行为与多态表现形式的实体。

本报告旨在针对 Naninovel 背景子系统进行详尽的解构分析，涵盖从底层的资源寻址策略到上层的脚本编排逻辑。特别针对“多源资源分布（Distributed Resource Allocation）”与“多演员协同（Multi-Actor Orchestration）”这两个高阶开发需求，提供系统性的解决方案。本手册整合了官方文档文本资料与教学视频的核心逻辑，旨在为技术美术（TA）与游戏逻辑程序员提供一份可以直接落地的操作指南。

### 1.1 背景（Background）与角色（Character）的架构异同

在深入具体操作之前，必须理解 Naninovel 中背景的架构本质。虽然背景与角色均继承自基础的 `IActor` 接口，共享诸如位置（Position）、旋转（Rotation）、缩放（Scale）、色调（Tint）与可见性（Visibility）等基础属性，但在渲染管线与默认行为上存在显著差异：

* **单例与多实例的辩证关系**：在传统的 AVG 叙事语法中，场景通常由单一的主背景主导。因此，Naninovel 在 API 设计上对背景进行了特殊优化——当脚本命令 `@back` 未指定 ID 时，系统默认操作名为 `MainBackground` 的单例演员。这种设计极大地简化了线性叙事的脚本编写。然而，引擎底层完全支持多背景实例并存，这为实现视差滚动（Parallax）、动态天气图层叠加以及前后景深构建提供了架构基础。
* **Z轴深度排序（Z-Ordering）的自动化**：背景默认位于场景的最深处（即 Z 轴正向最大值），以确保其渲染层级始终位于角色立绘、文本打印机与 UI 界面之后。Naninovel 引入了 Z Offset（基础深度偏移）与 Z Step（层级步进）参数，通过算法自动管理多个背景演员之间的遮挡关系，从根本上解决了 Z-fighting（深度冲突）导致的纹理闪烁问题。
* **资源生命周期的特殊性**：相比于角色立绘，背景资源（尤其是 4K 分辨率纹理或高清视频）对显存（VRAM）与带宽的占用更为显著。因此，背景系统在资源加载（Loading）、预加载（Preloading）与卸载（Unloading）策略上，需要与底层的资源提供者（Resource Providers）进行更紧密的配合，以平衡内存开销与 I/O 延迟。

---

## 2. 资源配置策略：实现“多处存放”的架构解析

用户提出的核心痛点之一在于：“如何搞明白我把资源放到多个地方……应该怎么用脚本配合？”。要回答这个问题，必须透过现象看本质，解析 Naninovel 的 **资源提供者抽象层（Resource Provider Abstraction Layer）**。

在 Naninovel 的架构中，脚本层（Script Layer）与资源物理层（Physical Asset Layer）是完全解耦的。脚本中的命令 `@back Beach` 仅仅是发出一个“请求”，而“去哪里找这个资源”完全由资源提供者的配置链决定。

### 2.1 资源提供者体系（Resource Providers System）

Naninovel 不直接依赖文件系统的绝对路径，而是通过一系列注册的“提供者”来解析资源的运行时地址。这种机制允许开发者将资源分散存储在不同的物理介质中（如本地包体、DLC、远程服务器），而无需修改任何一行业务脚本。

#### 2.1.1 核心提供者类型及其应用场景

| 提供者类型 | 对应 Unity 机制 | 物理位置 | 适用场景 | 优缺点分析 |
| :--- | :--- | :--- | :--- | :--- |
| **Project** | Resources Folder | 随包体发布 (assets/bin) | 核心 UI、加载界面、极少量通用背景 | **优点**：同步加载，无配置成本。<br>**缺点**：增加包体大小，启动慢，不可热更。 |
| **Addressable** | Addressable Assets | 本地构建包 或 远程 CDN | 主线剧情背景、高清图集、视频资源 | **优点**：异步加载，内存管理优秀（引用计数），支持热更与分包。<br>**缺点**：需预先构建 (Build)。 |
| **Local** | System.IO | 用户设备文件系统 | 社区模组 (Modding)、用户自定义头像 | **优点**：允许玩家运行时导入资源。<br>**缺点**：加载慢（需运行时转换），Web 平台受限。 |

#### 2.1.2 资源查找链（The Lookup Chain）

当脚本执行 `@back Beach id:MainBackground` 时，引擎内部的资源加载器（Loader）会按照以下逻辑遍历提供者列表：

1. **编辑器模式（Editor Mode）特权**：为了加速开发迭代，在 Unity 编辑器环境下，系统总是优先使用特殊的 `EditorResourceProvider`。它直接通过 `Naninovel -> Resources -> Backgrounds` 菜单中引用的资产数据库（AssetDatabase）来加载资源，完全忽略物理路径。这就是为什么开发者在编辑器里随便放资源都能跑，但打包后可能丢失的原因。
2. **构建环境（Runtime Build）逻辑**：
    * 引擎会读取 `Configuration -> Resource Provider` 中启用的提供者列表。
    * **链式回退（Chain of Responsibility）**：假设同时启用了 Addressable 和 Project 提供者。引擎首先向 Addressable 系统请求键值为 `Naninovel/Backgrounds/MainBackground/Beach` 的资源。如果 Addressable 系统返回“Key Not Found”，引擎会捕获该异常，并自动降级尝试向 Project 提供者（即 Resources 文件夹）请求 `Backgrounds/MainBackground/Beach`。

### 2.2 “多处存放”的具体实现方案

基于上述架构，我们可以设计出灵活的混合资源部署方案，满足用户“把资源放到多个地方”的需求。

#### 方案 A：基于 Addressable 的分组管理（推荐方案）

这是最现代化、最符合 Unity 性能优化标准的做法。通过 Addressable Group，你可以将资源物理上隔离（有的在本地包，有的在云端），但在逻辑上统一。

1. **环境配置**：
    * 安装 Unity Addressables Package。
    * 在 `Naninovel -> Configuration -> Resource Provider` 中，勾选 `Use Addressables`。
2. **物理分组（Grouping）**：
    * 打开 `Window -> Asset Management -> Addressables -> Groups`。
    * 创建 `Group_Local`：Build Path 设为 LocalBuildPath，Load Path 设为 LocalLoadPath。将基础背景拖入此组。
    * 创建 `Group_Remote_Chapter2`：Build Path 设为 RemoteBuildPath，Load Path 设为 RemoteLoadPath（指向 AWS S3 或阿里云 OSS）。将第二章的高清大图拖入此组。
3. **逻辑统一（Addressing）**：
    * 无论资源在哪个组，你必须遵循 Naninovel 的地址命名公约。
    * **手动修改 Addressable Name 为**：`Naninovel/Backgrounds/{ActorID}/{AppearanceName}`。
    * **关键步骤**：必须给这些资源添加一个名为 `Naninovel` 的 Label。
4. **脚本无感调用**：
    * 脚本中只需写 `@back AppearanceName`。引擎会通过地址系统自动定位，开发者无需关心它是从本地磁盘读取还是从网络下载。

#### 方案 B：利用路径前缀（Path Prefix）与多重 Resources 文件夹

Unity 允许在项目中存在多个名为 `Resources` 的文件夹。

* **操作**：你可以在 `Assets/Game/Core/Resources/Backgrounds` 放一部分图，在 `Assets/Game/DLC/Resources/Backgrounds` 放另一部分。
* **原理**：在构建时，Unity 会将所有 Resources 文件夹的内容合并生成索引。Naninovel 通过 `Resources.Load` 调用时，自然能找到所有内容。这种方法简单，但不支持热更新。

### 2.3 资源内存管理与性能优化

当资源分散时，内存管理变得至关重要。Naninovel 提供了几种策略来确保“多处存放”不会导致“内存溢出”。

* **按脚本打包（Bundle by Label Strategy）**：
  * 在 Addressable Group 设置中，建议将 Bundle Mode 设为 `Pack Together By Label`。
  * Naninovel 在构建时会扫描 `.nani` 脚本，分析哪些背景在同一个脚本中出现，并给它们打上相同的 Label。这样，当脚本加载时，相关联的背景资源会作为一个 Bundle 被加载，极大地减少了 I/O 碎片化。
* **资源策略（Resource Policy）**：
  * `Conservative`（保守型 - 默认）：进入脚本时加载所有资源，离开时卸载。适合大多数情况。
  * `Optimistic`（乐观型）：加载后常驻内存，直到显式调用 `release!`。适合从本地加载大量小资源，减少加载黑屏时间。

---

## 3. 背景演员配置与参数详析

要实现“多演员配合”，首先需要在配置层面定义这些演员的属性。Naninovel 的配置位于 `Configuration -> Backgrounds`，这里存储了所有背景演员的“元数据”。

### 3.1 演员记录（Actor Records）与 ID 管理

在复杂的项目中，依赖默认的 `MainBackground` 是不够的。你需要创建自定义的背景演员。

* **创建方法**：通过 `Naninovel -> Resources -> Backgrounds -> Create New`，或者创建 Actor Record 资产文件。
* **ID 的重要性**：ID 是脚本控制演员的唯一句柄。建议使用语义化的 ID，如 `Sky`（天空层）、`City`（城市层）、`Fore`（前景层）。
* **文件夹结构**：使用 Actor Record 资产可以将配置分散存储在 `Assets/NaninovelData/Resources/Naninovel/Backgrounds` 的子文件夹中，便于多人协作，避免修改同一个配置文件导致冲突。

### 3.2 渲染实现（Implementations）：形态决定功能

不同的背景可能需要不同的渲染技术。在演员配置中，`Implementation` 选项决定了背景的本质。

| 实现类型 | 技术原理 | 适用场景 | 脚本交互特点 |
| :--- | :--- | :--- | :--- |
| **SpriteBackground** | SpriteRenderer | 静态 2D 图片 (.png, .jpg) | 最常用，支持 Unlit 或 Lit 材质，性能开销小。 |
| **VideoBackground** | VideoPlayer | 动态视频 (.mp4, .webm) | 动态天空、过场动画。需注意 WebGL 平台限制。 |
| **LayeredBackground** | Prefab Hierarchy | 视差滚动、动态组合 | 允许在脚本中开关子图层 (`Group>Layer`)。 |
| **GenericBackground** | Unity Events | 3D 模型、粒子系统、复杂 Prefab | 最灵活，通过事件驱动 Animator 或自定义脚本。 |
| **SceneBackground** | SceneManager | 加载整个 .unity 场景 | 全 3D 探索环节，支持点击 3D 物体触发脚本。 |

### 3.3 全局参数与 Z-Sorting 算法

在 `Configuration -> Backgrounds` 中，有三个参数直接决定了多演员的层级关系：

1. **Z Offset (默认 100)**：这是背景层的“基准深度”。所有背景都会被放置在 Z >= 100 的位置。相比之下，角色通常在 Z=0 附近。这保证了背景永远在角色后面。
2. **Z Step (默认 0.1)**：这是多演员配合的关键。当脚本中出现多个背景时（例如同时显示 Sky 和 City），引擎会自动让后添加的背景 Z 值增加 0.1。
    * **算法**：`ActorZ = Z_Offset + (OrderIndex * Z_Step)`。
    * **效果**：Sky (Z=100.0) -> City (Z=100.1)。在 Unity 的摄像机视角中，Z 值越大离摄像机越远。因此，City 会渲染在 Sky 的后面。
    * **警示**：这与 Photoshop 的图层逻辑（后添加的在上面）是相反的。如果你希望后加载的图片盖在前面，你需要手动调整 `pos` 参数或使用负的 Z Step（虽然不推荐）。
3. **Match Mode (适配模式)**：
    * 根据教程视频分析，背景图的宽高比通常与屏幕不一致。
    * **Crop（裁剪）**：填充屏幕，多余部分切除。适合全屏沉浸感。
    * **Fit（适应）**：完整显示图片，可能有黑边。
    * **Custom**：自定义逻辑。可以在 Camera 配置中设置 `Match Screen Width` 来决定适配逻辑。

---

## 4. 脚本编排：多资源与多演员的协同控制

这是本手册的核心实战部分。我们将通过具体的脚本语法，演示如何将上述理论转化为游戏中的动态表现。

### 4.1 脚本语法解构：@back 命令

`@back` 是控制背景的核心命令。其完整语法结构如下：

```naninovel
@back [AppearanceName]. id: time: pos: tint:[Color] wait:
```

### 4.2 场景一：基础多层背景构建（Parallax Setup）

假设我们有一个赛博朋克场景，需要三个层次：

1. **远景 (Sky)**：动态的云层视频（资源在远程 Addressable）。
2. **中景 (City)**：静态的城市轮廓（资源在本地 Project）。
3. **前景 (Rail)**：栏杆，角色站在栏杆后（资源在本地 Project）。

**脚本实现：**

```naninovel
; 1. 加载远景视频
; 引擎会自动去 Addressable 系统找 "Naninovel/Backgrounds/Sky/CloudLoop"
; 注意：我们要让它在最远端，使用默认 Z (100) 即可
@back CloudLoop id:Sky

; 2. 加载中景城市
; 引擎会去 Resources 文件夹找 "Backgrounds/City/NightView"
; 关键点：我们希望 City 挡在 Sky 前面。
; 默认 Z Step 会让 City 的 Z=100.1 (比 Sky 远)，导致 City 被 Sky 挡住！
; 修正：手动指定 pos 的 Z 值。越小越近。
; 让 City 的 Z=99
@back NightView id:City pos:,,99

; 3. 加载前景栏杆
; 我们希望栏杆非常近，甚至挡住角色 (角色默认 Z~0)。
; 设置 Z=-10
@back Railing id:Rail pos:,,,-10

; 4. 角色入场
; 角色默认 Z=0，所以会被 Rail (-10) 挡住，但挡住 City (99) 和 Sky (100)
@char Kohaku
```

### 4.3 场景二：资源与演员的动态配合

**需求**：在剧情中，天亮了，城市灯光熄灭，同时雾气消散。我们需要同时操作三个演员，且资源加载不能卡顿。

**脚本实现：**

```naninovel
; 预加载资源 (可选，用于优化性能)
@preload time:0 NightView.Dissolve,DayView.Dissolve id:City

# DaybreakSequence
; 并行执行 (Parallel Execution) 是多演员配合的关键
; 使用 wait:false 让命令不阻塞，从而实现“同时发生”的效果

; 1. 天空切换到白天 (耗时 3秒)
@back DayLoop id:Sky time:3 transition:Crossfade wait:false

; 2. 城市切换到白天 (耗时 3秒)
@back DayView id:City time:3 transition:Crossfade wait:false

; 3. 雾气 (Fog Actor) 消散
; 直接修改可见性或者透明度
@hide Fog time:3 wait:true 

; wait:true 加在最后一条命令，表示等待上述所有 3秒的动画都完成后，才继续执行脚本。
```

### 4.4 场景三：分层背景 (Layered Background) 的精细控制

对于复杂的单一背景（如一间屋子，需要开关灯、开关电视），使用多演员太繁琐。此时应使用 `Layered Implementation`。

**资源准备**：

* 制作一个 Prefab，包含 `Base` (墙壁), `Light` (灯光层), `TV` (电视画面层)。
* 在 `Naninovel -> Resources -> Backgrounds` 注册为 `Room`。

**脚本控制**：

```naninovel
; 初始状态：只显示墙壁
@back Room.Base id:Room

; 玩家点击开关，开灯
; 语法：ActorID.Group>Layer (开启该组的特定层)
; 或者使用 Composition 表达式
@back Room.Base+Light id:Room transition:Dissolve

; 打开电视
@back Room.Base+Light+TV id:Room
```

这种方式将多个“逻辑层”封装在一个“物理演员”中，极大简化了脚本。

---

## 5. 高级技巧与特殊情况处理

### 5.1 视频背景的非循环播放 (Non-Looping Video)

在 WebGL 或特定剧情需求下，你可能希望视频背景播放一次后停止（如爆炸特效）。

* **命名规范法**：将视频文件命名为 `Explosion.NoLoop.mp4`。Naninovel 的导入器会识别 `NoLoop` 标记并自动设置导入设置。
* **Web 平台限制**：在 WebGL 上，由于 Unity VideoPlayer 无法直接读取压缩包内的视频流，Naninovel 会在构建时自动将视频复制到 StreamingAssets 目录。
  * **警告**：必须在 Resource Provider 配置中正确设置 `Video Stream Extension`（默认为`.mp4`）。如果你的素材是`.webm` 却未修改配置，加载将失败。

### 5.2 交互式 3D 背景 (Interactive Scene Background)

如果背景是一个完整的 Unity 3D 场景，并且需要玩家点击场景中的物体（如寻找线索）。

1. **配置**：创建一个背景演员，`Implementation` 选 `Scene`。
2. **3D 场景设置**：在 Unity 场景中，给需要交互的物体添加 Collider 和脚本。
3. **触发脚本**：使用 `Play Script` 组件或自定义脚本来触发 Naninovel 逻辑。
    * **注意**：场景加载是异步的。为了防止玩家在场景未加载完时操作，建议使用 `ISceneLoadingDetector` 接口进行状态检测。

### 5.3 解决 Z-Fighting 与 匹配模式问题

根据视频教程的实战经验：

* **Z-Fighting**：如果两个背景莫名其妙闪烁，检查 `Z Step` 是否被设为 0，或者两个演员被强制赋予了相同的 `pos` Z 值。
* **Match Mode**：当背景图比例（如 16:9）与 iPad 屏幕（4:3）不一致时，背景可能会漏出黑边（Fit 模式）或被切掉关键部分（Crop 模式）。
  * **技巧**：在 Camera 配置中启用 `Match Screen Width`，并制作比标准分辨率更宽的背景图（Over-scanning），以适应各种宽屏设备。

---

## 6. 总结与最佳实践清单

为了确保在“多资源、多演员”的复杂项目中保持系统的健壮性，建议遵循以下最佳实践：

| 维度 | 建议操作 | 禁忌 |
| :--- | :--- | :--- |
| **资源组织** | 使用 Addressable 分组隔离本地与远程资源；严格遵守 `Naninovel/Backgrounds/{ID}/{Name}` 命名规范。 | 在脚本中硬编码文件路径（如 C:/Assets/...）；混合使用 Resources 和 AssetBundles 而无明确策略。 |
| **演员管理** | 为每个逻辑层（天空、远景、中景、前景）分配固定的 Actor ID；使用 Actor Record 资产分散配置文件。 | 依赖默认的 `MainBackground` 处理所有事情；在运行时动态创建大量临时 ID。 |
| **脚本编写** | 使用 `wait:false` 处理并行过渡；显式指定 `pos` 的 Z 值以覆盖默认的自动排序。 | 依赖默认的 `Z Step` 处理复杂的前后遮挡关系（容易出错）；在 WebGL 上使用不支持的视频编码。 |
| **性能优化** | 对高清背景启用 Bundle by Label；在不需要背景时及时使用 `@hide` 释放显存。 | 加载巨大的未压缩 .png 序列帧作为背景（应使用视频）；忘记在 Addressable 中给资源打 Label。 |
