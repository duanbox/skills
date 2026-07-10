---
name: game-ui-designer
description: Design and review EndGods player-facing interfaces. Use when deciding or auditing screen hierarchy, flow, navigation, accessibility, feedback, economy clarity, or onboarding.
---

# Game UI Designer

## Overview

Use this skill for gameplay-facing interface design and implementation guidance. It is optimized for fast decisions on hierarchy, interaction cost, readability under pressure, controller navigation, economy surfaces, and tutorial pacing.

## Use This Skill

- Designing or refactoring HUD, crosshair, ability bars, health bars, boss frames, minimaps, quest trackers, or combat notifications
- Building main menu, pause menu, settings, map, codex, quest log, or game over flows
- Designing inventory, equipment, crafting, loot, vendor, gacha, or in-game shop screens
- Building first-time user experience, tutorials, hints, or guided onboarding
- Reviewing a game UI implementation for clarity, friction, accessibility, or production readiness

## Skip This Skill

- Backend economy logic with no UI impact
- Pure VFX work with no player interaction layer
- Engine tooling or editor extensions for developers only
- Generic web marketing pages unrelated to the game client

## Working Style

Prioritize the following in order:

1. Moment-to-moment readability
2. Input efficiency for controller, keyboard/mouse, and touch if relevant
3. Information hierarchy under stress
4. Feedback quality and state clarity
5. Economy trust and tutorial pacing
6. Visual style consistency

## Workflow

### 1. Classify the Surface

Decide which primary surface the task belongs to:

- `combat-hud`
- `menu-navigation`
- `inventory-loadout`
- `shop-economy`
- `onboarding-tutorial`

Then read only the matching reference file under `references/`.

### 2. Identify Player Context

Before proposing layouts or code, anchor the design to:

- camera distance: first-person, third-person, isometric, side-view, top-down
- session intensity: combat, traversal, downtime, meta-progression
- primary input: controller, keyboard/mouse, touch, hybrid
- decision speed: split-second, short, deliberate
- information permanence: always-on, contextual, or hidden by default

If the request is vague, infer the minimum viable assumptions and state them briefly.

### 3. Produce Output in This Shape

When designing or reviewing, structure the answer as:

1. `Surface and context`
2. `Layout decision`
3. `Interaction and navigation`
4. `Feedback and animation`
5. `Accessibility and edge cases`
6. `Implementation notes`

Keep recommendations concrete. Prefer placement, sizing, navigation, and state rules over abstract taste language.

## Core Rules

### HUD

- Reserve the center for aiming, enemies, and hazards; do not park persistent UI in the foveal zone.
- Persistent combat information must be scannable in under one second.
- Show only the stats that affect the next player decision.
- Use motion and flashes for change, not for idle decoration.
- Put urgent survival information near the player focal path; push secondary systems outward.

### Menus

- Every menu must answer: where am I, what can I do here, how do I go back.
- Controller navigation must be deterministic; avoid invisible focus jumps.
- Split broad navigation from deep configuration. Do not dump all settings into one flat list.
- Settings changes should preview immediately when safe, otherwise require explicit confirm/revert.

### Inventory and Shops

- Separate browse, compare, and commit states clearly.
- Always expose item rarity, slot, ownership/equipped state, and value without opening a detail modal when possible.
- Make currency sources and sinks trustworthy; never obscure the final spend or reward delta.
- Prevent misclick purchases with confirmation only for high-risk actions; low-value repeated actions should stay fast.

### Tutorials

- Teach only what unlocks the next successful action.
- Prefer contextual prompts over long pre-play instruction walls.
- Gate on demonstrated understanding where failure cost is low.
- Do not interrupt mastery loops with repeated modal teaching.

## Review Mode

When reviewing an existing UI, look for:

- unreadable combat information
- duplicated or competing focal points
- controller dead ends
- hidden economy risk or weak purchase clarity
- tutorial overload or prompt spam
- animation that delays player agency

Call out findings in severity order. Include exact screen or component names when possible.

## Reference Map

- HUD and combat overlays: `references/hud.md`
- Main menu, pause, settings, and navigation shells: `references/menus.md`
- Inventory, equipment, loot, crafting, and shops: `references/inventory-shop.md`
- Tutorial prompts, first-time flows, and guided onboarding: `references/onboarding.md`
- Fast audit checklist for implementation review: `references/review-checklist.md`

## EndGods Implementation Handoff

This skill decides the player experience; it does not override EndGods Unity implementation rules. Before implementation, hand off:

- exact screen and player-visible states, including empty, loading, locked, error, and repeat-entry states where relevant
- player context, primary task, information hierarchy, entry/exit paths, and back behavior
- keyboard/mouse and controller focus order, destructive-action safeguards, and accessibility requirements
- data displayed, owning gameplay service, update trigger, and null/ unavailable behavior
- animation intent, maximum acceptable interaction delay, and reduced-motion alternative
- approved mockup, `.pen`, per-UI brief, or existing screen used as visual authority

The implementing agent must then use the `UI & UX Design` skill, read `Docs/Pipeline/ui-vibe-coding-pipeline.md`, inspect the real Scene/Prefab and bindings, and produce the required Scene/GameView or PlayMode screenshot evidence. A design recommendation never authorizes runtime construction of final painterly components or replacement of Scene/Prefab layout authority.

## Output Constraints

- Prefer rules, wireframe logic, and implementation notes over lore or branding copy.
- If the user asks for code, preserve the project's existing engine and UI framework patterns.
- For Unity, mention Canvas grouping, layout hierarchy, safe area handling, event system focus, and prefab reuse where relevant.
- If proposing animations, keep them short and stateful; avoid long tween chains that slow interaction.
