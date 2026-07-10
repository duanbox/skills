---
name: interactive-narrative-review
description: Audit existing EndGods choices and branch contracts across .nani, variables, Commands, and C# owners. Use for agency, feedback, consequences, reconvergence, gates, time cost, fail-forward, repeat entry, save/load, or gameplay handoff. Read-only; not for writing, lore review, syntax fixes, or implementation.
---

# Interactive Narrative Review

Review read-only. Trace existing contracts; do not create branches, state, dialogue, or fixes.

## Authority router

1. Read `AGENTS.md` and `Docs/INDEX.md`.
2. Use `Docs/Knowledge/story_style_guide.md` for choice wording, immediate feedback, and textual QA.
3. Inspect the target plus two or three neighboring `.nani` scenarios.
4. Use `Docs/Knowledge/naninovel.md`, `Docs/systemspec/story_commands.md`, and concrete C# implementations for variables, conditions, and Commands.
5. Load only affected system authorities: time, save, quest, progression, map, town, dungeon, inventory, affinity, empress, or scheduler.
6. For time-sensitive choices, read the whitelist and progression sections in `Docs/systemspec/time_system.md`. For visible entry or exit behavior, read the applicable acceptance matrix.

Treat `Docs/Knowledge/naninovel_script_management.md` as a proposed diagnostic model unless current tooling proves it is implemented.

## Workflow

1. Bound one flow from entry through choice, state write, consequence, reconvergence, and exit.
2. Build a choice-contract matrix: `intent | immediate feedback | write and owner | downstream read | reconvergence or exit | time cost | repeat and save`.
3. Trace every state write to one owner and every delayed consequence to a reachable downstream read.
4. Separate narrative-only Naninovel state from authoritative C# gameplay state.
5. Check repeat entry, one-time rewards, idempotence, save/load restoration, invalid or legacy state, and missed-event fail-forward.
6. Check that only documented time-cost actions advance time and that ordinary movement, short dialogue, browsing, and quest intake remain free where specified.
7. Confirm the script returns control to its owning gameplay host instead of using long Naninovel jump chains to replace map or system progression.
8. Separate design defects, implementation defects, and evidence gaps. Static evidence alone cannot prove runtime behavior.

## Review dimensions

- Options express distinct player intentions and do not disguise synonyms as agency.
- The player receives prompt feedback proportionate to the choice.
- Delayed consequences have both a write and a reachable read.
- Reconverging branches remain valid when feedback, state, or later variation preserves the choice.
- UI wording does not promise an unimplemented route, reward, relationship change, or system response.
- Missable content has an intentional consequence or fail-forward path.
- Re-entry cannot duplicate rewards or irreversible progression.
- State ownership, save behavior, time cost, and gameplay handoff match current systemspecs.

## Non-goals

- Do not require every option to create a unique ending or numeric change.
- Do not require Backlog or Rollback; EndGods intentionally disables them.
- Do not introduce a generic StoryManager, parallel flag store, dialogue graph, multi-axis romance model, or full dating system.
- Do not rewrite choice text; route that work to `StoryWeaver`.
- Do not fix `.nani`, C#, data, or Scene files.

## Output

- Verdict: `PASS`, `FAIL`, or `INCONCLUSIVE`
- Flow scope and state owners
- Choice-contract matrix
- Findings ordered by P0 through P3 with exact evidence, broken contract, player impact, and owner
- Evidence gaps and required runtime verification
- Smallest owning handoff

Route general canon contradictions to `narrative-consistency-review`, syntax and Command issues to `naninovel`, gameplay ownership to `VN Game Systems`, and runtime proof to `Verify`.

## Method provenance

The review is independently written from EndGods authorities. GitHub interactive-fiction patterns were used only as comparative research; no unlicensed framework text, parallel story state, Godot architecture, rollback system, or romance subsystem is copied into this skill.
