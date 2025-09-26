# SNAKE_GAME

# 🐍 Classic Snake Game (Vanilla JavaScript)

An elegant, beginner-friendly **Snake Game** built with **HTML**, **CSS**, and **modern JavaScript modules**. The codebase is kept small, clean, and well-structured—perfect for learning game loops, keyboard input, collision detection, and DOM-based rendering.

---

## 🎮 Live Demo

- Host this folder on any static host (Vercel, Netlify, GitHub Pages), or run a local server (instructions below).
- Entry file: `index.html`

---

## ✨ Features

- Smooth game loop using `requestAnimationFrame`
- Arrow-key controls with built-in **direction lock** to prevent instant 180° turns
- **21×21 grid** with DOM grid layout
- **Food spawns** at random empty tiles (never on the snake)
- Snake **grows** when it eats food
- **Game Over** when the snake hits the wall or itself, with a quick **restart** prompt
- Minimal, readable code split into **modules**

---

## 🕹️ Controls

- **Arrow Up (↑)** — move up
- **Arrow Down (↓)** — move down
- **Arrow Left (←)** — move left
- **Arrow Right (→)** — move right

> Tip: The code prevents reversing direction immediately (e.g., going up to down in one frame).

---

## 🗂️ Project Structure
```
📦 snake-game
├── index.html        # Page, styles, and module script import
├── game.js           # Main loop: update/draw + game over handling
├── snake.js          # Snake state, movement, drawing, collisions
├── food.js           # Food placement, growth handling
├── grid.js           # Grid size + helpers (random positions, bounds)
└── input.js          # Keyboard input & direction locking
```

---

## 🚀 Getting Started (Local)
Because the project uses ES Modules (`type="module"`), open it with a **local server** (not by double-clicking the HTML).

### Option A: VS Code Live Server
1. Install the **Live Server** extension.
2. Right‑click `index.html` → **Open with Live Server**.

### Option B: Node (npx serve)
```bash
npx serve .
# then open the printed URL in your browser
```

### Option C: Python
```bash
# Python 3
python -m http.server 8080
# visit http://localhost:8080
```

---

## 🧠 How It Works (High-Level)
1. **Game Loop** — `requestAnimationFrame` repeatedly calls `main()`, which throttles updates to match the chosen snake speed.  
2. **Update Phase** — Move the snake, handle growth, place food, and check for death.  
3. **Draw Phase** — Clear the board and re-render the snake and food as grid‑positioned `<div>`s.  
4. **Game Over** — If the snake goes out of bounds or hits itself, show a confirm dialog and reload to restart.

---

## 🔍 Code Walkthrough (Step by Step)

### `index.html`
- Defines a centered square **game board** that uses CSS Grid for a 21×21 layout.
- Injects the `game.js` script as a **module** so it can import other files.
- Styles:
  - `.snake` tiles are greenish with a border.
  - `.food` tiles are red with a border.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Snake Game</title>
  <style>
    /* Center the board, make it a square using vmin */
    body {
      height: 100vh;
      width: 100vw;
      display: flex;
      justify-content: center;
      align-items: center;
      margin: 0;
      background-color: black;
    }
    /* 21x21 grid board */
    #game-board {
      background-color: white;
      width: 100vmin;
      height: 100vmin;
      display: grid;
      grid-template-rows: repeat(21, 1fr);
      grid-template-columns: repeat(21, 1fr);
    }
    .snake { background-color: #badd78; border: .25vmin solid black; }
    .food  { background-color: #ad2f2f; border: .25vmin solid black; }
  </style>
  <script src="game.js" defer type="module"></script>
</head>
<body>
  <div id="game-board"></div>
</body>
</html>
```

### `game.js`
- Imports **snake** and **food** update/draw functions.
- Manages the **main loop** with `requestAnimationFrame`.
- Limits updates to the chosen `SNAKE_SPEED` (times per second).
- Handles **game over**: if dead, asks to restart and reloads the page.

```js
import { update as updateSnake, draw as drawSnake, SNAKE_SPEED, getSnakeHead, snakeIntersection } from './snake.js';
import { update as updateFood, draw as drawFood } from './food.js';
import { outsideGrid } from './grid.js';

let lastRenderTime = 0;
let gameOver = false;
const gameBoard = document.getElementById('game-board');

function main(currentTime) {
  if (gameOver) {
    if (confirm('You lost. Press ok to restart.')) {
      window.location = '/';
    }
    return;
  }

  window.requestAnimationFrame(main);
  const secondsSinceLastRender = (currentTime - lastRenderTime) / 1000;
  if (secondsSinceLastRender < 1 / SNAKE_SPEED) return;

  lastRenderTime = currentTime;
  update();
  draw();
}
window.requestAnimationFrame(main);

function update() {
  updateSnake();
  updateFood();
  checkDeath();
}
function draw() {
  gameBoard.innerHTML = '';
  drawSnake(gameBoard);
  drawFood(gameBoard);
}
function checkDeath() {
  gameOver = outsideGrid(getSnakeHead()) || snakeIntersection();
}
```

### `snake.js`
- Exposes `SNAKE_SPEED` (moves per second).
- Maintains the `snakeBody` list of segments (head at index 0).
- Moves the snake by copying positions **from tail to head**, then updating head by the current input direction.
- `expandSnake(amount)` adds queued segments when the snake eats.
- Collision helpers: `getSnakeHead()`, `onSnake()`, `snakeIntersection()`.

```js
import { getInputDirection } from './input.js';

export const SNAKE_SPEED = 5;
const snakeBody = [{ x: 11, y: 11 }];
let newSegments = 0;

export function update() {
  addSegments();
  const inputDirection = getInputDirection();
  for (let i = snakeBody.length - 2; i >= 0; i--) {
    snakeBody[i + 1] = { ...snakeBody[i] };
  }
  snakeBody[0].x += inputDirection.x;
  snakeBody[0].y += inputDirection.y;
}

export function draw(gameBoard) {
  snakeBody.forEach(segment => {
    const el = document.createElement('div');
    el.style.gridRowStart = segment.y;
    el.style.gridColumnStart = segment.x;
    el.classList.add('snake');
    gameBoard.appendChild(el);
  });
}

export function expandSnake(amount) { newSegments += amount; }

export function onSnake(position, { ignoreHead = false } = {}) {
  return snakeBody.some((segment, index) => {
    if (ignoreHead && index === 0) return false;
    return segment.x === position.x && segment.y === position.y;
  });
}

export function getSnakeHead() { return snakeBody[0]; }

export function snakeIntersection() {
  return onSnake(snakeBody[0], { ignoreHead: true });
}

function addSegments() {
  for (let i = 0; i < newSegments; i++) {
    snakeBody.push({ ...snakeBody[snakeBody.length - 1] });
  }
  newSegments = 0;
}
```

### `input.js`
- Listens for **Arrow key** presses.
- Uses `lastInputDirection` to prevent immediate reversal (e.g., left → right instantly).
