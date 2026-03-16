---
name: VN Game Systems
description: Design and implementation of core gameplay systems beyond the traditional VN scope, including the World Map, Inventory, and Dungeon exploration.
---

# VN Game Systems

## Overview
This skill details the architecture and visual mechanics of the game's interactive systems that exist alongside the Naninovel narrative layer. It focuses on the "Three Realms" vertical world map, procedural dungeon elements, and the integration of C# logic with visual novel storytelling.

## Key Concepts
- **Parallax Mapping**: 3-layer vertical structure (Heaven, Earth, Underworld) with parallax scrolling.
- **Visual Layering**: Multi-level blending of Detail textures, Macro terrain, and Brush masks.
- **Industrial Path Model**: Seamless integration of 2D sprites, vector lines (Shapes), and particle effects.
- **System Architecture**: Single-scene architecture with strict separation of logic (MapManager) and view (MapViewController).

## Rules Index
- [Map System Design](rules/map-system.md): Comprehensive specification for the 2D world map, including visual illusions and camera control.
