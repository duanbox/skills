# Menus

## Use This Reference

Load this file for main menu, title screen, pause menu, settings, map, quest log, codex, social panels, and game over flows.

## Navigation Rules

- The player should always know the current layer and the back path.
- Top-level categories should be shallow and distinct. If two tabs feel similar, they probably need merging or stronger naming.
- Controller focus order must match the visual reading order.
- Never hide the focused item when scrolling; anchor focus and move content around it if needed.

## Main Menu

- Primary action should match the most likely return path:
  - first session: `Start`
  - existing save: `Continue`
- Secondary actions should not visually compete with the primary CTA.
- Legal, credits, and low-frequency actions should stay de-emphasized.

## Pause Menu

- Prioritize resume, settings, loadout if allowed, and quit.
- Keep combat-unsafe actions separated or disabled with a clear reason.
- If the game continues in the background, dimming and input lock must communicate that clearly.

## Settings

- Group by mental model, not by engine category:
  - video
  - audio
  - controls
  - gameplay
  - accessibility
- Show live preview for reversible changes like volume or subtitle size.
- For risky display changes, provide a timed revert.

## Accessibility and Comfort

- Subtitle, text size, contrast, motion reduction, and remapping should not be buried.
- Controller glyphs should change with the active device.
- Confirmation prompts should be reserved for destructive actions.

## Common Anti-Patterns

- vertical tab list plus horizontal subtabs plus nested accordions on the same screen
- default focus landing on a decorative widget
- settings labels with no immediate value preview
- backing out of a submenu and losing the previous selection context
