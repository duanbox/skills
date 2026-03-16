---
name: ai-prompts
description: AI art generation prompts library for zimage-turbo / flux2-klein
metadata:
  tags: ai, art, prompts, zimage, flux
  source: docs/rules/rules_prompt.md
---

# AI 美术资产生成提示词库 (AI Art Prompts Library)

**项目**: EndGods - 神陨修真废土 ADV  
**版本**: 2.1 (Merged Edition - Comprehensive)  
**核心风格**: 水墨画 (Ink Wash) × 赛博朋克废土 (Industrial Wasteland) × 道教符文科技 (Dao-Tech)  
**目标模型**: zimage-turbo, flux2-klein  
**最后更新**: 2026-02-05

---

## 核心风格定义 (Core Style Definition)

### 1. Master Style Block (生产级通用指令)
这是所有 Prompt 的基础前缀/后缀，用于统一游戏的整体视觉基调。**建议直接复制使用。**

> **Art style**: Traditional Chinese ink wash painting (Sumi-e) fused with gritty industrial cyberpunk. Texture of rough, aged Xuan paper. High contrast between heavy black ink strokes and glowing neon cyan/gold spiritual energy circuits. Atmospheric, melancholic, desolate but elegant. Sharp, expressive brushwork.

### 2. 详细风格描述 (Universal Base Style - for flux2/zimage)
如果模型需要更详细的自然语言描述，请使用以下段落：

```
A piece rendered in traditional Chinese ink wash painting style, evoking the aesthetic of brushwork on handmade Xuan paper. The composition features elegant, flowing brush strokes with characteristic ink splatters and rough, organic edges. The color palette is predominantly monochromatic, with subtle accents of neon cyan and pale gold that hint at technological elements.

The artwork blends ancient Daoist aesthetics with a cyberpunk post-apocalyptic atmosphere. Circuit-like glowing patterns emerge organically from the ink wash foundation, appearing as threads of ethereal spirit energy. Ancient runes glow softly with Qi energy, integrated seamlessly into what appears to be weathered mechanical parts. The overall mood conveys a desolate wasteland with decaying grandeur, melancholic beauty, and the fading power of divine beings—a visual representation of an era coming to an end where traditional ink painting meets industrial decay.
```

### 3. 色彩空间规范 (Color Palette)
*   **墨黑 (Ink Black)**: `#000000` - 基础线条与阴影
*   **青色 (Cyan)**: `#00FFFF` - 灵力、电路、UI 高亮 (道家/科技)
*   **朱红 (Vermilion)**: `#E34234` - 警告、危险、符咒印记 (鬼火/封印)
*   **淡金 (Pale Gold)**: `#F0E68C` - 神性残余、旧时代遗物

---

## 模型特性说明 (Model-Specific Guidelines)

> **来源**：Z-Image Turbo 官方 Prompting Guide (GitHub/HuggingFace)、FLUX.2 [klein] 官方文档 (docs.bfl.ml)
> **最后更新**：2026-03-02

---

### Z-Image Turbo — 核心要点

Z-Image Turbo 是 6B 参数的单流扩散 Transformer，专为快速指令跟随生成而蒸馏优化。

#### 关键特性（与传统 SD 模型完全不同）

1. **guidance_scale = 0** — 官方管线使用零 CFG。高于 0 会显著增加生成时间但收益递减
2. **完全不使用 negative_prompt** — 模型训练时不包含负面提示词。所有约束必须放在正面提示词中
3. **最佳步数 8-12** — Turbo 蒸馏模型专为极少步数优化，增加步数仅在出现明显噪点时使用
4. **原生分辨率 1024×1024** — 768 或 512 可用于草稿
5. **中英双语** — 同时支持英文和中文文本渲染与指令跟随
6. **提示词长度甜区 80-250 词** — 长且精确 = 好；长且华丽 = 差

#### 提示词结构脚手架（官方推荐）

```
[镜头与主体] + [年龄与外貌] + [服装与覆盖] + [环境/背景] + [灯光] + [情绪] + [风格/媒介] + [技术参数] + [约束条款]
```

#### "无负面提示词"的替代方案：内嵌约束

由于 Turbo 忽略 negative_prompt，所有控制通过正面提示词中的**约束短语**实现：

```
常用约束短语（添加在提示词末尾）:
- no text, no watermark, no logos
- plain background, not busy or cluttered
- no extra limbs or fingers, correct human anatomy
- no motion blur, sharp focus
- safe for work, non-sexual, fully clothed characters
```

#### 灯光关键词（Turbo 对灯光描述响应极好）

- `soft diffused daylight` — 柔和漫射日光
- `cinematic warm key light` — 电影暖调主光
- `noir high-contrast lighting` — 黑色电影高对比
- `studio portrait lighting` — 影棚人像灯光
- `rim lighting` — 轮廓光

---

### FLUX.2 [klein] — 核心要点

FLUX.2 [klein] 是 4B/9B 参数模型，亚秒级推理速度，不进行自动提示词增强。

#### 关键特性

1. **叙述性散文** — 使用流畅的段落描述场景，**绝不使用**逗号分隔的关键词列表
2. **灯光描述影响最大** — 光源、品质、方向、色温和表面交互是产出质量的最大变量
3. **词序决定注意力** — 模型注意力随位置递减，**前置最重要的元素**
4. **不做自动提示词增强** — 你写什么就生成什么，详细程度直接决定产出质量

#### 提示词结构

```
主体 → 场景 → 细节 → 灯光 → 氛围
```

#### 提示词长度指南

| 类别 | 词数    | 适用场景           |
| ---- | ------- | ------------------ |
| 短   | 10-30   | 风格探索、快速概念 |
| 中   | 30-80   | 标准生产           |
| 长   | 80-300+ | 复杂编辑、详细产品 |

#### 散文 vs 关键词对比

**推荐（散文式）**:
> A weathered fisherman in his late sixties stands at the bow of a small wooden boat, wearing a salt-stained wool sweater, hands gripping frayed rope.

**避免（关键词式）**:
> fisherman, aged, weathered, boat, wool sweater, rope, sepia tones

#### 风格/情绪标注（附加在场景描述后）

```
[场景描述]. Style: Country chic meets luxury editorial.
Mood: Serene, romantic, grounded.
```

```
[场景描述]. Shot on 35mm film (Kodak Portra 400) with shallow
depth of field—subject razor-sharp, background softly blurred.
```

---

### 两模型通用最佳实践

1. **使用自然语言叙述**：完整的句子描述，而非关键词堆砌
2. **避免权重标记**：不使用 `(keyword:1.2)` 这类权重，改用形容词强调
3. **灯光优先**：灯光描述对最终质量影响最大，务必详细指定
4. **前置重点**：最重要的元素放在提示词开头
5. **每个词都要有信息量**：删除无意义的填充词

---

## 一、UI 组件 (UI Components)

### 用途
生成按钮、窗口、边框、图标等界面元素。用于 `UIAssetDatabase` 自动同步。

### zimage-turbo / flux2-klein 提示词模板

**基础模板**:

```
Create a game UI element in the style of traditional Chinese ink wash painting merged with cyberpunk aesthetics. The design should feature textured Xuan paper as the base, with elegant brush strokes defining the [component type: window frame/button/icon holder/health bar/menu panel]. The edges should appear rough and organic, characteristic of ink painting techniques, with occasional ink splatters adding authenticity.

Incorporate subtle technological elements: circuit-like glowing patterns in neon cyan and pale gold should flow through the design like silk threads. Ancient Daoist runes should be integrated into the mechanical parts, appearing as if they're engraved into weathered metal components. The overall aesthetic should balance traditional artistry with post-apocalyptic industrial decay.

The design must be clean enough for game UI functionality while maintaining the artistic ink wash character. Ensure good contrast and readability for interface elements.
```

### 组件类别示例

#### 1. 按钮 (Buttons)

