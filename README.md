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
