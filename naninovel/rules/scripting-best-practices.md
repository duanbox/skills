---
name: scripting-best-practices
description: Naninovel v1.20 .nani 脚本完整语法参考与 C# 集成模式
metadata:
  tags: naninovel, scripting, integration, best-practices, v1.20
---

# Naninovel v1.20 脚本规范与集成模式

> **引擎版本**：v1.20.241120（嵌入式包）
> **参考文档**：https://naninovel.com/guide/naninovel-scripts
> **最后校对**：2026-04-04（基于项目内嵌源码验证）

---

## v1.20 Breaking Changes

| 移除命令 | 替代方案 | 说明 |
|----------|---------|------|
| `@style` | `@format` | 文本格式化命令已重命名 |
| `@br` | `<br>` | 换行改用 HTML 标签内联 |

> **重要**：如果在 .nani 脚本中发现 `@style` 或 `@br`，必须迁移为新写法。

---

## 一、脚本语法基础

### 行类型

| 起始符 | 类型 | 示例 |
|--------|------|------|
| `@` | 命令行 | `@back MainBg` |
| `#` | 标签行 | `# MyLabel` |
| `;` | 注释行 | `; 这是注释` |
| 无 | 对话/文本行 | `Xihe: 你好。` |

### 命令语法

```nani
@commandId paramId:paramValue

; 多个参数
@char Kohaku pos:0.5 time:0.3

; 字符串含空格须加引号
@print "Hello World"
```

> 命令名大小写不敏感，但参数名区分大小写。

### 参数值类型

| 类型 | 示例 |
|------|------|
| String | `SimpleText` / `"Text with spaces"` |
| Integer | `1`, `-25` |
| Decimal | `1.0`, `-0.005` |
| Boolean | `true` / `false` |
| Named | `Script001.LabelName` |
| List | `item1,item2,"item three"` |

### 布尔标志简写

```nani
; 以下两行等价
@char Kohaku visible:true
@char Kohaku visible!

; 以下两行等价
@char Kohaku visible:false
@char Kohaku !visible
```

---

## 二、对话与文本

### 标准对话格式

```nani
; 格式：角色ID.外观: 台词
Xihe.Happy: 今天天气不错。
Kohaku: 是的，阳光明媚。
```

### 多角色同时发言

```nani
; 逗号分隔多角色
Kohaku,Yuko: 我们一起出发！

; 通配符 + as 参数自定义显示名
*: 大家一起喊。 as:"众人"

; 单次覆盖显示名
Xihe: 我有话要说。 as:"神秘人"
```

### 旁白（无角色名）

角色配置中将 `Has Name` 设为 false 即可作为旁白角色，对话行不显示角色名。

也可以直接用裸文本行（无 `Speaker:` 前缀）作为旁白，无需角色配置：

```nani
; 裸文本行 = 旁白
矿道深处传来一阵粗重的鼻息。
XiHe: 什么东西......在那边。
```

### ⚠️ 用通用文本，不要用 @print 写对白

> **硬规则**：日常对白必须用通用文本语法（`Speaker: text` 或裸文本行），**不要用 `@print` 命令**。

两种语法走完全不同的代码路径，等待行为差异巨大：

| 语法 | 解析后行为 | 等待玩家点击？ |
|------|-----------|--------------|
| `XiHe: 你好。`（通用文本） | `WaitForInput = true` 在解析期硬编码注入 | **永远等待** |
| `@print "你好。" author:XiHe` | `WaitForInput` 字段默认值 = `ScriptPlayerConfiguration.WaitByDefault` | **看项目配置** |

**源码依据**：`Packages/com.elringus.naninovel/Runtime/Script/Parser/GenericTextLineParser.cs:128` 在解析通用文本行时强制写入 `print.WaitForInput = true`，**完全忽略 `WaitByDefault`**。

#### 项目当前配置

EndGods 的 `Assets/Game/Story/Resources/Naninovel/Configuration/ScriptPlayerConfiguration.asset` 设的是：

```yaml
WaitByDefault: 0   # @print 默认不等待，自动连播
```

