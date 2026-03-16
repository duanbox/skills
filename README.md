# Skills Directory - README

## 概述

这个目录包含两种不同格式的 "Skills"（技能），它们都是用来指导 AI Agent 执行特定任务的知识库。

---

## 两种 Skill 格式

### 📄 格式 A：Workflow-Based Skills（工作流型技能）

**文件格式**：单个 `.md` 文件  
**示例**：`Architect.md`, `CodeReview.md`, `Debug.md`, `StoryWeaver.md`, `Verify.md`

**特点**：
- ✅ **简单直接**：一个文件包含所有指令
- ✅ **面向流程**：定义明确的步骤和输出格式
- ✅ **与 Workflow 配合**：通过 `.agent/workflows/*.md` 调用

**结构示例**：
```markdown
# Skill: Architect Planning

## Goal
Create a technical implementation plan before writing code.

## Steps
1. Analyze Requirements
2. Check Constraints
3. Output Format

## Example Output
- System: InventoryManager.cs (Logic)
- Data: ItemDb.asset (Odin SO)
- UI: InventoryUI.nani (View)
```

**使用方式**：
```bash
# 用户在对话中调用 workflow
/architect

# Workflow 自动读取并执行 Architect.md 中的指令
```

---

### 📁 格式 B：Remotion-Style Skills（结构化技能）

**文件格式**：目录结构，包含 `SKILL.md` + `rules/` 子目录  
**示例**：`architecture/`, `naninovel/`

**特点**：
- ✅ **结构化**：主文件 + 多个细分规则文件
- ✅ **模块化**：每个规则文件专注一个主题
- ✅ **易扩展**：添加新规则只需新增 `.md` 文件
- ✅ **YAML 元数据**：包含 `name`, `description`, `tags`

**结构示例**：
```
architecture/
├── SKILL.md                           # 概述 + 索引
└── rules/                             # 细分规则
    ├── fundamental-fixes.md           # 寻求根本解决方案原则
    ├── namespace-structure.md         # 命名空间组织
    └── composition-over-inheritance.md # 组合优于继承
```

**SKILL.md 格式**：
```markdown
---
name: architecture-best-practices
description: Code organization and architectural principles
metadata:
  tags: architecture, code-organization, patterns
---

## When to use
Use this skill whenever you are working on Unity C# code organization.

## How to use
Read individual rule files:
- [rules/fundamental-fixes.md](rules/fundamental-fixes.md) - Critical debugging principle
- [rules/namespace-structure.md](rules/namespace-structure.md) - Namespace organization
```

**Rule 文件格式**：
```markdown
---
name: fundamental-fixes
description: Principle of seeking fundamental solutions
metadata:
  tags: debugging, problem-solving, root-cause-analysis
---

# Content with code examples...
```

**使用方式**：
```bash
# AI Agent 自动读取相关 skill 当需要时
# 或者明确调用：View the architecture skill for debugging principles
```

---

## 🔄 两种格式对比

| 特性         | Workflow-Based (A)     | Remotion-Style (B)     |
| ------------ | ---------------------- | ---------------------- |
| **文件数量** | 单文件                 | 多文件（目录）         |
| **组织方式** | 线性步骤               | 结构化规则库           |
| **调用方式** | 通过 workflow 明确调用 | Agent 自动查询相关规则 |
| **扩展性**   | 需修改整个文件         | 添加新规则文件即可     |
| **元数据**   | 无                     | YAML front-matter      |
| **适用场景** | 明确的工作流程         | 知识库、最佳实践       |
| **示例**     | "执行 Code Review"     | "Unity C# 架构模式"    |

---

## 📚 当前 Skills 清单

### Workflow-Based Skills

#### 🏗️ Architect
**用途**：创建技术实现计划  
**触发**：`/architect`  
**输出**：文件结构、数据结构、执行流程

#### 🔍 CodeReview
**用途**：代码质量审查  
**触发**：`/code-review`  
**标准**：架构、性能、可读性、错误处理  
**输出**：更新 `docs/rules/rules_code_structure.md`

#### 🐛 Debug
**用途**：诊断和修复问题  
**触发**：`/debug`  
**方法**：系统性排查、日志分析

