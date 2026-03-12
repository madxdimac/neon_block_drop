# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

**BLOCK DROP** — a single-file browser-based Tetris-style game (`block_drop.html`). No build step, no dependencies, no server required. Open the file directly in any modern browser.

## Architecture

Everything lives in `index.html` as a single `<canvas>`-based game using the vanilla JS Canvas 2D API. The file is organized into clearly labelled sections (marked with `// ===...===` banners) in this order:

1. **Canvas / constants** — `W`, `H`, `CELL`, `COLS`, `ROWS`, `BX`, `BY`, `BH`, lock-delay constants
2. **Starfield** — parallax 3-layer background, runs every frame
3. **Piece definitions** — `PIECE_DEFS` array (shape matrices + HSL color), `PIECE_NAMES`, `rotateCW`, `cloneShape`
4. **Game state** — all mutable state variables; `S` object defines the state machine (`INTRO → SHAPES → PLAY ↔ PAUSE`, `PLAY → OVER → INTRO`)
5. **Board / piece helpers** — `newBoard`, `randPiece`, `spawnPiece`, `startGame`
6. **Physics** — `valid`, `ghostY`, `isGrounded`, `resetLock`, movement/rotation functions, `hardDrop`, `lock` (scoring, combo, B2B, line-clear flash)
7. **Particles** — burst particles spawned on line clears
8. **Popups** — floating score/combo text notifications
9. **Input** — `keydown`/`keyup` listeners, DAS/ARR auto-repeat, `onKey` dispatcher, `processARR`
10. **Draw helpers** — `glow`/`noGlow`, `roundRect`, `txt`, `drawCell`, `divider`
11. **Board rendering** — `drawBoard`, `drawGhost`, `drawCurrent`, `drawLockBar`
12. **HUD** (right panel) — `drawHUD`: SCORE, LEVEL, LINES, COMBO, B2B badge, NEXT preview, SPEED bar
13. **Controls panel** (left panel) — `drawControls`: key reference, auto-sizes via `BH`
14. **Game header** — small "MAD BLOCKS" title drawn above the board during gameplay
15. **Intro screen** — `drawIntro`: title, level selector (◄ ►), key reference box
16. **Shapes screen** — `drawShapesScreen`: grid of all 11 pieces with names
17. **Overlays** — `drawPauseOverlay`, `drawGameOver`
18. **Main loop** — `loop(ts)` using `requestAnimationFrame`; dispatches to the active state's draw/tick functions
19. **Boot** — initialises stars and state, kicks off the RAF loop

## Layout

The canvas is `800 × 740px` with three vertical panels side-by-side:

| Panel | X range | Width | Content |
|---|---|---|---|
| Left (controls) | 10 → BX-10 | `BX-20` | Key labels |
| Board | BX → BX+COLS×CELL | `COLS×CELL` | Play field |
| Right (HUD) | BX+COLS×CELL+18 → 790 | ~272px | Score/stats |

`BX=260`, `COLS=10`, `CELL=24`, so the board occupies x 260–500. `BY=50`, `ROWS=28`, `BH=672`.

## Key values to know before editing

- `CELL` — pixel size of one grid cell (changing this scales the board width and height)
- `ROWS` — changing this also changes `BH` (derived as `ROWS*CELL`), which controls both side-panel heights
- `BX` — left edge of the board; also sets the left-panel width (`BX-20`) and where the HUD begins
- `LOCK_DELAY` (500ms) / `MAX_LOCK_RESETS` (15) — standard Guideline Tetris lock-delay values
- HUD Y offsets are hardcoded relative to `hy=BY`; if `BH` changes significantly they need redistribution

## Visual aesthetic

Matches the neon-space-shooter repo style: pure black background, Courier New bold monospace throughout, HSL-based neon piece colors at 100% saturation, Canvas `shadowBlur`/`shadowColor` for all glow effects (8–30px), cyan `#0ff` as the primary UI accent color.

## File reference

| File | Purpose |
|---|---|
| `block_drop.html` | Entire game — HTML + CSS + JS in one file |
| `block_shapes.jpeg` | Reference image used to select the 11 piece shapes |