所以一旦在剧本里写 `@print`，那一行**不会等玩家点击**，会立刻进入下一句。少量 @print 玩家不易察觉；连续多行 @print 体感是"对白突然加速失控"。

#### 真实事故案例

`P03_Mine_Battle.nani`（序章矿洞战前）原版用 4 行 @print，玩家点过去没察觉。后来扩写到 9 行 @print 时，所有对白一秒内冲完直接弹选项，玩家投诉"自动加速"。修法：把全部 @print 转回 `XiHe: ...` 通用语法，与 P01/P02/P04 一致。

#### @print 的合法使用场景（罕见）

只在以下情况才用 @print：

1. **作者名是变量**：`@print "..." author:{$currentSpeaker}`
2. **故意 wait:false 让两行视觉上连成一句**（极少用，需明确意图）
3. **程序化生成或本地化引用**：`@print |#&SOMEID|`

写新剧本前用 PowerShell `Select-String -Pattern '^@print'` 确认有没有混用 `@print`；CodeReview 时把匹配行当作可疑信号，逐行确认是否属于上述合法场景。

### 内联命令（命令注入文本中）

```nani
; 在文本中间触发命令（方括号语法，省略 @）
Felix: Lorem[char Felix.Happy pos:0.5] ipsum![sfx Explosion] dolor.

; 跳过当前输入等待
Xihe: 这句话不需要点击。[>]
```

### @format 文本格式化（替代已移除的 @style）

```nani
; 设置文本模板，%TEXT% 为正文占位符
@format bold,italic
Xihe: 这段文字会应用格式。

; 恢复默认
@format default

; 换行使用 <br> 标签（@br 已移除）
Xihe: 第一行<br>第二行
```

### 文本标识符 `|#N|`

```nani
; 由编译器自动生成，用于本地化和配音绑定
Kohaku: Hello!|#1| What's up?|#2|

; 引用已有标识符（避免重复翻译）
@print |#&SOMEID|
```

> **规则**：不要手动添加或删除 `|#N|`。这些标识符由 Naninovel 的脚本编译器自动维护，
> 删除它们会破坏本地化和配音关联。如需运行本地化前生成，使用 "Insert Line Keys" 工具。

---

## 三、标签与导航

### 标签定义

```nani
# MyLabel
; 标签后的内容
```

### @goto 跳转语法

```nani
; 同脚本内跳转（本地标签）
@goto #MyLabel

; 跨脚本跳转（从 Scenario 根目录的规范路径）
@goto CommonRoute/Day1/Scene1#Action

; 相对路径
@goto ./Scene2
@goto ../Day2/Scene1

; 通配符（脚本名唯一时可用）
@goto */Prologue

; 跳转到脚本开头（无标签）
@goto AnotherScript
```

### @gosub / @return 子程序调用

```nani
; 调用子程序，执行完后自动返回调用点
@gosub .MySubroutine

; 跳转到另一脚本的子程序
@gosub CommonScenes/Flashback#Start

; ...主流程继续...

# MySubroutine
角色: 这是子程序里的对白。
@return
```

> `@gosub` 会保存返回位置，`@return` 回到 `@gosub` 之后的下一行。
> 适合复用公共场景（如回忆、过场）。

---

## 四、条件与流程控制

### 单行条件

```nani
; 所有命令都支持 if / unless 参数
@choice "选项A" if:score>9000
@print "存活" unless:dead

; 复杂条件用花括号
@bgm Victory if:{ score > 10 | cleared }
```

### 条件块（缩进嵌套，推荐写法）

v1.20 支持基于 **4空格缩进** 的嵌套语法，不需要 @endif：

```nani
@if score>10
    @bgm Victory
    任务完成！
@else if:score>5
    还差一点。
@else
    任务失败。
```

> **缩进硬规则**：必须使用 **4个空格**，Tab 会被忽略。每层嵌套增加 4 个空格。

### 条件块（@endif 写法，兼容但不推荐）

```nani
@if score>10
@bgm Victory
任务完成！
@else if:score>5
还差一点。
@else
任务失败。
@endif
```

