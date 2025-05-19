<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Mines Game</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap');
        
        :root {
            --cell-size: clamp(40px, 15vw, 70px);
            --gap-size: clamp(5px, 2vw, 10px);
            --button-padding: clamp(8px, 3vw, 15px) clamp(15px, 5vw, 30px);
            --font-size: clamp(12px, 3vw, 16px);
            --promo-font-size: clamp(14px, 3.5vw, 18px);
            --bomb-font-size: clamp(20px, 6vw, 30px);
            --dot-size: clamp(8px, 3vw, 15px);
        }
        
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            -webkit-tap-highlight-color: transparent;
        }
        
        body {
            font-family: 'Press Start 2P', cursive;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            margin: 0;
            background: radial-gradient(circle, #0a0a2a, #00001a);
            color: white;
            overflow-x: hidden;
            padding: clamp(10px, 3vw, 20px);
            touch-action: manipulation;
        }

        .header {
            margin-bottom: clamp(15px, 5vw, 30px);
            text-align: center;
            width: 100%;
        }

        .promo-btn {
            padding: var(--button-padding);
            font-size: var(--promo-font-size);
            background: linear-gradient(145deg, #ff5722, #e64a19);
            color: white;
            border: none;
            border-radius: 50px;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 8px 15px rgba(0, 0, 0, 0.3);
            text-decoration: none;
            display: inline-block;
            animation: pulse 2s infinite;
            margin-bottom: clamp(10px, 3vw, 15px);
            width: auto;
            max-width: 100%;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }

        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.05); }
            100% { transform: scale(1); }
        }

        .game-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: clamp(15px, 5vw, 30px);
            width: 100%;
            max-width: calc(var(--cell-size) * 5 + var(--gap-size) * 4 + 20px);
        }

        .game-board {
            display: grid;
            grid-template-columns: repeat(5, var(--cell-size));
            grid-template-rows: repeat(5, var(--cell-size));
            gap: var(--gap-size);
            width: 100%;
        }

        .cell {
            width: 100%;
            height: 100%;
            border-radius: 12px;
            background: linear-gradient(145deg, #4fc3f7, #29b6f6);
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: var(--bomb-font-size);
            cursor: pointer;
            transition: all 0.3s ease;
            position: relative;
            overflow: hidden;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
            user-select: none;
        }

        .cell:hover, .cell:active {
            transform: scale(1.05);
            box-shadow: 0 6px 12px rgba(0, 0, 0, 0.3);
        }

        .cell.bomb {
            background: linear-gradient(145deg, #f44336, #b71c1c);
            animation: bombAppear 0.8s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            box-shadow: 0 0 20px #ff5252;
        }

        @keyframes bombAppear {
            0% { transform: scale(0) rotate(180deg); opacity: 0; }
            50% { transform: scale(1.2) rotate(-10deg); }
            70% { transform: scale(0.9) rotate(5deg); }
            100% { transform: scale(1) rotate(0deg); opacity: 1; }
        }

        .splash {
            position: absolute;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 1;
        }

        .dot {
            position: absolute;
            width: var(--dot-size);
            height: var(--dot-size);
            border-radius: 50%;
            background: #0d47a1;
            opacity: 0;
            animation: splash 1s ease-out forwards;
            will-change: transform, opacity;
        }

        @keyframes splash {
            0% {
                transform: translate(0, 0) scale(0);
                opacity: 1;
            }
            100% {
                transform: translate(var(--tx), var(--ty)) scale(1);
                opacity: 0;
            }
        }

        .controls {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: clamp(10px, 3vw, 20px);
            margin-top: clamp(10px, 3vw, 20px);
            width: 100%;
        }

        .count-buttons {
            display: flex;
            gap: clamp(5px, 2vw, 10px);
            flex-wrap: wrap;
            justify-content: center;
            width: 100%;
        }

        button {
            padding: var(--button-padding);
            font-size: var(--font-size);
            font-family: 'Press Start 2P', cursive;
            border: none;
            border-radius: 8px;
            background: linear-gradient(145deg, #2196f3, #0d47a1);
            color: white;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
            text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
            min-width: clamp(40px, 15vw, 60px);
            flex: 1 1 auto;
            user-select: none;
        }

        button:hover, button:active {
            transform: translateY(-3px);
            box-shadow: 0 6px 12px rgba(0, 0, 0, 0.4);
        }

        button:active {
            transform: translateY(1px);
        }

        .active {
            background: linear-gradient(145deg, #4caf50, #2e7d32);
            transform: scale(1.05);
        }

        .bomb-icon {
            animation: bombFloat 2s ease-in-out infinite;
            display: inline-block;
        }

        @keyframes bombFloat {
            0%, 100% { transform: translateY(0) rotate(0deg); }
            50% { transform: translateY(-5px) rotate(5deg); }
        }

        @media (orientation: portrait) and (max-height: 700px) {
            :root {
                --cell-size: clamp(35px, 12vh, 60px);
                --font-size: clamp(10px, 2.5vw, 14px);
            }
            
            .game-container {
                gap: clamp(10px, 3vh, 20px);
            }
        }

        @media (max-width: 400px) {
            :root {
                --cell-size: clamp(35px, 14vw, 50px);
                --font-size: clamp(10px, 3vw, 14px);
            }
            
            .count-buttons button {
                min-width: clamp(35px, 12vw, 50px);
                padding: clamp(5px, 2vw, 10px) clamp(8px, 3vw, 15px);
            }
        }
    </style>
</head>
<body>
    <div class="header">
        <a href="https://1wbfqv.life/v3/2158/1win-mines?p=12bz" class="promo-btn">GO 1WIN🚀</a>
    </div>

    <div class="game-container">
        <div class="game-board" id="gameBoard"></div>
        <div class="controls">
            <div class="count-buttons">
                <button id="count1" class="active">1</button>
                <button id="count3">3</button>
                <button id="count5">5</button>
                <button id="count7">7</button>
            </div>
            <button id="goButton">GO TO SIGNAL</button>
        </div>
    </div>

    <script>
        const boardSize = 5;
        let selectedBombCount = 1;
        let board = [];
        
        // Инициализация игрового поля
        function initializeBoard() {
            const gameBoard = document.getElementById('gameBoard');
            gameBoard.innerHTML = '';
            board = [];
            
            for (let i = 0; i < boardSize; i++) {
                board[i] = [];
                for (let j = 0; j < boardSize; j++) {
                    board[i][j] = { hasBomb: false };
                    
                    const cell = document.createElement('div');
                    cell.className = 'cell';
                    cell.dataset.row = i;
                    cell.dataset.col = j;
                    
                    // Обработчик для touch устройств
                    cell.addEventListener('click', handleCellClick);
                    cell.addEventListener('touchend', handleCellClick, { passive: true });
                    
                    gameBoard.appendChild(cell);
                }
            }
        }
        
        function handleCellClick(e) {
            e.preventDefault();
            const cell = e.currentTarget;
            const row = parseInt(cell.dataset.row);
            const col = parseInt(cell.dataset.col);
            
            if (!board[row][col].hasBomb) {
                createSplashEffect(cell);
            }
        }
        
        // Создание эффекта брызг
        function createSplashEffect(element) {
            const splash = document.createElement('div');
            splash.className = 'splash';
            
            // Оптимизация: меньше точек на мобильных
            const dotsCount = window.innerWidth < 500 ? 10 : 15;
            
            for (let i = 0; i < dotsCount; i++) {
                const dot = document.createElement('div');
                dot.className = 'dot';
                
                const angle = Math.random() * Math.PI * 2;
                const distance = 10 + Math.random() * 30;
                const tx = Math.cos(angle) * distance;
                const ty = Math.sin(angle) * distance;
                
                dot.style.setProperty('--tx', `${tx}px`);
                dot.style.setProperty('--ty', `${ty}px`);
                dot.style.left = '50%';
                dot.style.top = '50%';
                dot.style.backgroundColor = `hsl(${200 + Math.random() * 40}, 80%, 40%)`;
                
                splash.appendChild(dot);
            }
            
            element.appendChild(splash);
            
            // Автоматическая очистка
            setTimeout(() => {
                element.removeChild(splash);
            }, 1000);
        }
        
        // Размещение бомб случайным образом
        function placeBombs() {
            // Очищаем предыдущие бомбы
            const cells = document.querySelectorAll('.cell');
            cells.forEach(cell => {
                cell.classList.remove('bomb');
                cell.innerHTML = '';
            });
            
            // Сбрасываем состояние доски
            for (let i = 0; i < boardSize; i++) {
                for (let j = 0; j < boardSize; j++) {
                    board[i][j].hasBomb = false;
                }
            }
            
            // Размещаем новые бомбы
            let bombsPlaced = 0;
            const maxBombs = selectedBombCount;
            
            while (bombsPlaced < maxBombs) {
                const row = Math.floor(Math.random() * boardSize);
                const col = Math.floor(Math.random() * boardSize);
                
                if (!board[row][col].hasBomb) {
                    board[row][col].hasBomb = true;
                    const cell = document.querySelector(`.cell[data-row="${row}"][data-col="${col}"]`);
                    cell.classList.add('bomb');
                    cell.innerHTML = '<span class="bomb-icon">💣</span>';
                    bombsPlaced++;
                }
            }
        }
        
        // Инициализация кнопок выбора количества бомб
        function setupBombCountButtons() {
            const buttons = [
                document.getElementById('count1'),
                document.getElementById('count3'),
                document.getElementById('count5'),
                document.getElementById('count7')
            ];
            
            buttons.forEach(btn => {
                btn.addEventListener('click', function() {
                    selectedBombCount = parseInt(this.textContent);
                    updateActiveButton();
                });
                
                // Для touch устройств
                btn.addEventListener('touchend', function(e) {
                    e.preventDefault();
                    selectedBombCount = parseInt(this.textContent);
                    updateActiveButton();
                }, { passive: false });
            });
        }
        
        function updateActiveButton() {
            document.querySelectorAll('.count-buttons button').forEach(btn => {
                btn.classList.remove('active');
            });
            
            document.getElementById(`count${selectedBombCount}`).classList.add('active');
        }
        
        // Инициализация кнопки "GO TO SIGNAL"
        function setupGoButton() {
            const btn = document.getElementById('goButton');
            
            btn.addEventListener('click', placeBombs);
            btn.addEventListener('touchend', function(e) {
                e.preventDefault();
                placeBombs();
            }, { passive: false });
        }
        
        // Запуск игры
        function startGame() {
            initializeBoard();
            setupBombCountButtons();
            setupGoButton();
        }
        
        // Запускаем игру после полной загрузки
        if (document.readyState === 'complete') {
            startGame();
        } else {
            window.addEventListener('load', startGame);
        }
    </script>
</body>
</html>
