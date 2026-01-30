---
layout: post
title: "My Second Post"
date: 2026-01-30
---

<head>
  <meta charset="UTF-8" />
  <title>Snake Game</title>
  <style>
    body {
      background: #111;
      color: #eee;
      font-family: Arial, sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      margin: 0;
      padding: 20px;
    }
    #game {
      border: 2px solid #4caf50;
      background: #222;
    }
    .info {
      margin-top: 10px;
    }
    button {
      margin-top: 10px;
      padding: 8px 12px;
      background: #4caf50;
      border: none;
      color: #fff;
      cursor: pointer;
      border-radius: 4px;
    }
    button:hover {
      background: #43a047;
    }
  </style>
</head>
<body>
  <canvas id="game" width="400" height="400"></canvas>
  <div class="info">Score: <span id="score">0</span></div>
  <button id="restart">Restart</button>

  <script>
    const canvas = document.getElementById('game');
    const ctx = canvas.getContext('2d');
    const scoreEl = document.getElementById('score');
    const restartBtn = document.getElementById('restart');

    const gridSize = 20;       // pixels per cell
    const tileCount = canvas.width / gridSize; // 20x20 grid
    const initialSpeedMs = 120;

    let snake = [];
    let direction = { x: 1, y: 0 }; // start moving right
    let nextDirection = { x: 1, y: 0 };
    let food = { x: 10, y: 10 };
    let score = 0;
    let speedMs = initialSpeedMs;
    let timerId = null;
    let gameOver = false;

    function resetGame() {
      snake = [
        { x: 8, y: 10 },
        { x: 7, y: 10 },
        { x: 6, y: 10 }
      ];
      direction = { x: 1, y: 0 };
      nextDirection = { x: 1, y: 0 };
      score = 0;
      speedMs = initialSpeedMs;
      gameOver = false;
      scoreEl.textContent = score;
      placeFood();
      if (timerId) clearInterval(timerId);
      timerId = setInterval(gameLoop, speedMs);
      draw(); // immediate draw
    }

    function placeFood() {
      // Place food not on the snake
      let valid = false;
      while (!valid) {
        food.x = Math.floor(Math.random() * tileCount);
        food.y = Math.floor(Math.random() * tileCount);
        valid = !snake.some(seg => seg.x === food.x && seg.y === food.y);
      }
    }

    function gameLoop() {
      if (gameOver) return;

      // Update direction from buffered input
      direction = nextDirection;

      // Compute new head
      const newHead = {
        x: snake[0].x + direction.x,
        y: snake[0].y + direction.y
      };

      // Check wall collision
      if (
        newHead.x < 0 || newHead.x >= tileCount ||
        newHead.y < 0 || newHead.y >= tileCount
      ) {
        endGame();
        return;
      }

      // Check self collision
      if (snake.some(seg => seg.x === newHead.x && seg.y === newHead.y)) {
        endGame();
        return;
      }

      // Move snake
      snake.unshift(newHead);

      // Check food
      if (newHead.x === food.x && newHead.y === food.y) {
        score += 10;
        scoreEl.textContent = score;
        placeFood();
        // Optional: speed up modestly
        if (speedMs > 60) {
          speedMs -= 5;
          clearInterval(timerId);
          timerId = setInterval(gameLoop, speedMs);
        }
      } else {
        snake.pop(); // remove tail if no food
      }

      draw();
    }

    function draw() {
      // Clear
      ctx.fillStyle = '#222';
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      // Draw grid (optional light grid)
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

      // Draw food
      ctx.fillStyle = '#e53935';
      drawCell(food.x, food.y);

      // Draw snake
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
        ctx.fillText('Press Restart to play again', canvas.width / 2, canvas.height / 2 + 20);
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

    // Controls
    window.addEventListener('keydown', (e) => {
      const key = e.key;
      // Prevent reversing directly into self
      if (key === 'ArrowUp' && direction.y !== 1) nextDirection = { x: 0, y: -1 };
      else if (key === 'ArrowDown' && direction.y !== -1) nextDirection = { x: 0, y: 1 };
      else if (key === 'ArrowLeft' && direction.x !== 1) nextDirection = { x: -1, y: 0 };
      else if (key === 'ArrowRight' && direction.x !== -1) nextDirection = { x: 1, y: 0 };
    });

    restartBtn.addEventListener('click', resetGame);

    // Start
    resetGame();
  </script>
</body>

