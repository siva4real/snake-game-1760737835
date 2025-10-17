# Snake — Minimal, Modern, Playable

## Summary
This repository contains a minimal, self‑contained Snake game that runs entirely in the browser with no external dependencies. It features clean, modern UI, keyboard and mobile controls, pause/restart, high score persistence, and an optional configuration system that can be loaded dynamically via a URL query parameter.

The application is a single file (index.html) for easy deployment, including on GitHub Pages.

Key features:
- Classic Snake gameplay (Arrow keys / WASD)
- Mobile-friendly on-screen D‑Pad
- Pause/Resume (P), Restart (R)
- Wall wrapping toggle
- Adjustable speed
- High score saved to localStorage
- Optional external configuration via ?url=…

## Setup
There is nothing to install.

- Clone or download the repository.
- Open index.html in any modern browser.
- Or deploy to GitHub Pages (recommended) and open your Pages URL.

GitHub Pages:
1. Push this repo to GitHub.
2. In Settings → Pages, set the branch to main (or default) and folder to root.
3. Visit https://<your-username>.github.io/<your-repo>/ to play.

The game uses only standard web APIs and works offline. No build step is required.

## Usage
Basic controls:
- Start: Click Play or press any arrow key / WASD
- Move: Arrow keys or WASD
- Pause/Resume: P or Play button
- Restart: R or Restart button
- Adjust speed: Use the Speed slider
- Wrap toggle: Enable/disable passing through walls
- Theme: Light/Dark theme button

Score and high score are shown above the game. The high score persists across sessions via localStorage and is tracked per grid size and wrap mode.

### Configuration via URL (?url=…)
You can supply a JSON configuration via the query parameter `url`. This lets you change grid size, speed, wrap behavior, theme colors, and obstacles/levels.

Supported forms:
- Remote JSON (CORS-permitting):
  - ?url=https://example.com/config.json
- Data URL (no CORS issues):
  - ?url=data:application/json,%7B%22gridSize%22%3A24%7D
- Inline encoded JSON using a helper scheme (no fetch required):
  - ?url=json:%7B%22gridSize%22%3A24%2C%22speed%22%3A12%2C%22wrap%22%3Atrue%7D
    - This is simply `json:` followed by URL-encoded JSON.

If a config is successfully loaded, a small “Config ✓” badge appears in the header. The game remains fully playable even if loading fails (it falls back to defaults).

Configuration fields:
- gridSize: number (sets both rows and columns, e.g., 20)
- rows: number
- cols or columns: number
- speed: number (moves per second; clamped 3–20)
- wrap: boolean (true = wrap through edges, false = collide with walls)
- theme: object to override CSS variables (colors). Supported keys:
  - background, panel, foreground (or text), grid, accent, snake, snakeHead, food, danger
- obstacles: array of [x, y] pairs (0-indexed) marking blocked cells
- level: array of strings; use “#” to mark obstacles, e.g.:
  [
    "####################",
    "#..............#...#",
    "#..............#...#",
    "#..............#...#",
    "####################"
  ]

Notes:
- rows/cols (or gridSize) define the board size. If `level` is provided, its dimensions set rows/cols automatically.
- Obstacles are kept on restart. Food never spawns on obstacles or the snake.
- High score is tracked per board size and wrap mode.

Example configurations:
1) Inline JSON
- URL: https://<your-pages-url>/?url=json:%7B%22gridSize%22%3A24%2C%22speed%22%3A12%2C%22wrap%22%3Atrue%7D

2) Data URL with theme
- URL (line breaks added for readability):
  ?url=data:application/json,%7B
  %22gridSize%22%3A22,
  %22theme%22%3A%7B%22background%22%3A%22%230b1022%22,%22snake%22%3A%22%2320c884%22,%22food%22%3A%22%23ff4757%22%7D
  %7D

3) Remote JSON (ensure the server allows CORS)
- ?url=https://example.com/snake-level.json

Sample JSON:
{
  "gridSize": 24,
  "speed": 10,
  "wrap": false,
  "theme": {
    "background": "#101425",
    "accent": "#5c7cff",
    "snake": "#1bb275",
    "snakeHead": "#20c884",
    "food": "#ff3b30"
  },
  "obstacles": [[5,5],[6,5],[7,5]],
  "level": [
    "########################",
    "#......................#",
    "#......................#",
    "#......................#",
    "########################"
  ]
}

## Code Explanation
Everything lives in index.html and is split into three main parts: styles, markup, and a small, production-ready JavaScript module.

Highlights:
- Rendering: HTML5 Canvas with devicePixelRatio-aware sizing for crisp graphics. A subtle grid is drawn under the snake.
- Game loop: requestAnimationFrame with a fixed-step accumulator (msPerStep) to control movement speed independent of frame rate.
- State:
  - Board size: rows/cols
  - Snake as an array of segments [{x,y}, ...], with a head and body
  - Direction management to prevent instant 180-degree reversals
  - Food spawning avoids snake and obstacles
  - Obstacles are stored in a Set keyed by "x,y" for O(1) lookups
- Controls: Keyboard (Arrow/WASD), on-screen D‑Pad for mobile, buttons for Play/Pause/Restart, speed slider, wrap toggle, theme toggle.
- Persistence: High score saved to localStorage, namespaced by grid size and wrap mode.
- Configuration: The app reads the URL parameter `?url=...`, then:
  - If it starts with `json:`, it decodes and parses the inline JSON directly.
  - Otherwise, it fetches the URL (works for data: URLs and remote URLs if CORS allows).
  - The configuration can set grid size, wrap, speed, obstacles, a level map, and theme colors.
  - After applying configuration, the game resets cleanly.

Minimal public API for automated tests:
- window.__snake.start(), .pause(), .restart(), .setSpeed(n), .setWrap(bool), .applyConfig(cfg)

This API is optional and has no effect on normal gameplay, but can assist automated checks.

## License
MIT License

Copyright (c) 2025

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the “Software”), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.