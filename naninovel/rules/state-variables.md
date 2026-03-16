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

## 六、EndGods 项目约定

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

## 七、常用模式

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

## 八、提交前检查

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
