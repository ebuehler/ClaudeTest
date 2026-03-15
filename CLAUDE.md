# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the Games

Open any HTML file directly in a browser — no build step, no server needed:

```bash
open shooter.html
open tictactoe.html
```

## Repository Convention

This is a collection of self-contained browser games. Each game is a **single HTML file** with all styles, logic, and assets inline. No external dependencies, no network requests, no bundler.

## Git Workflow (Required)

Commit and push to `origin/main` after every meaningful unit of work — a feature added, a bug fixed, a refactor completed. Never leave work uncommitted at the end of a task. This ensures the project can always be reverted to a known-good state.

```bash
git add <file>
git commit -m "short imperative summary of what changed and why"
git push
```

Commit message rules:
- Use the imperative mood ("add", "fix", "remove", not "added" or "fixes")
- First line ≤ 72 characters; add a blank line then details if needed
- Reference what changed and why, not just what the code does

## shooter.html Architecture

800×600 Canvas 2D game. All sprites are drawn programmatically with `fillRect` — no image files.

**Game state machine:** `MENU → PLAYING → LEVEL_COMPLETE → PLAYING` (loops through 5 levels), with `GAME_OVER` and `WIN` as terminal states. Controlled by the `state` variable and `STATE` enum.

**Game loop:** `requestAnimationFrame` with capped delta-time (`dt`, max 50ms). `update(dt)` runs logic; `draw()` renders. Both are called every frame.

**Entity arrays:** `bullets`, `enemies`, `particles` — filtered each frame to remove dead entries (`dead` flag or `life <= 0`).

**Key systems:**
- `Player` class — acceleration-based movement, mouse-tracked aim angle, shoot cooldown, invincibility frames after damage
- `Enemy` class — three types (`grunt`, `charger`, `shooter`) share one class, behavior branched by `this.type`; shooter enemies push new `Bullet(fromEnemy=true)` into the shared `bullets` array
- `Particle` class — square pixels with velocity decay and alpha fade, used for muzzle flash and death bursts
- Collision detection — circle approximation using `dist()` vs `e.r + bullet.r`
- Screen shake — `shake` object with `dur`, applied as `ctx.translate` at draw time
- Level config — `LEVELS` array of plain objects; `level` index advances on completion

**Visual style:** Dark background `#0d0d0d`, neon green player, red/orange/purple enemies, yellow bullets. CSS scanline overlay sits above the canvas via `#scanlines` div with `pointer-events: none`.
