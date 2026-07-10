---
name: StoryWeaver
description: Write or revise EndGods narrative scenes in valid Naninovel syntax. Use when working on dialogue, choices, story beats, or scenarios, but not video storyboards or C# story systems.
---

# Story Weaver

Narrative consultation and drafting are read-only unless the user explicitly asks to edit scenario files.

## Authority and context

1. Read AGENTS.md and Docs/INDEX.md.
2. Read Docs/Knowledge/story_style_guide.md.
3. Read Docs/Knowledge/naninovel.md before producing or editing nani content. Read Docs/Knowledge/naninovel_script_management.md only when its proposed tooling is relevant, and treat it as a candidate design unless the current implementation proves otherwise.
4. Read the nearest story, demo, character, location, quest, or systemspec authority for the requested scene.
5. Inspect two or three neighboring nani scenarios and confirm character IDs, appearances, variables, labels, and custom commands already used by the project.

## Writing contract

- Preserve established lore, character voice, stakes, chronology, and terminology.
- Do not invent gameplay systems, commands, variables, assets, character IDs, or branch consequences to fill space.
- Keep free actions and progression rules consistent with their systemspec.
- Separate narrative intent from technical handoff when a new command or runtime behavior would be required.
- Route video or Seedance planning to storyboard-to-seedance.
- Route story-system C# implementation to the relevant systemspec and architecture workflow.
- Route cross-source canon and chronology audits to `narrative-consistency-review`.
- Route existing choice, consequence, and branch-contract audits to `interactive-narrative-review`.

## Scene and dialogue pass

1. State the scene's player-visible goal, pressure, and change. Atmosphere and relationship scenes may change understanding or tension instead of external state; do not force every scene into one formula.
2. Anchor each speaker with what they want, fear, notice, conceal, and how their rhythm and vocabulary differ from nearby characters.
3. Let dialogue carry more than exposition when natural: action, character, power, world detail, setup, or consequence. Keep motives partly in action and subtext instead of making every character explain themselves.
4. Keep choices to two through four distinct player intentions. Give prompt feedback and do not promise a branch, reward, relationship change, or system response that is not implemented.
5. Compare the draft with the style guide anchors before treating voice or pacing as project-aligned.

## Naninovel syntax

Follow working project syntax:

- Use semicolon-prefixed comments.
- Use at-prefixed commands that already exist in the project.
- Preserve the file-local speech form. New core scenarios currently prefer the `# CharacterId` block form documented by the style guide; older scenarios may retain `CharacterId:` when that is their established syntax.
- Use existing appearance, label, variable, choice, and custom-command forms from nearby scenarios.
- Do not introduce `@print` for ordinary dialogue or invent a print command.
- Preserve localization and Managed Text rules from project documentation.
- Keep text files in the project-required UTF-8 format and avoid mojibake.

## Editing and validation

When file edits are explicitly requested:

1. Limit changes to the authorized scenarios and required documentation.
2. Confirm every referenced character, appearance, background, label, variable, and custom command exists.
3. Reimport or validate Naninovel scripts using the documented project workflow.
4. Inspect Unity Console for parse, import, resource, or initialization errors.
5. Run a focused story flow only when runtime commands, variables, branching, or scene handoff changed.
6. Report whether unrelated nani files remained unchanged when that boundary matters.

## Output

Report authority sources, narrative intent, changed or drafted scenes, syntax and reference checks, unresolved lore questions, and validation evidence. Never call a draft integrated until the project scenario and Unity evidence exist.
