<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pac-Man Operator - เก็บกล่องสมบัติ Coding เพื่อเป็นสุดยอดโปรแกรมเมอร์</title>
    <style>
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-color: #2c3e50;
            color: #ecf0f1;
            font-family: 'Courier New', Courier, monospace;
            flex-direction: column;
            text-align: center;
        }
        #game-container {
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        #game-board {
            border: 4px solid #34495e;
            background-color: #1a232f;
            display: grid;
            box-shadow: 0 0 20px rgba(0, 0, 0, 0.5);
        }
        .cell {
            width: 30px;
            height: 30px;
            box-sizing: border-box;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        .wall {
            background-color: #34495e;
        }
        .path {
            background-color: #1a232f;
        }
        .player {
            background-color: #f1c40f;
            border-radius: 50%;
        }
        .monster {
            border-radius: 50%;
            animation: pulse 1s infinite alternate;
        }
        .treasure {
            font-size: 20px;
        }
        #ui {
            display: flex;
            width: 100%;
            justify-content: space-around;
            padding: 10px;
            font-size: 1.2rem;
            background-color: #34495e;
            border-radius: 8px;
            margin-bottom: 20px;
        }
        #lives-display {
            display: flex;
            align-items: center;
        }
        #lives-display span {
            margin-right: 5px;
        }
        .heart {
            color: red;
            font-size: 1.5rem;
        }
        #question-modal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.7);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 100;
        }
        #question-box {
            background-color: #2c3e50;
            border: 4px solid #f1c40f;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            box-shadow: 0 0 30px rgba(241, 196, 15, 0.5);
            width: 90%;
            max-width: 500px;
        }
        #question-box h2 {
            margin-top: 0;
        }
        #question-expression {
            background-color: #1a232f;
            padding: 10px;
            border-radius: 5px;
            font-size: 1.5rem;
            margin: 15px 0;
            color: #2ecc71;
        }
        .question-button {
            background-color: #e74c3c;
            color: white;
            border: none;
            padding: 10px 20px;
            font-size: 1.2rem;
            cursor: pointer;
            border-radius: 5px;
            margin: 0 10px;
        }
        .question-button.correct {
            background-color: #2ecc71;
        }
        #game-info {
            background-color: rgba(0, 0, 0, 0.8);
            color: white;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            display: none;
            z-index: 200;
        }
        #game-info h1, #game-info p {
            margin: 5px 0;
        }
        .hidden {
            display: none;
        }
        h1 {
            color: #f1c40f;
            margin-bottom: 5px;
        }
        p {
            margin-top: 0;
            color: #bdc3c7;
        }

        @keyframes pulse {
            from {
                transform: scale(0.9);
            }
            to {
                transform: scale(1.1);
            }
        }
    </style>
