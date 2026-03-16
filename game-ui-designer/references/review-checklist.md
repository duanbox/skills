# Review Checklist

Use this for fast audits of a screen, prefab, or flow.

## HUD

- Is the center gameplay lane clean?
- Can the player locate health, ammo, cooldowns, and objective state immediately?
- Are urgent changes clearer than passive states?
- Does any effect animation obscure aiming or enemy reads?

## Menus

- Is the current location and back path obvious?
- Does controller focus move predictably?
- Are top-level categories distinct?
- Are destructive actions clearly separated?

## Inventory and Shops

- Can the player compare items without modal churn?
- Is equipped or owned state visible at a glance?
- Is total cost or reward delta visible before commit?
- Are confirmations reserved for high-risk actions only?

## Tutorials

- Does each prompt unlock one next action?
- Are prompts timed to relevance instead of front-loaded?
- Can players recover if they skip or miss a hint?
- Does teaching stop once the player demonstrates understanding?

## Implementation

- Are safe area, aspect ratio, and controller glyph swaps handled?
- Are empty, locked, disabled, and loading states designed explicitly?
- Are sound, motion, and text all conveying state changes together?
- Does the layout still work under localization growth?
