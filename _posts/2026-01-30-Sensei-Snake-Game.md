---
layout: post
title: "Sensei Snake Game"
date: 2026-01-30
---

<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Snake Game (Mobile)</title>
  <style>
    :root {
      --panel: #222;
      --accent: #4caf50;
      --accent2: #81c784;
      --food: #e53935;
      --grid: #333;
    }
    * { box-sizing: border-box; }
    body {
      background: var(--bg);
      color: var(--text);
      font-family: Arial, sans-serif;
      margin: 0;
      display: flex;
      flex-direction: column;
      align-items: center;
      min-height: 100vh;
      -webkit-touch-callout: none;
      -webkit-user-select: none;
      user-select: none;
    }
    .container {
      width: 100%;
      max-width: 520px;
      padding: 12px;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 10px;
    }
    #game {
      border: 2px solid var(--accent);
      background: var(--panel);
      touch-action: none; /* allow custom swipe handling */
      width: 100%;
      max-width: 420px;
      aspect-ratio: 1 / 1; /* square canvas responsive */
    }
    .info {
      width: 100%;
      max-width: 420px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-size: 16px;
    }
    .controls {
      display: grid;
      grid-template-columns: 80px 80px 80px;
      grid-template-rows: 80px 80px 80px;
      gap: 8px;
      justify-content: center;
      align-items: center;
      margin-top: 6px;
      touch-action: manipulation;
      user-select: none;
    }
    .btn {
      background: var(--accent);
      color: #fff;
      border: none;
      border-radius: 10px;
      font-size: 18px;
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 0;
      width: 80px;
      height: 80px;
    }
    .btn:active { background: #43a047; }
    .btn.small {
      height: 40px;
      border-radius: 6px;
      font-size: 14px;
      padding: 8px 12px;
      width: auto;
    }
    .hidden { display: none; }
  </style>
</head>
<body>
  <div class="container">
    <canvas id="game"></canvas>

    <div class="info">
      <div>Score: <span id="score">0</span></div>
      <div>
        <button id="pauseBtn" class="btn small">Pause</button>
        <button id="restartBtn" class="btn small">Restart</button>
      </div>
    </div>

    <!-- On-screen directional pad -->
    <div class="controls" id="controls">
      <div></div>
      <button class="btn" id="up">▲</button>
      <div></div>
      <button class="btn" id="left">◀</button>
      <div></div>
      <button class="btn" id="right">▶</button>
      <div></div>
      <button class="btn" id="down">▼</button>
      <div></div>
    </div>
  </div>

  <script>
    // Responsive canvas setup
    const canvas = document.getElementById('game');
    const ctx = canvas.getContext('2d', { alpha: false });

    function resizeCanvas() {
      // Match CSS size (which maintains square via aspect-ratio)
      const rect = canvas.getBoundingClientRect();
      canvas.width = Math.floor(rect.width);
      canvas.height = Math.floor(rect.height);
      // Recompute tileCount based on gridSize
      tileCount = Math.floor(canvas.width / gridSize);
      // Keep it square by using min dimension
      const minDim = Math.min(canvas.width, canvas.height);
      canvas.width = canvas.height = minDim;
      tileCount = Math.floor(minDim / gridSize);
      // Ensure min grid dimension
      if (tileCount < 10) {
        gridSize = Math.floor(minDim / 20);
        tileCount = Math.floor(minDim / gridSize);
      }
      draw(); // redraw after resize
    }

    // Game variables
    let gridSize = 20;
    let tileCount = 20; // will be updated on resize
    const initialSpeedMs = 130;

    let snake = [];
    let direction = { x: 1, y: 0 };
    let nextDirection = { x: 1, y: 0 };
    let food = { x: 10, y: 10 };
    let score = 0;
    let speedMs = initialSpeedMs;
    let timerId = null;
    let gameOver = false;
    let paused = false;

    // Touch swipe detection
    let touchStart = null;

    function resetGame() {
      score = 0;
      speedMs = initialSpeedMs;
      gameOver = false;
      paused = false;
      direction = { x: 1, y: 0 };
      nextDirection = { x: 1, y: 0 };
      // Center-start snake
      const cx = Math.floor(tileCount / 2);
      const cy = Math.floor(tileCount / 2);
      snake = [{ x: cx, y: cy }, { x: cx - 1, y: cy }, { x: cx - 2, y: cy }];
      placeFood();
      updateScore();
      if (timerId) clearInterval(timerId);
      timerId = setInterval(gameLoop, speedMs);
      draw();
    }

    function placeFood() {
      let valid = false;
      while (!valid) {
        food.x = Math.floor(Math.random() * tileCount);
        food.y = Math.floor(Math.random() * tileCount);
        valid = !snake.some(seg => seg.x === food.x && seg.y === food.y);
      }
    }

    function gameLoop() {
      if (gameOver || paused) return;

      direction = nextDirection;
      const newHead = { x: snake[0].x + direction.x, y: snake[0].y + direction.y };

      // Wrap-around walls (mobile-friendly)
      newHead.x = (newHead.x + tileCount) % tileCount;
      newHead.y = (newHead.y + tileCount) % tileCount;

      // Self collision
      if (snake.some(seg => seg.x === newHead.x && seg.y === newHead.y)) {
        endGame();
        return;
      }

      snake.unshift(newHead);

      // Food check
      if (newHead.x === food.x && newHead.y === food.y) {
        score += 10;
        updateScore();
        placeFood();
        // gentle speed-up
        if (speedMs > 70) {
          speedMs -= 5;
          clearInterval(timerId);
          timerId = setInterval(gameLoop, speedMs);
        }
      } else {
        snake.pop();
      }

      draw();
    }

    function updateScore() {
      document.getElementById('score').textContent = score;
    }

    function draw() {
      // Clear
      ctx.fillStyle = '#222';
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      // Optional grid
      ctx.strokeStyle = '#333';
      ctx.lineWidth = 1;
      for (let i = 1; i < tileCount; i++) {
        ctx.beginPath();
        ctx.moveTo(i * gridSize, 0);
        ctx.lineTo(i * gridSize, canvas.height);
        ctx.stroke();
        ctx.beginPath();
        ctx.moveTo(0, i * gridSize);
        ctx.lineTo(canvas.width, i * gridSize);
        ctx.stroke();
      }

      // Food
      ctx.fillStyle = '#e53935';
      drawCell(food.x, food.y);

      // Snake
      for (let i = 0; i < snake.length; i++) {
        ctx.fillStyle = i === 0 ? '#4caf50' : '#81c784';
        drawCell(snake[i].x, snake[i].y);
      }

      if (gameOver) {
        ctx.fillStyle = 'rgba(0,0,0,0.6)';
        ctx.fillRect(0, 0, canvas.width, canvas.height);
        ctx.fillStyle = '#fff';
        ctx.textAlign = 'center';
        ctx.font = '20px Arial';
        ctx.fillText('Game Over', canvas.width / 2, canvas.height / 2 - 10);
        ctx.font = '14px Arial';
        ctx.fillText('Tap Restart to play again', canvas.width / 2, canvas.height / 2 + 20);
      }
    }

    function drawCell(x, y) {
      ctx.fillRect(x * gridSize + 1, y * gridSize + 1, gridSize - 2, gridSize - 2);
    }

    function endGame() {
      gameOver = true;
      clearInterval(timerId);
      draw();
    }

    // Controls: buttons
    function setDir(dx, dy) {
      // Prevent reversing into self
      if ((dx === 1 && direction.x === -1) || (dx === -1 && direction.x === 1) ||
          (dy === 1 && direction.y === -1) || (dy === -1 && direction.y === 1)) {
        return;
      }
      nextDirection = { x: dx, y: dy };
    }

    document.getElementById('up').addEventListener('click', () => setDir(0, -1));
    document.getElementById('down').addEventListener('click', () => setDir(0, 1));
    document.getElementById('left').addEventListener('click', () => setDir(-1, 0));
    document.getElementById('right').addEventListener('click', () => setDir(1, 0));

    // Keyboard for desktop
    window.addEventListener('keydown', (e) => {
      const k = e.key;
      if (k === 'ArrowUp') setDir(0, -1);
      else if (k === 'ArrowDown') setDir(0, 1);
      else if (k === 'ArrowLeft') setDir(-1, 0);
      else if (k === 'ArrowRight') setDir(1, 0);
      else if (k.toLowerCase() === 'p') togglePause();
    });

    // Touch swipe for direction
    canvas.addEventListener('touchstart', (e) => {
      if (e.touches.length > 0) {
        const t = e.touches[0];
        touchStart = { x: t.clientX, y: t.clientY, time: Date.now() };
      }
    }, { passive: true });

    canvas.addEventListener('touchend', (e) => {
      if (!touchStart) return;
      const touch = e.changedTouches[0];
      const dx = touch.clientX - touchStart.x;
      const dy = touch.clientY - touchStart.y;
      const dt = Date.now() - touchStart.time;
      const minDist = 20; // minimum swipe distance
      const maxTime = 500; // swipe time

      if (dt < maxTime && (Math.abs(dx) > minDist || Math.abs(dy) > minDist)) {
        if (Math.abs(dx) > Math.abs(dy)) {
          setDir(dx > 0 ? 1 : -1, 0);
        } else {
          setDir(0, dy > 0 ? 1 : -1);
        }
      } else {
        // Tap toggles pause
        togglePause();
      }
      touchStart = null;
    }, { passive: true });

    // Pause/Restart
    function togglePause() {
      if (gameOver) return;
      paused = !paused;
      document.getElementById('pauseBtn').textContent = paused ? 'Resume' : 'Pause';
      if (!paused) {
        clearInterval(timerId);
        timerId = setInterval(gameLoop, speedMs);
      }
    }
    document.getElementById('pauseBtn').addEventListener('click', togglePause);
    document.getElementById('restartBtn').addEventListener('click', () => {
      resetGame();
      document.getElementById('pauseBtn').textContent = 'Pause';
    });

    // Resize handling
    window.addEventListener('resize', () => {
      resizeCanvas();
    });

    // Init
    resizeCanvas();
    resetGame();
  </script>
</body>
</html>
