<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Blue Tic Tac Toe</title>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap" rel="stylesheet">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Poppins', sans-serif;
        }

        body {
            background: linear-gradient(135deg, #1e3c72, #2a5298);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            color: white;
        }

        .container {
            text-align: center;
            background-color: rgba(255, 255, 255, 0.1);
            padding: 2rem;
            border-radius: 15px;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
            max-width: 500px;
            width: 90%;
            backdrop-filter: blur(5px);
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        h1 {
            margin-bottom: 0.5rem;
            color: white;
            text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
        }

        .subtitle {
            margin-bottom: 1.5rem;
            color: rgba(255, 255, 255, 0.8);
            font-weight: 600;
        }

        .game-info {
            margin-bottom: 1.5rem;
        }

        #status {
            font-size: 1.2rem;
            margin-bottom: 0.5rem;
            font-weight: 600;
            color: white;
            text-shadow: 0 1px 2px rgba(0, 0, 0, 0.2);
        }

        .scores {
            display: flex;
            justify-content: center;
            gap: 2rem;
            margin-bottom: 1rem;
        }

        .scores span {
            background-color: rgba(255, 255, 255, 0.1);
            padding: 0.3rem 0.8rem;
            border-radius: 20px;
        }

        .board {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            grid-gap: 10px;
            margin: 1.5rem auto;
            max-width: 300px;
        }

        .cell {
            aspect-ratio: 1;
            background-color: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 3rem;
            cursor: pointer;
            transition: all 0.3s ease;
            border: 1px solid rgba(255, 255, 255, 0.1);
        }

        .cell:hover {
            background-color: rgba(255, 255, 255, 0.2);
            transform: translateY(-2px);
        }

        .cell.x {
            color: #ff9e9e;
            text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
        }

        .cell.o {
            color: #a0e7e5;
            text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
        }

        #reset-btn {
            background-color: rgba(255, 255, 255, 0.2);
            color: white;
            border: none;
            padding: 0.8rem 1.5rem;
            font-size: 1rem;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s ease;
            font-weight: 600;
            margin-top: 1rem;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        #reset-btn:hover {
            background-color: rgba(255, 255, 255, 0.3);
            transform: translateY(-2px);
        }

        .winning-cell {
            animation: pulse 1s infinite;
            background-color: rgba(46, 204, 255, 0.3);
        }

        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.05); }
            100% { transform: scale(1); }
        }

        @media (max-width: 480px) {
            .container {
                padding: 1.5rem;
            }
            
            h1 {
                font-size: 1.5rem;
            }
            
            .cell {
                font-size: 2.5rem;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🌊 Blue Tic Tac Toe</h1>
        <p class="subtitle">Player (O) vs Computer (X)</p>
        
        <div class="game-info">
            <div id="status">Your turn! Place an O</div>
            <div class="scores">
                <span id="player-score">Player: 0</span>
                <span id="computer-score">Computer: 0</span>
            </div>
        </div>
        
        <div class="board" id="board">
            <div class="cell" data-index="0"></div>
            <div class="cell" data-index="1"></div>
            <div class="cell" data-index="2"></div>
            <div class="cell" data-index="3"></div>
            <div class="cell" data-index="4"></div>
            <div class="cell" data-index="5"></div>
            <div class="cell" data-index="6"></div>
            <div class="cell" data-index="7"></div>
            <div class="cell" data-index="8"></div>
        </div>
        
        <button id="reset-btn">New Game</button>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const board = document.getElementById('board');
            const cells = document.querySelectorAll('.cell');
            const status = document.getElementById('status');
            const resetBtn = document.getElementById('reset-btn');
            const playerScoreEl = document.getElementById('player-score');
            const computerScoreEl = document.getElementById('computer-score');
            
            let boardState = ['', '', '', '', '', '', '', '', ''];
            let currentPlayer = 'o';
            let gameActive = true;
            let playerScore = 0;
            let computerScore = 0;
            
            const winningConditions = [
                [0, 1, 2], [3, 4, 5], [6, 7, 8],
                [0, 3, 6], [1, 4, 7], [2, 5, 8],
                [0, 4, 8], [2, 4, 6]
            ];
            
            function initGame() {
                boardState = ['', '', '', '', '', '', '', '', ''];
                currentPlayer = 'o';
                gameActive = true;
                status.textContent = 'Your turn! Place an O';
                
                cells.forEach(cell => {
                    cell.textContent = '';
                    cell.classList.remove('x', 'o', 'winning-cell');
                });
            }
            
            function handleCellClick(e) {
                const clickedCell = e.target;
                const clickedCellIndex = parseInt(clickedCell.getAttribute('data-index'));
                
                if (boardState[clickedCellIndex] !== '' || !gameActive) return;
                
                makeMove(clickedCell, clickedCellIndex, currentPlayer);
                
                if (checkWin()) {
                    status.textContent = 'You won! 🎉';
                    playerScore++;
                    playerScoreEl.textContent = `Player: ${playerScore}`;
                    gameActive = false;
                    highlightWinningCells();
                    return;
                }
                
                if (checkDraw()) {
                    status.textContent = 'Game ended in a draw!';
                    gameActive = false;
                    return;
                }
                
                currentPlayer = 'x';
                status.textContent = 'Computer is thinking...';
                setTimeout(computerMove, 800);
            }
            
            function makeMove(cell, index, player) {
                boardState[index] = player;
                cell.textContent = player.toUpperCase();
                cell.classList.add(player);
            }
            
            function computerMove() {
                if (!gameActive) return;
                
                let move = findWinningMove('x');
                if (move === -1) move = findWinningMove('o');
                if (move === -1) {
                    if (boardState[4] === '') move = 4;
                    else {
                        const availableCells = boardState.map((cell, index) => 
                            cell === '' ? index : null).filter(val => val !== null);
                        move = availableCells[Math.floor(Math.random() * availableCells.length)];
                    }
                }
                
                if (move !== undefined && move !== -1) {
                    makeMove(cells[move], move, 'x');
                    
                    if (checkWin()) {
                        status.textContent = 'Computer won! 😢';
                        computerScore++;
                        computerScoreEl.textContent = `Computer: ${computerScore}`;
                        gameActive = false;
                        highlightWinningCells();
                        return;
                    }
                    
                    if (checkDraw()) {
                        status.textContent = 'Game ended in a draw!';
                        gameActive = false;
                        return;
                    }
                    
                    currentPlayer = 'o';
                    status.textContent = 'Your turn! Place an O';
                }
            }
            
            function findWinningMove(player) {
                for (let condition of winningConditions) {
                    const [a, b, c] = condition;
                    if (boardState[a] === player && boardState[b] === player && boardState[c] === '') return c;
                    if (boardState[a] === player && boardState[c] === player && boardState[b] === '') return b;
                    if (boardState[b] === player && boardState[c] === player && boardState[a] === '') return a;
                }
                return -1;
            }
            
            function checkWin() {
                return winningConditions.some(condition => {
                    const [a, b, c] = condition;
                    return boardState[a] !== '' && 
                           boardState[a] === boardState[b] && 
                           boardState[a] === boardState[c];
                });
            }
            
            function checkDraw() {
                return boardState.every(cell => cell !== '');
            }
            
            function highlightWinningCells() {
                for (let condition of winningConditions) {
                    const [a, b, c] = condition;
                    if (boardState[a] !== '' && 
                        boardState[a] === boardState[b] && 
                        boardState[a] === boardState[c]) {
                        cells[a].classList.add('winning-cell');
                        cells[b].classList.add('winning-cell');
                        cells[c].classList.add('winning-cell');
                        break;
                    }
                }
            }
            
            cells.forEach(cell => cell.addEventListener('click', handleCellClick));
            resetBtn.addEventListener('click', initGame);
            
            initGame();
        });
    </script>
</body>
</html>
