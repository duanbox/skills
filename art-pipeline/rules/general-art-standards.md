---
name: general-art-standards
description: General art standards, resolution, formats, and naming conventions
metadata:
  tags: art, standards, resolution, naming, formats
  source: docs/rules/rules_assets.md
---

## 1. 总体美术规范 (General Art Standards)

### 视觉风格 (Visual Style)
* **核心关键词**: 水墨废土 (Ink-Wash Wasteland) + 赛博霓虹 (Cyber Neon) + 东方古典 (Eastern Classic).
* **色调**: 黑白灰（水墨基底）+ 高饱和度霓虹色（灵能/道化科技）+ 锈蚀金/青铜色（旧时代遗物）.

### 分辨率标准 (Resolution Standards)
* **源文件 (Source)**: **3840 x 2160 (4K)**
    * 所有美术资产（背景、CG、UI设计稿）必须以4K分辨率制作，以适应未来的高清化需求及特写镜头（Zoom-in）。
* **工程文件 (Game Dev)**: **1920 x 1080 (FHD)**
    * 导入Unity前需进行缩放或压缩，以优化包体大小。

### 核心资产格式规范 (Asset Formats) - [关键更新]
为适应 **Naninovel** 引擎特性及 **Unity** 高清渲染管线，请严格遵守以下格式要求：

| 资产类别        | 源文件格式 (Source)   | 交付/工程格式 (Export) | 关键要求 (Critical Specs)                                           |
| :-------------- | :-------------------- | :--------------------- | :------------------------------------------------------------------ |
| **角色立绘**    | **PSD / PSB** (分层)  | PNG (按需切片)         | **必须分层**。眼睛、嘴巴、身体、配件需独立图层。背景透明。          |
| **UI / 图标**   | **SVG** (矢量)        | SVG / PNG              | **矢量首选**，支持无限缩放。若用 PNG，需提供 1024x1024 高清源文件。 |
| **地图图示**    | **SVG** (矢量)        | SVG / PNG              | 用于地图移动物体（如马车），要求边缘绝对清晰，无锯齿。              |
| **背景 (静态)** | **JPG** (Quality 90+) | JPG                    | 3840x2160，无透明通道，性价比最高。                                 |
| **背景 (动态)** | **PSD** (分层)        | PNG (分层导出)         | 用于**视差滚动** (Parallax)，需将前/中/远景拆分图层。               |
| **VFX 特效**    | **PSD** 或 **AE工程** | PNG (序列帧)           | 保持每帧尺寸一致，背景透明。                                        |

### 命名规范 (Naming Convention)
* **格式**: `Category_SubCategory_Name_Variant`
* **语言**: 英文 (English), 驼峰命名 (CamelCase) 或 下划线 (Snake_Case).
* **禁止**: 中文文件名、空格、特殊符号.

---

## 2. 角色资源 (Character Assets)

**源文件**: **PSD / PSB (多图层)**
**分辨率**: 画布高度 **2500px - 3000px**
**文件前缀**: `Chara_`

### 立绘分辨率标准 (Standing Picture Resolution)
*   **全身立绘 (Full Body)**: 高度 **2800px - 3200px**。此分辨率旨在支持 **1.5x 镜头推近 (Zoom-in)** 而不失真，适应 4K 屏幕显示。
*   **半身/特写 (Half Body)**: 高度 **1800px - 2200px**。用于特定的剧情特写时刻。
*   **注意**: 即使在 1080p 工程中，源文件也必须保持此高分辨率，以便未来高清化或制作营销素材。

### 制作要求
1.  **分层结构**:
    * `Layer_Face_Eyes`: 包含多种眼部状态（睁眼、闭眼、惊讶等）。
    * `Layer_Face_Mouth`: 包含多种口型（闭嘴、说话、大笑等）。
    * `Layer_Body`: 身体躯干。
    * `Layer_Accessory`: 可拆卸配件（如罗盘、武器）。
2.  **目的**: 方便在 Naninovel 中使用 `Layered Character` 功能，实现动态表情切换和眨眼动画，大幅节省显存。

