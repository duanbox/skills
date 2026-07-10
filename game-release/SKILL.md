---
name: game-release
description: Audit or prepare an EndGods release candidate with fresh build, startup, flow, rights, and rollback evidence. Use for release gates, candidate builds, packaging, ship-readiness, or final demo verification. Readiness is read-only; building, upload, publish, tags, pushes, and store changes require explicit authorization.
---

# Game Release Gate

Use `READINESS` mode by default. Here read-only means no tracked source, store, tag, branch, or release-state changes. Fresh diagnostics may change disposable Unity-derived state when the request authorizes executing checks; a strict static review must mark executable gates `BLOCKED` or `SKIPPED`. Use `CANDIDATE` only when the user authorizes creating a build in a named output location. Never upload, publish, tag, push, overwrite a prior candidate, or change store state without separate authorization.

## Authority router

1. Read `AGENTS.md` and `Docs/INDEX.md`.
2. Inspect `ProjectSettings/EditorBuildSettings.asset`, `ProjectSettings/ProjectSettings.asset`, `Packages/manifest.json`, the Unity version, and the actual maintained build entry.
3. Load `Docs/rules/rules_testing.md`, `Docs/rules/rules_test_acceptance_matrix.md`, `Docs/rules/unitymcp.md`, and only the system authorities exercised by the candidate.
4. Include `Docs/Knowledge/naninovel.md`, `Docs/systemspec/save_system.md`, `Docs/rules/rules_resources.md`, and `Docs/rules/rules_assets.md` when story, save, data, or assets ship.
5. Load audio, video, UI scene-flow, platform, licensing, privacy, or store requirements only when they apply to the named target and channel.

Historical scorecards, old test runs, and prior screenshots are context, not fresh release evidence.

## Candidate identity

Before a verdict, record:

`commit | dirty tree | Unity version | target platform and architecture | product/company/bundle ID | version | build configuration | scene list | output path | delivery channel`

Return `INCONCLUSIVE` when the target, version, candidate revision, required scenes, or reproducible build method is unknown. Do not guess a platform or create an all-platform checklist.

## Gate workflow

1. Confirm scope, allowed outputs, replacement policy, and rollback point.
2. Check source state, package resolution, local or mutable dependencies, Build Settings, serialized references, and unresolved Console or importer errors.
3. Run the project rule scan, Unity recompile, Console check, and applicable Core, Feature, Regression, importer, Naninovel, UI, and scene-flow gates with fresh evidence.
4. In authorized `CANDIDATE` mode, run the maintained build path and capture complete logs. Verify artifact existence, hash, size, manifest, and startup outside the Editor.
5. Exercise the candidate's critical flow: launch, New Game, Continue, representative save/load and legacy-save compatibility, required story handoffs, core gameplay loop, and demo or release ending.
6. Check promoted-asset provenance, temporary-asset cleanup, fonts, audio, video, third-party packages, license notices, credits, privacy disclosures, and channel-required metadata.
7. Record known issues, rollback or previous-good candidate, recovery steps, and any skipped gate with residual risk.

## Truth rules

- Editor play, lint, recompile, or an old green test run does not prove a Player build.
- A build artifact does not prove startup, content reachability, save compatibility, or legal readiness.
- Do not mark a required blocked or skipped gate as PASS.
- Do not install dependencies, modify implementation, fix findings, or change release settings during a readiness review.
- Do not assume Steam, DRM, cloud save, telemetry, crash reporting, or marketing requirements unless the target actually uses them.

## Output

- Candidate identity
- Gate matrix: `gate | status | evidence | protected scope`
- Status values: `PASS`, `FAIL`, `BLOCKED`, or `SKIPPED`
- Release blockers and non-blocking risks
- Artifact manifest and hashes when a build was authorized
- Known issues, rollback point, and minimum next action
- Verdict: `READY`, `NOT_READY`, or `INCONCLUSIVE`

Route missing implementation to the owning workflow, content completeness to `content-coverage-audit`, human experience risk to `game-playtest`, and fresh technical evidence to `Verify`.

## Method provenance

This gate adapts release-candidate coverage from Donchitos' MIT-licensed [release-checklist](https://github.com/Donchitos/Claude-Code-Game-Studios/blob/main/.claude/skills/release-checklist/SKILL.md). Generic studio departments, fixed production directories, and unrequested multi-platform or store assumptions are not adopted.
