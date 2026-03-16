---
name: SmartFix
description: Workflow for SmartFix
---

# Skill: SmartFix (智能问题修复)

**来源**：改编自 [wshobson/commands](https://github.com/wshobson/commands) · `workflows/smart-fix.md`

Trigger: 当用户说 `/smart-fix`、“智能修复”、“帮我解决这个问题”，或问题跨越多个领域时触发。

## 目标

通过自动分析问题类型，选择最合适的专家方向进行处理，解决复杂或跨领域的技术问题。

## 分析阶段：识别问题领域

在开始修复前，先分析问题属于哪个类型。

| 问题类型 | 关键字 | 处理方向 |
|---------|--------|---------|
| **部署/构建** | Build Error, 打包失败, Assembly | DevOps 排查 |
| **代码 Bug** | NullRef, Exception, 崩溃, 逻辑错误 | 深度 Debug |
| **性能** | 卡顿, GC Alloc, FPS 低, 帧率 | 性能工程 |
| **技术债** | 重构, 过时代码, 架构混乱 | 架构现代化 |
| **多领域** | 以上多项组合 | 多方向协同 |

## 修复流程

### Step 1：问题分析
- 读取错误信息、日志、用户描述
- 判断主要领域（参考上表）
- 若跨多领域，列出所有涉及方向

### Step 2：单领域修复
根据类型选择对应处理方式。

**① 代码 Bug**
1. 读取 `LastUnityError.txt`（若存在）
2. 定位堆栈中的用户代码文件和行号
3. 分析根因（非症状），参见 Debug Skill 流程
4. 修复时优先事件驱动方案，禁止 `Task.Delay` 临时补丁

**② 性能问题**
1. 确认热路径（Update/FixedUpdate/频繁调用的方法）
2. 识别 GC Alloc 来源（字符串拼接/LINQ/装箱）
3. 建议：对象池、缓存 Component 引用、异步分帧

**③ 构建/依赖**
1. 检查 Assembly Definition 配置
2. 验证 namespace 和 using 是否正确
3. 确认 `#if UNITY_EDITOR` 包裹 Editor 专用代码

### Step 3：多领域协同
当问题跨越多个领域时：
1. 先修复阻塞性问题（崩溃 > 性能 > 债务）
2. 记录其他领域待办，追加到任务列表
3. 验证修复不引入新问题

## 验证清单

修复完成后确认：
- [ ] 根因已消除（非绕过）
- [ ] 去除 `Debug.Log`，改为 `GameLogger`
- [ ] 无硬编码数值，常量进 `GameConstants.cs`
- [ ] 无临时 workaround（`Task.Delay`、轮询等）
- [ ] 相关逻辑/表现分离（Manager vs Naninovel Script）

## 输出格式

```
## 问题诊断
- **类型**：[Bug / 性能 / 构建 / 混合]
- **根因**：[具体描述]

## 修复方案
[代码修改说明]

## 验证步骤
[如何确认修复有效]

## 遗留问题（如有）
[其他领域的待处理项]
```
