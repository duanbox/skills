---
name: StoryWeaver
description: Write or revise EndGods narrative scenes in valid Naninovel syntax. Use when working on dialogue, choices, story beats, or scenarios, but not video storyboards or C# story systems.
---

# Story Weaver

Narrative consultation and drafting are read-only unless the user explicitly asks to edit scenario files.

## Authority and context

1. Read AGENTS.md and Docs/INDEX.md.
2. Read Docs/Knowledge/story_style_guide.md.
3. Read Docs/Knowledge/naninovel.md and Docs/Knowledge/naninovel_script_management.md before producing or editing nani content.
4. Read the nearest story, demo, character, location, quest, or systemspec authority for the requested scene.
5. Inspect two or three neighboring nani scenarios and confirm character IDs, appearances, variables, labels, and custom commands already used by the project.

## Writing contract

- Preserve established lore, character voice, stakes, chronology, and terminology.
- Do not invent gameplay systems, commands, variables, assets, character IDs, or branch consequences to fill space.
- Keep free actions and progression rules consistent with their systemspec.
- Separate narrative intent from technical handoff when a new command or runtime behavior would be required.
- Route video or Seedance planning to storyboard-to-seedance.
- Route story-system C# implementation to the relevant systemspec and architecture workflow.

## Naninovel syntax

Follow working project syntax:

- Use semicolon-prefixed comments.
- Use at-prefixed commands that already exist in the project.
- Use CharacterId: dialogue for character speech.
- Use existing appearance, label, variable, choice, and custom-command forms from nearby scenarios.
- Do not emit hash-prefixed character headings or an invented print command.
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
