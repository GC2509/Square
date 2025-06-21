<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8">
  <title>Dodge the Blocks – Mobile</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <style>
    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      background: #111;
      color: white;
      font-family: sans-serif;
      text-align: center;
    }

    canvas {
      display: block;
      margin: 0 auto;
      background: #222;
      border: 3px solid #444;
    }

    #overlay {
      margin-top: 10px;
      font-size: 1rem;
    }

    #scores {
      margin-top: 5px;
      font-size: 1rem;
    }

    .controls {
      display: flex;
      justify-content: center;
      margin: 15px 0;
    }

    .btn {
      width: 100px;
      height: 60px;
      margin: 0 15px;
      font-size: 1.2rem;
      font-weight: bold;
      border: none;
      border-radius: 10px;
      background-color: #4CAF50;
      color: white;
      cursor: pointer;
    }

    .btn:active {
      background-color: #388e3c;
    }
  </style>
</head>
<body>

<canvas id="game" width="360" height="540"></canvas>

<div id="overlay">
  🟩 Tippe LINKS/RECHTS um auszuweichen!
  <div id="scores">
    🔢 Punkte: <span id="score">0</span> |
    🏆 Highscore: <span id="highscore">0</span>
  </div>
</div>

<div class="controls">
  <button class="btn" id="leftBtn">⬅️</button>
  <button class="btn" id="rightBtn">➡️</button>
</div>

<script>
  const canvas = document.getElementById('game');
  const ctx = canvas.getContext('2d');

  const scoreDisplay = document.getElementById("score");
  const highscoreDisplay = document.getElementById("highscore");

  let highscore = localStorage.getItem("highscore") || 0;
  highscoreDisplay.textContent = highscore;

  const spieler = {
    x: 160,
    y: 500,
    width: 40,
    height: 40,
    speed: 20
  };

  let hindernisse = [];
  let gameOver = false;
  let score = 0;
  let speed = 2;

  function zeichneSpieler() {
    ctx.fillStyle = '#2ecc71';
    ctx.fillRect(spieler.x, spieler.y, spieler.width, spieler.height);
  }

  function erstelleHindernis() {
    const breite = 40;
    const x = Math.floor(Math.random() * (canvas.width - breite));
    hindernisse.push({ x, y: -40, width: breite, height: 40 });
  }

  function zeichneHindernisse() {
    ctx.fillStyle = '#e74c3c';
    hindernisse.forEach(h => {
      ctx.fillRect(h.x, h.y, h.width, h.height);
    });
  }

  function bewegeHindernisse() {
    hindernisse.forEach(h => h.y += speed);
    hindernisse = hindernisse.filter(h => h.y < canvas.height);
  }

  function kollision() {
    return hindernisse.some(h =>
      spieler.x < h.x + h.width &&
      spieler.x + spieler.width > h.x &&
      spieler.y < h.y + h.height &&
      spieler.y + spieler.height > h.y
    );
  }

  function spielLoop() {
    if (gameOver) return;

    ctx.clearRect(0, 0, canvas.width, canvas.height);

    zeichneSpieler();
    bewegeHindernisse();
    zeichneHindernisse();

    if (kollision()) {
      gameOver = true;
      if (score > highscore) {
        localStorage.setItem("highscore", score);
        alert("🏆 Neuer Highscore: " + score + " Punkte!");
      } else {
        alert("💥 Game Over! Dein Score: " + score);
      }
      location.reload();
    }

    score++;
    scoreDisplay.textContent = score;

    if (score % 100 === 0) speed += 0.2;

    requestAnimationFrame(spielLoop);
  }

  function bewegeLinks() {
    if (spieler.x > 0) spieler.x -= spieler.speed;
  }

  function bewegeRechts() {
    if (spieler.x + spieler.width < canvas.width) spieler.x += spieler.speed;
  }

  // Pfeiltasten-Steuerung (optional für Desktop)
  document.addEventListener('keydown', e => {
    if (e.key === 'ArrowLeft') bewegeLinks();
    if (e.key === 'ArrowRight') bewegeRechts();
  });

  // Touch-Steuerung
  document.getElementById('leftBtn').addEventListener('click', bewegeLinks);
  document.getElementById('rightBtn').addEventListener('click', bewegeRechts);

  setInterval(erstelleHindernis, 800);
  spielLoop();
</script>

</body>
</html>
