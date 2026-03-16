---
name: TechDebt
description: Workflow for TechDebt
---

# Skill: TechDebt (技术债分析与清偿)

**来源**：改编自 [wshobson/commands](https://github.com/wshobson/commands) · `tools/tech-debt.md`

Trigger: 当用户说 `/tech-debt`、“技术债”、“代码腐化”、“需要重构”、“架构混乱”时触发。

## 目标

识别、量化并制定清偿计划，将技术债系统化处理，而非随机“碰到哪改哪”。

## Phase 1：债务清单

### 1.1 代码层
扫描 `Assets/Game/Scripts/` 中的以下问题：

```bash
# 魔法数字（违反 GameConstants 规则）
grep -rn "[^a-zA-Z_][ ]*[0-9]\+[^a-zA-Z_0-9.f]" --include="*.cs"

# Debug.Log（违规）
grep -rn "Debug\.Log" --include="*.cs"

# 协程（违规）
grep -rn "IEnumerator\|yield return" --include="*.cs"

# 临时补丁
grep -rn "Task\.Delay\|Thread\.Sleep" --include="*.cs"

# 旧版 UI Text
grep -rn "UnityEngine\.UI\.Text\b" --include="*.cs"
```

### 1.2 架构层

| 债务类型 | 检查内容 |
|---------|---------|
| 单例不规范 | 缺少 `_isQuitting` 保护 |
| 直接引用 | 未通过 `ServiceLocator` 获取服务 |
| 命名空间混乱 | 脚本不在正确的 `Game.*` 命名空间 |
| 逻辑/表现混合 | Manager 中含 Naninovel 表现代码 |
| 接口缺失 | Service 类未实现对应 `I*` 接口 |

### 1.3 数据同步层
- CSV Importer 与数据结构是否对齐（`MapNodeEntry` 等）
- Naninovel Managed Text 本地化条目是否完整
- `GameConstants.cs` 中是否有已废弃的常量

### 1.4 文档层
- 无注释的复杂算法（战斗公式、地牢生成逻辑等）
- 过期的 walkthrough 文档

## Phase 2：影响评估

对每项债务评估：

| 维度 | 评分标准 |
|------|---------|
| **阻塞性** | 是否阻碍新功能开发？ |
| **风险性** | 是否会引发 Bug 或崩溃？ |
| **范围** | 影响多少个文件/系统？ |
| **修复成本** | 需要多少工作量？ |

## Phase 3：清偿计划

### 快速清偿（1-2小时，立即处理）
- 替换所有 `Debug.Log` 为 `GameLogger.Log`
- 补充 `_isQuitting` 到不规范的单例
- 移动魔法数字进 `GameConstants.cs`

### 中期重构（需排期，影响较大）
- 协程 -> UniTask 迁移
- 缺少接口的 Service 补充 `I*` 接口
- 命名空间整理

### 长期架构改造（需设计，跨多 Sprint）
- 逻辑/表现边界重新划分
- 数据层统一重构

## Phase 4：预防机制

清偿后建立护栏：
- 在 CodeReview Skill 中加入对应检查项
- 更新 CLAUDE.md 的代码审查清单
- 关键路径添加架构测试（编辑器验证脚本）

## 输出格式

```
## 技术债审计报告

### 债务清单

| 类型 | 文件 | 描述 | 严重性 |
|------|------|------|--------|
| 代码层 | ... | ... | 高/中/低 |

### 优先清偿队列
1. **[立即]** ...
2. **[本周]** ...
3. **[下个迭代]** ...

### 预防建议
- ...
```
