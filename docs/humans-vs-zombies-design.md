# Humans VS Zombies — Game Design Document

## Overview

A tower-defense style browser game where players drag human units onto a grid to defend against zombie waves. Local macOS testing via `index.html`, future web hosting planned.

---

## UI & Navigation

### Main Menu
- Title: **"Humans VS Zombies"**
- Subtitle: Endless mode kill record displayed
- Two buttons: **PLAY** and **LEVELS**
- **💾 Save** button in top-left corner
- Shows selected level (or "You have selected ENDLESS")

### Level Selection Screen
- 40 level buttons in a **5 rows × 8 columns** grid
- **♾ ENDLESS** button below the grid (41st entry) — turns green when selected
- **Back to Menu** button top-right
- Selected level/endless button turns green

### Level Start Flow
1. Select a level or Endless → button turns green
2. Press PLAY on main menu
3. Level begins

---

## Gameplay Mechanics

- Grid: **10 columns × 5 rows**
- Mouse drag to place units from shop onto grid
- Each row is independent for targeting
- Ranged units attack only zombies in their own row, unlimited range
- Melee units attack within a limited block range
- Bullets travel horizontally along their row (no sideways drift)
- Banker units generate $20 every 5 seconds passively
- Shielder units block zombies but deal no damage

---

## Economy & Lives

| Level Range | Starting Money | Starting Lives |
|-------------|---------------|----------------|
| 1–10        | $500          | 3              |
| 11–19       | $500          | 5              |
| 20–30       | $1000         | 7              |
| 31–40       | $1000         | 10             |
| Endless     | $500          | 3              |

- Each zombie kill: **+$20**

---

## Terrain & Base by Level

| Levels  | Terrain  | Base Building    |
|---------|----------|------------------|
| 1–10    | Grass    | Hospital         |
| 11–20   | Rocky    | Colosseum        |
| 21–30   | Sandy    | Desert Temple    |
| 31–40   | Volcanic | Obsidian Temple  |
| Endless | Grass    | Hospital         |

---

## Fortress Guard

- Each base has a built-in **Machine Gunner** that cannot be removed
- Deals **5 damage** per shot, fires every 0.3 sec
- Can target **any zombie on any row**
- Has **200 HP** — dies when overrun, costing 1 life

---

## Human Units

| Unit         | Cost  | HP  | Damage      | Fire Rate  | Range           | Special Variant                          |
|--------------|-------|-----|-------------|------------|-----------------|------------------------------------------|
| Fist Fighter | $25   | 75  | 25/punch    | 1/0.8s     | Melee (1 block) | None                                     |
| Baseball Bat | $120  | 100 | 18/hit      | 1/0.7s     | Melee (1 block) | Metal Bat (50%) — 28 dmg/hit             |
| Swordsman    | $125  | 100 | 25/hit      | 1/0.9s     | 2 blocks        | Armored (25%) — 200 HP                   |
| Pistol       | $150  | 100 | 30/bullet   | 1/0.7s     | Unlimited (row) | None                                     |
| Rifle        | $200  | 125 | 30/bullet   | 1/0.4s     | Unlimited (row) | None                                     |
| Machine Gun  | $250  | 150 | 15/bullet   | 1/0.2s     | Unlimited (row) | Godlike (1%) — 1000 HP, 60 dmg, 3/0.1s  |
| Banker       | $100  | 150 | None        | —          | None            | Earns $20 every 5 sec, cannot attack     |
| Shielder     | $80   | 350 | None        | —          | 1 block         | Blocks zombies, no damage                |

---

## Zombie Units

| Type            | HP  | Damage  | Speed          | Spawn Chance | Appears From |
|-----------------|-----|---------|----------------|--------------|--------------|
| Normal Zombie   | 150 | 20/hit  | 1 square/sec   | 65%          | Level 1      |
| Speed Zombie    | 80  | 10/hit  | 2 squares/sec  | 20%          | Level 3+     |
| Muscular Zombie | 400 | 40/hit  | 1 square/2 sec | 15%          | Level 5+     |

---

## Endless Mode

- Zombies spawn indefinitely, game ends only on death
- Kill counter tracked during session
- Record saved to localStorage
- Record displayed on main menu

---

## Animations & Audio

- **Humans:** Bob up and down on their tile
- **Zombies:** Walking leg/arm animation, head bob; speed zombies move faster
- **Zombie death:** Moan sound via Web Audio API
- **Bullets:** Travel horizontally locked to their row

---

## Save System

- Saves to `localStorage`: endless kill record, selected level
- Save button on main menu (top-left) and in-game (bottom-left)

---

## Platform

- **Current:** Local macOS — open `index.html` in browser
- **Future:** Hosted website
- **Tech:** Vanilla HTML5 Canvas + JavaScript, no dependencies
