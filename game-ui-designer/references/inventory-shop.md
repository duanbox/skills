# Inventory, Equipment, and Shops

## Use This Reference

Load this file for inventory grids, equipment panes, loot popups, crafting benches, vendors, auction-style listings, and premium currency shops.

## Core Player Questions

Every screen should answer these quickly:

1. What do I own?
2. What is new?
3. What is equipped?
4. Is this better or worse?
5. What will this action cost?
6. Can I undo or recover from it?

## Layout Patterns

### Inventory

- Browse region: item grid or list
- Compare region: selected item details and stat deltas
- Action region: equip, use, split, salvage, sell, favorite

Keep these stable. Do not move core actions between states unless the input device changes.

### Equipment

- Character silhouette or slot map should reinforce slot ownership, not become decorative clutter.
- Hover or focus on a slot should reveal current item, empty state, and eligible replacements.

### Shops

- Separate purchase categories from owned inventory.
- Display player balance near purchase actions.
- Show post-purchase balance or affordability before commit.
- Premium currency offers need explicit quantity, unit value, bonus value, and final total.

## Comparison Rules

- Use signed deltas with icon and color support.
- Separate absolute stats from derived ratings.
- If an item has tradeoffs, show them in the same panel. Do not hide negatives behind a deeper inspect layer.
- New item badges should clear predictably after inspect or equip.

## Safety Rules

- Protect destructive or irreversible actions:
  - salvage
  - reroll
  - premium spend
  - dismantle equipped item
- Do not confirm every cheap repeat purchase. Use hold-to-buy, quantity steppers, or batch purchase flows instead.

## Common Anti-Patterns

- detail panel that changes layout shape for every item type
- currency total far away from buy button
- compare flow that requires opening and closing multiple popups
- loot rarity doing all the communication while actual slot and function stay unclear