</head>
<body>

    <div id="game-info">
        <h1>Pac-Man Operator</h1>
        <p>เก็บกล่องสมบัติ Coding เพื่อเป็นสุดยอดโปรแกรมเมอร์</p>
        <p>ใช้ปุ่มลูกศรเพื่อควบคุม Pac-Man</p>
        <button onclick="startGame()" style="padding: 10px 20px; font-size: 1.2rem; cursor: pointer; background-color: #2ecc71; color: white; border: none; border-radius: 5px; margin-top: 10px;">เริ่มเกม</button>
    </div>

    <div id="game-container" class="hidden">
        <div id="ui">
            <div>คะแนน: <span id="score">0</span></div>
            <div id="lives-display">
                <span>ชีวิต:</span>
            </div>
            <div>กล่องสมบัติ: <span id="treasures-left">10</span>/10</div>
        </div>
        <div id="game-board"></div>
    </div>
    
    <div id="question-modal">
        <div id="question-box">
            <h2>คำถาม Coding</h2>
            <p>นิพจน์ต่อไปนี้มีค่าเป็นอะไร?</p>
            <div id="question-expression"></div>
            <button class="question-button" id="true-button">true</button>
            <button class="question-button" id="false-button">false</button>
        </div>
    </div>

    <audio id="collect-sound" src="https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3" preload="auto"></audio>
    <audio id="correct-sound" src="https://www.soundhelix.com/examples/mp3/SoundHelix-Song-2.mp3" preload="auto"></audio>
    <audio id="wrong-sound" src="https://www.soundhelix.com/examples/mp3/SoundHelix-Song-3.mp3" preload="auto"></audio>
    <audio id="hit-sound" src="https://www.soundhelix.com/examples/mp3/SoundHelix-Song-4.mp3" preload="auto"></audio>
    <audio id="win-sound" src="https://www.soundhelix.com/examples/mp3/SoundHelix-Song-5.mp3" preload="auto"></audio>
    <audio id="lose-sound" src="https://www.soundhelix.com/examples/mp3/SoundHelix-Song-6.mp3" preload="auto"></audio>

    <script>
        const COLS = 21;
        const ROWS = 21;
        const CELL_SIZE = 30;
        const TREASURES_TO_COLLECT = 10;
        const INITIAL_LIVES = 5;

        const board = document.getElementById('game-board');
        const scoreDisplay = document.getElementById('score');
        const livesDisplay = document.getElementById('lives-display');
        const treasuresLeftDisplay = document.getElementById('treasures-left');
        const questionModal = document.getElementById('question-modal');
        const questionExpression = document.getElementById('question-expression');
        const trueButton = document.getElementById('true-button');
        const falseButton = document.getElementById('false-button');
        const gameInfoScreen = document.getElementById('game-info');
        const gameContainer = document.getElementById('game-container');

        let player = { x: 1, y: 1 };
        let monsters = [];
        let treasures = [];
        let score = 0;
        let lives = INITIAL_LIVES;
        let treasuresCollected = 0;
        let isGamePaused = true;
        let monsterColors = ['red', 'cyan', 'magenta', 'lime', 'orange'];
        let boardMap = [];
        let isGameOver = false;

        const sounds = {
            collect: document.getElementById('collect-sound'),
            correct: document.getElementById('correct-sound'),
            wrong: document.getElementById('wrong-sound'),
            hit: document.getElementById('hit-sound'),
            win: document.getElementById('win-sound'),
            lose: document.getElementById('lose-sound'),
        };

        const questionBank = [
            { expr: '(5 % 2 == 1) and (10 > 3)', result: true },
            { expr: 'not (True and False)', result: true },
            { expr: '(3 * 4 == 12) or (7 % 3 != 1)', result: true },
            { expr: '(5 > 6) or (15 / 3 == 5)', result: true },
            { expr: '(10 >= 10) and not (3 < 2)', result: true },
            { expr: '(20 - 5) == (3 * 5) and (4 % 2 == 0)', result: true },
            { expr: '(10 == 10) and (2 * 2 != 4)', result: false },
            { expr: '(False or True) and (not True)', result: false },
            { expr: '5 + 5 == 10 and 10 / 2 != 5', result: false },
            { expr: '(10 > 5) or (10 < 5) and (3 == 3)', result: true },
            { expr: 'True and (not False) and (5 > 2)', result: true },
            { expr: '(5 * 5 == 25) and (3 + 3 != 6)', result: false },
            { expr: 'not (True or False)', result: false },
            { expr: '(100 % 10 != 0) or (5 - 5 == 0)', result: true },
            { expr: '1 + 1 == 2 and 2 + 2 == 5', result: false },
        ];

        function createMap() {
            boardMap = [];
            for (let i = 0; i < ROWS; i++) {
                boardMap[i] = [];
                for (let j = 0; j < COLS; j++) {
                    boardMap[i][j] = 'path';
                }
            }

            // Create maze-like walls
            function createWalls(x1, y1, x2, y2) {
                if (Math.abs(x2 - x1) < 2 || Math.abs(y2 - y1) < 2) return;
                let isVertical = Math.random() < 0.5;
                if (Math.abs(x2 - x1) > Math.abs(y2 - y1)) {
                    isVertical = false;
                } else if (Math.abs(y2 - y1) > Math.abs(x2 - x1)) {
                    isVertical = true;
                }

                if (isVertical) {
                    let wallX = Math.floor(Math.random() * (x2 - x1 - 1)) + x1 + 1;
                    for (let y = y1; y <= y2; y++) {
                        boardMap[y][wallX] = 'wall';
                    }
                    let passageY = Math.floor(Math.random() * (y2 - y1 + 1)) + y1;
                    boardMap[passageY][wallX] = 'path';

                    createWalls(x1, y1, wallX - 1, y2);
                    createWalls(wallX + 1, y1, x2, y2);
                } else {
                    let wallY = Math.floor(Math.random() * (y2 - y1 - 1)) + y1 + 1;
                    for (let x = x1; x <= x2; x++) {
                        boardMap[wallY][x] = 'wall';
                    }
                    let passageX = Math.floor(Math.random() * (x2 - x1 + 1)) + x1;
                    boardMap[wallY][passageX] = 'path';

                    createWalls(x1, y1, x2, wallY - 1);
                    createWalls(x1, wallY + 1, x2, y2);
                }
            }

            for (let i = 0; i < ROWS; i++) {
                boardMap[i][0] = 'wall';
                boardMap[i][COLS - 1] = 'wall';
            }
            for (let j = 0; j < COLS; j++) {
                boardMap[0][j] = 'wall';
                boardMap[ROWS - 1][j] = 'wall';
            }

            createWalls(1, 1, COLS - 2, ROWS - 2);

            // Ensure start and end points are clear
            boardMap[1][1] = 'path';
            boardMap[1][2] = 'path';
            boardMap[2][1] = 'path';

            // Clean up single walls
            for (let i = 1; i < ROWS - 1; i++) {
                for (let j = 1; j < COLS - 1; j++) {
                    if (boardMap[i][j] === 'wall') {
                        let neighbors = 0;
                        if (boardMap[i-1][j] === 'wall') neighbors++;
                        if (boardMap[i+1][j] === 'wall') neighbors++;
                        if (boardMap[i][j-1] === 'wall') neighbors++;
                        if (boardMap[i][j+1] === 'wall') neighbors++;
                        if (neighbors < 2) boardMap[i][j] = 'path';
                    }
                }
            }
        }

        function createMonsters(count) {
            for (let i = 0; i < count; i++) {
                let monsterPos = getRandomEmptyPosition();
                monsters.push({
                    x: monsterPos.x,
                    y: monsterPos.y,
                    color: monsterColors[i % monsterColors.length],
                    lastDirection: null,
                    nextMove: null
                });
            }
        }

        function spawnMonster() {
            let newMonsterPos = getRandomEmptyPosition();
            monsters.push({
                x: newMonsterPos.x,
                y: newMonsterPos.y,
                color: monsterColors[monsters.length % monsterColors.length],
                lastDirection: null,
                nextMove: null
            });
        }

        function placeTreasures() {
            treasures = [];
            for (let i = 0; i < TREASURES_TO_COLLECT; i++) {
                treasures.push(getRandomEmptyPosition());
            }
        }

        function getRandomEmptyPosition() {
            let x, y;
            do {
                x = Math.floor(Math.random() * (COLS - 2)) + 1;
                y = Math.floor(Math.random() * (ROWS - 2)) + 1;
            } while (boardMap[y][x] !== 'path' || isOccupied(x, y));
            return { x, y };
        }

        function isOccupied(x, y) {
            if (player.x === x && player.y === y) return true;
            for (const monster of monsters) {
                if (monster.x === x && monster.y === y) return true;
            }
            for (const treasure of treasures) {
                if (treasure.x === x && treasure.y === y) return true;
            }
            return false;
        }

        function drawBoard() {
            board.innerHTML = '';
            board.style.gridTemplateColumns = `repeat(${COLS}, ${CELL_SIZE}px)`;
            board.style.gridTemplateRows = `repeat(${ROWS}, ${CELL_SIZE}px)`;

            for (let y = 0; y < ROWS; y++) {
                for (let x = 0; x < COLS; x++) {
                    const cell = document.createElement('div');
                    cell.classList.add('cell');
                    cell.classList.add(boardMap[y][x]);
                    cell.id = `cell-${x}-${y}`;
                    board.appendChild(cell);
                }
            }
        }

        function updateBoard() {
            // Reset
            document.querySelectorAll('.player, .monster, .treasure').forEach(el => {
                if (el.className.includes('treasure')) {
                    el.innerHTML = '';
                }
                el.classList.remove('player', 'monster');
                el.style.backgroundColor = '';
            });

            // Draw player
            const playerCell = document.getElementById(`cell-${player.x}-${player.y}`);
            if (playerCell) playerCell.classList.add('player');

            // Draw monsters
            monsters.forEach(monster => {
                const monsterCell = document.getElementById(`cell-${monster.x}-${monster.y}`);
                if (monsterCell) {
                    monsterCell.classList.add('monster');
                    monsterCell.style.backgroundColor = monster.color;
                }
            });

            // Draw treasures
            treasures.forEach(treasure => {
                const treasureCell = document.getElementById(`cell-${treasure.x}-${treasure.y}`);
                if (treasureCell) {
                    treasureCell.classList.add('treasure');
                    treasureCell.innerHTML = '🎁';
                }
            });
        }

        function updateUI() {
            scoreDisplay.textContent = score;
            treasuresLeftDisplay.textContent = TREASURES_TO_COLLECT - treasuresCollected;
            livesDisplay.innerHTML = '<span>ชีวิต:</span>' + '❤️'.repeat(lives);
        }

        function movePlayer(dx, dy) {
            if (isGamePaused || isGameOver) return;

            let newX = player.x + dx;
            let newY = player.y + dy;

            if (boardMap[newY][newX] === 'path') {
                player.x = newX;
                player.y = newY;
                checkCollision();
                checkTreasureCollection();
                updateBoard();
            }
        }

        function moveMonsters() {
            if (isGamePaused || isGameOver) return;

            monsters.forEach(monster => {
                let { x, y, lastDirection } = monster;
                let possibleMoves = [];

                const moves = [
                    { dx: 0, dy: -1, dir: 'up' },
                    { dx: 0, dy: 1, dir: 'down' },
                    { dx: -1, dy: 0, dir: 'left' },
                    { dx: 1, dy: 0, dir: 'right' }
                ];

                for (const move of moves) {
                    let newX = x + move.dx;
                    let newY = y + move.dy;
                    if (boardMap[newY][newX] === 'path') {
                        if (lastDirection) {
                            // Don't move backwards unless it's a dead end
                            let isReverse = (move.dir === 'up' && lastDirection === 'down') ||
                                            (move.dir === 'down' && lastDirection === 'up') ||
                                            (move.dir === 'left' && lastDirection === 'right') ||
                                            (move.dir === 'right' && lastDirection === 'left');
                            let canGoForward = false;
                            for(const checkMove of moves) {
                                if (!((checkMove.dir === 'up' && move.dir === 'down') ||
                                      (checkMove.dir === 'down' && move.dir === 'up') ||
                                      (checkMove.dir === 'left' && move.dir === 'right') ||
                                      (checkMove.dir === 'right' && move.dir === 'left'))) {
                                    if (boardMap[y + checkMove.dy][x + checkMove.dx] === 'path') {
                                        canGoForward = true;
                                        break;
                                    }
                                }
                            }

                            if (!isReverse || !canGoForward) {
                                possibleMoves.push(move);
                            }
                        } else {
                            possibleMoves.push(move);
                        }
                    }
                }
                
                if (possibleMoves.length > 0) {
                    let randomMove = possibleMoves[Math.floor(Math.random() * possibleMoves.length)];
                    monster.x += randomMove.dx;
                    monster.y += randomMove.dy;
                    monster.lastDirection = randomMove.dir;
                }
            });

            checkCollision();
            updateBoard();
        }

        function checkCollision() {
            monsters.forEach(monster => {
                if (player.x === monster.x && player.y === monster.y) {
                    handlePlayerDeath();
                }
            });
        }

        function handlePlayerDeath() {
            if (isGameOver) return;
            sounds.hit.play();
            lives--;
            updateUI();
            if (lives <= 0) {
                endGame(false);
            } else {
                player.x = 1;
                player.y = 1;
                updateBoard();
            }
        }

        function checkTreasureCollection() {
            let treasureIndex = treasures.findIndex(t => t.x === player.x && t.y === player.y);
            if (treasureIndex !== -1) {
                sounds.collect.play();
                isGamePaused = true;
                treasures.splice(treasureIndex, 1);
                treasuresCollected++;
                updateUI();
                if (treasuresCollected >= TREASURES_TO_COLLECT) {
                    endGame(true);
                } else {
                    showQuestion();
                }
            }
        }

        function showQuestion() {
            let randomIndex = Math.floor(Math.random() * questionBank.length);
            let question = questionBank[randomIndex];
            questionExpression.textContent = question.expr;
            questionModal.style.display = 'flex';

            trueButton.onclick = () => handleAnswer(question.result === true);
            falseButton.onclick = () => handleAnswer(question.result === false);
        }

        function handleAnswer(isCorrect) {
            questionModal.style.display = 'none';
            if (isCorrect) {
                sounds.correct.play();
                score += 100;
                updateUI();
            } else {
                sounds.wrong.play();
                spawnMonster();
                updateBoard();
            }
            isGamePaused = false;
        }

        function gameLoop() {
            if (!isGamePaused && !isGameOver) {
                moveMonsters();
            }
            setTimeout(gameLoop, 500); // Monster speed
        }

        function endGame(isWin) {
            isGameOver = true;
            if (isWin) {
                sounds.win.play();
                gameInfoScreen.innerHTML = `<h1>คุณชนะ!</h1><p>คุณเป็นสุดยอดโปรแกรมเมอร์! 🎉</p><p>คะแนน: ${score}</p><button onclick="window.location.reload()">เล่นใหม่</button>`;
            } else {
                sounds.lose.play();
                gameInfoScreen.innerHTML = `<h1>เกมจบ!</h1><p>ชีวิตหมดแล้ว 😔</p><p>คะแนนสุดท้าย: ${score}</p><button onclick="window.location.reload()">เล่นใหม่</button>`;
            }
            gameInfoScreen.style.display = 'flex';
            gameContainer.classList.add('hidden');
        }
        
        function startGame() {
            gameInfoScreen.style.display = 'none';
            gameContainer.classList.remove('hidden');
            
            // Reset game state
            player = { x: 1, y: 1 };
            monsters = [];
            treasures = [];
            score = 0;
            lives = INITIAL_LIVES;
            treasuresCollected = 0;
            isGamePaused = false;
            isGameOver = false;

            createMap();
            drawBoard();
            createMonsters(3);
            placeTreasures();
            updateBoard();
            updateUI();
            gameLoop();
        }

        document.addEventListener('keydown', (e) => {
            if (isGamePaused || isGameOver) return;
            switch (e.key) {
                case 'ArrowUp':
                    movePlayer(0, -1);
                    break;
                case 'ArrowDown':
                    movePlayer(0, 1);
                    break;
                case 'ArrowLeft':
                    movePlayer(-1, 0);
                    break;
                case 'ArrowRight':
                    movePlayer(1, 0);
                    break;
            }
        });
        
        // Initial setup
        updateUI();
        gameInfoScreen.style.display = 'flex';

    </script>
</body>
</html>
