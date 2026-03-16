# HUD

## Use This Reference

Load this file for combat HUD, exploration HUD, status overlays, boss frames, minimaps, quest trackers, damage indicators, and crosshair-adjacent UI.

## Layout Rules

- Keep the center 30 to 40 percent of the screen as clear as possible.
- Put survival and combat readiness near the focal path:
  - player health, shield, stamina, ammo, cooldowns
- Put supporting information farther out:
  - buffs, debuffs, companion state, quest tracker, minimap
- Use corners for persistent low-urgency systems, edges for contextual alerts, and center-near elements only for immediate threats.

## Priority Bands

### Critical

- health or death risk
- current weapon or active ability lockout
- incoming damage direction
- revive or fail state

### Important

- ammo reserve
- ultimate charge
- objective progress
- nearby threat markers

### Secondary

- currencies
- passive buffs with long duration
- lore or environmental tips

## Readability

- Minimum readable text should assume distance viewing on TV or laptop, not just close desktop inspection.
- Favor shape-coded icons plus numbers. Color alone is not sufficient.
- Avoid stacked outlines, glows, and gradients that blur at motion speed.
- Combat notifications should fade quickly and never cover target tracking.

## Animation

- Use quick transitions to signal state changes: gain, loss, cooldown ready, out of range, low health.
- Idle looping animation should be subtle enough to disappear from attention.
- Critical warnings should escalate in intensity, not just flash on and off.

## Common Anti-Patterns

- minimap, quest list, and buff rail all competing in the same corner
- giant damage numbers blocking the crosshair
- cooldown indicators that require fine color discrimination
- persistent tutorial prompts sitting on top of combat UI
- low-health warnings that pulse constantly and numb the player