### 内联条件（文本中）

```nani
结果：[if score>8] 完美！[else] 还不错。[endif]
```

### @while 循环

```nani
; 条件为真时循环执行嵌套内容
@set attempts=0
@while { attempts < 3 }
    @set attempts++
    尝试第 {attempts} 次...
```

### @random 随机执行

```nani
; 随机选一个嵌套命令执行
@random
    今天天气不错。
    下雨了。
    起风了。
```

### @group 命令归组

```nani
; 在 @random 中将多行命令作为一组
@random
    @group
        @sfx Thunder
        下暴雨了！
    @group
        @sfx Birds
        阳光明媚。
```

---

## 五、变量系统

### @set 赋值

```nani
; 普通赋值
@set score=1
@set PlayerName="Felix"

; 自增
@set g_TotalRuns++

; 默认赋值（仅当变量未设置时）
@set name?="Alex"
@set g_ClearedRoute?=false

; 多变量同行赋值
@set var1=1; var2="hello"; var3=true
```

### 变量注入（表达式插值）

```nani
; 在文本和参数中使用 {} 注入变量和表达式
@set PlayerName="Felix"
Archibald: 你好，{PlayerName}！
@char {PlayerName} pos:50

; 数学表达式
一加二等于 {1 + 2}。
```

### 变量命名规则

| 前缀 | 存档类型 | 用途 |
|------|---------|------|
| 无前缀 | GameSave（per-slot） | 当前游戏进度、场景状态 |
| `g_` / `G_` | GlobalSave（全局） | 跨存档持久数据，如 `g_FinishedMainRoute` |

> 变量名以字母开头，只能包含拉丁字母、数字、下划线。大小写不敏感。

### 内置表达式函数

| 函数 | 说明 | 示例 |
|------|------|------|
| `random(min, max)` | 随机整数/浮点/字符串 | `random(0, 100)` / `random("a","b","c")` |
| `calculateProgress()` | 剧情完成度（0.0-1.0） | `{calculateProgress()}` |
| `isUnlocked(id)` | 解锁项是否已解锁 | `isUnlocked("Tips/MyTip")` |
| `hasPlayed()` | 当前命令是否已播放过 | `hasPlayed()` |
| `hasPlayed(path)` | 指定脚本是否已播放过 | `hasPlayed("MyScript")` |
| `getName(charId)` | 获取角色显示名 | `getName("Kohaku")` |
| `pow(n, p)` | 幂运算 | `pow(2, 3)` |
| `sqrt(n)` | 平方根 | `sqrt(2)` |
| `cos(n)` / `sin(n)` | 三角函数 | `cos(180)` |
| `log(n)` | 自然对数 | `log(0.5)` |
| `abs(n)` | 绝对值 | `abs(-5)` |
| `max(...)` / `min(...)` | 最大/最小值 | `max(1, 10, -9)` |
| `round(n)` | 四舍五入 | `round(0.9)` |
| `approx(a, b)` | 浮点近似相等 | `approx(0.15, 0.15)` |

自定义表达式函数：在 C# 静态方法上标注 `[ExpressionFunction("name")]` 即可注册。

---

## 六、异步执行

### wait 参数

```nani
; 等待命令完成后再继续
@hide Kohaku wait!
@show Yuko
```

### 并发轨道（@async / @await / @stop）

```nani
; 开启命名异步轨道（loop! 表示循环）
@async AmbientLoop loop!
    @spawn RainEffect
    @wait 2

; 等待异步轨道完成
@await AmbientLoop

; 停止并立即完成
@await CombatAnim complete!

; 停止轨道
@stop AmbientLoop
```

### @sync（强制同步点）

```nani
; 确保所有并发轨道在此点汇合
@sync
```

---

## 七、选项系统

### 基本 @choice 语法

```nani
; 基础：注册选项 + goto 跳转
@choice "去市集" goto:.Market
@choice "先回据点" goto:.Outpost
@stop
```

> **关键**：`@choice` 只注册选项，**不会自动暂停**脚本。必须用 `@stop` 显式等待。

