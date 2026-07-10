---
name: state-variables
description: Naninovel 状态管理（IStateManager）与自定义变量（ICustomVariableManager）完整 API
metadata:
  tags: naninovel, state, save-load, custom-variables, persistence
---

# 状态管理与自定义变量

> **参考文档**：https://naninovel.com/guide/state-management | https://naninovel.com/guide/custom-variables
> **最后校对**：2026-02-19（基于官方文档实时验证）

---

## 一、三种状态类型

| 状态类型 | 存储位置 | 特点 |
|---------|---------|------|
| **GameState** | 每个存档槽独立 | 当前场景、角色位置、脚本进度 |
| **GlobalState** | 单一全局文件 | 跨存档持久，如"已读文本"记录、`g_` 变量 |
| **SettingsState** | Saves 目录外 | 用户设置，可手动编辑（分辨率、音量等） |

存储格式：默认 `.nson`（JSON 二进制），可配置为 `.json` 明文。

---

## 二、IStateManager — 存档/读档

### 获取服务

```csharp
var stateManager = Engine.GetService<IStateManager>();
```

### 存档与读档

```csharp
// 按槽名存档
await stateManager.SaveGame("Slot001");
await stateManager.LoadGame("Slot001");

// 快速存档
await stateManager.QuickSave();
await stateManager.QuickLoad();

// 重置引擎状态（从 VN 模式返回游戏世界时使用）
await stateManager.ResetState();
```

### 异步事件（无法 await 时）

```csharp
stateManager.OnGameSaveFinished += OnSaved;
stateManager.OnGameLoadFinished += OnLoaded;
```

---

## 三、自定义状态序列化

将游戏系统数据委托给 Naninovel 的存档系统：

```csharp
// 定义自定义状态数据
[Serializable]
private class MySystemState
{
    public int Level;
    public List<string> CollectedItems;
}

private IStateManager _stateManager;

private void OnEnable()
{
    _stateManager = Engine.GetService<IStateManager>();
    // 订阅序列化/反序列化任务
    _stateManager.AddOnGameSerializeTask(SerializeState);
    _stateManager.AddOnGameDeserializeTask(DeserializeState);
}

private void OnDisable()
{
    _stateManager?.RemoveOnGameSerializeTask(SerializeState);
    _stateManager?.RemoveOnGameDeserializeTask(DeserializeState);
}

private void SerializeState(GameStateMap stateMap)
{
    var state = new MySystemState
    {
        Level = currentLevel,
        CollectedItems = new List<string>(collectedItems)
    };
    stateMap.SetState(state);
}

private Awaitable DeserializeState(GameStateMap stateMap)
{
    var state = stateMap.GetState<MySystemState>();
    if (state != null)
    {
        currentLevel = state.Level;
        collectedItems = state.CollectedItems ?? new List<string>();
    }
    return Async.Completed;
}
```

### 多实例序列化（同一类型的多个对象）

```csharp
// 用 instanceId 区分同类型的不同实例
var monster1 = stateMap.GetState<MonsterState>("monster_1");
var monster2 = stateMap.GetState<MonsterState>("monster_2");
```

### 访问 GlobalState / SettingsState

```csharp
// 全局状态（跨存档）
var myGlobal = stateManager.GlobalState.GetState<MyGlobalData>();

// 设置状态
var mySettings = stateManager.SettingsState.GetState<MySettings>();
```

---

## 四、ICustomVariableManager — 自定义变量

### 变量命名规则（与 CLAUDE.md §5 一致）

| 命名规则 | 存档类型 | 示例 |
|---------|---------|------|
| 无 `g_` 前缀 | **GameSave**（每存档槽）| `score`、`PlayerName`、`currentRoute` |
| `g_` 或 `G_` 前缀 | **GlobalSave**（跨存档）| `g_FinishedMainRoute`、`G_TotalRuns` |

变量名只能包含字母、数字和下划线，且以字母开头，**不区分大小写**。

### 获取服务

```csharp
var vars = Engine.GetService<ICustomVariableManager>();
```

### 读取变量

```csharp
// 读取为 CustomVariableValue（含类型信息）
var value = vars.GetVariableValue("score");
if (value != null)
{
    float numVal = value.Number;
    string strVal = value.String;
    bool boolVal = value.Boolean;
}

// 类型化读取（更安全）
if (vars.TryGetVariableValue<float>("score", out var score))
{
    GameLogger.Log("State", $"当前分数：{score}");
}

if (vars.TryGetVariableValue<string>("PlayerName", out var name))
{
    titleText.text = name;
}

if (vars.TryGetVariableValue<bool>("g_FinishedRoute1", out var cleared))
{
    showEndingButton = cleared;
}
```