```
Design a rectangular button interface element that resembles a stone tablet rendered in ink wash painting style. The surface should have the texture of weathered stone with visible brush stroke marks. Engraved Daoist symbols run along the edges, subtly glowing with cyan energy. The corners feature small mechanical reinforcements made of tarnished metal. A label area exists in the center, designed to hold brush calligraphy characters.

The button should convey a sense of ancient durability mixed with salvaged technology, perfect for a post-apocalyptic cultivation game interface.

Variants to explore:
- Primary action: brighter cyan glow with sharp, defined edges
- Secondary action: dimmer gold glow with softer, rounded edges  
- Danger/warning: red runic warnings with cracked texture suggesting urgency
```

#### 2. 窗口框架 (Window Frames)

```
Create a large UI window frame that combines traditional Chinese lattice patterns with circuit board aesthetics. The border should be rendered in thick, expressive ink wash strokes, with ornate corner decorations featuring small mechanical gears that appear weathered and ancient. Faded gold accents highlight key structural points.

Spirit Qi energy should visibly flow along the frame's edges as subtle glowing lines, suggesting this interface is powered by mystical energy. The frame should feel substantial and architectural, like looking through a portal into game information rather than just a generic UI box.
```

#### 3. 血条/能量条 (Health/Energy Bars)

```
Design a horizontal energy bar that takes inspiration from traditional bamboo tube containers. The container itself should be rendered in ink wash style with visible texture and brush strokes. Inside, glowing cyan liquid (representing spirit energy) should be visible, with small floating particles of Qi energy suspended within.

Daoist talisman symbols serve as markers along the bar's length, indicating threshold points. The overall design should feel organic and hand-crafted, as if made by a wandering cultivator rather than mass-produced.
```

---

## 二、角色立绘 (Character Sprites) — 模块化系统

### 用途
生成角色差分、表情、姿势变化。用于 Naninovel 角色系统。
模块化设计允许自由组合 **身份 + 动作 + 表情 + 服装** 来快速生成多样性变体。

> **💡 核心设计原则：视觉锚点 (Visual Anchors)**
> 提示词中**必须**包含角色的面部或身体记忆点（如：独特的疤痕、泪痣、特征性的卷发、断眉等），拒绝千篇一律的通用完美 AI 脸。在撰写 Module A 的身份基底时，务必将特征明确化。

### 组合公式

```
[Style Block] + [Base Module] + [Pose Module] + [Expression Module] + [Outfit Module] + [Constraint Block]
```

**使用方法**：从下方每个模块中选一段，拼接成完整提示词。修改任何一个维度只需替换对应模块。

---

### Module A: 基础身份 (Base Identity)

> 定义角色的核心身份、体型、发型等不变特征。每个角色写一份，长期复用。

**A1. 通用角色基底**
```
Create a full-body character portrait in the style of traditional Chinese ink wash painting, suitable for use as a visual novel character sprite. The artwork employs fluid brush strokes and traditional Chinese painting techniques, maintaining a primarily monochromatic palette with selective color emphasis on key details. The background is pure white for easy integration into game scenes.

The character is [NAME], a [IDENTITY/PROFESSION], appearing to be [AGE] years old. [GENDER] has a [BUILD: slender/athletic/curvy] frame. [HAIR DESCRIPTION: e.g. long black hair flowing naturally with subtle golden highlights]. [EYE DESCRIPTION: e.g. deep amber eyes with faint rune patterns]. [DISTINGUISHING FEATURES (**REQUIRED** — 视觉锚点，必须包含至少一个面部或身体记忆点): e.g. a faded scar across the left cheek, spirit circuit tattoos on forearms].
```

**A2. 羲和 (Xihe - 鬼手·废土浪客)**
```
A full-body character portrait rendered in traditional Chinese ink wash painting style on pure white background, suitable for visual novel sprite cutting. Bold, sharp brushstrokes with dramatic ink splatter accents conveying raw energy. The color palette is muted charcoal and aged parchment, with neon cyan spirit-energy highlights and subtle gold flickers around the eyes.

The character is Xihe, codename "Ghost Hand," an 18-to-22-year-old male wasteland scavenger with the roguish charisma of a young wandering swordsman. He is lean and wiry, his body hardened by years of survival in the Flaming Mountains. His face is strikingly handsome in a rough, untamed way—sharp jawline, high cheekbones weathered by desert wind, and a faint cocky half-grin that suggests he knows something you don't.

His most arresting feature is his heterochromatic eyes: the left eye is deep oceanic blue, the right eye is cold silver like moonlight on steel. Both gleam with fierce intelligence and restless cunning. A faint green third-eye mark glows on his forehead, barely visible, like a half-forgotten divine seal.

His dark hair is wild and wind-swept, partially tied back with a frayed cord but with rebellious strands falling across his brow. His "Ghost Hands" are his signature—both forearms and hands are laced with intricate spirit-ink circuit tattoos that pulse with dim cyan luminescence, ancient calligraphy branded directly into his meridians, glowing brighter at the fingertips.
```

**A3. 通用女主**
```
Create a full-body character portrait in the style of traditional Chinese ink wash painting, suitable for use as a visual novel character sprite. The artwork employs fluid brush strokes and traditional Chinese painting techniques, maintaining a primarily monochromatic palette with selective color emphasis on key details. The background is pure white for easy integration into game scenes.

The character is a young female [PROFESSION: scavenger/cultivator/mechanic], appearing to be between 20-35 years old. She has a [BUILD] frame suited to surviving harsh wasteland conditions. Her [HAIR STYLE] and [EYE COLOR] reflect her [PERSONALITY: determined/gentle/mysterious] nature.
```

**A4. 玉女 (Yu Nu - 罗盘器灵)**
```
A full-body character portrait rendered in traditional Chinese ink wash painting style on pure white background, suitable for visual novel sprite cutting. Sharp, professional brushstrokes with high contrast between heavy charcoal ink and pale parchment tones. No glow effects—all luminescence conveyed through ink density gradients and crisp linework. 2D game sprite style with cinematic shadows.

The character is Yu Nu, an ancient artifact spirit bound to a celestial compass. She has the elegant, mature facial features of a classical Chinese goddess (Xiannu), but her entire physical scale is miniaturized to a palm-sized entity, giving her a precious, doll-like proportion while retaining full adult grace. Her dark hair is swept up in an elaborate traditional style secured with tiny bronze ornamental pins. Her eyes are sharp, intelligent, and carry the quiet authority of something far older than it appears.

Her most distinctive feature is her spectral lower body—below the waist, her form gradually dissolves into sharp, crystalline ink fragments and delicate spirit-energy wisps, as if she is perpetually emerging from or dissolving back into the compass. Orbiting her at all times are intricate, weathered bronze compass rings, rotating slowly like miniature astrolabes.
```

