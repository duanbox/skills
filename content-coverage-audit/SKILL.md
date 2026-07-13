---
name: content-coverage-audit
description: Audit EndGods planned content against data, scripts, assets, Unity wiring, and fresh evidence. Use for demo, quest, event, map, town, dungeon, story, UI, audio, video, or character coverage. Read-only; not for ideation, generation, implementation, or general code review.
---

# Content Coverage Audit

Audit read-only. Do not repair implementation, generate content, promote assets, or rewrite plans during the audit.

## Authority router

1. Read `AGENTS.md`, then use `Docs/INDEX.md` to locate the current authority for every content area in scope.
2. For the D1-D10 demo, use `Docs/Todo/demo/demo_content_systems_plan.md` as the plan authority and load the matching systemspecs, SOPs, rules, and acceptance matrices.
3. For time, tasks, and POIs, include `Docs/systemspec/time_system.md` and `Docs/systemspec/task_time_poi_design.md` where applicable.
4. Inspect real CSV rows and IDs, imported database assets, `.nani` scenarios, Commands, C# owners, Scene/Prefab references, art/audio/video assets, and current tests or screenshots.
5. Treat TODO text, candidate designs, unpromoted assets, generated stubs, and filenames as claims to verify, not proof of playable content.

## Coverage unit

Define the smallest meaningful unit before counting coverage: quest ID, event ID, map node, town/POI, dungeon floor or node, scenario entry, character event, UI state, audio key, video shot, or acceptance requirement. Never use raw file count as the completion metric.

Trace each unit through:

`authority requirement -> stable ID or entry -> source data/script/asset -> imported or serialized wiring -> runtime owner and reachable entry -> player-visible result -> fresh proof`

## Workflow

1. Bound the milestone, content types, player path, and explicit non-goals.
2. Extract required units and acceptance outcomes from the most specific current authority.
3. Trace every unit through the coverage chain and record exact evidence.
4. Classify it as `PROVEN`, `WIRED_UNVERIFIED`, `PARTIAL`, `PLANNED_ONLY`, `MISSING`, `BLOCKED`, or `OUT_OF_SCOPE`.
5. Separate missing implementation, broken wiring, stale plan, conflicting authority, missing asset, and missing proof.
6. Check entry and exit reachability, prerequisite gates, IDs and foreign keys, repeat entry, reward/state effects, and required presentation assets.
7. Rank only blocking or actionable gaps and identify the smallest owning follow-up.

## Evidence rules

- Cite file and line, CSV row or ID, Command target, serialized reference or GUID, acceptance ID, test result, importer result, screenshot, or runtime observation as appropriate.
- A passing lint, compile, or isolated test proves only its stated scope.
- A file or asset existing does not prove import, wiring, reachability, presentation, or runtime behavior.
- Do not infer implementation from a plan checkbox or describe unavailable runtime evidence as PASS.

## Guardrails

- Do not fill empty calendar slots or invent training, work, healing, crafting, escort, puzzle, arena, daily, or live-operations systems.
- Do not convert free actions into time-cost actions.
- Do not broaden P0 with attractive but unapproved content.
- Do not replace `CodeReview`, including its deep/comprehensive mode; use this skill only for plan-to-playable content coverage.

## Output

- Verdict: `COVERED`, `GAPS_FOUND`, or `INCONCLUSIVE`
- Scope, non-goals, and authority documents
- Coverage table: `unit | requirement | data/script/asset | runtime entry | proof | status`
- Authority conflicts and stale claims
- Hard gaps and unverified wiring ordered by player-path impact
- Smallest next bounded tasks and owning skills

Route planning to `Architect`, story writing to `StoryWeaver`, Naninovel work to `naninovel`, gameplay ownership to `VN Game Systems`, assets to `art-pipeline`, and fresh runtime evidence to `Verify`.

## Method provenance

This EndGods-specific ID and entrypoint audit adapts the coverage and consistency intent of Donchitos' MIT-licensed [content-audit](https://github.com/Donchitos/Claude-Code-Game-Studios/blob/main/.claude/skills/content-audit/SKILL.md) and [consistency-check](https://github.com/Donchitos/Claude-Code-Game-Studios/blob/main/.claude/skills/consistency-check/SKILL.md). Their generic GDD paths, file-count metrics, role simulation, and automatic report writes are not adopted.
