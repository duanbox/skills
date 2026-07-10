---
name: stitch-design-md
description: Analyze one or more Google Stitch screens and synthesize an evidence-backed semantic design-system proposal. Use when documenting a Stitch project's visual language, comparing screen consistency, or preparing a DESIGN.md-style brief for later Stitch exploration; in EndGods this remains an upstream design artifact and never becomes Pencil, Unity, asset, or acceptance authority.
---

# Stitch Design MD

Extract a reusable visual language from real Stitch project evidence. Prefer screenshots and Stitch project metadata; use generated HTML only as optional supporting evidence.

## Route Stitch tools

1. Require a configured Google Stitch MCP server.
2. Discover the actual Stitch tool namespace from the tools exposed in the current session. Do not invent or hard-code a prefix.
3. Resolve these logical calls when available:
   - `list_projects`
   - `list_screens`
   - `get_screen`
   - `get_project`
4. If a required tool is missing, stop and name the missing Stitch dependency. Do not fall back to raw HTTP endpoints or inspect credential files.

## Preserve the EndGods authority boundary

When the current project is EndGods:

- Treat Stitch screens and the resulting design document as design exploration or an upstream visual brief only.
- Never create, edit, replace, or normalize a Pencil `.pen` file. Pencil work remains owned by the Pencil workflow.
- Never write or overwrite a Unity Scene, Prefab, generator, runtime UI script, `UIDataAsset`, or production bitmap.
- Never treat Stitch HTML as production Unity implementation.
- Never override `AGENTS.md`, `Docs/Pipeline/ui-vibe-coding-pipeline.md`, `Docs/rules/rules_ui.md`, or `Docs/rules/rules_assets.md`.
- Preserve the project rule that Scene/Prefab serialization is the final static UI authority.
- Treat mockups as visual targets, not final cut sources. Production bitmaps still require the `ai/...` staging, provenance, QA, and promotion workflow.
- Do not claim completion or parity from a Stitch render. EndGods signoff still requires a real Scene/GameView screenshot and the project verification workflow.

## Build the evidence set

1. Resolve the project:
   - Use the supplied project ID when present.
   - Otherwise call `list_projects` and identify the project by title or URL.
2. Select representative screens:
   - Call `list_screens`.
   - Include the screens named by the user.
   - For a system-wide extraction, include at least one primary screen and each materially different component family or state.
3. Retrieve evidence:
   - Call `get_screen` for each selected screen.
   - Use `screenshot.downloadUrl` as visual evidence when present.
   - Use `htmlCode.downloadUrl` only when exact values or component structure need corroboration.
   - Call `get_project` for project theme data, device targets, and screen-instance context.
4. Track confidence:
   - Mark values from metadata or code as `exact`.
   - Mark values estimated from screenshots as `inferred`.
   - Do not invent missing values.

## Synthesize the system

Describe the recurring design grammar rather than one screen's incidental details:

- atmosphere, density, and intended emotional register;
- functional color roles, including exact values only when evidenced;
- typography roles, hierarchy, weight, and spacing character;
- component silhouettes, corner and edge treatment, borders, ornaments, and 9-slice implications;
- depth, material, lighting, texture, shadow, and layer separation;
- spacing rhythm, alignment, grouping, and information density;
- component-family invariants across buttons, cards, panels, slots, tabs, badges, tooltips, and overlays;
- state grammar for default, selected, focused, disabled, warning, and completed states when evidenced;
- layout and navigation principles appropriate to the target surface;
- allowed variation axes and properties that must remain consistent.

Call out contradictions between screens instead of averaging them into a fictitious rule.

## Produce the artifact

If the user names an output path, write there. Otherwise return the proposed content inline and ask where it should live before writing a project file.

Use this compact structure:

```markdown
# Design System: [Project or exploration name]

## Authority and status
[Exploration scope, source screens, and downstream authority limits]

## Evidence
[Screen IDs/URLs and exact versus inferred notes]

## Visual theme and atmosphere
[Mood, density, materials, and aesthetic intent]

## Color roles
[Descriptive name, value when exact, functional role, confidence]

## Typography roles
[Display, body, label, data, and hierarchy rules]

## Shape, material, and depth
[Silhouette, borders, corners, ornaments, texture, lighting, elevation]

## Component-family grammar
[Shared invariants and allowed deviations]

## Layout and interaction states
[Spacing, alignment, hierarchy, navigation, and visible states]

## Open questions and conflicts
[Missing evidence, contradictions, and decisions still requiring approval]
```

Use stable terminology throughout. Explain each rule's functional purpose, not only its appearance.

## Upstream attribution

Adapted from Google Labs Code's `design-md` skill:
https://github.com/google-labs-code/stitch-skills/tree/main/plugins/stitch-utilities/skills/design-md

Upstream repository license: Apache-2.0.
https://github.com/google-labs-code/stitch-skills/blob/main/LICENSE

This draft is modified for Codex and EndGods design-exploration boundaries. Preserve the upstream license and attribution when redistributing it.
