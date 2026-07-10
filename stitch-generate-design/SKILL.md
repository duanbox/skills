---
name: stitch-generate-design
description: Generate, edit, and compare Google Stitch screens through the Stitch MCP server using structured prompts, existing project design systems, and explicit source evidence. Use for text-to-screen exploration, targeted screen edits, or controlled design variants; in EndGods every output is an upstream mockup and may not replace Pencil artifacts, Unity Scene/Prefab authority, production assets, project rules, or real screenshot acceptance.
---

# Stitch Generate Design

Create design explorations in Stitch. Keep generation structural and evidence-driven, then hand selected directions to the owning downstream workflow.

## Route Stitch tools

1. Require a configured Google Stitch MCP server.
2. Discover the actual Stitch tool namespace from the tools exposed in the current session. Do not invent or hard-code a prefix.
3. Resolve these logical calls when available:
   - `list_projects`
   - `create_project`
   - `get_project`
   - `list_screens`
   - `get_screen`
   - `list_design_systems`
   - `generate_screen_from_text`
   - `edit_screens`
   - `generate_variants`
4. If a required tool is missing, stop and name the missing Stitch dependency. Do not fall back to raw service endpoints, inspect credential files, or embed secrets.

## Preserve the EndGods authority boundary

When the current project is EndGods:

- Treat every Stitch result as design exploration, a mockup, or an upstream handoff only.
- Never create, edit, replace, or normalize a Pencil `.pen` file. If the requested deliverable is `.pen`, hand off to the Pencil workflow.
- Never write or overwrite Unity Scenes, Prefabs, generators, runtime UI code, `UIDataAsset`, production sprites, or asset manifests.
- Never use Stitch HTML as production Unity code.
- Never override `AGENTS.md`, `Docs/Pipeline/ui-vibe-coding-pipeline.md`, `Docs/rules/rules_ui.md`, or `Docs/rules/rules_assets.md`.
- Preserve the shared primitive / screen-specific asset / temporary candidate classification before downstream implementation.
- Treat generated bitmaps as candidates only; they still require project staging, provenance, QA, and promotion.
- Do not claim visual acceptance from Stitch output. Final EndGods acceptance still requires a real Scene/GameView screenshot and project verification.

## Establish project context

1. Use a supplied project ID when present; otherwise call `list_projects`.
2. If no suitable project exists, describe the proposed project and obtain confirmation before calling `create_project`.
3. Call `list_design_systems` for the selected project.
4. If a design system exists, use its asset ID and omit theme tokens from generation prompts.
5. If no system exists and consistent multi-screen work is requested, route through `$stitch-manage-design-system` before generation.
6. Read relevant existing screens with `list_screens` and `get_screen` before editing or extending a visual family.

## Structure prompts for any UI surface

Replace vague language with precise UI terms, but do not default to a Web landing-page structure.

Use this form:

```markdown
[Surface purpose, user intent, and current state]

**TARGET:** [device class, orientation, viewport role, and input context]

**INFORMATION HIERARCHY:**
1. [Primary task or status]
2. [Secondary information]
3. [Supporting actions or details]

**REGIONS AND COMPONENTS:**
1. [HUD region, panel, navigation, tab set, list, grid, card, slot, tooltip, overlay, or dialogue area]
2. [Content and component relationships]

**INTERACTION STATES:**
- [Default, selected, focused, disabled, empty, loading, warning, completed, or other relevant states]

**CONTENT CONSTRAINTS:**
- [Required labels, data density, localization allowance, aspect-ratio constraints]

**PRESERVE:**
- [Approved structure, component family, or source-screen invariants]
```

When a project design system exists, do not repeat colors, fonts, roundness, or theme values in a new-screen prompt. Those tokens belong to the project-level design system. A targeted edit may name an exact visual value only when the user explicitly requests that local change.

## Select the mode

### Generate from text

1. Build the structured prompt.
2. Call `generate_screen_from_text` with:
   - `projectId`;
   - the enhanced prompt;
   - the design-system asset name when available;
   - the device type required by the exposed schema.
3. Surface the returned text description and suggestions from `outputComponents`.
4. Record the created screen ID and URLs.

### Generate from an image or mockup

1. Prefer an image already represented by a Stitch screen or accessible through an exposed Stitch upload tool.
2. This draft contains no local uploader and no credential workflow. If the image is only a local file and no safe Stitch MCP upload tool is exposed, stop and report the separate upload dependency.
3. After the image becomes a Stitch screen, identify it with `list_screens` and refine it through the edit flow.
4. Describe the intended structure and semantics, not merely "make it look like this."

### Edit an existing screen

1. Resolve the exact screen with `list_screens` or `get_screen`.
2. Describe one bounded change with its location, preserved invariants, desired result, and affected states.
3. Call `edit_screens` with the target project, selected screen IDs, and focused prompt.
4. Surface `outputComponents`, then retrieve the updated screen.
5. Prefer another focused edit over a full regeneration when the underlying layout direction remains valid.

### Generate variants

1. Resolve the source screen and state what must remain invariant.
2. Choose a deliberate range:
   - `REFINE` for subtle polish;
   - `EXPLORE` for balanced alternatives;
   - `REIMAGINE` only when the user requests a substantially different direction.
3. Request one to five variants and only the relevant aspects supported by the exposed schema, such as `LAYOUT`, `COLOR_SCHEME`, `IMAGES`, `TEXT_FONT`, or `TEXT_CONTENT`.
4. Call `generate_variants`.
5. Compare variants against the brief and existing component family; do not select solely because a variant is more decorative.

## Review and hand off

For every generated or edited result:

1. Show the screen ID, visual URL, text description, and suggestions returned by Stitch.
2. Check information hierarchy, component-family consistency, density, state clarity, and obvious clipping.
3. State what is preserved, what changed, and what remains uncertain.
4. Do not download artifacts into a hard-coded directory. If local copies are requested, use the user-selected staging location.
5. For EndGods, label the result `Upstream design exploration - not Unity acceptance evidence`.
6. Hand selected directions to the appropriate Pencil, Product Design, asset, Unity UI, or verification workflow rather than implementing across those boundaries from this skill.

## Upstream attribution

Adapted from Google Labs Code's `generate-design` skill:
https://github.com/google-labs-code/stitch-skills/tree/main/plugins/stitch-design/skills/generate-design

Upstream repository license: Apache-2.0.
https://github.com/google-labs-code/stitch-skills/blob/main/LICENSE

This draft is modified for Codex and EndGods design-exploration boundaries. Preserve the upstream license and attribution when redistributing it.
