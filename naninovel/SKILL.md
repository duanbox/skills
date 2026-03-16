---
name: Naninovel Integration
description: Best practices and rules for using the Naninovel visual novel engine within the Unity project.
---

# Naninovel Integration

## Overview
This skill focuses on the specific usage of Naninovel for the EndGods project. It covers background management, resource providers, command extensibility, state management, and the integration of Naninovel's narrative features with the custom game systems.

**官方文档**：https://naninovel.com/guide/（所有规则基于此文档实时校对）

## Key Concepts
- **背景系统**：使用 Addressables 管理静态/动态背景，Z-ordering 规则
- **自定义命令**：继承 `Command`，使用 `Awaitable Execute(ctx)` API（禁止 using Cysharp.Threading.Tasks）
- **状态管理**：`IStateManager` 存档/读档，`ICustomVariableManager` 变量（`g_` = GlobalSave）
- **本地化**：Naninovel Managed Text 系统，禁止 I2 Localization
- **C# 集成**：`Engine.GetService<T>()`，`player.MainTrack.LoadAndPlay(...)`

## Rules Index

### 脚本与集成
- [Scripting Best Practices](rules/scripting-best-practices.md): `.nani` 脚本完整语法、条件块、`@async`/`@await` 并发轨道、C# 集成模式、常见问题排查（2026-02-19 校对）
- [Custom Commands](rules/custom-commands.md): 自定义命令完整 API —— `Awaitable Execute(ctx)`、参数类型全表、`ILocalizable`/`IPreloadable`、项目规范

### 状态与本地化
- [State Variables](rules/state-variables.md): `IStateManager` 存档 API、自定义序列化、`ICustomVariableManager`、变量命名规则（`g_` = GlobalSave）
- [Managed Text](rules/managed-text.md): Managed Text 系统、`[ManagedText]` attribute、`ManagedTextProvider` 组件、`T_` 前缀脚本引用

### 渲染与背景
- [Background System](rules/background-system.md): 背景 Actor 配置、Z-sorting、Addressables 资源管理
- [Camera Integration](rules/camera-integration.md): URP 摄像机堆叠、`MainCameraLookContainer` 管理、AudioListener 冲突处理
