<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Snake Game</title>
  <style>
    
    body {
      margin: 0;
      padding: 0;
      background: transparent;
      font-family: Arial, sans-serif;
    }

    /* Center ONLY inside the content column */
    .snake-page {
      max-width: 800px;
      margin: 40px auto; /* centers in MkDocs content area */
      text-align: center;
    }

    canvas {
      background: #000;
      border: 4px solid #2ecc71;
      display: block;
      margin: 12px auto;
    }

    .info {
      margin-top: 8px;
      font-size: 18px;
    }

    .controls {
      font-size: 14px;
      color: #777;
    }
  </style>
</head>
<body>

  <div class="snake-page">
    <h1>🐍 Snake (IJKL)</h1>
    <div class="controls">Use I J K L to move · Press R to restart</div>

    <canvas id="game" width="400" height="400" tabindex="0"></canvas>
    <div class="controls">(Click the game once to focus)</div>

    <div class="info" id="score">Score: 0</div>
  </div>

  <script>
    const canvas = document.getElementById("game");
    const ctx = canvas.getContext("2d");

    const gridSize = 20;
    const tileCount = canvas.width / gridSize;

    let snake, food, dx, dy, score, gameOver;

    function resetGame() {
      snake = [{ x: 10, y: 10 }];
      dx = 1;
      dy = 0;
      score = 0;
      gameOver = false;
      spawnFood();
      updateScore();
    }

    function spawnFood() {
      food = {
        x: Math.floor(Math.random() * tileCount),
        y: Math.floor(Math.random() * tileCount)
      };
    }

    function updateScore() {
      document.getElementById("score").textContent = `Score: ${score}`;
    }

    function gameLoop() {
      if (gameOver) {
        ctx.fillStyle = "rgba(0,0,0,0.6)";
        ctx.fillRect(0, 0, canvas.width, canvas.height);
        ctx.fillStyle = "white";
        ctx.font = "30px Arial";
        ctx.fillText("Game Over", 110, 190);
        ctx.font = "16px Arial";
        ctx.fillText("Press R to restart", 115, 220);
        return;
      }

      update();
      draw();
    }

    function update() {
      const head = { x: snake[0].x + dx, y: snake[0].y + dy };

      if (
        head.x < 0 || head.y < 0 ||
        head.x >= tileCount || head.y >= tileCount
      ) {
        gameOver = true;
        return;
      }

      for (let part of snake) {
        if (head.x === part.x && head.y === part.y) {
          gameOver = true;
          return;
        }
      }

      snake.unshift(head);

      if (head.x === food.x && head.y === food.y) {
        score++;
        updateScore();
        spawnFood();
      } else {
        snake.pop();
      }
    }

    function draw() {
      ctx.fillStyle = "black";
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      ctx.fillStyle = "red";
      ctx.fillRect(food.x * gridSize, food.y * gridSize, gridSize, gridSize);

      ctx.fillStyle = "#2ecc71";
      for (let part of snake) {
        ctx.fillRect(part.x * gridSize, part.y * gridSize, gridSize, gridSize);
      }
    }

    /* WASD ONLY — PREVENT PAGE SCROLL */
    // Capture key events BEFORE MkDocs shortcuts
    window.addEventListener("keydown", e => {
      const key = e.key.toLowerCase();

      if (["i", "j", "k", "l", "r"].includes(key)) {
        e.preventDefault();
      }
      if (key === "i" && dy === 0) { dx = 0; dy = -1; }
      if (key === "k" && dy === 0) { dx = 0; dy = 1; }
      if (key === "j" && dx === 0) { dx = -1; dy = 0; }
      if (key === "l" && dx === 0) { dx = 1; dy = 0; }
      if (key === "r") resetGame();
    });

    resetGame();
    setInterval(gameLoop, 120);
  </script>

</body>
</html>
