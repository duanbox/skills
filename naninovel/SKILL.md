---
name: naninovel
description: Implement and verify EndGods Naninovel scripts and integration. Use when changing .nani syntax, Commands, variables, localization, actors, audio or backgrounds, import, narrative UI, or gameplay handoff.
---

# Naninovel Integration

The installed package, current project Commands, and repository specifications are authoritative. The audited package baseline is `1.20.241120`; verify `Packages/com.elringus.naninovel/package.json` before using version-specific APIs.

## Scope Boundary

- `naninovel` owns `.nani` syntax, engine Commands, custom Command implementation, variables, managed text, narrative UI, actors, audio commands, and script import.
- `vn-game-systems` owns map/town/dungeon/inventory/time state, node progression, continue-game landing, and gameplay flow.
- When both apply, gameplay state stays in C# systems; the script presents one event and invokes explicit project Commands. Script completion returns control to the owning system.

## Authority Router

Read `AGENTS.md` and the Naninovel routes in `Docs/INDEX.md`, then load only what the task requires:

- project overview and configuration: `Docs/Knowledge/naninovel.md`
- custom Command aliases and contracts: `Docs/systemspec/story_commands.md` and `Assets/Game/Scripts/Story/Commands/`
- script import and baking: `Docs/Knowledge/naninovel_script_management.md`
- backgrounds: `Docs/rules/rules_nani_bg.md`
- audio/BGM ownership: `Docs/design/audio.md`
- narrative UI sorting: `Docs/rules/rules_uilayer.md`
- Text Animator tags: `Docs/Knowledge/textanimator.md`
- map bridge behavior: `Docs/systemspec/map_system.md`
- exact built-in parameters: `Packages/com.elringus.naninovel/Runtime/Command/`

Detailed, retained references:

- [Scripting Best Practices](rules/scripting-best-practices.md)
- [Custom Commands](rules/custom-commands.md)
- [State Variables](rules/state-variables.md)
- [Managed Text](rules/managed-text.md)
- [Character Animation](rules/character-animation.md)

If a retained reference conflicts with current package source, project code, or systemspec, use the current authority and report the stale section.

## Script Rules That Must Survive

### Dialogue Waiting

Write ordinary dialogue with generic text syntax (`XiHe: ...`) or a bare narration line, not `@print`. EndGods sets `ScriptPlayerConfiguration.WaitByDefault: 0`; direct `@print` can advance without player input, while the generic-text parser injects `WaitForInput = true`. Reserve `@print` for cases that actually require its explicit parameters.

`@stop` stops script execution; it is not the general fix for ordinary dialogue pacing. Use it to halt after registering choices or when the script intentionally returns control.

### Command Parameters and Import-Time Errors

- Distinguish built-in Commands from project Commands before declaring an implementation missing.
- Inspect the concrete Command class for its own parameters. Do not copy a parameter from another Command.
- `@toast` (`ShowToastUI`) accepts its defined text/appearance/time parameters and does not accept `wait`; `@back ... wait!` being valid does not make `wait` universal.
- `unsupported parameter` and other parse errors occur during import, not normal runtime playback. A build may therefore appear healthy while stale imported Script assets hide errors.
- After broad syntax or Command-parameter changes, run `Tools/EndGods/Naninovel/Reimport All Scripts`, then read the Unity Console. If full reimport reveals old errors, use `git blame` before attributing them to the current change.

### Variables and Repeat Entry

- `@set` evaluates the right-hand side. Persist a string as `@set foo="bar"`; `@set foo=bar` reads variable `bar`.
- Before `+=`, `-=`, `++`, or `--`, define the variable with a numeric initial value in `CustomVariablesConfiguration.asset`.
- Design explicit one-time variables for first conversation, reward claimed, POI visited, or chapter grant. Re-entering a script must not duplicate rewards or rewrite irreversible state.
- Do not use `@modifyFavor` for a non-companion NPC. Use scoped narrative variables or the owning reputation system.
- Any variable or Command that affects gameplay progression requires save/load and repeat-entry verification.

### Map, Town, and System Handoff

- One location script presents one location event; do not chain region scripts to replace map progression.
- Town entry scripts show the town-level first impression. Keep the background switch immediate (`time:0`) and do not duplicate POI-specific description or NPC dialogue.
- Use project Commands to request state changes, and verify their aliases and parameters against `story_commands.md` and the C# implementation.
- Custom Commands resolve gameplay services through `CommandServices.Dependencies.cs`; do not scatter new runtime `ServiceLocator.Get` calls or search scene objects to conceal missing bootstrap.

### Background and Audio Incidents

- In the current configuration, `@bgm none` and `@back none` are not safe generic cleanup commands. Follow `Docs/design/audio.md` for BGM stop/restore and `rules_nani_bg.md` for background cleanup.
- Pair looped SFX with `@stopSfx`. When a sound must occur on a specific word, prefer an inline `[sfx X]` command after verifying the command and asset key.
- Preserve the single-source BGM contract. A script that temporarily owns BGM must follow the documented `@bgm`/`@stopBgm` interceptor pairing.
- If the map camera jumps when dialogue returns control, inspect stale drag/input state such as the last pointer position before adding camera cache/restore or delay workarounds.
- If an old/green background persists after returning to gameplay, inspect whether all active Naninovel background and character actors are cleaned up on mode exit; do not hide only the currently observed actor.

### Text Effects

Use Febucci tags as focused narrative emphasis, usually one or two short phrases per dialogue screen. Preserve effective existing tags unless the user asks to reduce them.

- `<wave>`: unease, dream, echo
- `<shake>`: pain, fear, anger, impact
- `<wiggle>`: supernatural perception or uncertainty
- `<bounce>`: playful or emphatic voice
- `<pend>` / `<fade>`: hypnosis, mystery, weakness, disappearance
- `<rainb>`: explicit multicolor spiritual energy
- `<decrypt>`: rare overload, ancient-device, or system-anomaly moments

Use `@char`, `@shake`, and other actor Commands for actor/camera motion; text tags only animate text. Keep long passages readable and do not wrap ordinary route or transaction instructions in effects.

## Workflow

1. Classify the task: script, built-in Command, custom Command, variable/save, localization, actor/text effect, background/audio, import, or gameplay bridge.
2. Inspect the installed package class and two or three current project usages before changing syntax or APIs.
3. Confirm aliases, parameter types, variable ownership, repeat-entry behavior, and save implications.
4. Keep `.nani` presentation separate from C# gameplay state and service ownership.
5. Reimport the affected script assets; use full reimport when the change can affect parsing broadly.

## Verification

- `.nani` content: reimport, clean parse Console, and play the affected narrative state.
- Custom Command C#: update `Docs/systemspec/story_commands.md` when behavior changes, run project lint, Unity recompile, Console check, and the smallest focused test/flow.
- Variable or progression change: new game/default values, repeat entry, save/load round trip, and invalid/legacy state where relevant.
- UI/actor/background/audio change: verify the real player-visible state; include a screenshot when the visual screen changes.

Report the package/API source checked, scripts or Commands changed, import result, Console state, gameplay/save implications, and focused flow evidence.
