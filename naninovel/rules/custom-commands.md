---
name: custom-commands
description: Naninovel 自定义命令（Custom Command）完整 API 规范（v1.20 实测）
metadata:
  tags: naninovel, custom-commands, csharp, api
---

# Naninovel 自定义命令规范

> **Naninovel 版本**：1.20.241120（已安装版本，基于源码实测）
> **官方参考**：https://naninovel.com/guide/custom-commands
> **注意**：官方在线文档可能比已安装版本更新（使用 `Awaitable` API），
> 本文档以项目实际安装版本（`UniTask` API）为准。

---

## 一、基础结构

```csharp
using Naninovel;
using Naninovel.Commands;
using Game.Core.Utils;

namespace Game.Story.Commands
{
    [CommandAlias("hello")]
    public class HelloWorldCommand : Command
    {
        [ParameterAlias("name")]
        public StringParameter Name;

        public override UniTask Execute(AsyncToken asyncToken = default)
        {
            if (Assigned(Name))
                GameLogger.Log("NaniCommand", $"Hello, {(string)Name}!");
            else
                GameLogger.Log("NaniCommand", "Hello World!");

            return UniTask.CompletedTask;  // 同步命令返回此值
        }
    }
}
```

**脚本中调用**：
```nani
@HelloWorldCommand
@hello name:"Felix"
```

---

## 二、⚠️ 项目强制规范

```csharp
// ✅ 正确
using Naninovel;
using Naninovel.Commands;

// ❌ 禁止：导致 CS0104 UniTask 歧义（Naninovel 内置同名类型）
using Cysharp.Threading.Tasks;
```

- **禁止** `using Cysharp.Threading.Tasks;` 在 Command 子类中
- **禁止** `Debug.Log` — 使用 `GameLogger.Log("Module", "Message")`
- **命名空间**：`Game.Story.Commands`
- **服务访问**：`Engine.GetService<IServiceInterface>()` 或 `ServiceLocator.Get<T>()`

---

## 三、Execute 方法签名（v1.20 实际 API）

### 同步命令

```csharp
public override UniTask Execute(AsyncToken asyncToken = default)
{
    DoSomethingSync();
    return UniTask.CompletedTask;
}
```

### 异步命令

```csharp
public override async UniTask Execute(AsyncToken asyncToken = default)
{
    // 执行异步操作
    await SomeAsyncOperation();

    // ⚠️ 每次 await 后检查 Token
    asyncToken.ThrowIfCanceled();

    // 玩家跳过时 duration = 0，立即完成动画
    var duration = asyncToken.Completed ? 0f : 1.5f;
    await someActor.ChangeVisibility(false, duration);
}
```

### AsyncToken 状态

| 属性/方法 | 含义 | 处理方式 |
|-----------|------|---------|
| `asyncToken.Canceled` | 引擎销毁/重置 | `ThrowIfCanceled()` 抛出异常 |
| `asyncToken.Completed` | 玩家正在跳过 | 动画 duration 设为 0 |
| `asyncToken.ThrowIfCanceled()` | 取消时抛出异常 | 每次 await 后调用 |
| `asyncToken.EnsureNotCanceledOrCompleted()` | 取消时抛出，返回是否未 Completed | 可选用 |

---

## 四、装饰器属性

### 类级别

```csharp
[CommandAlias("myCmd")]     // 脚本中的命令名
public class MyCommand : Command { }

// 用法：@myCmd
// 或：@MyCommand（不设 Alias 时用类名）
```

### 参数级别

```csharp
// 命名参数
[ParameterAlias("name")]
public StringParameter PlayerName;
// 用法：@myCmd name:"Felix"

// 无名参数（紧跟命令名）
[ParameterAlias(NamelessParameterAlias)]
public StringParameter Target;
// 用法：@myCmd "Felix"

// 必填参数
[ParameterAlias("id"), RequiredParameter]
public StringParameter BattleId;
// 未提供时报错
```

### 参数存在性检查

```csharp
if (Assigned(Name)) { }      // 推荐
if (Name.HasValue) { }       // 等价
```

---

## 五、参数类型

| C# 类型 | 脚本示例 | 说明 |
|---------|---------|------|
| `StringParameter` | `text:"Hello"` / `Hello` | 字符串 |
| `IntegerParameter` | `count:10` | 整数 |
| `DecimalParameter` | `speed:0.5` | 浮点数 |
| `BooleanParameter` | `loop:true` / `loop!` / `!loop` | 布尔值 |
| `NamedStringParameter` | `target:Script.Label` | 命名字符串（`key.value`） |
| `NamedIntegerParameter` | `pos:left.50` | 命名整数 |
| `NamedDecimalParameter` | `pos:x.0.5` | 命名浮点数 |
| `StringListParameter` | `items:a,b,"c d"` | 字符串列表 |
| `IntegerListParameter` | `nums:1,2,3` | 整数列表 |
| `LocalizableTextParameter` | `text:"Hello\|#id\|"` | 可本地化文本 |

---

## 六、高级接口

### Command.ILocalizable — 含用户可见文本