### 角色设计原则：视觉锚点 (Visual Anchors) - [关键]
*   **拒绝脸谱化**: 坚决避免千篇一律的“网红脸”或通用的完美 AI 脸。角色面部设计必须有明显的**瑕疵感或独特性**。
*   **独特的记忆点**: 设计角色时**必须**赋予其 1-2 个独一无二的外貌视觉锚点，让玩家一眼就能记住。
*   **锚点示例**: 
    *   **面部特征**: 眼角/嘴角的泪痣、断眉、经历废土洗礼的切割疤痕或烧伤痕迹、不对称的异色瞳。
    *   **毛发特征**: 极具辨识度的狂野自然卷、反叛的呆毛、凌乱未经打理的碎发。
    *   **提示词应用**: 在生成时，将这些特征放在提示词最前方的明显位置进行强调（例如：`a distinct faded scar across the bridge of his nose`, `wild unruly curly hair that refuses to be tamed`, `a prominent beauty mark under her left eye`）。


### AI 辅助工作流 (AI Workflow)
*   **基本逻辑**: 使用 AI 生成多表情变体 (Variants)，通过 **Smart Face Cropper** 工具提取面部差分。
*   **工具使用**:
    1.  准备基础立绘 (Base Body) 和 AI 生成的表情变体图 (Expression Variant)。
    2.  确保两者尺寸完全一致。
    3.  使用 Unity 菜单 `Tools/ADV Pipeline/Smart Face Cropper` 打开工具。
    4.  拖入底图和变体图，调整 Padding (默认 40px)，点击提取。
    5.  生成的 Face Sprite 可直接用于 Naninovel 的表情层，无需手动扣除五官，工具会自动处理裁切区域。

### 特殊角色实现 (Special Character Implementation)
*   **玉女 (YuNu)** - *进阶效果*
    *   **登场效果 (Entrance)**: 利用 **Alpha 溶解 (Dissolve)** 或自定义 Shader，配合提示词中的“烟雾溶解”概念，表现从罗盘中凝聚登场的感觉。
    *   **空间感 (Spatial Depth)**:
        *   **Z-Pos**: 将其 Z 轴坐标微调至比羲和更远的位置 (e.g., Z+10)。
        *   **悬浮动画 (Floating)**: 使用 DOTween 制作微弱的 Y 轴上下浮动循环动画 (Loop)，强化“灵体/漂浮”的非人质感。
