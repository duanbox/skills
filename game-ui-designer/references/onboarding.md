# Onboarding and Tutorials

## Use This Reference

Load this file for first-time user experience, early mission teaching, contextual hints, mechanic unlocks, and tutorial prompt systems.

## Teaching Sequence

Teach in this order:

1. immediate survival or movement
2. the next required action
3. the reward or outcome of doing it
4. one optional mastery hint

Delay everything else.

## Delivery Patterns

### Contextual Prompt

Best for controls and single actions. Trigger at the exact moment the mechanic becomes relevant.

### Safe Sandbox

Best for new combat or traversal mechanics. Let the player succeed once before stacking more demands.

### Goal-Oriented Step

Best for crafting, inventory, or quest loops. Phrase as a short objective, not a full instruction wall.

### Reminder Hint

Best for players who ignored or forgot the first teaching beat. Repeat only after a meaningful failure or time threshold.

## Prompt Rules

- One prompt per teaching beat.
- Keep wording action-first:
  - `Press RT to fire`
  - `Open Inventory to equip the bow`
- Place prompts near the relevant world object or UI control when possible.
- Allow advanced players to dismiss, skip, or reduce hint density.

## Gating

- Gate only when failure would compound confusion.
- If a mechanic is optional, teach it without blocking progress unless the build specifically requires mastery.
- Detect success from player behavior, not just prompt display.

## Common Anti-Patterns

- four tutorial popups on first spawn
- modal tutorial text during active enemy pressure
- repeating the same hint every few seconds regardless of player progress
- teaching inventory, crafting, map, and shop before the player needs any of them
