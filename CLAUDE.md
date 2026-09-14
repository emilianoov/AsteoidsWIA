# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Asteroids clone built with plain HTML5 Canvas and vanilla ES6+ JavaScript. No dependencies, no bundler, no build step, no package.json.

## Running

Open `index.html` directly in a browser, or serve it locally:

```bash
npx serve .
```

There is no build/lint/test tooling in this repo — verify changes by reloading the page in a browser.

## Architecture

Everything lives in `game.js` (single file, loaded directly by `index.html` via a plain `<script>` tag — no modules). The structure is:

- **Entity classes**: `Bullet`, `Asteroid`, `Ship`, `Particle`. Each has `update(dt)` and `draw()`; entities mark themselves `dead = true` and are filtered out of their arrays rather than removed in place.
- **Global mutable game state**: `ship`, `bullets`, `asteroids`, `particles`, `score`, `lives`, `level`, `state` (`'playing' | 'dead' | 'gameover'`), `deadTimer`. Reset via `initGame()`, advanced via `nextLevel()`.
- **Fixed-timestep-free game loop**: `requestAnimationFrame(loop)` computes `dt` in seconds (clamped to 0.05 max) and calls `update(dt)` then `draw()` each frame.
- **Input**: raw keyboard state tracked in `keys` (held) and `justPressed` (edge-triggered, consumed via `pressed(code)`), read directly from the `Ship` and `update()` — no input abstraction layer.
- **Collision & splitting**: asteroid size follows 3 (large) → 2 (medium) → 1 (small), with `RADII`/`SPEEDS`/`POINTS` indexed by size. Destroying an asteroid calls `.split()` to produce two smaller asteroids (size 1 asteroids don't split).
- **Toroidal space**: all moving entities wrap position via `wrap(v, max)` against canvas width `W` (800) and height `H` (600).
- **Rendering**: no sprites/images — everything is drawn as vector paths (`ctx.beginPath`/`lineTo`/`stroke`) in each entity's `draw()`, in world coordinates translated/rotated per-entity via `ctx.save()`/`ctx.translate()`/`ctx.rotate()`/`ctx.restore()`.

## Notes

- The README (in Spanish) describes power-ups and a "shooting star" asteroid type as features; these are not present in `game.js` yet — treat the README as aspirational/outdated in that respect, not as a spec of current behavior.
- Game text/UI strings (HUD, game-over overlay) are in Spanish; keep new user-facing strings consistent with that unless told otherwise.