### @choice 嵌套回调（v1.20 推荐）

选中后直接执行嵌套命令，无需 goto：

```nani
@choice "拿剑"
    @set weapon="sword"
    你拿起了一把剑。
@choice "拿弓"
    @set weapon="bow"
    你选择了弓箭。
@stop
```

> **注意**：使用嵌套回调时，`goto`、`gosub`、`set`、`play` 参数会被忽略。两种模式不可混用。

### @choice 高级参数

```nani
; 条件锁定（不满足条件时选项灰显）
@choice "进入密室" lock:{ key_count < 1 }

; 指定选项处理器和位置（ButtonArea 模式）
@choice "向左走" handler:ButtonArea pos:25,50
@choice "向右走" handler:ButtonArea pos:75,50

; 使用 gosub 调用子程序
@choice "查看回忆" gosub:Flashback#Start

; set 参数直接赋值
@choice "选择A" set:choice=1

; 可用处理器：ButtonList（默认）、ButtonArea、ChatReply
```

---

## 八、转场效果

### 点号语法

在 `@back`、`@char` 等命令中，用点号附加转场效果名：

```nani
; 背景切换使用 DropFade 转场
@back River.DropFade time:1.5

; 角色出现使用 CircleReveal
@char Kohaku.Default.CircleReveal time:0.8
```

### 内置转场效果（24种 + Custom）

| 效果名 | 效果名 | 效果名 |
|--------|--------|--------|
| Crossfade | BandedSwirl | Blinds |
| CircleReveal | CircleStretch | CloudReveal |
| Crumble | Dissolve | DropFade |
| LineReveal | Pixelate | RadialBlur |
| RadialWiggle | RandomCircleReveal | Ripple |
| RotateCrumble | Saturate | Shrink |
| SlideIn | SwirlGrid | Swirl |
| Water | Waterfall | Wave |
| Custom（用户自定义遮罩） | | |

---

## 九、Title Script

特殊脚本，引擎初始化后自动播放，通过标签响应玩家操作：

```nani
@back MainBackground
@bgm MenuMusic
@show TitleUI
@stop

# OnNewGame
@sfx NewGameSFX
@stopBgm wait!
@stop

# OnLoad
; 玩家选择存档后执行

# OnExit
; 玩家点击退出后执行
```

---

## 十、常用命令速查

### 背景

```nani
@back BackgroundName
@back BackgroundName tint:#FF0000 time:0.5
@back River.DropFade time:1.5
@back none
```

### 角色

```nani
@char CharacterID
@char CharacterID.HappyFace pos:0.5 time:0.3 wait!
@char CharacterID look:left
@hide CharacterID time:0.5
@hideChars
```

### 音频

```nani
@bgm MusicTrack
@bgm MusicTrack volume:0.8
@bgm BattleThemeMain intro:BattleThemeIntro
@stopBgm time:1.5
@sfx SoundEffect
@sfx SoundEffect volume:0.5 loop!
@stopSfx SoundEffect
```

> `@bgm` 的 `intro` 参数：intro 片段播放一次，然后主曲目循环。
> 音频路径用正斜杠：`Music/Ambient/Noise002`

### 特效

```nani
@shake Camera
@shake CharacterID count:3 time:0.5
@spawn Rain
@despawn Rain
```

### 流程控制

```nani
@stop                    ; 等待玩家点击 / 脚本结束
@wait 1.5               ; 等待 1.5 秒
@goto #MyLabel           ; 跳转到当前脚本标签
@goto OtherScript#Label  ; 跳转到其他脚本标签
@gosub .Sub              ; 调用子程序
@return                  ; 从子程序返回
```

### 变量

```nani
@set score=10
@set g_Cleared=true
@set name?="默认名"
```

### 条件

```nani
@if score>10
    通过！
@else
    失败。
```

### 选项

```nani
; goto 模式
@choice "选项A" goto:#OptionA
@choice "选项B" goto:#OptionB
@stop

; 嵌套回调模式
@choice "选项A"
    @set result="A"
    选择了A。
@choice "选项B"
    @set result="B"
    选择了B。
@stop
```

