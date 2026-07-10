---
name: narrative-consistency-review
description: Audit EndGods canon across two or more authorities or scenarios. Use for lore, character knowledge or voice, chronology, causality, world-rule, terminology, setup/payoff, or narrative-state conflicts. Read-only; not for drafting, choice design, syntax fixes, or implementation.
---

# Narrative Consistency Review

Review read-only. Do not edit files, invent canon, or auto-fix findings.

## Authority router

1. Read `AGENTS.md`, then use `Docs/INDEX.md` to locate current authorities.
2. Use `Docs/BackGround/story.md` and `Docs/BackGround/character.md` for canon and character facts.
3. Use `Docs/Knowledge/story_style_guide.md` and its listed anchor scenarios for voice and textual style.
4. For chronology and progression, read the nearest current story or demo plan, affected systemspec, real data, and target scenarios.
5. For narrative variables and Commands, inspect `Docs/Knowledge/naninovel.md`, `Docs/systemspec/story_commands.md`, `CustomVariablesConfiguration.asset`, and the concrete C# Command implementation.
6. Inspect the target plus two or three neighboring `.nani` scenarios. Treat `Docs/Knowledge/naninovel_script_management.md` as a candidate design unless current implementation proves otherwise.

When sources disagree, follow the most specific current authority and report the losing source as stale or conflicting. Return `INCONCLUSIVE` when authority cannot be established.

## Workflow

1. Bound the artifacts, chapter range, characters, locations, and time window.
2. Build a claim ledger with `subject | claim | timepoint | source | authority status | state owner`.
3. Compare every suspected contradiction across at least two sources and cite both sides.
4. Check character identity, knowledge, motivation, voice, chronology, location, causality, world rules, terminology, setup/payoff, and narrative-state references.
5. Exclude intentional lies, unreliable narration, point-of-view differences, time jumps, deliberate ambiguity, and superseded plans before reporting a defect.
6. Trace variables and Commands to their actual owner. A narrative variable is not gameplay authority unless the current contract says so.
7. Classify the result, identify the authority winner, and route remediation without applying it.

## Finding rules

- Report only actionable contradictions supported by file-and-line evidence.
- Use P0 for a release-blocking mainline or irreversible-state contradiction, P1 for a hard current-canon or chronology conflict, P2 for localized voice, terminology, setup, or state-reference drift, and P3 for a low-risk consistency note.
- Do not turn prose preference, missing runtime proof, or an unimplemented candidate design into a confirmed narrative defect.
- Do not critique a single file's prose quality or decide whether a player choice is meaningful.

## Output

- Verdict: `PASS`, `FAIL`, or `INCONCLUSIVE`
- Scope and authorities inspected
- Claim ledger summary
- Findings ordered by P0 through P3
- Evidence gaps
- Owning handoff

For each finding report the title, severity, Evidence A `file:line`, Evidence B `file:line`, authority winner or unresolved conflict, player impact, and smallest owning follow-up.

## Handoff

- Prose or canon revision: `StoryWeaver`
- Choice honesty or branch behavior: `interactive-narrative-review`
- Naninovel syntax, variables, Commands, or import: `naninovel`
- Gameplay state: `VN Game Systems` or architecture
- Runtime proof: `Verify`

## Method provenance

The evidence-pair and false-positive checks are an EndGods-specific adaptation inspired by Wordflowlab's MIT-licensed [story-consistency-monitor](https://github.com/wordflowlab/novel-writer-skills/blob/5bc9b373ff609e8910e0e8d179e4a697bf2b1268/templates/skills/quality-assurance/consistency-checker/SKILL.md). Project authorities and this workflow control behavior; no background monitor, fixed novel directory, or automatic repair is adopted.
