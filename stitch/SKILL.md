---
name: stitch
description: Explore Google Stitch screens and project design systems through Stitch MCP. Use when analyzing, generating, editing, comparing, or managing Stitch designs; EndGods treats them as upstream references only.
---

# Stitch

Use one bounded mode at a time: `ANALYZE`, `GENERATE`, or `MANAGE`.

## Connect safely

1. Require a configured Google Stitch MCP server and discover its actual tool namespace from the current session.
2. Resolve only the logical operations needed for the selected mode, such as project, screen, generation, variant, or design-system calls.
3. If a required operation is unavailable, stop and name it. Do not use raw service endpoints, inspect credential files, or embed secrets.
4. Resolve the project and exact screens from supplied IDs or the exposed list/get operations before acting.

## Preserve EndGods authority

For EndGods, every Stitch result is upstream exploration only:

- Do not create or modify Pencil `.pen` files, Unity Scenes or Prefabs, generators, runtime UI code, `UIDataAsset`, production bitmaps, or asset manifests.
- Do not use generated HTML as production Unity implementation.
- Do not override `AGENTS.md`, `DESIGN.md`, the UI pipeline, asset rules, or Scene/Prefab serialization.
- Label outputs `Upstream design exploration - not Unity acceptance evidence`.
- Final visual acceptance still requires the owning Unity workflow and a real Scene/GameView screenshot.

## ANALYZE: synthesize a semantic design proposal

Use when the user wants to understand or document one or more Stitch screens.

1. Inspect project metadata and representative screens. Include every materially different component family or state in a system-wide analysis.
2. Prefer screen metadata and screenshots; use generated code only to corroborate exact values or structure.
3. Mark evidence as `exact` or `inferred`. Surface contradictions instead of averaging them.
4. Describe atmosphere, functional color roles, typography hierarchy, shape and material, component-family invariants, spacing, navigation, interaction states, allowed variation, and open conflicts.
5. Return a compact `DESIGN.md`-style proposal. Write a file only when the user names or approves the output path.

## GENERATE: create, edit, or compare screens

Use for a new screen, a bounded edit, or controlled variants.

1. Inspect the target project, existing design system, and related screens first.
2. Structure prompts around purpose, device/input context, information hierarchy, regions and components, states, content constraints, and invariants to preserve.
3. Reuse a project design system when present; do not duplicate its theme tokens in every prompt.
4. For edits, target exact screen IDs and one bounded change. Prefer a focused edit over regeneration when the direction remains valid.
5. For variants, state invariants and choose a deliberate range: `REFINE`, `EXPLORE`, or user-requested `REIMAGINE`.
6. Report created or changed screen IDs, visual URLs, returned descriptions, preserved invariants, differences, and unresolved issues.

If a local reference image cannot be uploaded through an exposed Stitch tool, report that dependency instead of improvising an uploader.

## MANAGE: inspect or change a Stitch design system

Use for Stitch project-level palette, typography, roundness, other supported tokens, or applying a system to selected screens.

1. Inspect the target project, existing design systems, screens, and the live tool schema.
2. Classify the request as read-only inspection, creation from an approved proposal, bounded token update, or application to named screens.
3. Before any create, update, upload, or apply call, show the project, design-system name, exact changed fields, and exact target screen IDs.
4. Mutate only after explicit confirmation, unless the user already approved that exact payload and target set in the current turn.
5. Never broaden approval from one screen to all screens. Exclude design-system instances when applying to ordinary screens.
6. Read the result back and report actual stored values, successes, failures, and untouched screens.

## Handoff

Always report the mode, project and screen identifiers, evidence used, operations performed, limitations, and the owning downstream workflow. Do not claim Unity parity or implementation completion from Stitch output.

## Attribution

Adapted from Google Labs Code's `design-md`, `generate-design`, and `manage-design-system` skills in [`google-labs-code/stitch-skills`](https://github.com/google-labs-code/stitch-skills), licensed under Apache-2.0. Preserve the repository notice and license when redistributing this adaptation.