### 设置变量

```csharp
// 设置数值
vars.SetVariableValue("score", new(42));       // Numeric
vars.SetVariableValue("score", new(42.5f));    // Numeric（浮点）

// 设置字符串
vars.SetVariableValue("PlayerName", new("Felix"));

// 设置布尔
vars.SetVariableValue("g_Cleared", new(true));

// 类型化设置
vars.TryGetVariableValue<float>("score", out var current);
current += 10f;
vars.TrySetVariableValue("score", current);
```

### 变量类型（由首次赋值决定）

| .nani 赋值 | C# 类型 | 读取属性 |
|-----------|---------|---------|
| `@set foo="Hello"` | String | `.String` |
| `@set foo=42` | Numeric | `.Number` |
| `@set foo=true` | Boolean | `.Boolean` |

---

## 五、在 .nani 脚本中操作变量

```nani
; 普通赋值
@set score=0
@set PlayerName="Felix"

; 数学运算
@set score=score+10
@set g_TotalRuns++

; 默认赋值（仅当变量未设置时）
@set name?="旅行者"
@set g_ClearedRoute1?=false

; 条件
@if score>100
    恭喜达成高分！
    @set g_HighScore=true

; 变量注入文本
Archibald: 你好，{PlayerName}！
```

---

## 六、变量集中管理

### 6.1 Naninovel 预定义变量面板（推荐）

在 Unity Editor 中通过 **Naninovel → Configuration → Custom Variables** 面板集中定义所有变量的名称和初始值。

- **Local 预定义变量**：每次 state reset 时重新初始化为面板设定值
- **Global 预定义变量**（`g_` 前缀）：仅首次启动时初始化
- Value 字段接受脚本表达式，不是原始字符串

**这是项目的变量注册中心（single source of truth）**。所有新变量必须先在此面板中注册，再在脚本中使用。

**变量安全性由初始化保证**：预定义面板中注册的变量在新存档时自动初始化为默认值，脚本中 `@if` 读取时变量一定存在。因此脚本中直接写 `@if p04_mineComplete==true` 即可，**不需要** `BoolVar()`/`StringVar()` 等容错包装。如果运行时变量不存在导致卡死，说明初始化链有 bug，应该修初始化（在面板中补注册），而不是用容错函数掩盖问题。

### 6.2 C# 常量对照

C# 代码需要读写的变量名，在 `GameConstants.cs` 中定义常量：

```csharp
// Assets/Game/Scripts/Core/Constants/GameConstants.cs
public const string VAR_LAST_BATTLE_VICTORY = "lastBattleVictory";
public const string VAR_LAST_ITEM_USED      = "lastItemUsed";
```

仅 C# 代码中使用的变量才需要在此注册。纯脚本内部变量（如对话选择分支变量）只需在 Naninovel 配置面板注册即可。

### 6.3 变量命名规范

| 前缀 | 作用域 | 示例 | 用途 |
|------|--------|------|------|
| 无前缀 | GameSave（存档槽） | `p02_mineIntel` | 游戏进度、剧情分支 |
| `g_` / `G_` | GlobalSave（跨存档） | `g_finishedPrologue` | 成就、解锁、累计统计 |

命名使用 **camelCase**，只能包含拉丁字母、数字、下划线，以字母开头，不区分大小写。

### 6.4 变量命名分层（必须遵守）

所有变量必须带**来源前缀**，一看名字就知道归属：

| 前缀格式 | 含义 | 示例 |
|---------|------|------|
| `p{场景号}_` | 序章（Prologue）变量 | `p02_mineIntel`、`p02_houYingIntroDone`、`p04_hiddenLore` |
| `ch{章节}_{场景号}_` | 章节变量 | `ch1_03_metDengChanyu`、`ch1_07_bossDefeated` |
| `npc_{NPC名}_` | NPC 状态变量 | `npc_houYing_trust`、`npc_houYing_introDone` |
| `sys_` | 系统级变量（C# 读写） | `sys_lastBattleVictory`、`sys_lastItemUsed` |
| `g_` | 全局跨存档 | `g_finishedPrologue`、`g_totalDeaths` |