---

## 十一、C# 集成：逻辑/表现分离

### 核心原则

```
Naninovel .nani  →  表现层（UI、对话、背景、音效）
C# Managers      →  逻辑层（战斗、背包、地图状态）

通信方向：
  Naninovel → C#：自定义 Command 类（@myCommand）
  C# → Naninovel：Engine.GetService<IScriptPlayer>()
```

### 从 C# 播放脚本

```csharp
// v1.20 API
var player = Engine.GetService<IScriptPlayer>();
await player.MainTrack.LoadAndPlay("Script001");

// 跳转到标签
await player.MainTrack.LoadAndPlay("Script001", "MyLabel");
```

### 等待脚本播放完成

```csharp
var player = Engine.GetService<IScriptPlayer>();
var tcs = new UniTaskCompletionSource();

System.Action<Script> onStop = _ => tcs.TrySetResult();
player.OnStop += onStop;

try
{
    await player.MainTrack.LoadAndPlay(scriptName);
    await tcs.Task;
}
finally
{
    player.OnStop -= onStop;
}
```

### 初始化检查（异步）

```csharp
// Naninovel 初始化是异步的，Awake/Start 中不可直接调用
if (Engine.Initialized) DoWork();
else Engine.OnInitializationFinished += DoWork;
```

### 手动初始化（非自动启动场景）

```csharp
await RuntimeInitializer.Initialize();
```

### 重置引擎状态（返回游戏时）

```csharp
var stateManager = Engine.GetService<IStateManager>();
await stateManager.ResetState();
```

---

## 十二、项目特定集成（EndGods）

### NaninovelBridge 模式（地图↔对话切换）

```csharp
// 进入对话模式
await NaninovelBridge.Instance.EnterNovelMode(
    scriptName,
    label: null,
    hideMapWorld: true,
    forcesTransparency: false
);

// 地牢对话叠加模式（保留背景）
await NaninovelBridge.Instance.EnterNovelMode(
    scriptName,
    label: null,
    hideMapWorld: false,
    forcesTransparency: true
);

// 退出对话模式
await ExitStoryMode();
```

### MainCameraLookContainer 管理

```csharp
// 进入对话：显示 Naninovel 渲染层
SetMainCameraLookContainerActive(true);

// 回到地图/游戏：隐藏（防止遮挡游戏世界）
SetMainCameraLookContainerActive(false);
```

### AudioListener 冲突处理

```csharp
// 进入对话时：禁用 Map Camera 的 AudioListener
SetMapAudioListenerActive(false);

// 退出对话时：重新启用
SetMapAudioListenerActive(true);
```

### ContinueInputUI 管理（防止阻挡游戏点击）

```csharp
private void OnScriptPlay(Script script) => SetNaninovelInputVisible(true);
private void OnScriptStop(Script script) => SetNaninovelInputVisible(false);

private void SetNaninovelInputVisible(bool visible)
{
    var uiManager = Engine.GetService<IUIManager>();
    var continueUI = uiManager?.GetUI("ContinueInputUI");
    if (continueUI != null) continueUI.Visible = visible;
}
```

---

## 十三、常见问题排查

| 现象 | 原因 | 解决 |
|------|------|------|
| 对话自动推进 | 缺少 `@stop` | 在需要暂停处添加 `@stop` |
| 黑屏 | `MainCameraLookContainer` 被禁用 | 确认进入对话时已 `SetActive(true)` |
| 对话框不响应点击 | `ContinueInputUI` 未显示 | 确认 `OnScriptPlay` 事件已连接 |
| 脚本不播放 | 脚本名/路径错误 | 路径区分大小写，确认在 Scenario 根下 |
| AudioListener 警告 | 两个摄像机同时启用 | 在 `EnterNovelMode` 时禁用 Map 摄像机的 Listener |
| 使用 @style 报错 | v1.20 已移除 | 改用 `@format` |
| 使用 @br 报错 | v1.20 已移除 | 改用 `<br>` 标签 |

---

## 十四、提交前检查

