---
name: character-animation
description: Naninovel 立绘动效规则：位置移动、抖动、缩放、视线、色调等效果的使用规范
metadata:
  tags: naninovel, character, animation, shake, tint, look, easing
---

# 立绘动效规范

> **目的**：让对话场景中的角色立绘"活"起来，避免站桩式对话的呆板感。
> **原则**：动效服务叙事，不要为了动而动。每个动效都应该有对应的情绪或行为动机。

---

## 一、可用动效一览

### 1. 位置移动（pos + easing）

角色在场景中滑动，表达走位、靠近、退后。

```nani
; 基础移动
@char XiHe pos:40 time:0.5

; 带缓动的移动（推荐）
@char XiHe pos:40 time:0.5 easing:EaseOutBack
@char TongLing pos:60 time:0.3 easing:EaseOutBounce

; 从场景外入场
@char TongLing pos:120 time:0
@char TongLing pos:70 time:0.6 easing:EaseOutBack
```

**常用缓动函数**：
| 缓动 | 效果 | 适用场景 |
|------|------|---------|
| `EaseOutBack` | 滑过头再弹回 | 活泼入场、惊讶后退 |
| `EaseOutBounce` | 弹跳落地 | 搞笑角色、兴奋跑来 |
| `EaseInOutSine` | 平滑缓入缓出 | 正常走位、从容移动 |
| `EaseOutQuad` | 快起慢停 | 普通移动 |
| `EaseInBack` | 蓄力后弹走 | 快速离场、被吓跑 |

### 2. 抖动（@shake）

角色震动，表达惊讶、愤怒、害怕、笑到发抖。

```nani
; 轻微震动（惊讶、微愣）
@shake XiHe count:1 power:0.2

; 中等抖动（生气、震惊）
@shake XiHe count:2 power:0.4

; 强烈抖动（愤怒、恐惧）
@shake XiHe count:3 power:0.6

; 持续抖动（直到手动停止）
@shake TongLing count:0 power:0.3 loop!
; ... 对话 ...
@shake TongLing count:0  ; 停止
```

**参数**：
- `count` — 抖动次数（0=持续循环直到停止）
- `power` — 振幅（0.1~1.0，推荐 0.2~0.5）
- `time` — 单次抖动时长（默认 0.15s）

### 3. 缩放（scale）

微调大小，表达靠近镜头、退缩、气势变化。

```nani
; 微微放大（靠近、威压）
@char XiHe scale:1.05 time:0.3

; 微微缩小（退缩、心虚）
@char TongLing scale:0.95 time:0.2

; 恢复原始大小
@char TongLing scale:1 time:0.2
```

**注意**：缩放幅度不宜超过 ±10%，否则看起来像 bug 而非动效。

### 4. 视线方向（look）

角色面朝方向，表达对话目标、回避、思考。

```nani
@char XiHe look:right    ; 转向右边（看铜铃）
@char TongLing look:left  ; 转向左边（看羲和）
@char XiHe look:center    ; 面向正前方（面向玩家）
```

**使用时机**：
- 两人对话时互相 look
- 独白/内心活动时 look:center 面向玩家
- 回避/不想面对时 look 到反方向

### 5. 色调变化（tint）

调整角色亮度/颜色，表达说话/沉默、特殊状态。

```nani
; 变暗（不是焦点、沉默）
@char XiHe tint:#aaaaaa time:0.3

; 恢复正常亮度（开始说话）
@char XiHe tint:#ffffff time:0.3

; 特殊色调（回忆、异象）
@char YuNv tint:#aaccff time:0.5
```

**说话高亮模式**：可在 CharactersConfiguration 中开启 `HighlightWhenSpeaking`，自动实现说话角色亮、其他角色暗的效果，无需手动 tint。

### 6. 外观切换过渡（transition via:）

切换表情/姿态时的过渡效果。

```nani
; 淡入淡出切换表情
@char HouYing.Smile time:0.3

; 带特殊过渡效果
@char YuNv.Sad via:Dissolve time:1
```

---

## 二、场景动效模式

### 入场模式

```nani
; 标准入场：从略偏的位置滑入
@char TongLing pos:80 time:0
@char TongLing pos:70 time:0.5 easing:EaseOutBack

; 急促入场：搞笑/紧急
@char TongLing pos:110 time:0
@char TongLing pos:70 time:0.4 easing:EaseOutBounce

; 缓慢入场：神秘/从容
@char HouYing pos:60 time:0
@char HouYing pos:50 time:1 easing:EaseInOutSine
```

### 离场模式

```nani
; 标准离场
@char TongLing pos:110 time:0.5 easing:EaseInBack
@hide TongLing time:0

; 淡出离场
@hide TongLing time:1
```

### 对话中的微动

```nani
; 说话时微微靠近
@char TongLing pos:68 time:0.3

; 被说中要害，微微后退
@char TongLing pos:72 time:0.2

; 惊讶时抖一下
@shake XiHe count:1 power:0.2
```

---

## 三、使用原则

1. **动效必须有动机**：每个 `@shake`、位置变化都应该对应角色的情绪或行为
2. **少即是多**：不是每句话都要动，关键节点才用动效
3. **幅度克制**：位置偏移 2~5 个单位、shake power 0.2~0.5 就够了
4. **不要阻塞节奏**：动效时间不宜超过 0.5s（除特殊演出外），用 `time:0.3` 为默认
5. **视线要自然**：两人对话时保持互相 look，独白时 look:center
