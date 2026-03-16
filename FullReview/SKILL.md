---
name: FullReview
description: Workflow for FullReview
---

# Skill: FullReview (多维度代码审查)

**来源**：改编自 [wshobson/commands](https://github.com/wshobson/commands) · `workflows/full-review.md`

Trigger: 当用户说 `/full-review`、“全面审查”、“深度 Review”或“多角度检查”时触发。

## 目标

从六个专业角度对代码进行系统性审查，覆盖代码质量、安全、架构、性能、测试和 TDD 合规。

> **与 `CodeReview.md` 的区别**：CodeReview 是日常快速审查，FullReview 是深度多维审查，适合大功能合并前或定期质量检查。

## 审查维度

### 维度 1：代码质量
- 职责单一原则（每个 Manager/System 只做一件事）。
- DRY 原则（重复逻辑是否提取）。
- 可读性（命名是否清晰、注释是否说明“为什么”）。
- 死代码（已注释、未使用的方法、字段）。
- 项目规范合规（GameLogger、GameConstants、TMP_Text）。

### 维度 2：安全与健壮性（Unity 上下文）
- 空引用保护（ServiceLocator.Get 的返回值是否检查）。
- 数组/列表越界保护。
- 单例 `_isQuitting` 保护（防止退出时的空引用）。
- Editor 代码隔离（`#if UNITY_EDITOR`）。
- Naninovel 变量命名规范（`g_` 前缀用于 GlobalSave）。

### 维度 3：架构合规
- 逻辑/表现是否分离（Manager 不含 Naninovel 调用）。
- 服务访问是否通过 `ServiceLocator`。
- 命名空间是否符合 `Game.Systems.[Module]` 规范。
- 目录结构是否正确（参见 CLAUDE.md §2）。
- 接口是否定义（`I` 前缀）。

### 维度 4：性能
- 热路径是否有 GC Alloc（Update 中的分配）。
- Component 引用是否已缓存（避免每帧 GetComponent）。
- 异步模式是否正确（UniTask，无协程，无 `Task.Delay`）。
- 大数据集是否有分帧处理。

### 维度 5：可测试性
- 关键逻辑是否解耦（是否能在不启动 Unity 的情况下单元测试？）。
- 服务是否通过接口注入（方使 Mock）。
- 事件系统是否正确订阅/取消订阅（OnEnable/OnDisable）。

### 维度 6：Naninovel 专项（如涉及）
- Command 子类中有无 `using Cysharp.Threading.Tasks`（禁止）。
- 剧情变量用 `g_` 前缀 / 系统变量不用 `g_` 前缀。
- Managed Text 是否定义（避免硬编码文本）。
- 本地化 Insert Line Keys 是否运行。

## 审查流程

1. **确认范围**：审查的文件/PR/功能是什么？
2. **逐维度检查**：按上述 6 个维度依次审查。
3. **问题分级**：
   - 🔴 **Critical**：必须修复，阻塞合并
   - 🟡 **Warning**：建议修复，影响质量
   - 🟢 **Suggestion**：改进建议，不强制
4. **生成报告**：使用下方格式输出。

## 输出格式

```
## FullReview 报告
**审查范围**：[文件/功能/PR]
**审查时间**：[日期]

---

### 🔴 Critical Issues（必须修复）
1. [文件:行号] **[维度]** - 描述

### 🟡 Warnings（建议修复）
1. [文件:行号] **[维度]** - 描述

### 🟢 Suggestions（改进建议）
1. [文件:行号] **[维度]** - 描述

### ✅ 合规项（做得好）
- ...

---

### 总结
- Critical 数量：X（必须修复后方可合并）
- 整体质量评分：[A/B/C/D]
- 建议优先处理：...
```