*   **嫦娥 (Chang'e)** - *精神干预*
    *   **冷月场域 (Cold Moon Field)**:
        *   **Post-processing**: 登场时通过 Naninovel 命令或 Service 降低全屏 **Saturation** (饱和度) 并叠加淡蓝色的 **Tint**，营造压抑废土燥热的冷月气场。
    *   **动态立绘 (Dynamic Sprite)**:
        *   **长袖飘动 (Sleeve Anim)**: 对极长的衣袖使用 **Vertex Animation** (顶点动画)，实现微弱的摆动效果，增加神性与流动感。
*   **聂小倩 (Nie Xiaoqian)** - *数据幽灵 (Melancholic Ghost)*
    *   **视觉核心 (Visual Style)**:
        *   **气质**: 悲凄的苍白 (Mournfully Pale) + 身不由己的楚楚可怜。
        *   **道化科技 (Dao-Tech Specter)**: 加入故障数据线 (Glitching Data-threads) 和数字伪影 (Digital Artifacts)，暗示其为“残留意识数据流”。
        *   **材质**: 半透明染墨丝绸 (Semi-transparent Ink Silk)，裙摆消散为书法烟缕。
    *   **关键道具 (Props)**: 生锈铁笼灯笼 (Rusted Iron Lantern Cage) + 微弱脉冲灵球 (Weakly Pulsing Spirit-Orb)。
    *   **Naninovel 技术实现**:
        *   **Shader**: 全程开启 **Flowing/Noise Shader** (基于噪声的顶点偏移)，模拟水中倒影般的不稳定感。
        *   **Alpha**: 设置为 **0.8 - 0.9**，强化非实体的虚幻感。
*   **邓婵玉 (Deng Chanyu)** - *形态切换 (Transformation)*
    *   **切换演出 (Switch Logic)**:
        *   **瞬间切换 (Instant)**: 战斗形态切换时使用 `time:0`，配合 **SFX** (重型机械音/爆发音) 和 **Shake Camera** (震屏)，拒绝平滑过渡，强调爆发力。
    *   **视觉压迫感 (Visual Oppression)**:
        *   **Scale**: 战斗形态设置为 **1.1** (110%)，视觉上瞬间膨胀。
        *   **Pos**: Y 轴微调 (e.g., `pos:50,10`)，稍微抬高位置，配合俯视镜头 (High Angle / Upshot) 的提示词设定，营造玩家被“俯视”的压迫感。
        *   **差分表情 (Expressions)**:
            *   **常态 (Normal)**: `Eyes_Closed` (闭眼不屑), `Smile_Sarcastic` (嘲讽笑)。
            *   **战斗 (Combat)**: `Face_Madness` (狂笑/颜艺)。
    *   **故障特效 (Glitch VFX)**: 利用 `@glitch` 命令和 `@sfx ArmorBreak` 音效模拟道化植入体过载，表现从“高傲女王”到“疯狗猎手”的剧烈反差。
    *   **物理动态与音效 (Physical Dynamics & SFX)**:
        *   **动态方案 (Animation)**:
            *   **进阶**: Live2D / Spine 动态立绘。
            *   **基础**: 此形态胸甲需独立分层 (Separate Layer)，在攻击/受击时使用 DOTween `DOPunchScale` 模拟沉重的弹性/乳摇 (Heavy Impact/Jiggle)，增强视觉反馈。
        *   **音效 (SFX)**: 战斗形态登场配合 **SFX_Hydraulic_Move** (重型液压声)，强化性感与毁灭性的重装重量感。
*   **九天玄女 (Nine Heavens Xuannu)** - *谋略之神 (God of Strategy)*
    *   **数据流特效 (Visual FX)**:
        *   **水墨数据流 (Ink Data Stream)**: 在推演或说话时，于立绘周围叠加半透明的、缓慢流动的黑色水墨数据粒子系统 (Particle System)，具象化其计算过程。
    *   **计算音效 (SFX)**:
        *   **精密仪器 (Precision Instrument)**: 语音或登场音效需带有极其轻微的“老式计算机运算声”或“电流底噪”，暗示其非人的精密仪器本质。
    *   **动态表现 (Dynamic Visuals)**:
        *   **朱砂流光 (Cinnabar Glitch)**: 利用 Naninovel 的 Glitch 或自定义 Shader，让她身上的朱砂红色块偶尔闪烁，模拟古老电路接触不良但能量巨大的感觉。
        *   **拖尾效果 (Data Tail)**: 制作 **Looping Animation** (循环动画)，在立绘后挂载粒子特效，让黑色墨点和红色代码不断从裙摆流出，寓意“写入命运”。
*   **白骨精 (Baigujing)** - *画皮 (Painted Skin)*
    *   **换脸差分 (Face Swapping)**:
        *   **Default**: 完美的冷艳美女 (Cold Beauty)。
        *   **TrueForm**: 当激怒或攻击时，瞬间切换至“黑玉骨骼”或“皮肤崩裂”状态 (Black Jade Bone/Cracked)，制造恐怖谷效应。
    *   **故障音效 (Glitch SFX)**:
        *   **骨骼摩擦 (Bone Cracking)**: 当她谈论“美”时，背景音效需混合轻微的骨骼摩擦声与电流声，暗示皮囊下机械过载的高负荷运转。
*   **龙吉公主 (Longji Princess)** - *净化与控制 (Purification & Control)*
    *   **Odin Inspector 设计 (Data Structures)**: 在 Unity 中为她设计独特的数据结构以支持其特殊机制。
    *   **核心机制 (Mechanics)**:
        *   **状态叠加 (Status Effects)**: 攻击施加 **湿润 (Wet)** 状态，对已湿润目标再次攻击转化为 **冻结 (Freeze)**。
        *   **净水交易 (Purified Water Trading)**: 唯一能将 **受污染的冷却液 (Polluted Coolant)** 转化为 **可饮用灵水 (Potable Spirit Water)** 的 NPC，作为核心资源循环的一部分。
*   **白蛇 (White Snake)** - *净化系统 (Purification System)*
    *   **环境演出 (Atmosphere)**:
        *   **视觉滤镜 (Visual Filter)**: 登场时叠加淡淡的 **白色雾气 (White Fog)** 并轻微 **模糊背景 (Blur)**，营造湿润、朦胧且空气清新的净化感。
    *   **音效 (SFX)**:
        *   背景音需加入微弱的 **蛇行声 (Slithering)** 与 **高科技加湿器/呼吸机 (Humidifier)** 的白噪音。
*   **妲己 (Daji)** - *理智侵蚀 (Sanity Erosion)*
    *   **动态立绘 (Dynamic Sprites)**: 基于 **理智值 (Sanity)** 变量 (`@if`) 切换立绘状态。
        *   **高理智 (>80)**: *真实视角* - 穿着破烂紧身衣、拖着九根电缆的疯女人。
        *   **低理智 (<40)**: *幻觉视角* - 绝世美女，尾巴呈现发光仙气，语音带混响诱惑。
        *   **中段 (Mid-Range)**: 对应“八尾半”的其他分裂人格。
    *   **UI 干扰 (UI Glitch)**:
        *   在场时，对话框 (TextBox) 偶尔出现乱码或抖动 (使用 **Text Animator**)，选项按钮忽左忽右移动，模拟精神干扰。
*   **洛神 (LuoShen)** - *动静之美 (Dynamic Beauty)*
    *   **动态演出 (Dynamic Motion)**:
        *   **Shader 波动 (Vertex Displacement)**: 对飘带和裙摆应用基于正弦波的 **Vertex Displacement Shader**，使其始终保持如水波般的缓慢流动，即使在静止时也充满动感。
    *   **残影特效 (Afterimage)**:
        *   移动或离场时使用 **Trail Renderer** 或生成 **半透明残影**，表现“步履轻盈，过目难忘”的神韵，而非简单的透明度淡出。

### 角色清单
* **主角**: 羲和 (Xihe), 玉女 (YuNu)
* **领袖**: 柏鉴 (BaiJian), 邓婵玉 (DengChanYu), 白骨精 (WhiteBone), 九天玄女 (XuanNu), 聂小倩 (NieXiaoQian), 妲己 (DaJi), 龙吉公主 (LongJi), 白蛇 (WhiteSnake), 洛神 (LuoShen)
* **怪物**: 灵石鼠, 机械傀儡, 僵尸道士

### 后期处理 (Post-Processing)
*   **工具**: Photoshop
*   **风格**: 配合 Unity **Shapes** 矢量库的风格。
*   **操作**: 为主角立绘边缘添加一层极细的“灵气扰动”外框 (Spiritual Aura Distortion Outline)。此线条应呈现矢量质感，锐利且带有能量流动的视觉暗示。

---

## 3. 背景资源 (Background Assets)

**源文件**: **3840 x 2160 (16:9)**
**文件前缀**: `BG_`

### 静态背景 (JPG)
适用于大多数对话场景。
* **长安**: `BG_ChangAn_Palace_Night`, `BG_ChangAn_Slum_Neon`, `BG_ChangAn_Lab_Alert`
* **火焰山**: `BG_FlameMtn_Mine_Normal`, `BG_FlameMtn_Wasteland_Sandstorm`, `BG_FlameMtn_Factory_Operational`
* **其他**: 西湖断桥, 丰都鬼门关, 昆仑千年阁

### 动态/视差背景 (PSD 分层)
适用于主菜单、关键过场或大地图背景。
* **要求**: 至少拆分为 `Far_View` (远景), `Mid_View` (中景), `Near_View` (前景/遮挡物)。
* **示例**: `BG_MainMenu_Parallax.psd` (流动的云、摇曳的灯笼、静止的山)。

### 地牢背景 (Dungeon Backgrounds) - [Rance 10 风格]
此类背景作为 UI 下方滚动的氛围层，AI 生成时需严格遵守 **"3不1要"** 原则：

1.  **要：纯环境 (Environment Only)**
    *   **Prompt**: `top down view`, `isometric`, `dungeon texture`, `dark atmosphere`, `foggy`.
    *   **目的**: 仅提供“纹理”或“底图”。

2.  **不：不要生成路/网格 (No Path/Grid)**
    *   **Prompt 🚫**: `road`, `path`, `grid`, `tiles`.
    *   **原因**: 路是由 UI 动态生成的。背景若含路，会导致视觉穿帮。

3.  **不：不要中心构图 (No Center Object)**
    *   **Prompt 🚫**: `castle in middle`, `hero in center`.
    *   **原因**: 屏幕中心是玩家和 UI，背景必须是均匀分布的（如废墟、深渊）。

4.  **后期处理 (Post-Processing)**
    *   **高斯模糊**: 必须在 PS 中进行模糊处理 (Radius 10-20px) 以分离视觉层级。
    *   **压暗**: Unity 中通过覆盖 50% 黑遮罩来统一色调。

---

## 4. 事件插画 (Event CGs)

**源文件**: **3840 x 2160 (16:9)**
**格式**: **PSD (推荐分层)** 或 **JPG (单张)**
**文件前缀**: `CG_`

* **分层建议**: 如果 CG 中有角色表情变化（如哭泣变成微笑），请提供 PSD 并拆分面部表情层。
* **清单**:
    * `CG_Ch0_Awakening_Light` (序章苏醒)
    * `CG_Intro_GodCorpse` (神仙末世)
    * `CG_End_Ascension` (飞升之路)
    * `CG_Deng_Transform_Burst` (邓婵玉变身)

---

## 5. UI 与 图标资源 (UI & Icons)

**源文件**: **SVG (矢量)** 或 **4K PSD**
**输出**: 导出 @1x (1080p) 和 @2x (4K)
**文件前缀**: `UI_` / `Icon_`

### 矢量化优势
* UI 边框、按钮、进度条建议使用 **SVG**，在 Unity 中配合 `Vector Graphics` 包可实现完美无损缩放。

### 资源清单
* **通用**: 对话框 (`UI_Dialog_Box`), 按钮 (`UI_Btn_Confirm`), 设置面板.
* **物品图标** (建议 512x512 PNG 或 SVG):
    * 神器: 昆仑镜, 伏羲琴, 炼妖壶.
    * 物品: 铁蒺藜, 灵石碎片, 烟雾弹, 鬼手罗盘.

---

## 6. 地图图示资源 (Map Sprites / Tokens)

**源文件**: **SVG (矢量)** - **强烈推荐**
**替代方案**: 1024x1024 高清 PNG (透明背景)
**文件前缀**: `Map_`

### 为什么用 SVG?
大地图上的棋子（Token）经常需要缩放（Zoom In/Out）。SVG 能保证棋子在缩放到极小或极大时边缘依然锐利，不会出现马赛克。

### 清单
1.  **玩家图示**:
    * `Map_Player_Vehicle_Default` (蒸汽马车/载具) - **SVG**
    * `Map_Player_Char_Walk` (主角Q版) - **SVG**
2.  **标记点 (Markers)**:
    * `Map_Pin_Quest_Active` (任务叹号)
    * `Map_Pin_Enemy_Skull` (危险骷髅)
    * `Map_Loc_City_Icon` (城市微缩标)

---

## 7. 视觉特效 (VFX Assets)

**格式**: **PNG 序列帧** (Sprite Sheet) 或 **粒子贴图** (Particle Texture)
**文件前缀**: `VFX_`

* **水墨烟雾**: `VFX_Tex_InkSmoke_01` (黑白通道图)
* **全息故障**: `VFX_Tex_Glitch_Noise`
* **技能特效**: `VFX_Hit_Slash_Ink` (序列帧)