```
.nani 脚本：
- [ ] 不使用已移除命令：@style（用 @format）、@br（用 <br>）
- [ ] 不要手动添加/删除 |#N| 标识符（由编译器管理）
- [ ] 对话暂停点已有 @stop
- [ ] @choice 后有 @stop 等待输入
- [ ] @choice 嵌套回调模式不混用 goto/set 参数
- [ ] 缩进嵌套使用 4 个空格（不用 Tab）
- [ ] @goto 使用 #Label 格式（同脚本）
- [ ] 角色 ID 与配置匹配（区分大小写）
- [ ] 资产（背景/音乐）已存在并配置

C# 集成：
- [ ] 使用 player.MainTrack.LoadAndPlay(...)（v1.20 API）
- [ ] Engine.Initialized 检查或订阅 OnInitializationFinished
- [ ] IStateManager.ResetState() 在返回游戏时调用
- [ ] 自定义 Command 见 custom-commands.md 规范
- [ ] 自定义表达式函数用 [ExpressionFunction] 标注
```

---

## 十五、脚本重导入与调试记录

### 1. `Scenario Root` 或脚本路径变更后，必须重导入 `.nani`
- Naninovel 会在导入时把脚本的内部 `Script.Path` 固化到资源里。
- 如果之前的 `Scenario Root` 是 `Assets/Game/Story/Scenarios`，后来又改了配置或移动了脚本，旧的 `Script.Path` 不会自动刷新。
- 典型现象是脚本能开始播放，但打印对白时报警：`Failed to hold 'Scripts/...' ... resource is not loaded.`
- 根本原因是：本地化文本资源仍在使用旧的 `Script.Path`，而不是当前的 `Scenario Root` 相对路径。
- 处理方式：修改 `Scenario Root`、移动 `.nani`、重命名脚本目录后，立即批量 `reimport` 所有 `.nani`。
- EndGods 内部工具：`Tools/EndGods/Naninovel/Reimport All Scripts`。

### 2. `.nani` 条件块语法要点
- `@if` 的条件是一个无名参数，不要在表达式周围写多余空格。
- 推荐写法：`@if FactionProgress("TongTian")>50`。
- 只有 `@else if:...`，没有 `@elseif`。
- 如果一行既有文本又有命令，例如对白后立刻跟 `@goto`、`@addItem`，优先拆成多行，避免解析器把它们合并成错误参数。
- 像 `[TODO] ...` 这种占位符不能直接出现在脚本里，要改成 `; TODO: ...` 注释。

### 3. 项目自定义命令参数别名要以 C# 实现为准
- 不要想当然写参数名，先看对应的 `Command` 实现。
- 例如 EndGods 的 `@addItem` 参数是 `id`、`count`、`bag`，不是 `amount`。
- 修改脚本命令后，要立刻查 Unity Console，以官方解析错误为准，不要凭感觉猜。

### 4. `none` 不是 EndGods 当前资源配置里的安全收尾写法
- 在 EndGods 当前 Naninovel 资源配置下，脚本尾部使用 `@bgm none` 可能触发 `Failed to play BGM 'none': resource not found.`
- 脚本尾部使用 `@back none` 可能继续触发 `Failed to load 'none' appearance texture for 'MainBackground'`，并在 `SpriteActor.ChangeAppearance` 内抛空引用。
- 这类异常会让脚本没有正常收尾，上层桥接层会一直停在等待收尾的阶段，看起来像“对话播完卡住”。
- 当前项目内更安全的收尾方式：BGM 用 `@stopBgm fade:X`；不要在结尾对 `MainBackground` 再切 `none` 外观。

### 5. 排查乱码时不要直接相信默认终端输出编码
- Windows PowerShell 默认读取编码可能和仓库 UTF-8 文件不一致，直接 `Get-Content` 看到的乱码不一定真写进了文件。
- 复核脚本文本时，要显式使用 UTF-8 读取，例如 `Get-Content -Encoding UTF8`。
- 判断是否真的污染文件，以磁盘内容和 Unity 重导入结果为准，不要只凭终端展示判断。
