<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Rompecabezas del Club</title>
  <style>
    body {
      font-family: sans-serif;
      text-align: center;
      background-color: #f4f4f4;
      padding: 20px;
    }
    h1 {
      color: #2b2b84;
    }
    #puzzle {
      width: 300px;
      height: 300px;
      margin: 0 auto;
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      grid-template-rows: repeat(3, 1fr);
      gap: 2px;
    }
    .piece {
      width: 100%;
      height: 100%;
      background-image: url('Logo%20gu%C3%ADas.jpeg');
      background-size: 300px 300px;
      cursor: pointer;
    }
    .hidden {
      background: #ccc;
      cursor: default;
    }
    #timer, #score {
      margin: 10px;
      font-size: 1.2em;
    }
    @media (max-width: 400px) {
      #puzzle {
        width: 90vw;
        height: 90vw;
      }
      .piece {
        background-size: 90vw 90vw;
      }
    }
  </style>
</head>
<body>
  <h1>Rompecabezas del Club</h1>
  <div id="timer">⏱ Tiempo: 0s</div>
  <div id="score">⭐ Puntos: 100</div>
  <div id="puzzle"></div>

  <audio id="moveSound" src="https://actions.google.com/sounds/v1/cartoon/wood_plank_flicks.ogg"></audio>
  <audio id="winSound" src="https://actions.google.com/sounds/v1/cartoon/concussive_hit_guitar_boing.ogg"></audio>

  <script>
    const puzzle = document.getElementById('puzzle');
    const timerEl = document.getElementById('timer');
    const scoreEl = document.getElementById('score');
    const moveSound = document.getElementById('moveSound');
    const winSound = document.getElementById('winSound');

    let pieces = [];
    let emptyIndex = 8;
    let time = 0;
    let score = 100;
    let timer;

    function updateTimer() {
      time++;
      score = Math.max(0, 100 - time);
      timerEl.textContent = `⏱ Tiempo: ${time}s`;
      scoreEl.textContent = `⭐ Puntos: ${score}`;
    }

    function shuffle(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
    }

    function isSolved() {
      return pieces.every((val, idx) => val === idx);
    }

    function initPuzzle() {
      puzzle.innerHTML = '';
      clearInterval(timer);
      time = 0;
      score = 100;
      timerEl.textContent = '⏱ Tiempo: 0s';
      scoreEl.textContent = '⭐ Puntos: 100';

      pieces = Array.from({ length: 9 }, (_, i) => i);
      shuffle(pieces);
      emptyIndex = pieces.indexOf(8);
      timer = setInterval(updateTimer, 1000);

      pieces.forEach((n, i) => {
        const div = document.createElement('div');
        div.className = 'piece';
        if (n === 8) div.classList.add('hidden');
        div.style.backgroundPosition = `-${(n % 3) * 100}px -${Math.floor(n / 3) * 100}px`;
        div.dataset.index = i;
        div.addEventListener('click', () => movePiece(i));
        puzzle.appendChild(div);
      });
    }

    function movePiece(i) {
      const [row, col] = [Math.floor(i / 3), i % 3];
      const [erow, ecol] = [Math.floor(emptyIndex / 3), emptyIndex % 3];

      const distance = Math.abs(row - erow) + Math.abs(col - ecol);
      if (distance === 1) {
        [pieces[i], pieces[emptyIndex]] = [pieces[emptyIndex], pieces[i]];
        emptyIndex = i;
        moveSound.play();
        initPuzzleFromCurrent();

        if (isSolved()) {
          clearInterval(timer);
          winSound.play();
          alert(`🎉 ¡Felicidades! Lo lograste en ${time} segundos con ${score} puntos.`);
        }
      }
    }

    function initPuzzleFromCurrent() {
      puzzle.innerHTML = '';
      pieces.forEach((n, i) => {
        const div = document.createElement('div');
        div.className = 'piece';
        if (n === 8) div.classList.add('hidden');
        div.style.backgroundPosition = `-${(n % 3) * 100}px -${Math.floor(n / 3) * 100}px`;
        div.dataset.index = i;
        div.addEventListener('click', () => movePiece(i));
        puzzle.appendChild(div);
      });
    }

    initPuzzle();
  </script>
</body>
</html>