```csharp
[CommandAlias("showMessage")]
public class ShowMessageCommand : Command, Command.ILocalizable
{
    [ParameterAlias(NamelessParameterAlias), RequiredParameter]
    public LocalizableTextParameter Text;

    public override UniTask Execute(AsyncToken asyncToken = default)
    {
        GameLogger.Log("ShowMsg", (string)Text);
        return UniTask.CompletedTask;
    }
}
```

### Command.IPreloadable — 预加载资源

```csharp
[CommandAlias("playClip")]
public class PlayClipCommand : Command, Command.IPreloadable
{
    [ParameterAlias(NamelessParameterAlias), RequiredParameter]
    public StringParameter ClipPath;

    public async UniTask PreloadResources()
    {
        // DynamicValue = 含脚本表达式，无法预加载
        if (!Assigned(ClipPath) || ClipPath.DynamicValue) return;
        await LoadAudioClipAsync(ClipPath);
    }

    public void ReleasePreloadedResources()
    {
        if (!Assigned(ClipPath) || ClipPath.DynamicValue) return;
        UnloadAudioClip(ClipPath);
    }

    public override UniTask Execute(AsyncToken asyncToken = default)
    {
        PlayPreloadedClip(ClipPath);
        return UniTask.CompletedTask;
    }
}
```

---

## 七、覆盖内置命令

```csharp
// 使用与内置命令相同的 Alias 即可替换
[CommandAlias("print")]
public class MyCustomPrintCommand : PrintText
{
    public override async UniTask Execute(AsyncToken asyncToken = default)
    {
        GameLogger.Log("Print", $"Custom: {Text}");
        await base.Execute(asyncToken);
    }
}
```

> **注意**：覆盖后需在 Unity 中 Reimport 所有关联的 `.nani` 脚本。

---

## 八、访问引擎服务与自定义变量

```csharp
public override UniTask Execute(AsyncToken asyncToken = default)
{
    // 项目游戏服务（通过 ServiceLocator）
    var inventory = ServiceLocator.Get<IInventoryService>();

    // Naninovel 内置服务
    var varManager = Engine.GetService<ICustomVariableManager>();

    // ✅ 设置变量：用正确的构造函数（float/string/bool）
    varManager.SetVariableValue("score", new CustomVariableValue(42f));           // Numeric
    varManager.SetVariableValue("playerName", new CustomVariableValue("Felix"));  // String
    varManager.SetVariableValue("lastVictory", new CustomVariableValue(true));    // Boolean

    // ❌ 错误：不要用 string 存 boolean
    // varManager.SetVariableValue("flag", new CustomVariableValue("true"));

    // 读取变量
    if (varManager.TryGetVariableValue("score", out CustomVariableValue val))
        GameLogger.Log("Combat", $"Score: {val.Number}");

    return UniTask.CompletedTask;
}
```

---

## 九、完整示例：战斗触发命令

```csharp
using Naninovel;
using Naninovel.Commands;
using Game.Core.Utils;

namespace Game.Story.Commands
{
    [CommandAlias("startBattle")]
    public class StartBattle : Command
    {
        [ParameterAlias("id"), RequiredParameter]
        public StringParameter BattleId;

        public override async UniTask Execute(AsyncToken asyncToken = default)
        {
            if (Game.Systems.Combat.CombatManager.Instance == null)
            {
                GameLogger.LogError("StartBattle", "CombatManager not found!");
                return;
            }

            GameLogger.Log("StartBattle", "Starting battle: " + BattleId);

            // 可直接 await 外部 Cysharp UniTask（await 模式兼容，无需 using 导入）
            bool victory = await Game.Systems.Combat.CombatManager.Instance.StartBattle(BattleId);

            asyncToken.ThrowIfCanceled();

            var varManager = Engine.GetService<ICustomVariableManager>();
            if (varManager != null)
            {
                // 无前缀 = GameSave（per-slot）
                varManager.SetVariableValue("lastBattleVictory", new CustomVariableValue(victory));
            }
        }
    }
}
```

```nani
; 脚本中使用
@startBattle id:"Boss_DragonKing"

; 根据战斗结果分支
@if lastBattleVictory
    胜利了！
    @goto #Victory
@else
    失败了...
    @goto #Defeat
```

---

## 十、提交前检查

```
自定义命令（v1.20）：
- [ ] 无 using Cysharp.Threading.Tasks
- [ ] 无 Debug.Log，改用 GameLogger.Log
- [ ] 命名空间 Game.Story.Commands
- [ ] 类装饰器 [CommandAlias]，参数装饰器 [ParameterAlias]
- [ ] 同步命令：return UniTask.CompletedTask（不加 async 关键字）
- [ ] 异步命令：每次 await 后调用 asyncToken.ThrowIfCanceled()
- [ ] 跳过支持：asyncToken.Completed 时 duration = 0
- [ ] CustomVariableValue 用正确构造函数（float/string/bool）
- [ ] 变量命名：无前缀 = GameSave，g_ = GlobalSave
- [ ] 修改参数后在 Unity 中 Reimport 相关 .nani 脚本
```