**A5. 嫦娥 (Chang'e - 哀婉的囚凰)**
```
A full-body character portrait rendered in traditional Chinese ink wash painting style on pure white background, suitable for visual novel sprite cutting. Sharp, professional brushstrokes with high contrast between heavy charcoal ink and pale parchment tones. Cold silver undertones throughout. Cinematic shadows with no warm colors. 2D game sprite style.

The character is Chang'e, the Lunar Goddess, an ancient deity of devastating cold beauty now bound in spiritual chains. She has a stunning, mature countenance—porcelain skin, high cheekbones, and a gaze as sharp and distant as frosted glass, conveying both regal authority and deep, unspoken sorrow. Her eyes are pale silver-white, almost colorless, reflecting moonlight that no longer exists.

Her hair is an architectural masterpiece of traditional high-coiled buns, secured by tarnished silver lunar-phase needles and threaded with faint fiber-optic strands—the only technological element, cold and barely luminous. Stray wisps of hair have escaped the structure, suggesting a perfection slowly unraveling under duress.

Her most distinctive feature is the fragmented stone ring hovering behind her—a broken moon rendered as cracked, non-glowing grey stone segments orbiting her shoulders like a shattered halo. It is inert and dead, a monument to lost divinity.
```

**A6. 聂小倩 (Nie Xiaoqian - 幽谷残烛)**
```
A full-body character portrait rendered in traditional Chinese ink wash painting style on pure white background, suitable for visual novel sprite cutting. Sharp, professional brushstrokes with high contrast between heavy charcoal ink and pale parchment tones. Cold desaturated palette with pale ghostly blue as the sole accent color. 2D game sprite style with cinematic shadows.

The character is Nie Xiaoqian, the Dying Candle of the Dark Valley—a sorrowful ghost spirit who is secretly an avatar-fragment of the Fengdu Emperor, the lord of the underworld. Despite her spectral nature, she carries the composed dignity of a scholar and teacher of the Ancient Way. She has a breathtakingly beautiful but mournfully pale countenance, her skin almost translucent as if lit from within by a dying light. Her eyes are deep, dark, and downturned—pools of ancient sorrow that have witnessed millennia of souls passing through the underworld.

Her long black hair flows and drifts as if perpetually submerged in still water, loosely gathered with tattered spirit-binding ribbons and threaded with faint glitching data-filaments—remnants of the Fengdu Emperor's control tether. The hair moves with a ghostly life of its own, never fully still.

Her most distinctive feature is the weathered iron lantern cage she carries, rusted and ancient. Inside the lantern, instead of fire, a cold pale blue spirit-orb pulses weakly, emitting faint data particles—the only light source on her person. Her bare feet are visible below the hem of her robes, gradually fading into swirling ink smoke and faint digital glitch artifacts, as if her physical form is perpetually dissolving at the extremities.
```

**A7. 邓婵玉 (Deng Chanyu - 巨力女王)**
```
A full-body character portrait rendered in traditional Chinese ink wash painting style on pure white background, suitable for visual novel sprite cutting. Sharp, professional brushstrokes with high contrast between heavy charcoal ink and pale parchment tones. Warm vermilion red and tarnished gold accents throughout—the only warm-palette character. 2D game sprite style with cinematic shadows.

The character is Deng Chanyu, the Empress of Giant Strength, queen of the Lilliputians and a political powerhouse of the Flaming Mountains faction. She is a tall, voluptuous woman of mature, commanding beauty—the kind that fills a room before she speaks. Her face is regally striking: high cheekbones, a strong jaw softened by full lips, and a gaze that could end a political career. Her eyes are deep amber-gold with faint crimson veins radiating from the pupils—the subtle, permanent mark of Kua Fu's giant blood coursing through her veins.

Her dark hair is swept into an elaborate imperial updo, crowned by a magnificent phoenix headdress constructed from scavenged gold, tarnished wire filigree, and broken spirit-circuit chips repurposed as jeweled eyes in the phoenix motifs. The crown is both beautiful and brutal—a trophy assembled from a conquered world's scraps.

Her most distinctive feature is the faint vermilion vein patterns visible on her forearms and the backs of her hands, like hairline cracks of magma beneath porcelain skin. These are the physical traces of the forbidden giant blood she consumed, and they pulse brighter when her emotions surge. In her human form, they are subtle—a warning written in her flesh.
```

**A8. 九天玄女 (Jiu Tian Xuan Nv - 执棋的智者)**
```
A full-body character portrait rendered in traditional Chinese ink wash painting style on pure white background, suitable for visual novel sprite cutting. Sharp, professional brushstrokes with high contrast between heavy charcoal ink and pale parchment tones. Deep cinnabar red and dark antique bronze accents throughout—austere and ceremonial, not opulent. 2D game sprite style with cinematic shadows.

The character is Jiu Tian Xuan Nv, the Chess-Playing Sage—an ancient Phoenix Goddess who presides over war, military strategy, and celestial numerology. She is the final examiner, the last remnant of divine order in a world of chaos. She has a stunningly beautiful, majestic countenance that radiates supreme authority and timeless wisdom—the face of one who has commanded armies and calculated the fate of nations. Her skin is warm ivory with an almost imperceptible inner luminance. Her eyes are dark and deep-set, piercingly intelligent, with faint hexagram patterns barely visible in the irises—the mark of a celestial strategist whose gaze reads probability itself.

Her dark hair is swept into an elaborate, severe traditional coiffure befitting a war goddess, secured with dark bronze military-style ornamental pins. Crowning her head is a magnificent, open-faced Imperial Phoenix Crown (Fengguan)—a structured masterpiece of filigreed dark bronze, antique gold, and red jade nodules that function as dim spirit-circuit nodes. The crown frames her face without covering it, its phoenix motifs sharp and angular like weapons rather than jewelry.

Her most distinctive feature is the long ribbons of "Data-Feathers" flowing behind her—fiber-optic silk streamers in deep cinnabar and ink-black that trail elegantly from her crown and shoulders, displaying faint tactical hexagram patterns that shift like slow calculations. In one hand, she holds an elegant jade command tablet (Hu) at chest height, the symbol of absolute imperial military authority.
```

**A9. 白骨精 (Bai Gu Jing - 墨骨画皮)**
```
A full-body character portrait rendered in traditional Chinese ink wash painting style on pure white background, suitable for visual novel sprite cutting. Sharp, professional brushstrokes with high contrast between heavy charcoal ink and pale parchment tones. Bone-white and ink-black palette with pale jade-green as the sole eerie accent color. 2D game sprite style with cinematic shadows.

The character is Bai Gu Jing, codename "Ink-Bone Painted Skin"—a ghost-type double agent and apex infiltrator, a fusion of soul and Dao-tech machinery engineered into the perfect disguise. In her default "Painted Skin" form, she presents as an unsettlingly flawless cold beauty. Her face is porcelain-perfect—too perfect, every feature mathematically symmetrical, every contour unnervingly smooth, as if sculpted rather than born. Her skin is pale to the point of artificiality, with a faint waxy luminance that registers as wrong before the conscious mind can identify why. Her eyes are pale jade-green, faintly luminous even in shadow, and utterly still—they track movement with mechanical precision but never betray emotion. The irises are too uniform in color, too clean, like polished stone rather than living tissue.

Her long black hair flows and cascades like spilled ink, heavy and liquid in its movement. Threaded through the strands at irregular intervals are hair-thin metallic wires that catch the light—data filaments from her Dao-tech internal structure, the one element her disguise cannot fully conceal. The hair moves with an almost-natural quality, but occasionally a section shifts with too-perfect uniformity, as if each strand is individually controlled.

Her most distinctive feature is the network of hairline fracture seams barely visible at key joints—along her jawline, at her wrists, at the sides of her neck—where the "painted skin" exterior doesn't quite seal over the dark mechanical infrastructure beneath. In normal lighting these seams are nearly invisible, but at certain angles a sliver of ink-black mechanical structure or polished bone-white material is visible underneath, like glimpsing a puppet's joints through the silk.
```

**A7b. 邓婵玉·巨化形态 (Deng Chanyu - Giant Blood Awakened)**
```
A full-body character portrait rendered in traditional Chinese ink wash painting style on pure white background, suitable for visual novel sprite cutting. Sharp, professional brushstrokes with high contrast between heavy charcoal ink and pale parchment tones. Searing vermilion and molten gold palette—every surface radiates heat. 2D game sprite style with cinematic shadows.

The character is Deng Chanyu in her Giant Blood Awakened form—the forbidden power of Kua Fu's titan blood fully unleashed. She retains her human shape but her presence has become colossal and crushing, as if gravity itself bends around her. Her regally striking face is flushed with exertion, the veins at her temples visibly pulsing. Her eyes have transformed: the amber-gold irises are now blazing molten gold, the crimson veins that once subtly radiated from her pupils have consumed the entire sclera in a web of angry red—the eyes of something that is no longer entirely human.

Her elaborate imperial updo has partially collapsed from the transformation's violence, dark hair cascading in heavy, sweat-dampened strands across her shoulders. The phoenix headdress remains but is cracked and shifted askew, its scavenged gold warped by heat, the spirit-circuit chip jewels overloading and sparking.

The vermilion vein patterns that were once subtle traces on her forearms have erupted across her entire body—neck, collarbones, shoulders, arms, every visible inch of skin now shows the angry red-gold vein network of Kua Fu's blood burning beneath the surface. Her skin radiates visible heat distortion, like air above sun-baked stone. Her hands are clenched, the veins on them glowing brightest of all.
```

---

### Module B: 动作/姿态 (Pose & Action)

> 定义角色的身体姿态和动态。替换此模块即可生成同角色不同姿势。

**B1. 站立 — 正面平视**
```
She stands upright facing the viewer directly, weight evenly distributed, arms relaxed at her sides. Her posture is confident and composed, projecting quiet authority. The full body is visible from head to toe.
```

**B2. 站立 — 侧身回眸**
```
She stands with her body angled three-quarters away from the viewer, turning her head back to look over her shoulder. One hand lightly touches the opposite arm. The pose conveys a mix of departure and lingering connection. The full body is visible from head to toe.
```

**B3. 战斗 — 施法蓄力**
```
She takes an aggressive forward stance, one hand extended forward with spirit Qi energy gathering visibly around her palm, the other hand pulled back channeling power. Her hair and garments billow from the energy surge. Her weight shifts onto her front foot, ready to unleash an attack.
```

**B4. 战斗 — 持武器**
```
She grips a [WEAPON: sword/staff/talisman] in a ready stance, weapon held at an angle suggesting both defense and imminent strike. Her body is tensed with controlled power, feet planted shoulder-width apart. Faint spirit energy trails along the weapon's edge.
```

**B5. 休闲 — 坐姿**
```
She sits casually on a makeshift seat, one leg tucked beneath her and the other hanging free. One hand rests on her knee while the other supports her chin. Her posture is relaxed and unguarded, a rare moment of peace.
```

**B6. 行走 — 前行**
```
She strides forward purposefully, mid-step with one foot ahead. Her robes sway with the movement, and one arm swings naturally while the other holds a [ITEM/empty]. The pose captures a moment of determined travel through the wasteland.
```

**B7. 防御 — 护身结界**
```
She raises both hands before her, conjuring a translucent barrier of spirit energy. Ancient runes orbit the protective shield in slow rotation. Her stance is wide and braced, feet firmly planted, expression focused and defiant.
```

**B8. 行礼 — 抱拳揖礼**
```
He stands facing the viewer directly, performing a traditional Chinese fist-and-palm salute—left open palm wraps around right fist, held at chest height. Elbows slightly out, spine straight, chin dipped in a brief, dashing bow. The gesture is performed with casual elegance rather than rigid formality, as if greeting an old rival with equal parts respect and amusement. The full body is visible from head to toe.
```

**B9. 悬浮 — 端庄飘逸**
```
She floats in a graceful, upright posture, hovering slightly above where the ground would be. Her arms rest naturally at her sides or one hand is raised in a subtle guiding gesture. Her tattered robes and hair drift gently as if suspended in still water. The pose is serene and otherworldly, conveying an entity untouched by gravity. The full figure is visible.
```

**B10. 站立 — 侧身平视**
```
He/She stands in a strict 90-degree side profile view, looking straight ahead off-screen, NOT looking at the viewer. The face is in complete profile. The posture is balanced with one foot slightly forward, emphasizing the clean silhouette and the layered depth of clothing and gear. One arm hangs relaxed, fully exposing the side profile. The full body is visible from head to toe.
```

**B11. 站立 — 背身**
```
He/She stands facing completely away from the viewer, showing their back. The face is completely hidden from view—they are NOT looking back over their shoulder. Shoulders are squared, and feet are planted shoulder-width apart. This angle emphasizes the back details of the outfit, the texture of the hair from behind, and any equipment strapped to the back or waist. The full body is visible from head to toe from behind.
```

**B12. 站立 — 微俯视**
```
She stands upright facing the viewer, chin tilted slightly downward so her gaze falls upon the viewer from above. Shoulders are straight, arms relaxed at her sides. The posture is statuesque and untouchable, projecting dignified indifference. The full body is visible from head to toe.
```

---

### Module C: 表情 (Expression)

> 定义面部表情和微动态。替换此模块即可生成同姿势不同表情。

**C1. 平静 (Neutral)**
```
Her expression is calm and composed. Her gaze meets the viewer steadily with quiet confidence. Her lips rest in a neutral line, neither smiling nor frowning. Her eyes are clear and focused.
```

**C2. 微笑 (Gentle Smile)**
```
A gentle, knowing smile plays across her lips. Her eyes soften with warmth, crinkling slightly at the corners. The expression conveys compassion and quiet wisdom.
```

**C3. 怒 (Angry)**
```
Her brows furrow sharply and her jaw tightens with barely restrained fury. Her eyes blaze with intensity, pupils contracted. Her nostrils flare slightly and her lips press into a thin, dangerous line.
```

**C4. 悲 (Sad)**
```
Her eyes are downcast, glistening with unshed tears rendered in delicate ink wash strokes. Her brows tilt upward in sorrow. Her lips tremble almost imperceptibly, and her whole expression carries the weight of ancient grief.
```

**C5. 惊 (Surprised)**
```
Her eyes widen with genuine shock, eyebrows raised high. Her lips part slightly in an involuntary gasp. Her brows lift sharply and her pupils dilate, every facial muscle locked in a frozen instant of disbelief.
```

**C6. 冷笑 (Cold Smirk)**
```
One corner of her mouth curves upward in a sardonic half-smile. Her eyes narrow with calculating amusement, conveying superiority and veiled threat. The expression is controlled and deliberate.
```

**C7. 坚定 (Determined)**
```
Her jaw sets firmly, chin slightly raised. Her eyes burn with unwavering resolve, focused on something beyond the viewer. Her brows lower in concentrated determination, every line of her face speaking of iron will.
```

**C8. 疲惫 (Exhausted)**
```
Dark circles shadow her eyes, which are half-lidded with fatigue. Her features sag slightly, the usual sharpness softened by weariness. Yet a stubborn spark persists in her gaze, refusing to surrender despite obvious depletion.
```

**C9. 潇洒从容 (Dashing Smirk)**
```
A confident, easygoing smirk plays across the lips, one eyebrow raised ever so slightly. The eyes glint with playful mischief and quiet self-assurance. The expression says this person could fight you or buy you a drink, and hasn't decided which yet.
```

**C10. 高冷端庄 (Aloof Dignity)**
```
Her expression is calm, aloof, and dignified. Her gaze is level and appraising, as if measuring the worth of everything before her. Her lips are set in a composed, faintly imperious line. The look conveys ancient wisdom wrapped in cool detachment.
```

**C11. 清冷哀婉 (Frost & Sorrow)**
```
Her expression is cold and untouchable, yet beneath the frost lies unmistakable sorrow. Her pale eyes look downward at the viewer with dignified indifference, but the faintest tension at the corners of her mouth betrays grief held under absolute control. She appears as one who has long ceased to weep, not from healing, but from exhaustion.
```

**C12. 空灵哀怨 (Ethereal Melancholy)**
```
Her expression is hollow and hauntingly sorrowful, as if gazing at something the living cannot perceive. Her dark eyes are half-lidded and distant, brimming with quiet grief that has aged beyond tears. Her lips are slightly parted, frozen mid-sigh. The overall look is ghostly and detached—present in body but absent in spirit.
```

**B13. 悬浮 — 重压支配**
```
She hovers above the ground in a heavy, dominant posture—not the ethereal floating of a spirit, but the raw gravitational defiance of something too powerful to remain earthbound. Her body hangs slightly forward, arms spread wide at her sides with fingers splayed, as if pressing down on the world beneath her. Heat distortion ripples the air around her. The pose radiates overwhelming, crushing physical dominance. The full figure is visible.
```

**C13. 傲慢蔑视 (Imperious Contempt)**
```
Her expression radiates absolute, withering contempt. Her eyes look down at the viewer with the casual cruelty of one who considers all beneath her unworthy of even anger. One eyebrow arches ever so slightly. Her lips press into a thin, dismissive line, curled with faint disgust. This is not cold indifference—it is active, scorching disdain.
```

---

### Module D: 服装/造型 (Outfit & Attire)

> 定义角色的穿着和装饰。替换此模块即可生成换装变体。

**D1. 道法战甲 (Dao-Tech Combat Gear)**
```
She wears a fusion of traditional Hanfu armor with salvaged industrial plates. Daoist talismans serve as makeshift armor reinforcements, inscribed with protective runes that glow faintly with cyan energy. Tattered silk underlayers peek through gaps in the mechanical plating. Leather straps and buckles secure scavenged components. A glowing spirit Orb power source sits at her waist. Practical combat boots complete the ensemble, scuffed and worn from countless battles.
```

**D2. 修行道袍 (Cultivation Robes)**
```
She wears flowing traditional Daoist robes in muted ink-wash tones, layered in the classical style with wide sleeves that drape elegantly. The fabric bears subtle circuit-pattern embroidery visible only when spirit energy flows through it. A simple cloth belt cinches the waist, holding a jade pendant and small pouches of cultivation materials. Her feet are clad in simple cloth shoes suited for meditation and mountain paths.
```

**D3. 废土猎装 (Wasteland Hunter)**
```
She wears a patched leather duster over a fitted inner garment of reinforced fabric. Multiple utility belts cross her torso, holding tools, vials, and salvaged components. A half-mask of tarnished metal hangs around her neck for sandstorm protection. Fingerless gloves expose spirit circuit tattoos on her hands. Heavy-soled boots with metal toe caps are built for rough terrain traversal.
```

**D4. 神女礼服 (Divine Ceremonial)**
```
She wears an elaborate ceremonial gown that recalls the height of the divine era. Layers of gossamer silk in gold and white flow from her shoulders, though the fabric shows signs of age and careful mending. Intricate spirit circuit embroidery traces constellations across the skirt. An ornate headpiece crowns her hair, its jewels still flickering with residual divine energy. Long trailing ribbons extend from her sleeves, floating with an otherworldly lightness.
```

**D5. 日常便装 (Casual Daily)**
```
She wears a simple modified qipao-style top over practical trousers, the fabrics a patchwork of salvaged materials in muted earth tones. A light scarf wraps loosely around her neck. The outfit is unadorned except for a single small spirit Orb worn as a pendant, its glow providing the only color accent. The look is practical, comfortable, and suitable for daily life in a settlement.
```

**D6. 重伤/破损 (Battle-Damaged)**
```
Her clothing is torn and singed from recent combat. One sleeve is missing entirely, exposing the spirit circuit tattoos beneath. Scorch marks and cuts mar the remaining fabric. Armor plates are cracked or missing. Bandages wrap hastily around one arm. Despite the damage, she wears the destruction with dignity, each tear evidence of survival.
```

**D7. 道袍废土混搭 (Daoist-Wasteland Hybrid)**
```
A tattered traditional Daoist robe serves as the base layer, its once-fine fabric sun-bleached and frayed at the hems, whipping slightly in desert wind. Over it, weathered tactical survival gear is layered pragmatically—a reinforced leather chest piece with buckled straps crossing the torso. Mechanical talisman pouches hang from the belt alongside small spirit orb batteries, their surfaces scratched and dented from hard use. The robes and gear merge organically, suggesting years of gradual modification by a survivor who wastes nothing. Worn boots complete the outfit, scuffed from endless desert roads.
```

**D8. 天女灵裳 (Celestial Spirit Silk)**
```
She wears tattered, flowing celestial silk robes that drift ethereally despite no wind, layered in pale whites and faded silvers. The fabric is translucent at the edges, fraying into ink-wash wisps where it meets air. Faint bronze-toned embroidery traces ancient star charts across the bodice. The garment appears both impossibly ancient and weightless, befitting an artifact spirit that predates living memory.
```

**D9. 月神囚裳 (Lunar Prisoner Robes)**
```
She wears a structured, multilayered Hanfu made of heavy ink-stained silk and frosted translucent fabrics in cold silver and ash-white tones. Rusted silver lunar-phase plates are mounted along the collar and belt like ceremonial armor that has oxidized over centuries. Her sleeves are extraordinarily long, drifting like tattered battle flags, their frayed ends dissolving into ink wisps. Faint traces of chain-like spirit restraints are woven into the fabric's seams, barely visible—the physical manifestation of her binding curse.
```

**D10. 幽谷鬼裳 (Spectral Ghost Silk)**
```
She wears sweeping, multilayered flowing robes made of semi-transparent spectral silk in ashen whites and faded ink-black tones. The fabric is impossibly thin and weightless, drifting and billowing as if submerged in invisible water. The edges of every layer are tattered and fraying, dissolving into calligraphy-like ink wisps that trail behind her like slow-burning smoke. Faint spirit-binding seals are stitched into the inner lining in barely visible pale blue thread—marks of her master's dominion. The robes carry no ornament, no color, no warmth; they are the garments of one who belongs to the realm of the dead.
```

**C14. 暴怒失控 (Berserker Fury)**
```
Her face contorts with primal, uncontrollable rage—not the calculated anger of a ruler, but the raw, involuntary fury of a body pushed beyond its limits. Her brows crush together, teeth bared in a snarl that is half battle cry, half scream of pain. Her eyes blaze wide and wild, pupils dilated. Sweat and exertion flush her features. The expression is terrifying not because she chose it, but because she cannot stop it.
```

**C15. 庄严睿智 (Majestic Wisdom)**
```
Her expression is solemn, majestic, and deeply wise. Her dark eyes regard the viewer with the quiet, absolute authority of a goddess who has never needed to raise her voice. Her brows rest in a composed, level line. Her lips are set in a firm, regal curve—not cold, but beyond the need for warmth. The look conveys unquestionable divine legitimacy: this is the face of cosmic law made flesh.
```

**C16. 空洞冷艳 (Hollow Beauty)**
```
Her expression is cold, beautiful, and completely hollow. Her pale eyes regard the viewer with clinical detachment—not hostile, not curious, simply processing. Her lips rest in a faint, practiced smile that does not reach her eyes. The face is a masterwork of mimicry: every element individually correct, but the totality registers as uncanny, as if the concept of "beautiful woman" was perfectly executed by something that has never actually been one.
```

**D11. 废土凤袍 (Wasteland Phoenix Court Robes)**
```
She wears a magnificent, heavy, multilayered traditional Chinese court robe—an Empress's gown of dark silk interwoven with gold thread and trimmed with thick salvaged fur at the collar and cuffs. The robe is styled with commanding femininity: wide structured shoulders, a cinched waist bound by a broad ceremonial belt of scavenged metal plates and woven crimson silk, and deep layered folds that cascade to the floor. Tarnished gold phoenix motifs are embroidered across the bodice, their eyes replaced by tiny spirit-circuit chips that flicker with residual power. The neckline is cut deliberately low in the traditional court style, framing an ornate pendant of fused gold and jade. Beneath the heavy outer layer, inner silk of deep vermilion is visible at the slits and hem. Elaborate golden-heeled shoes peek from beneath the trailing hem, anchoring the full imperial figure from crown to sole.
```

**D12. 崩裂凤袍 (Ruptured Phoenix Regalia)**
```
Her magnificent court robe has been destroyed by the transformation from within—the dark silk split open across every seam as her body surged with titan power. The wide structured shoulders are torn apart, heavy fabric hanging in strips. The ceremonial belt of scavenged metal has buckled and snapped, plates bent outward like something erupted through them. Gold thread phoenix embroidery is stretched to breaking, the circuit-chip eyes sparking and overloading. Beneath the ruined outer layers, the deep vermilion inner silk clings to her frame, the only layer that survived intact. Her golden-heeled shoes have cracked open at the soles from the force of her transformation, barely holding together.
```

**D13. 凤翼帝袍 (Phoenix-Wing Empress Robe)**
```
She wears a grand "Phoenix-Wing Empress Robe"—a structured, heavy ceremonial garment of flowing ink-stained silk in deep charcoal and muted cinnabar red. The shoulders are accented with stylized, sharp metal-feather plating in dark bronze that extends outward like folded raptor wings. The robe is layered and architectural, cinched at the waist by a broad ceremonial belt of dark bronze plates inscribed with barely visible strategic hexagrams. Her sleeves are extensive and trailing, their weighted silk drifting like folded wings. Beneath the outer charcoal layers, deep cinnabar red inner silk is visible at the openings. The robe carries the authority of ancient military ritual, not courtly luxury. Sturdy ceremonial shoes of dark bronze-capped silk are visible beneath the trailing hem, anchoring the full figure from phoenix crown to sole.
```

**D14. 画皮潜行装 (Painted Skin Infiltrator)**
```
She wears a form-fitting, layered ensemble designed for infiltration—a sleeveless dark inner suit of matte technical fabric that clings to her frame, overlaid with a flowing outer robe of ink-black silk slit high at the thighs for mobility. The robe's surface bears faint patterns that shift between calligraphy and circuit traces depending on the angle. A slender belt of polished bone-white segments cinches her waist, each segment inscribed with barely visible Dao-tech runes. Her forearms are wrapped in dark fabric arm guards that conveniently conceal the wrist-seam fracture lines. Elegant dark silk boots with reinforced soles rise to mid-calf, practical enough for silent movement, anchoring her uncannily perfect figure from head to toe.
```

---

### 组合示例

**示例 1：羲和 · 站立正面 · 坚定 · 道袍废土混搭**
> 将 A2 + B1(男) + C7(男) + D7 拼接，末尾加约束条款

```
A full-body character portrait rendered in traditional Chinese ink wash painting style on pure white background, suitable for visual novel sprite cutting. Bold, sharp brushstrokes with dramatic ink splatter accents conveying raw energy. The color palette is muted charcoal and aged parchment, with neon cyan spirit-energy highlights and subtle gold flickers around the eyes.

The character is Xihe, codename "Ghost Hand," an 18-to-22-year-old male wasteland scavenger with the roguish charisma of a young wandering swordsman. He is lean and wiry, his body hardened by years of survival in the Flaming Mountains. His face is strikingly handsome in a rough, untamed way—sharp jawline, high cheekbones weathered by desert wind, and a faint cocky half-grin that suggests he knows something you don't. His most arresting feature is his heterochromatic eyes: the left eye is deep oceanic blue, the right eye is cold silver like moonlight on steel. Both gleam with fierce intelligence and restless cunning. A faint green third-eye mark glows on his forehead, barely visible, like a half-forgotten divine seal. His dark hair is wild and wind-swept, partially tied back with a frayed cord but with rebellious strands falling across his brow. His "Ghost Hands" are his signature—both forearms and hands are laced with intricate spirit-ink circuit tattoos that pulse with dim cyan luminescence, ancient calligraphy branded directly into his meridians, glowing brighter at the fingertips.

He stands facing the viewer directly, squared shoulders, feet planted shoulder-width apart. One hand hangs relaxed at his side while the other rests on his belt near a talisman pouch. His posture is upright yet loose, projecting quiet confidence without stiffness. The full body is visible from head to toe.

His jaw sets firmly, chin slightly raised. His heterochromatic eyes burn with unwavering resolve, staring through the viewer as if challenging fate itself. Every line of his face speaks of iron will forged in the wasteland.

He wears a tattered traditional Daoist robe as the base layer, its once-fine fabric now sun-bleached and frayed at the hems. Over it, weathered tactical survival gear is layered pragmatically—a reinforced leather chest piece, buckled straps crossing his torso. Mechanical talisman pouches hang from his belt alongside small spirit orb batteries, their surfaces scratched and dented from hard use. The robes and gear merge organically, suggesting years of gradual modification by a survivor who wastes nothing. Worn boots complete the outfit, scuffed from endless desert roads.

The overall atmosphere conveys a lone wolf wandering a fallen world—dangerous, magnetic, and untamed. The aesthetic marries traditional ink painting with subtle sci-fi elements. No text, no watermark, no logos. Correct human anatomy, sharp focus, clean white background with no environmental elements.
```

**示例 2：通用女主 · 战斗施法 · 怒 · 废土猎装**
> A3 + B3 + C3 + D3（使用原文，代词为 She/Her）

**示例 3：羲和 · 坐姿 · 疲惫 · 日常便装**
> A2 + B5(男) + C8(男) + D5(男)（将代词替换为 He/His）

---

### 自定义扩展指南

需要新增模块时，遵循以下规则：
1. **每个模块 40-80 词**，保持信息密度
2. **模块之间零耦合**：动作不描述表情，表情不涉及服装，服装不限定姿势
3. **编号连续**：新动作用 B14, B15...；新表情用 C17, C18...；新服装用 D15, D16...
4. **命名格式**：`[编号]. [中文名] ([English Name])`
5. **性别代词**：B/C/D 模块默认用 She/Her，用于男性角色时替换为 He/His。服装模块尽量使用中性描述以减少替换量
6. **定妆标配**：每个角色定稿时产出「定妆照」提示词 + 「变体替换段落库」

---

### 三视图模板 (Character Turnaround Sheet)

> 用于生成角色设定参考用的三视图（正面 / 侧面 / 背面），统一纯白背景、统一姿态，便于游戏开发建模和动画参考。
> **使用方法**：将下方模板中的 `[A模块]` 和 `[D模块]` 替换为对应角色的身份和服装模块即可。表情统一使用 C1（平静），不使用个性化表情以保持参考图的中性。

**三视图组合公式**：
```
[A模块(身份)] + [三视图姿态描述] + [C1(平静)] + [D模块(服装)] + [三视图约束条款]
```

**三视图姿态描述（替代 B 模块）**：
```
Character turnaround reference sheet showing three identical views of the same character side by side on a single image: front view (facing viewer), side view (strict 90-degree left profile), and back view (facing away). All three poses are standing upright in a neutral, relaxed T-pose variant with arms slightly away from the body to reveal outfit details. Feet are shoulder-width apart. The three views are evenly spaced and aligned at the same height, presented as a professional character design reference sheet.
```

**三视图约束条款（替代普通约束条款）**：
```
Professional character design turnaround reference sheet. All three views must show the exact same character, same outfit, same proportions, same colors—perfect consistency across views. Clean solid white background with no environmental elements, no shadows on the ground. No text, no watermark, no logos, no labels. Correct human anatomy, sharp focus, clean linework. The image should read as a single cohesive reference sheet, not three separate illustrations.
```

---

## 三、地图美术 (Map Assets)

### 用途
生成世界地图节点图标、区域拼贴、装饰物。用于 `MapViewController` 渲染。

### zimage-turbo / flux2-klein 提示词模板

```
Create a world map element in the style of traditional Chinese landscape ink wash painting, designed for top-down or isometric map view. The composition should be minimalist, leveraging negative space strategically as in classical landscape paintings. The textured Xuan paper background provides the foundation.

The map element depicts [element type: city node/dungeon entrance/mountain region/wasteland area] using simple brush strokes and ink gradients. Geometric shapes are kept simple and clear for game functionality, but rendered with the organic imperfection of hand-painted artwork.

Technological details manifest subtly: spirit Qi flow lines connect various locations like luminescent threads across the map. Node markers glow softly to indicate interactivity. Faded Daoist seal stamps mark points of significance, their red ink barely visible on the aged paper. Mechanical ruins appear as dark silhouettes on the horizon, while neon cyan path indicators show available travel routes.

The overall view should suggest a desolate wasteland from above, with remnants of ancient civilizations visible in the composition. The aesthetic perfectly balances post-apocalyptic beauty with the functional requirements of a tactical game map rendered in ink painting style.
```

### 地图元素类别

#### 1. 节点图标 (Node Icons)

```
Design a map node icon in the style of a circular Daoist seal stamp, rendered with characteristic brush stroke borders. The center features a symbol representing [city/dungeon/shrine/ruin location type]. A subtle glow effect emanates from the icon, color-coded by type. The background maintains the ink wash aesthetic. The icon should be optimized for 64x64 pixels while remaining crisp and recognizable.

Variant guidelines:
- City nodes: building silhouette with cyan glow suggesting active spirit energy
- Dungeon nodes: cave entrance with red warning glow indicating danger
- Shrine nodes: torii gate with golden divine glow showing sacred ground
- Wasteland nodes: cracked earth pattern with dim gray glow showing desolation
```

#### 2. 区域拼贴 (Region Tiles)

```
Create a large region tile suitable for seamless tiling in a game map, rendered entirely in ink wash painting style. The terrain type depicted is [mountain/forest/desert/ruins]. Details should be minimalist, using atmospheric perspective to suggest depth rather than explicit detail. Edges must fade naturally to ensure seamless tiling with adjacent pieces.

The color palette remains monochromatic with a single accent color appropriate to the terrain type. The tile should work at 512x512 or 1024x1024 resolution, maintaining the artistic ink painting character at both sizes. The overall effect should create a cohesive map when multiple
 tiles are placed together, much like traditional Chinese landscape scroll paintings.
```

#### 3. 路径装饰 (Path Decorations)

```
Design travel path markers in ink wash line art style that can be rendered with vector lines (Shapes Polyline). The path represents [railway tracks/ancient canals/deteriorated roads], drawn with characteristic brush strokes in a dashed or segmented style.

Glowing spirit Qi particles drift along the path's length, indicating it's still energetically active despite physical decay. The weathered appearance suggests centuries of use and neglect. These path markers should connect map nodes visually while maintaining the game's distinctive ink painting aesthetic.
```

---

## 四、道具图标 (Item Icons)

### 用途
生成背包物品、装备、消耗品图标。用于 `ItemDatabase` 和 UI 显示。

### zimage-turbo / flux2-klein 提示词模板

```
Create an item icon rendered as a traditional Chinese still life painting in ink wash style. The object study should focus on [item name and type], showcasing [material characteristics] with detailed brush strokes and subtle ink gradients for shading. The background should be pure white or transparent, with the item centered in a 256x256 pixel composition. The silhouette must be clear and immediately recognizable for game UI purposes.

For magical or technological items, incorporate subtle glow effects and circuit patterns where appropriate. Daoist symbols should appear naturally on talismans and mystical objects. Tech items show weathering and decay, suggesting they've been scavenged from pre-collapse civilization. The overall aesthetic conveys found artifacts and post-apocalyptic treasures—ancient relics still containing residual power, rendered with ink painting realism.

The icon must serve both artistic and functional purposes: beautiful enough to appreciate individually, yet clear enough to read at small sizes in game interface.
```

### 道具类别示例

#### 1. 灵珠 (Spirit Orbs) - 核心货币

```
Illustrate a glowing spirit Orb in ink wash painting style. The orb appears as a translucent sphere containing swirling energy in cyan or gold tones. Within the sphere, spirit energy moves in patterns suggesting both liquid flow and gaseous diffusion. A faint Daoist rune serves as the core, visible through the translucent outer layers. The orb emanates an ethereal glow with an ink wash rendered aura surrounding it, suggesting mystical power beyond mere illumination.

Rarity indicates different visual treatments:
- Common orbs: shows dim white glow with simple internal patterns
- Rare orbs: exhibits cyan glow with clearly visible runes in complex arrangements
- Legendary orbs: displays rich gold glow with intricate rune arrays suggesting immense stored power
```

#### 2. 武器/装备 (Weapons/Gear)

```
Render a [weapon type: sword/staff/talisman] as a detailed weapon study in ink wash painting tradition. The design merges traditional Chinese weapon forms with mechanical modifications, creating something that could only exist in a world where cultivation techniques persist alongside salvaged technology.

The weapon features spirit circuit engravings along its length, visible as faint glowing lines when active. The metal shows appropriate weathering and patina suggesting age and heavy use. Fabric elements like wrappings or tassels appear torn and frayed. The weapon's edge or striking surface glows subtly, indicating Qi infusion and readiness for spiritual combat.

The overall impression should be of a treasured artifact from before the divine collapse, now repurposed by survivors who understand only fragments of its original power.
```

#### 3. 消耗品 (Consumables)

```
Depict a consumable item [elixir bottle/food/crafting material] as an ink wash still life. If it's a container, show simple ceramic or wooden construction with paper labels bearing brush calligraphy characters. Magical or alchemical items should have a subtle internal glow visible through semi-transparent materials. Mundane items should emphasize realistic texture and material properties, celebrating the beauty of ordinary objects through ink wash techniques.

The artwork should suggest these are valuable resources in a desperate world—carefully preserved, possibly old, but still functional and precious to those who understand their use.
```

---

## 五、CG 插画 (Event CG)

### 用途
生成关键剧情事件的全屏插画。用于 Naninovel 的 `@back` 命令。

### zimage-turbo / flux2-klein 提示词模板

```
Create a masterpiece-quality illustration in traditional Chinese ink wash painting style, designed as a cinematic full-screen event CG for a visual novel. The composition should employ dramatic use of negative space and emotional atmosphere, drawing on techniques from classical landscape painting masters. The color palette maintains a monochromatic base with strategic color accents only on the most important elements to draw the eye.

The scene depicts [detailed scene description including character actions and environmental details]. The mood is [epic/tragic/romantic/mysterious], conveyed through careful composition and the interplay of light and shadow created through ink gradients and density variation.

The background environment is [wasteland ruins/ancient temple/neon megacity/mountain peak], rendered with detailed ink wash landscape techniques. Atmospheric perspective creates depth, while the fusion of industrial decay with natural beauty reinforces the game's unique aesthetic. Spirit Qi energy flows should be visible within the environment itself, not just around characters, showing how spiritual power permeates this fallen world.

Lighting comes from [sunset/moonlight/neon glow/divine radiance], creating dramatic shadows and ethereal backlighting effects. Volumetric spirit energy mist adds depth and atmosphere, while ink wash rendered light rays pierce through environmental details.

The overall atmosphere embodies post-apocalyptic grandeur and melancholic beauty. This is a world where the divine era fades but human perseverance continues. Every element should serve the emotional storytelling, using the environment itself as a narrative device.
```

### CG 场景类别

#### 1. 战斗场景 (Combat)

```
Illustrate [character name] engaged in Dao-Tech combat, their cultivation techniques visible as spirit Qi energy clashing with opponent's power. Use ink splash effects to represent the impact of spiritual attacks meeting resistance. Character poses should be dynamic, with flowing robes and hair capturing the motion and energy of combat.

The background consists of mechanical ruins from pre-collapse civilization, now serving as the battlefield. Dramatic lighting emphasizes the conflict's intensity. The scene should convey both the beauty and violence of cultivation combat in a fallen world, where gods' techniques are wielded by mortal survivors.
```

#### 2. 对话场景 (Dialogue)

```
Depict two characters in meaningful conversation, located in [abandoned temple/neon-lit bar/wasteland camp]. Their emotional expressions and body language tell as much of the story as any dialogue would. Atmospheric lighting sets the mood—perhaps harsh neon for conflict, soft divine glow for revelation, or dim firelight for intimacy.

The environment itself should contribute to storytelling: details in the background reveal character, history, and world-building without requiring explicit narration. Use depth of field effects through ink wash blur techniques to keep focus on the characters while suggesting a rich, detailed world around them.
```

#### 3. 探索发现 (Discovery)

```
Show a character discovering [ancient artifact/lost relic of the gods/one of the Ten Divine Artifacts]. Capture their sense of awe through pose and expression. Divine light rays emanate from the discovered object, rendered with ink wash techniques to create ethereal illumination. Sacred geometry patterns may appear in the air around the artifact, suggesting its inherent power and significance.

The composition should feel monumental—this is a watershed moment for the character and potentially the world. The scene balances the grandeur of divine power with the humility of a mortal encountering something far beyond their comprehension.
```

---

## 六、背景图 (Backgrounds)

### 用途
生成场景背景图。用于 Naninovel 的 `@back` 命令或地图底图。

### zimage-turbo / flux2-klein 提示词模板

```
Create a panoramic background landscape in traditional Chinese ink wash painting style, suitable for use as a visual novel scene background or world map base. The composition should use classical landscape painting techniques including atmospheric perspective to create layered depth, with details diminishing appropriately in the distance.

The scene type is [wasteland/ruined city/ancient temple/mountain range/desert expanse], conveying a vast sense of open space. The landscape should embody both desolation and beauty—the characteristic aesthetic of finding grace in decay that defines the game's world. Subtle industrial elements merge with natural features: ancient machinery overgrown by nature, collapsed structures becoming new geological features, spirit energy visible as mist in the valleys.

Specific environmental elements include [crumbling architecture/dead trees/flickering neon signs/weathered deity statues]. Each element receives ink wash rendering with careful attention to texture variation. Surfaces show realistic weathering and age, while vegetation grows wild over abandoned constructions, reclaiming what civilization left behind.

The time of day is [dawn/dusk/night/storm], during [spring/summer/autumn/winter] season. These choices create a melancholic mood appropriate to the narrative moment, finding beauty in decay and post-apocalyptic serenity. The entire composition should feel like a meditation on endings and persistence, rendered with ink painting's characteristic ambiance.

Technical requirements: the aspect ratio should be 16:9 or 4:3 optimized for game display. The composition needs clear separation between foreground, midground, and background layers. Leave appropriate space for UI overlay elements like dialogue boxes. The background should be high enough quality to maintain immersion but not so detailed it distracts from foreground story elements.
```

### 背景类别示例

#### 1. 废土营地 (Wasteland Camp)

```
Render a makeshift survivor camp in the desert wasteland. Tattered tents reinforced with scavenged mechanical supports create shelter from harsh conditions. Spirit Orb tech debris scatters across the ground—broken pieces of the civilization that collapsed. A campfire glows warmly in the center, its light the only warmth in this desolate place.

In the far distance, a ruined city's silhouette breaks the horizon line, too far to reach today but a constant reminder of what was lost. Dust particles drift through the air, caught and illuminated by the low-angle sunlight of late afternoon. The scene should evoke survival, community against odds, and the daily struggle of life in a fallen world.
```

#### 2. 古刹遗迹 (Ancient Temple Ruins)

```
Depict the remains of a collapsed Daoist temple, nature slowly reclaiming what devotion once maintained. Vines grow through cracks in stone walls, wrapping around broken pillars that still bear faint circuit engravings from when divine beings walked the earth. Cracked stone Buddha statues sit in eternal meditation, weathered but not destroyed, patient witnesses to the fall of gods.

Divine light filters through holes torn in the temple roof by time and violence, creating dramatic shafts of illumination through the dusty air. Ethereal spirit Qi mist pools in the lowest areas, suggesting this sacred place retains some measure of its former spiritual power. The scene balances destruction with lingering divinity, abandonment with sacred persistence.
```

#### 3. 霓虹都市 (Neon Megacity)

```
Illustrate a cyberpunk city district rendered with ink wash architecture techniques. Neon signs blazing with Chinese characters provide the primary light sources, their glow reflecting off wet surfaces below. The urban landscape consists of crowded narrow alleys between towering structures, overhead cables creating a web against the night sky.

Rain-slicked streets reflect the multicolored neon lights, creating a mirror world beneath pedestrians' feet. Atmospheric pollution creates a perpetual haze, softening distant details while the foreground remains sharp. Despite the technological trappings, the fundamental composition and rendering technique remain true to ink wash tradition, creating a unique fusion of ancient artistic methods with futuristic urban decay.
```

---

## 七、ComfyUI 工作流建议 (Updated for flux2/zimage)

### 1. 模型选择建议
- **zimage-turbo**: 适合快速迭代、批量生成 UI 组件和图标
- **flux2-klein**: 适合高质量 CG、角色立绘、复杂场景构图

### 2. 提示词结构最佳实践
```
结构建议:
1. 开场定义艺术风格（自然语言完整句）
2. 详细场景/对象描述（叙述性文字）
3. 氛围和情绪塑造（感性描述）
4. 技术要求（如分辨率、构图）
5. 可选的简短 negative（仅必要时）

避免:
- 关键词堆砌
- 过度使用括号和权重
- 冗长的 negative prompt 列表
```

### 3. ControlNet 使用
- **UI 组件**: Canny 保持结构清晰
- **角色立绘**: OpenPose/DWPose 精确控制姿势
- **地图/背景**: Depth 创造空间层次

### 4. 参数建议 (flux2-klein)
- **Guidance Scale**: 3.5-5.0（flux2 对 CFG 更敏感，保持低值）
- **Steps**: 20-30（flux2 收敛快）
- **Sampler**: Euler a 或 DPM++ SDE

### 5. 参数建议 (zimage-turbo)
- **Guidance Scale**: 0（官方推荐零 CFG，高于 0 显著增加生成时间但收益递减）
- **Steps**: 8-12（Turbo 蒸馏模型专为极少步数优化）
- **Sampler**: 默认推荐设置
- **Negative Prompt**: 不使用（模型训练时不包含负面提示词，所有约束放在正面提示词中）

### 6. 批量生成策略
- 使用详细的核心提示词作为 base
- 通过微调叙述性细节创造变体
- Seed 控制用于系列生成（如角色表情集）

---

## 八、资产命名规范 (Asset Naming Convention)

与 `UIAssetDatabase` 和 `MapDatabase` 保持一致：

```
格式: [类型]_[类别]_[名称]_[变体].png

示例:
- UI_Btn_Confirm_Wasteland.png (废土风格确认按钮)
- Char_Xihe_Neutral_FullBody.png (羲和中性表情全身立绘)
- Map_Node_City_Capital.png (首都城市节点图标)
- Item_Orb_Spirit_Rare.png (稀有灵珠图标)
- BG_Temple_Ruins_Dusk.png (黄昏古刹废墟背景)
- CG_Battle_XiheVsBoss_Epic.png (羲和 vs Boss 战斗 CG)
```

---

## 九、质量检查清单 (Quality Checklist)

生成后必须检查的项目：

### 风格一致性
- [ ] 水墨画笔触明显且自然
- [ ] 宣纸质感存在
- [ ] 色调统一（单色 + 霓虹点缀）
- [ ] 机械与传统元素融合自然

### 技术要求
- [ ] 分辨率符合用途（UI: 256-512px, CG: 1920x1080+）
- [ ] 透明通道正确（UI 组件）
- [ ] 边缘清晰（适合 UIAssetDatabase 识别）
- [ ] 文件大小合理（< 2MB per asset）

### 可用性
- [ ] 在游戏 UI 中清晰可辨
- [ ] 与其他资产搭配和谐
- [ ] 符合场景/角色设定
- [ ] 无版权/风格冲突元素

### flux2/zimage 特定检查
- [ ] 提示词使用自然语言叙述
- [ ] 避免了过度的权重标记
- [ ] 场景描述足够详细具体
- [ ] 艺术风格在提示词中明确表达

---

## 十、提示词撰写实战技巧

### 从关键词到自然语言的转换示例

**❌ 旧式 SD 风格（不适合 flux2/zimage）**:
```
(ink wash:1.4), (Chinese painting:1.3), girl, standing, (Hanfu:1.2), 
(mechanical parts:1.1), post-apocalyptic, glowing eyes, white background
Negative: anime, colorful, 3d, photorealistic
```

**✅ 新式 flux2/zimage 风格（推荐）**:
```
A young woman rendered in traditional Chinese ink wash painting style, 
standing gracefully against a pure white background. She wears modified Hanfu 
robes integrated with salvaged mechanical components, creating a unique fusion 
of ancient elegance and post-apocalyptic necessity. Her eyes glow subtly with 
residual spiritual energy, suggesting cultivation abilities. The artwork employs 
fluid brush strokes characteristic of classical Chinese portraiture, with a 
predominantly monochromatic palette accented by neon cyan details on the 
technological elements.
```

### 关键词转换对照表

| 旧式关键词                  | flux2/zimage 自然语言表达                                                           |
| --------------------------- | ----------------------------------------------------------------------------------- |
| `(ink wash:1.3)`            | "rendered in traditional ink wash painting style with characteristic brush strokes" |
| `(post-apocalyptic:1.2)`    | "set in a post-apocalyptic world where civilization has collapsed"                  |
| `glowing runes`             | "ancient runes that glow softly with residual spiritual energy"                     |
| `mechanical parts`          | "salvaged mechanical components integrated organically into the design"             |
| `monochromatic with accent` | "predominantly monochromatic color palette with strategic cyan accents"             |
| `(masterpiece:1.4)`         | 去除权重，直接在开头说 "Create a masterpiece-quality illustration..."               |

---

**记住**: flux2-klein 和 zimage-turbo 最大的优势是理解自然语言，所以用完整的句子讲故事，而不是堆砌关键词。把你想要的画面用文字详细描述出来，就像在给一位理解力极强的艺术家下达创作委托。
