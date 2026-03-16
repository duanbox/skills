---
name: PerformanceOpt
description: Workflow for PerformanceOpt
---

# Skill: PerformanceOpt (Unity 性能优化)

**来源**：改编自 [wshobson/commands](https://github.com/wshobson/commands) · `workflows/performance-optimization.md`

Trigger: 当用户说 `/perf`、`/performance`、“性能优化”、“卡顿”、“GC”、“FPS 低”时触发。

## 目标

系统性分析并优化 Unity 项目的运行时性能，覆盖 CPU、GPU、内存、GC 五个维度。

## Phase 1：性能分析（先量化，再优化）

### 1.1 热路径识别
扫描以下文件中的 Update/FixedUpdate/LateUpdate：
- `Assets/Game/Scripts/Systems/` 下所有 Manager
- `Assets/Game/Scripts/UI/` 下所有 UI 脚本

重点检查：
- 是否在 Update 中调用 `GetComponent<>()`（应缓存）
- 是否在 Update 中调用 `FindObjectOfType<>()`（禁止）
- 是否有每帧 LINQ 查询（应预计算）
- 是否有频繁字符串拼接（用 `StringBuilder` 或 `$""` 谨慎使用）

### 1.2 GC Alloc 分析
常见 GC 来源：
```csharp
// ❌ 每帧分配
void Update() {
    var list = new List<Enemy>(); // GC!
    string msg = "Score: " + score; // GC!
}

// ✅ 缓存
private List<Enemy> _enemies = new List<Enemy>();
private StringBuilder _sb = new StringBuilder();
```

### 1.3 UniTask 异步分析
检查是否有以下反模式：
```csharp
// ❌ 无限轮询
while (!condition) await UniTask.Delay(100);

// ✅ 事件驱动
await UniTask.WaitUntil(() => condition);
// 或用 UniTaskCompletionSource
```

## Phase 2：CPU 优化

### 2.1 Update 合并
- 将多个小脚本的 Update 合并到单一 Manager 的 Update
- 使用 `ServiceLocator` 模式，Manager 统一 tick 子系统

### 2.2 算法优化
- 对象查找：Dictionary 替代 List 线性搜索
- 空间查询：使用 Unity Physics OverlapSphere 而非全局遍历
- 战斗/地牢系统：数据查询优先考虑缓存结果

### 2.3 异步分帧
```csharp
// 大量数据初始化时分帧处理
foreach (var item in largeCollection) {
    ProcessItem(item);
    if (frameCounter++ % 10 == 0)
        await UniTask.Yield(PlayerLoopTiming.Update);
}
```

## Phase 3：内存优化

### 3.1 对象池
```csharp
// 战斗特效、地牢格子等频繁实例化的对象
// 使用 Unity ObjectPool<T> 或自定义池
```

### 3.2 资源卸载
- 场景切换时确认 `Resources.UnloadUnusedAssets()`
- Addressables 引用计数正确 Release
- Naninovel 背景/角色资源确认卸载策略

### 3.3 ScriptableObject 共享
- 战斗数据 (`CombatConfig`)、地牢配置 (`DungeonConfig`) 使用 SO，避免运行时复制

## Phase 4：GPU / 渲染优化

- 静态物体启用 Static Batching
- 同材质物体开启 GPU Instancing
- Shapes 库图形：检查是否有不必要的实时重绘
- UI Canvas：分离频繁更新的元素到独立 Canvas（避免全 Canvas Rebuild）

## Phase 5：验证与监控

### 分析工具
```
Unity Profiler -> CPU Usage -> 找热点方法
Memory Profiler -> 快照对比 -> 找内存泄漏
Frame Debugger -> 找多次 DrawCall
```

### 优化后验证
- [ ] 目标帧率稳定（60FPS/30FPS）
- [ ] GC Alloc per frame < 0KB（理想）
- [ ] DrawCall 在目标平台合理范围内
- [ ] 无内存持续增长趋势

## 输出格式

```
## 性能分析报告

### 发现的问题
1. [文件:行号] 描述 · 影响：[高/中/低]
2. ...

### 优化建议
| 优先级 | 文件 | 问题 | 建议方案 | 预期收益 |
|--------|------|------|---------|---------|
| 高 | ... | ... | ... | ... |

### 已修复项
- [修改说明]
```
