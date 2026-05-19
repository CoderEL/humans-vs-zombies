# Boss Level (Level 42) — Logic Breakdown

## Overview

The boss level is a special level with an interstellar theme. Instead of waves of zombies coming from the right, there's a **Zombie Base** that spawns alien zombies. Once the base is destroyed, a **Huge Zombie** boss spawns that all units can attack.

---

## Timeline / Phases

```
[PLAY pressed] → [Countdown] → [Fight Phase] → [Attack Base] → [Boss Fight] → [Win]
```

### Phase 1: Countdown (0–4 seconds)
- Screen shows "3", "2", "1", "GO!" (1 second each)
- Game is **paused** — no zombies spawn, no units fire
- `countdownPhase = 1` during this time
- After 4 seconds: `countdownPhase = 2`, game begins

### Phase 2: Fight Zombies (4–34 seconds)
- Zombie Base spawns **1 alien zombie every 2 seconds** on a random row
- Zombie Base is **invulnerable** for 30 seconds (`invulnTimer = 30000`)
- Player places units and fights incoming alien zombies
- Units CANNOT attack the base during this phase

### Phase 3: Attack the Base (after 34 seconds)
- `invulnTimer` reaches 0 — base becomes vulnerable
- Ranged units can attack the base **only if no zombies are in their row**
- Base has **3000 HP**
- Base continues spawning zombies while being attacked
- Player must balance killing zombies AND damaging the base

### Phase 4: Huge Zombie Boss Fight
- When base HP reaches 0: base is destroyed, screen shakes
- **Huge Zombie** spawns at the right edge, moves along row 2 (center)
- Huge Zombie stats: 2000 HP, 100 damage, 1 square per 3 seconds
- **ALL units on ALL rows** can attack the Huge Zombie simultaneously
- Melee units in row 2 can also hit it if within range

### Phase 5: Victory
- Game checks: `hugeZombie.dead === true && hugeZombie.hp <= 0`
- Only then does `gameWon = true` trigger
- "YOU WIN!" overlay appears

---

## Key Variables

| Variable | Type | Purpose |
|----------|------|---------|
| `isBossLevel` | boolean | `true` when `selectedLevel === 42` |
| `countdownPhase` | 0/1/2 | 0=not started, 1=counting down, 2=game active |
| `countdownStart` | number | `performance.now()` timestamp when level started |
| `zombieBase` | object/null | The enemy base on the right side |
| `zombieBase.hp` | number | Starts at 3000 |
| `zombieBase.invulnTimer` | number | Starts at 30000ms, counts down each frame |
| `zombieBase.alive` | boolean | `false` once destroyed |
| `zombieBase.lastSpawn` | number | Timestamp of last zombie spawn |
| `zombieBase.spawnRate` | number | 2000ms between spawns |
| `hugeZombie` | object/null | The boss, spawns after base dies |
| `hugeZombie.hp` | number | Starts at 2000 |
| `hugeZombie.dead` | boolean | Win condition checks this |

---

## Code Flow in `update(ts)`

```
1. If countdownPhase === 1:
   - Check if 4 seconds passed → set countdownPhase = 2
   - Return early (game paused)

2. If boss level AND countdownPhase === 2 AND base alive:
   - Decrement invulnTimer by dt
   - Spawn zombie every 2 seconds

3. Units shoot:
   - canHitHuge: true if hugeZombie exists and alive
   - canHitBase: true if base alive AND invulnTimer <= 0 AND no zombies in row AND unit is ranged
   - Priority: hugeZombie > row zombies > zombie base

4. Bullet hits base (targetRef === -3):
   - Reduce base HP
   - If HP <= 0: destroy base, spawn hugeZombie

5. Win condition:
   - Only triggers when hugeZombie exists AND hugeZombie.dead AND hp <= 0
```

---

## Code Flow in `drawGame(ts)`

```
1. Draw terrain (interstellar: stars, nebula, planets)
2. Draw blood stains
3. Draw fortress (Earth sphere)
4. Draw placed units
5. Draw zombies (alien-styled: purple skin, antennae, third eye)
6. Draw huge zombie (if exists and alive)
7. Draw zombie base (dark fortress with green glow)
8. Draw bullets
9. Draw particles
10. ctx.restore() — end screen shake
11. Draw HUD (money, hearts, power buttons)
12. Draw Shop
13. If countdownPhase === 1: draw countdown overlay on top
14. If gameOver: draw game over screen
15. If gameWon: draw win overlay
```

---

## Alien Zombies

On boss level, all spawned zombies have `isAlien = true`, which gives them:
- Purple skin color (`#8844cc`) instead of green
- Dark purple shirt (`#2a0a4a`)
- Antennae with glowing magenta tips
- A third eye on the forehead

Same 3 types still spawn (normal 65%, speed 20%, muscular 15%).

---

## Theme

| Element | Value |
|---------|-------|
| Terrain | Interstellar (deep space, stars, nebula, distant planets) |
| Base | Earth (blue sphere with continents, clouds, atmosphere) |
| Lives | 15 |
| Starting Money | $1000 |
| Zombie Base HP | 3000 |
| Huge Zombie HP | 2000 |
| Invuln Timer | 30 seconds |

---

## Win/Lose Conditions

**Win:** Huge Zombie is dead (hp <= 0)
**Lose:** Lives reach 0 (each zombie entering fortress costs 1 life)