**规则**：
1. 纯分支跳转用 `goto`，不创建变量
2. 只有**跨脚本读取**或**C# 系统读取**时才创建变量
3. 一个 NPC 的状态变量超过 3 个时，考虑后续归入阵营声望系统
4. NPC 信任度/态度用 `npc_` 前缀变量，后续迁移到声望系统时统一替换

**后续规划**：NPC 状态变量（`npc_` 前缀）将在阵营声望系统实现后迁移至该系统统一管理，届时脚本侧改用 ExpressionFunction 查询（如 `NpcReputation("HouYing")`）

### 6.4 安全赋值运算符 `?=`

```nani
; 仅在变量不存在时赋值，避免覆盖已有存档数据
@set prologueMineComplete ?= false
@set g_FirstLaunch ?= true
```

在初始化脚本或变量首次使用处优先使用 `?=`。

### 6.5 何时需要变量 vs 只用 goto

**不需要变量**：纯脚本内部的分支跳转（`@choice ... goto:.Label`），选了就跳走，后续不再读取。

**需要变量**：
- **跨脚本读取**：A 脚本设置，B 脚本用 `@if` 判断
- **影响后续对话内容**：同一脚本的后续段落根据此值分支
- **C# 系统需要读写**：如战斗结果、地图标签

### 6.6 当前变量清单

#### 系统变量（C# 命令设置）

| 变量名 | 类型 | 设置方 | 用途 |
|--------|------|--------|------|
| `lastBattleVictory` | boolean | `@startBattle` | 战斗胜负结果 |
| `lastItemUsed` | boolean | `@requireItem` | 道具使用结果 |
| `lastSkillSuccess` | boolean | `@useCompanionSkill` | 技能使用结果 |
| `current_env_tags` | string | MapManager | 当前地图环境标签 |

#### 序章变量

| 变量名 | 类型 | 初始值 | 设置脚本 | 读取脚本 | 用途 |
|--------|------|--------|----------|----------|------|
| `mineIntel` | string | `""` | P02_Market | P02_Market(回访) | 矿坑情报等级，影响回访对话 |
| `prologueMineComplete` | boolean | `false` | P04_Awakening | P02_Market | 矿坑完成，切换 POI 对话 |
| `hiddenLore` | boolean | `false` | P04_Awakening | 后续章节 | 隐藏剧情解锁 |
| `prologueComplete` | boolean | `false` | P02_Market(回访) | 后续章节 | 序章完成标志 |

> **维护要求**：添加新变量时，先确认是否真正需要跨脚本/跨系统读取。纯分支跳转用 `goto` 即可，不要创建变量。需要变量时，同步更新此清单和 Naninovel 配置面板。

---

## 七、存档约定

### 全局持久数据（GlobalSave）

```csharp
// 使用 g_ 前缀的变量跨存档持久
// 在 C# 中读写：
vars.SetVariableValue("g_FinishedChapter1", new(true));
vars.SetVariableValue("g_TotalDeaths", new(deathCount));
```

```nani
; 对应 .nani 写法
@set g_FinishedChapter1=true
@set g_TotalDeaths++
```

### 存档槽数据（GameSave）

```csharp
// 无前缀变量跟随存档槽
vars.SetVariableValue("currentDungeon", new("DungeonA"));
vars.SetVariableValue("combatPhase", new(2));
```

### C# 系统状态（复杂对象）

对于 `List<>` / 自定义类等复杂数据，不要用 `ICustomVariableManager`，
改用 `IStateManager` 的序列化任务（见第三节）。

---

## 八、常用模式

### 成就/解锁标志

```csharp
// 设置成就
Engine.GetService<ICustomVariableManager>()
    .SetVariableValue("g_AchievementDragonSlayer", new(true));
```

```nani
; 条件显示专属结局
@goto #EndingDragonSlayer if:g_AchievementDragonSlayer
```

### 首次游戏检测

```nani
@set g_FirstLaunch?=true   ; 仅首次设置

@if g_FirstLaunch
    ; 显示新手引导
    @goto #Tutorial
    @set g_FirstLaunch=false
```

---

## 九、提交前检查

```
状态管理：
- [ ] 复杂数据（List/自定义类）用 IStateManager 序列化任务
- [ ] OnEnable 订阅，OnDisable 取消订阅序列化任务
- [ ] ResetState() 在从 VN 返回游戏时调用

自定义变量：
- [ ] 全局数据加 g_ 前缀
- [ ] 存档内数据不加 g_ 前缀
- [ ] TryGetVariableValue<T> 做安全读取
- [ ] 变量类型由首次赋值决定，保持一致
```
