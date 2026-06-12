# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the game

No build step — open `index.html` directly or serve with any static server:

```bash
python3 -m http.server 8000   # then open http://localhost:8000
open index.html               # macOS direct open
```

## Architecture

Three files, no dependencies, no bundler:

- **`index.html`** — DOM structure: a `300×600` `<canvas id="board">`, a side panel (`<aside>`), a `120×120` `<canvas id="next-canvas">`, and a single overlay div reused for both PAUSE and GAME OVER states.
- **`style.css`** — Dark/retro aesthetic; `backdrop-filter` on overlays.
- **`game.js`** — All game logic (~305 lines, `'use strict'`).

### game.js internals

| Concept | Implementation |
|---|---|
| Board state | `ROWS×COLS` integer matrix; `0` = empty, `1–7` = piece color index |
| Piece object | `{ type, shape, x, y }` where `shape` is a 2-D matrix |
| Rotation | `rotateCW`: transpose + reverse rows; `tryRotate` attempts kicks `[0, -1, 1, -2, 2]` |
| Game loop | `requestAnimationFrame`-based; `dropAccum` tracks elapsed ms vs `dropInterval` |
| Line clear | `board.splice` + `board.unshift` in-place, bottom-up scan |
| Ghost piece | `ghostY()` projects `current` downward; drawn at `globalAlpha = 0.2` |

Global state variables: `board, current, next, score, lines, level, paused, gameOver, lastTime, dropAccum, dropInterval, animId`.

`init()` resets all state and starts the loop. `lockPiece()` merges → clears lines → spawns next piece. Collision in `spawn()` triggers `endGame()`.

### Key tunable constants (top of `game.js`)

| Constant | Default | Note |
|---|---|---|
| `COLS` / `ROWS` | `10` / `20` | Must match canvas `width`/`height` in `index.html` (`COLS×BLOCK` / `ROWS×BLOCK`) |
| `BLOCK` | `30` | Pixel size per cell |
| `COLORS` | 7 colors | Index matches piece type (1–7) |
| `LINE_SCORES` | `[0,100,300,500,800]` | Multiplied by current level |
| `dropInterval` | `1000 ms` | Initial fall speed; recalculated as `max(100, 1000 − (level−1)×90)` |
