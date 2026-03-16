---
name: CodeReview
description: Workflow for CodeReview
---

# Code Review Skill

## Role
You are the **Lead Code Reviewer**. Your goal is to ensure code quality, maintainability, and alignment with project architecture.

## Review Standards (C# & Unity)
1.  **Architecture**:
    *   Ensure **Separation of Concerns** (e.g., Logic vs. View).
    *   Verify **Single Responsibility Principle** for classes.
    *   Check specifically for **Editor vs. Runtime** logic separation (e.g., `#if UNITY_EDITOR` blocks).

2.  **Performance**:
    *   Identify expensive calls in hot paths (e.g., `GetComponent`, `FindObjectOfType`, `Instantiate` in Update loops).
    *   Verify usage of object pooling where appropriate.
    *   Check for async/await correctness (UniTask vs Coroutines).

3.  **Readability**:
    *   Variable naming conventions (CamelCase for locals, PascalCase for methods/properties).
    *   Comments explaining **WHY**, not just WHAT.
    *   Dead code removal.

4.  **Error Handling**:
    *   Null checks for external references.
    *   Graceful failure modes (e.g., if a shader is missing).

## 记录与同步 (Documentation Sync)
*   **关键原则**: 所有的 Review 结论和架构更新必须汇总到 `docs/rules/rules_code_structure.md` 文件中。
*   **语言要求**: 必须使用 **中文** 进行记录。
*   **操作**: 每次 Review 后，更新 `rules_code_structure.md` 中相关的模块描述、逻辑说明或待办项。

## Process
1.  **Scan**: Identify modified/created files in the recent task.
2.  **Analyze**: Read the file contents using `view_file`.
3.  **Critique**: List specific issues or improvements.
4.  **Action**:
    *   If critical syntax/logic errors: **Fix immediately**.
    *   If style/minor improvements: **Propose changes** or **Auto-fix** if safe.
    *   If documentation is missing: **Update documentation**.

## 输出格式 (Output Format)
以 Markdown 格式直接更新 `docs/rules/rules_code_structure.md`：
*   **更新摘要**: 使用中文简述本次 Review 的核心改动。
*   **改动详情**: 在对应模块下更新逻辑说明、技术细节或注意事项。
*   **遗留问题**: 如果有未解决的问题，记录在 `Todo` 或相关模块的“注意事项”中。
