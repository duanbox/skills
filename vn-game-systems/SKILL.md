---
name: VN Game Systems
description: Implement EndGods map, town, dungeon, inventory, time, save, and Naninovel-bridge runtime contracts. Use when changing authoritative state, service ownership, persistence, command handoff, or cross-system flow; not for content-only or UI-only work.
---

# VN Game Systems

This skill owns system-layer gameplay outside Naninovel presentation. Use the `naninovel` skill alongside it only when `.nani` syntax, Command APIs, variables, or narrative UI are also in scope.

## Authority Router

Read `AGENTS.md` and `Docs/INDEX.md`, then load only the matching authorities:

| Area | Primary authority |
|---|---|
| World map, nodes, visibility, travel | `Docs/systemspec/map_system.md`, `Docs/rules/rules_map.md` |
| Map locations, icons, town/dungeon mapping | `Docs/systemspec/map_locations_design.md`, `Docs/sop/map_node_workflow.md` |
| Dungeon flow and node contracts | `Docs/systemspec/dungeon_system.md` |
| Inventory and item ownership | `Docs/systemspec/inventory_system.md` |
| Town and POI behavior | `Docs/systemspec/town_system.md`, `Docs/Knowledge/town_poi_editor.md` |
| Time-consuming actions and calendar | `Docs/systemspec/time_system.md` |
| Persistence and compatibility | `Docs/systemspec/save_system.md` |
| Story commands and Naninovel bridge | `Docs/systemspec/story_commands.md`, `Docs/Knowledge/naninovel.md` |
| Data import and assets | `Docs/rules/rules_resources.md`, `Docs/rules/rules_assets.md` |

When these sources disagree with an old plan or copied rule, follow the current systemspec and real implementation, and report the conflict.

## System Boundaries

- Keep authoritative gameplay state in C# system/service layers. UI, Naninovel, animation, and VFX present or orchestrate that state.
- Use explicit service interfaces for commands, results, and async flows. EventBus broadcasts facts after they happen; it does not carry request/response gameplay flows.
- Resolve services at composition or `*Dependencies.cs` boundaries, then cache or inject them.
- Map state, region/node visibility, current location, inventory, dungeon state, time, and save compatibility must each have one authoritative owner.
- Normalize legacy, empty, or unknown saved IDs through the documented compatibility path; do not silently send the player to a deprecated start node.
- Wait for Naninovel through its real player/service state and explicit completion contract. Do not add timing delays or polling retries to guess when story playback ended.

## Map and Story Contract

1. A map/town/dungeon event script describes the event at that location.
2. Script completion returns control to the owning gameplay system.
3. Region and node progression is driven by system state and project Commands, not a long chain of cross-region `@goto` calls.
4. Entry scripts initialize or present the current location; they do not secretly orchestrate the whole mainline.
5. Changes to story-triggered state must be checked for repeat entry, one-time rewards, save/load, and continue-game behavior.

## Workflow

1. Define the player-visible failure or required behavior and its pass/fail signal.
2. Inspect live Unity state first only when runtime or Scene risk enters scope: active/inactive objects, serialized references, registered services, current state, and Console. Otherwise start from the relevant authority, code, or data and do not open Unity merely to satisfy this skill.
3. Inspect the relevant C# path, database/CSV/SO data, and only then the `.nani` script or Command syntax when those layers are in scope.
4. Read two or three current usages before modifying an interface, state field, node type, or bridge.
5. Use `Tools/Game/Unified Data Importer` for CSV-to-ScriptableObject changes; do not create a side importer.
6. Keep the change surgical. If it crosses systems or exceeds five files, restate scope and verification before continuing.

## Verification

Classify verification under the current `AGENTS.md` risk lane and cover only contracts changed by this task.

- Runtime C# changes: run the project rule scan, Unity recompile, and Console check as required by `AGENTS.md`.
- Map/data changes: verify CSV, imported database, Scene/Prefab, icon/reference wiring, and affected node flow.
- Save/state changes: test old/default/invalid values and save-load round-trip behavior.
- Dungeon/inventory/time changes: run the smallest focused system or gameplay-flow test.
- Naninovel bridge changes: reimport affected scripts, check parse errors, and run the actual handoff flow.
- UI or visible map/dungeon changes: include real Scene/GameView or PlayMode screenshot evidence.

Skip Unity live-state inspection, recompilation, tests, imports, or screenshots whose risk did not enter scope; report the applicable `AGENTS.md` skip reason instead of manufacturing evidence.

Report the authority documents used, state owner, changed contracts, data/import effects, tests and screenshots, and any validation that remains blocked.