#### 📖 StoryWeaver
**用途**：Naninovel 故事脚本编写  
**触发**：`/story-weaver`  
**范围**：`.nani` 脚本、对话流程

#### ✅ Verify
**用途**：验证更改并生成文档
**触发**：`/verify`
**输出**：Walkthrough 文档

#### 🔧 SmartFix
**用途**：智能问题修复（自动分类并路由到合适的修复方向）
**触发**：`/smart-fix`
**来源**：改编自 [wshobson/commands](https://github.com/wshobson/commands)
**特点**：自动识别 Bug/性能/构建/架构问题，禁止临时补丁

#### ⚡ PerformanceOpt
**用途**：Unity 运行时性能分析与优化
**触发**：`/perf`
**来源**：改编自 [wshobson/commands](https://github.com/wshobson/commands)
**覆盖**：CPU、GC、内存、GPU、UniTask 异步

#### 🏦 TechDebt
**用途**：技术债审计与清偿计划
**触发**：`/tech-debt`
**来源**：改编自 [wshobson/commands](https://github.com/wshobson/commands)
**输出**：债务清单 + 优先清偿队列

#### 🔬 FullReview
**用途**：六维度深度代码审查（比 CodeReview 更全面）
**触发**：`/full-review`
**来源**：改编自 [wshobson/commands](https://github.com/wshobson/commands)
**维度**：代码质量、安全健壮、架构合规、性能、可测试性、Naninovel 专项

---

### Remotion-Style Skills

#### 🏛️ architecture
**用途**：Unity C# 架构最佳实践  
**规则**：
- `fundamental-fixes.md` - **核心**：寻求根本解决方案而非 workaround
- `namespace-structure.md` - 命名空间组织规范
- `composition-over-inheritance.md` - 组合优于继承原则

#### 🎮 naninovel
**用途**：Naninovel 集成模式  
**规则**：
- `managed-text.md` - **关键**：使用 Naninovel Managed Text 实现本地化（禁用 I2）
- `camera-integration.md` - URP 摄像机堆叠和集成

---

## 🎯 使用建议

### 何时使用 Workflow-Based Skills？
- 你需要执行**明确的流程**（如 Code Review、规划架构）
- 通过 `/command` 显式调用
- 需要标准化的输出格式

### 何时使用 Remotion-Style Skills？
- 你需要**查询最佳实践**（如"如何组织命名空间？"）
- AI Agent 自动选择相关规则
- 需要细粒度的、模块化的知识库

### 如何选择创建哪种格式？
- **新增工作流程** → 创建 Workflow-Based Skill
- **新增知识库/规范** → 创建 Remotion-Style Skill

---

## 🛠️ 创建新 Skill

### 创建 Workflow-Based Skill

1. 在 `.agent/skills/` 创建 `YourSkill.md`
2. 定义 Goal、Steps、Output Format
3. 在 `.agent/workflows/` 创建对应的 `your-skill.md`

### 创建 Remotion-Style Skill

1. 创建目录结构：
```bash
.agent/skills/your-skill/
├── SKILL.md
└── rules/
    ├── rule1.md
    └── rule2.md
```

2. 在 `SKILL.md` 添加 YAML front-matter 和规则索引
3. 在每个规则文件添加 YAML front-matter 和详细内容

---

## 📖 参考

- **Remotion Skills 原仓库**：https://github.com/remotion-dev/skills
- **Skills 实现计划**：查看 `brain/skills_implementation_plan.md`
- **目录结构文档**：查看 `brain/directory_structure.md`

---

## ⚡ 快速开始

**使用现有 Workflow Skills**：
```bash
/architect      # 规划架构
/code-review    # 代码审查（快速）
/full-review    # 代码审查（深度六维度）
/debug          # 调试问题
/smart-fix      # 智能修复（自动分类问题类型）
/perf           # Unity 性能优化
/tech-debt      # 技术债审计
/story-weaver   # 编写故事
/verify         # 验证和文档
```

**查询 Remotion-Style Skills**：
```
"How should I organize namespaces?"
→ AI 自动读取 architecture/rules/namespace-structure.md

"What's the localization strategy?"
→ AI 自动读取 naninovel/rules/managed-text.md
```

---

**最后更新**：2026-02-19  
**维护者**：EndGods 项目团队
