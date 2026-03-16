---
name: Art & Assets Pipeline
description: Standards and workflows for creating, managing, and integrating art assets (Characters, CGs, UI, VFX) using AI tools and Unity best practices.
---

# Art & Assets Pipeline

## Overview
This skill covers the end-to-end pipeline for game art assets, from AI generation to Unity integration. It strictly follows the "Ink-Wash Wasteland + Cyber Neon" aesthetic and emphasizes high-resolution sources (4K) optimized for game performance (Addressables, Atlases).

## Key Concepts
- **AIGC Workflow**: Using AI (zimage-turbo, flux2-klein) with natural language prompts to generate consistent assets.
- **Resolution Strategy**: Authoring in 4K (Source) -> Exporting in FHD/Optimized formats.
- **Format Standards**: PSD/PSB for layered characters/parallax, SVG for scalable UI/Icons.
- **Naming Conventions**: Strict `Category_SubCategory_Name_Variant` format for automation compatibility.

## Rules Index
- [General Art Standards](rules/general-art-standards.md): Resolution, formats, and naming rules.
- [AI Prompts Library](rules/ai-prompts.md): Comprehensive prompt library for characters, UI, and scenes.
