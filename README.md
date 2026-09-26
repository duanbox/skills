# EndGods Skillshare Source

This directory is the single global source for EndGods skills distributed by skillshare. Edit skills here, not in target directories such as `.codex/skills`, `.claude/skills`, or `.cursor/skills`.

## Current baseline

- skillshare CLI: machine-local; use `skillshare version` (Windows setup verified with `v0.21.8`)
- `skillshare` skill: project-owned thin router; command syntax comes from live CLI `--help`
- shared skills: 28
- targets: antigravity, claude, codex, cursor, gemini, grok, kilocode, opencode, trae
- target mode: copy, except grok uses merge
- project rules and workflows are separate extras; they are not skills

The repository authority is `AGENTS.md` and `Docs/INDEX.md` in the active EndGods checkout; resolve its root on the current machine rather than assuming a drive letter. A distributed skill should route to current project documents and tools instead of copying large sections that will drift.

## Inventory

Workflow and review:

- `Architect`
- `CodeReview`
- `Debug`
- `grill-me`
- `PerformanceOpt`
- `StoryWeaver`
- `TechDebt`
- `Verify`
- `agent-board`
- `content-coverage-audit`
- `interactive-narrative-review`
- `narrative-consistency-review`

Playtest and release:

- `game-playtest`
- `game-release`

Domain and implementation:

- `architecture`
- `art-pipeline`
- `game-ui-designer`
- `naninovel`
- `ui-ux`
- `unify-ui-components`
- `unity-animation`
- `vn-game-systems`

Asset and media:

- `generate-bgm`
- `generate-sfx`
- `process-portrait`
- `storyboard-to-seedance`

External tooling:

- `skillshare`
- `stitch`

## Skill authoring rules

1. Keep frontmatter descriptions concise and trigger-focused. Put process details in the body.
2. State read-only versus mutating authority explicitly for review, diagnosis, planning, and verification skills.
3. Route to `AGENTS.md`, `Docs/INDEX.md`, and the smallest relevant project authority.
4. Keep executable commands aligned with the real Windows and Unity toolchain.
5. Prefer a thin wrapper around maintained project scripts instead of duplicating parameters or implementation code.
6. Keep only durable supporting references. Delete copied handbooks when the repository already owns the rule.
7. Preserve evidence gates: project lint, Unity recompile and Console, focused tests, importers, screenshots, or media QA as required by risk.
8. Review external GitHub skills for license, maintenance, project fit, and conflicting assumptions before adopting them. Absorb precise mechanisms instead of installing a broad generic pack when EndGods rules are more specific.

## Explicit workflow invocation

- `CodeReview` alone selects `STANDARD`. Use `$CodeReview DEEP <scope>` or explicitly request a full, deep, or comprehensive review for the broader review matrix.
- `$grill-me <plan>` is a manual pre-implementation pressure test. It never auto-triggers from risk or ambiguity.
- `$agent-board <task>` is reserved for cross-day or cross-thread persistence, durable Unity-exclusive resource coordination, or an independent QA evidence record. Ordinary same-session decomposition uses native subagents without cards.

## Compatibility names

The following frontmatter names are intentionally retained because `Docs/agent_board/skill_registry.md`, role defaults, and existing cards use them:

- `Architect`, `CodeReview`, `Debug`, `PerformanceOpt`, `StoryWeaver`, `TechDebt`, `Verify`
- `VN Game Systems`

They are known exceptions to the lowercase hyphen-case recommendation. Rename them only as an explicit migration that updates the Agent Board registry, roles, prompts, and cards together. `unity-animation` has already been normalized because it had no such dependency.

## Validation and distribution

Use the project Python environment for the OpenAI quick validator and the installed skillshare binary for distribution:

```powershell
$ss = 'C:\Users\duanb\AppData\Local\Programs\skillshare\skillshare.exe'
$py = Join-Path (Get-Location).Path 'Tools/venv/Scripts/python.exe' # Run from the active EndGods checkout

& $ss analyze --global --json
& $ss audit --global --threshold high --format json --yes
& $ss sync --all --global --json
& $ss status --global --json
& $ss diff --global --json
& $ss doctor --global --json
```

Run `quick_validate.py` with `PYTHONUTF8=1` on Windows. Treat only the compatibility names above as documented validator exceptions.

The installed Skillshare CLI and the project-owned `skillshare` router have independent update paths. Use `skillshare upgrade --cli --force` for the CLI, re-read the upgraded command help, and adjust this source's thin router only if the live command contract changed. Never run bare `skillshare upgrade` or `skillshare upgrade --skill` against this source because they can overwrite the router with the built-in skill; evaluate useful upstream mechanisms separately and absorb only the needed delta before validating and syncing.

After sync, every target must contain the same 28 shared skill trees. Where installed by Grok, additionally preserve these local-only skills under its merge target: `help`, `imagine`, and `create-skill`; never collect or sync them into this source.

Grok-local skills are outside this repository. Never collect them into the central source or include them in public commits.

## Extras boundary

`EndGods_rules` is managed under the skillshare extras source; its project copy is versioned in EndGods. The retired `EndGods_workflows` extra must not be recreated. If an extras target contains newer project-owned content, collect or reconcile it back to the extras source before syncing. Never overwrite the newer repository copy merely to make the status green.

## Cross-machine Git workflow

This checkout tracks https://github.com/duanbox/skills. EndGods authorizes completed skill improvements to be synced, verified, committed, and pushed in the same task. Edit this source, inspect the diff, stage only task-owned files, and push to the configured upstream without force. Keep unrelated local changes intact. Report the commit and any push failure; local distribution alone is not a successful GitHub sync.

On another configured machine, use `skillshare pull` from a clean source to pull and distribute. On a fresh Windows machine, clone this repository to `%APPDATA%/skillshare/skills`, install Skillshare, configure targets with `init --no-copy --no-git --no-skill`, then sync. Do not import stale target copies over the cloned source. Store target configuration and machine paths outside this repository.
