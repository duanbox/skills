# EndGods Skillshare Source

This directory is the single global source for EndGods skills distributed by skillshare. Edit skills here, not in target directories such as `.codex/skills`, `.claude/skills`, or `.cursor/skills`.

## Current baseline

- skillshare CLI and bundled skill: `v0.20.21`
- shared skills: 31
- targets: antigravity, claude, codex, cursor, gemini, grok, kilocode, opencode, trae
- target mode: copy, except grok uses merge
- project rules and workflows are separate extras; they are not skills

The repository authority remains `D:\Project\EndGods\AGENTS.md` and `Docs/INDEX.md`. A distributed skill should route to current project documents and tools instead of copying large sections that will drift.

## Inventory

Workflow and review:

- `Architect`
- `CodeReview`
- `Debug`
- `FullReview`
- `PerformanceOpt`
- `SmartFix`
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
- `stitch-design-md`
- `stitch-generate-design`
- `stitch-manage-design-system`

## Skill authoring rules

1. Keep frontmatter descriptions concise and trigger-focused. Put process details in the body.
2. State read-only versus mutating authority explicitly for review, diagnosis, planning, and verification skills.
3. Route to `AGENTS.md`, `Docs/INDEX.md`, and the smallest relevant project authority.
4. Keep executable commands aligned with the real Windows and Unity toolchain.
5. Prefer a thin wrapper around maintained project scripts instead of duplicating parameters or implementation code.
6. Keep only durable supporting references. Delete copied handbooks when the repository already owns the rule.
7. Preserve evidence gates: project lint, Unity recompile and Console, focused tests, importers, screenshots, or media QA as required by risk.
8. Review external GitHub skills for license, maintenance, project fit, and conflicting assumptions before adopting them. Absorb precise mechanisms instead of installing a broad generic pack when EndGods rules are more specific.

## Compatibility names

The following frontmatter names are intentionally retained because `Docs/agent_board/skill_registry.md`, role defaults, and existing cards use them:

- `Architect`, `CodeReview`, `Debug`, `FullReview`, `PerformanceOpt`, `SmartFix`, `StoryWeaver`, `TechDebt`, `Verify`
- `Architecture & Code Structure`, `UI & UX Design`, `VN Game Systems`

They are known exceptions to the lowercase hyphen-case recommendation. Rename them only as an explicit migration that updates the Agent Board registry, roles, prompts, and cards together. `unity-animation` has already been normalized because it had no such dependency.

## Validation and distribution

Use the project Python environment for the OpenAI quick validator and the installed skillshare binary for distribution:

```powershell
$ss = 'C:\Users\duanb\AppData\Local\Programs\skillshare\skillshare.exe'
$py = 'D:\Project\EndGods\Tools\venv\Scripts\python.exe'

& $ss analyze --global --json
& $ss audit --global --threshold high --format json --yes
& $ss sync --all --global --json
& $ss status --global --json
& $ss diff --global --json
& $ss doctor --global --json
```

Run `quick_validate.py` with `PYTHONUTF8=1` on Windows. Treat only the compatibility names above and the pinned upstream `skillshare` schema extension as documented validator exceptions.

After sync, every target must contain the same 31 shared skill trees. Grok additionally keeps its native local skills; `diff --json` may describe those as local-only removals even though merge mode intentionally preserves them.

Grok-local skills are outside this repository. Never collect them into the central source or include them in public commits.

## Extras boundary

`EndGods_rules` and `EndGods_workflows` are managed under the skillshare extras source. If an extras target contains newer project-owned content, collect or reconcile it back to the extras source before syncing. Never overwrite the newer repository copy merely to make the status green.
