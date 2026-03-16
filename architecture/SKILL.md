---
name: Architecture & Code Structure
description: Core architectural principles, project structure, and coding standards for the EndGods project.
---

# Architecture & Code Structure

## Overview
This skill defines the foundational rules for the EndGods codebase. It enforces strict separation of concerns (Logic vs. Data vs. View), a standardized directory structure, and specific coding practices to ensure scalability and maintainability in a mixed Unity/Naninovel environment.

## Key Concepts
- **Isolation**: `Assets/Game` is the only development directory.
- **Data-Driven**: Logic resides in Scripts, Data in ScriptableObjects/CSV.
- **Addressables First**: All dynamic assets are managed via Addressables.
- **Naninovel Integration**: C# Managers handle logic, Naninovel handles View/Story.

## Rules Index
- [Folder Structure](rules/folder-structure.md): Detailed breakdown of the `Assets/Game` directory and file organization.
- [Project Code Map](rules/project-code-map.md): Analysis of core scripts, their roles, and interactions.
- [Tech Stack & Standards](rules/tech-stack.md): Defined technology stack (Naninovel, Odin, UniTask, etc.) and coding conventions.
