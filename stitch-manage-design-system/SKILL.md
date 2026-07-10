---
name: stitch-manage-design-system
description: Inspect, create, update, and apply Google Stitch project-level design systems through the Stitch MCP server with explicit mutation checkpoints. Use when synchronizing visual tokens across Stitch screens or turning an approved DESIGN.md proposal into a Stitch design-system asset; in EndGods this manages Stitch exploration only and cannot change Pencil, Unity, project rules, assets, or acceptance evidence.
---

# Stitch Manage Design System

Manage the design system inside a Stitch project without treating it as authority outside Stitch.

## Route Stitch tools

1. Require a configured Google Stitch MCP server.
2. Discover the actual Stitch tool namespace from the tools exposed in the current session. Do not invent or hard-code a prefix.
3. Resolve only tools that are actually exposed, including as needed:
   - `list_projects`
   - `get_project`
   - `list_screens`
   - `get_screen`
   - `list_design_systems`
   - `upload_design_md`
   - `create_design_system_from_design_md`
   - `update_design_system`
   - `apply_design_system`
4. If a required operation is unavailable through Stitch MCP, stop and report the missing tool. Do not call raw service endpoints, inspect credential files, or embed secrets.

## Preserve the EndGods authority boundary

When the current project is EndGods:

- Manage only the Stitch project and explicitly requested draft artifacts.
- Treat the Stitch design system as an upstream exploration aid, never the source of truth for the game.
- Never create, edit, replace, or normalize Pencil `.pen` files.
- Never modify Unity Scenes, Prefabs, generators, runtime code, `UIDataAsset`, production sprites, or asset manifests.
- Never override `AGENTS.md`, `Docs/Pipeline/ui-vibe-coding-pipeline.md`, `Docs/rules/rules_ui.md`, or `Docs/rules/rules_assets.md`.
- Do not claim that applying a Stitch design system proves Unity parity or visual acceptance; EndGods still requires real Scene/GameView screenshot evidence.

## Inspect before mutating

1. Resolve the target project with the supplied ID or `list_projects`.
2. Call `get_project` to inspect screen instances and project metadata.
3. Call `list_design_systems` and summarize existing assets.
4. Determine whether the request is:
   - read-only inspection;
   - creation from an approved design document;
   - direct token update;
   - application to selected screens.
5. If a semantic design document must first be synthesized, route that work through `$stitch-design-md`.

## Require a mutation checkpoint

Before `upload_design_md`, `create_design_system_from_design_md`, `update_design_system`, or `apply_design_system`:

1. Show the target project.
2. Show the design-system display name and the proposed palette, fonts, roundness, and other changed fields.
3. List the exact target screen titles and IDs for an apply operation.
4. State whether the operation creates, updates, or applies external Stitch state.
5. Wait for explicit user confirmation unless the user already approved this exact payload and target set in the current turn.

Do not broaden an approval for one screen into an all-screen application.

## Create from an approved design document

1. Read the approved design content from the user-specified path or current artifact.
2. If `upload_design_md` is exposed and the payload fits the tool contract, call it with the target `projectId` and the encoded design content required by the tool schema.
3. If the MCP upload cannot safely carry the content, stop and report that the separate upstream upload workflow is required. Do not improvise a network uploader.
4. After upload, call `get_project` and locate the corresponding real screen instance.
5. Call `create_design_system_from_design_md` with:
   - the target `projectId`;
   - `selectedScreenInstance.id`;
   - `selectedScreenInstance.sourceScreen`;
   - the intended device type when the exposed schema requires it.
6. Call `list_design_systems` again and report the created asset name.

## Update an existing system

1. Resolve the design-system asset through `list_design_systems`.
2. Inspect the exposed `update_design_system` schema before constructing a payload.
3. Change only fields explicitly approved by the user.
4. Do not guess enum values, unsupported fonts, or token fields.
5. Read the result back and report the actual stored values.

## Apply to screens

1. Call `get_project` for current `screenInstances`.
2. Exclude every instance whose type is `DESIGN_SYSTEM_INSTANCE`.
3. Resolve the design system with `list_design_systems`; pass the asset identifier in the format required by the exposed tool.
4. Call `apply_design_system` with `selectedScreenInstances` containing only:
   - `id`
   - `sourceScreen`
5. Do not include `x`, `y`, `width`, `height`, or other instance fields.
6. Re-read the project and report successes, failures, and untouched screens.

## Report clearly

Return:

- project and design-system identifiers;
- read-only findings or the approved mutation performed;
- exact target screens;
- fields that changed;
- unresolved tool or payload constraints;
- an explicit reminder that EndGods downstream authority and verification remain unchanged.

## Upstream attribution

Adapted from Google Labs Code's `manage-design-system` skill:
https://github.com/google-labs-code/stitch-skills/tree/main/plugins/stitch-design/skills/manage-design-system

Upstream repository license: Apache-2.0.
https://github.com/google-labs-code/stitch-skills/blob/main/LICENSE

This draft is modified for Codex and EndGods design-exploration boundaries. Preserve the upstream license and attribution when redistributing it.
