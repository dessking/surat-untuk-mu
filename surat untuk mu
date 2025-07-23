<!DOCTYPE html>
<html>
<head>
    <title>Catur Pro - Game Lengkap</title>
    <style>
        :root {
            --light-square: #f0d9b5;
            --dark-square: #b58863;
            --highlight: rgba(255, 255, 0, 0.4);
            --move-hint: rgba(0, 255, 0, 0.3);
            --capture-hint: rgba(255, 0, 0, 0.3);
        }
        
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            margin: 0;
            padding: 20px;
            color: white;
        }
        
        .game-container {
            background: rgba(0, 0, 0, 0.7);
            padding: 2rem;
            border-radius: 15px;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.5);
            backdrop-filter: blur(10px);
        }
        
        h1 {
            margin: 0 0 1rem;
            font-size: 2.5rem;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
            background: linear-gradient(to right, #fff, #ddd);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
        }
        
        #chessboard {
            width: min(80vw, 80vh);
            height: min(80vw, 80vh);
            display: grid;
            grid-template-columns: repeat(8, 1fr);
            grid-template-rows: repeat(8, 1fr);
            border: 3px solid #444;
            border-radius: 5px;
            overflow: hidden;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.5);
        }
        
        .square {
            position: relative;
            display: flex;
            justify-content: center;
            align-items: center;
            user-select: none;
        }
        
        .square.light {
            background-color: var(--light-square);
        }
        
        .square.dark {
            background-color: var(--dark-square);
        }
        
        .square.highlight {
            background-color: var(--highlight);
        }
        
        .square.move-hint {
            background-color: var(--move-hint);
        }
        
        .square.capture-hint {
            background-color: var(--capture-hint);
        }
        
        .piece {
            width: 85%;
            height: 85%;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: min(6vw, 6vh);
            cursor: grab;
            position: relative;
            z-index: 10;
            transition: transform 0.2s;
            text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.5);
        }
        
        .piece.dragging {
            transform: scale(1.1);
            z-index: 20;
            cursor: grabbing;
        }
        
        .piece.white {
            color: white;
            text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.8);
        }
        
        .piece.black {
            color: black;
            text-shadow: 1px 1px 3px rgba(255, 255, 255, 0.5);
        }
        
        .notation {
            position: absolute;
            font-size: 0.8rem;
            pointer-events: none;
        }
        
        .file {
            bottom: 2px;
            right: 4px;
        }
        
        .rank {
            top: 2px;
            left: 4px;
        }
        
        .controls {
            display: flex;
            gap: 1rem;
            margin-top: 1.5rem;
            flex-wrap: wrap;
            justify-content: center;
        }
        
        button {
            padding: 0.6rem 1.2rem;
            font-size: 1rem;
            background: linear-gradient(to right, #4e54c8, #8f94fb);
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            transition: all 0.3s;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
        }
        
        button:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
        }
        
        button:active {
            transform: translateY(0);
        }
        
        .status {
            margin-top: 1rem;
            font-size: 1.2rem;
            font-weight: bold;
            text-align: center;
            min-height: 2rem;
        }
        
        .move-history {
            background: rgba(0, 0, 0, 0.5);
            padding: 1rem;
            border-radius: 10px;
            max-height: 200px;
            overflow-y: auto;
            margin-top: 1rem;
        }
        
        /* Animations */
        @keyframes slideIn {
            from { transform: translateY(-20px); opacity: 0; }
            to { transform: translateY(0); opacity: 1; }
        }
        
        .piece.moving {
            transition: all 0.3s ease-out;
            z-index: 15;
        }
        
        .capture-animation {
            animation: capture 0.3s ease-out forwards;
        }
        
        @keyframes capture {
            to { transform: scale(0); opacity: 0; }
        }
        
        /* Promotion modal */
        .promotion-modal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.7);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 100;
        }
        
        .promotion-choices {
            background: #333;
            padding: 2rem;
            border-radius: 10px;
            display: flex;
            gap: 1rem;
        }
        
        .promotion-choice {
            font-size: 2rem;
            padding: 1rem;
            cursor: pointer;
            transition: all 0.2s;
            border-radius: 5px;
        }
        
        .promotion-choice:hover {
            background: #444;
            transform: scale(1.1);
        }
    </style>
</head>
<body>
    <div class="game-container">
        <h1>Chess Pro</h1>
        <div id="chessboard"></div>
        <div class="status" id="status">Giliran: Putih</div>
        <div class="controls">
            <button id="flip-board">Putar Papan</button>
            <button id="toggle-ai">Mode vs AI: OFF</button>
            <button id="new-game">Game Baru</button>
        </div>
        <div class="move-history" id="move-history"></div>
    </div>

    <script>
        // Konfigurasi Game
        const CHESS_PIECES = {
            'r': '♜', 'n': '♞', 'b': '♝', 'q': '♛', 'k': '♚', 'p': '♟',
            'R': '♖', 'N': '♘', 'B': '♗', 'Q': '♕', 'K': '♔', 'P': '♙'
        };

        const initialFEN = 'rnbqkbnr/pppppppp/8/8/8/8/PPPPPPPP/RNBQKBNR w KQkq - 0 1';

        // State Game
        let boardState = Array(8).fill().map(() => Array(8).fill(null));
        let currentPlayer = 'white';
        let gameActive = true;
        let aiEnabled = false;
        let boardFlipped = false;
        let selectedSquare = null;
        let validMoves = [];
        let moveHistory = [];
        let castlingRights = { white: { king: true, queen: true }, black: { king: true, queen: true } };
        let enPassantTarget = null;
        let halfMoveClock = 0;
        let fullMoveNumber = 1;

        // Elemen UI
        const chessboard = document.getElementById('chessboard');
        const statusDisplay = document.getElementById('status');
        const moveHistoryDisplay = document.getElementById('move-history');
        const flipBoardBtn = document.getElementById('flip-board');
        const toggleAIBtn = document.getElementById('toggle-ai');
        const newGameBtn = document.getElementById('new-game');

        // Inisialisasi Game
        function initGame() {
            parseFEN(initialFEN);
            renderBoard();
            updateStatus();
            updateMoveHistory();
        }

        // Parse FEN string
        function parseFEN(fen) {
            const parts = fen.split(' ');
            const boardPart = parts[0];
            const rows = boardPart.split('/');
            
            // Reset board
            boardState = Array(8).fill().map(() => Array(8).fill(null));
            
            // Parse posisi pion
            for (let row = 0; row < 8; row++) {
                let col = 0;
                for (const char of rows[row]) {
                    if (/\d/.test(char)) {
                        col += parseInt(char);
                    } else {
                        const pieceColor = char === char.toUpperCase() ? 'white' : 'black';
                        const pieceType = char.toLowerCase();
                        boardState[row][col] = {
                            type: pieceType,
                            color: pieceColor,
                            symbol: CHESS_PIECES[char]
                        };
                        col++;
                    }
                }
            }
            
            // Parse giliran
            currentPlayer = parts[1] === 'w' ? 'white' : 'black';
            
            // Parse hak rokade
            castlingRights = { white: { king: false, queen: false }, black: { king: false, queen: false } };
            if (parts[2].includes('K')) castlingRights.white.king = true;
            if (parts[2].includes('Q')) castlingRights.white.queen = true;
            if (parts[2].includes('k')) castlingRights.black.king = true;
            if (parts[2].includes('q')) castlingRights.black.queen = true;
            
            // Parse en passant
            enPassantTarget = parts[3] === '-' ? null : parts[3];
            
            // Parse halfmove clock dan fullmove number
            halfMoveClock = parseInt(parts[4]);
            fullMoveNumber = parseInt(parts[5]);
            
            gameActive = true;
        }

        // Render papan catur
        function renderBoard() {
            chessboard.innerHTML = '';
            
            for (let row = 0; row < 8; row++) {
                for (let col = 0; col < 8; col++) {
                    const displayRow = boardFlipped ? 7 - row : row;
                    const displayCol = boardFlipped ? 7 - col : col;
                    
                    const square = document.createElement('div');
                    square.className = (displayRow + displayCol) % 2 === 0 ? 'square light' : 'square dark';
                    square.dataset.row = row;
                    square.dataset.col = col;
                    
                    // Tambahkan notasi
                    if (displayRow === 7) {
                        const fileNotation = document.createElement('div');
                        fileNotation.className = 'notation file';
                        fileNotation.textContent = String.fromCharCode(97 + displayCol);
                        square.appendChild(fileNotation);
                    }
                    
                    if (displayCol === 0) {
                        const rankNotation = document.createElement('div');
                        rankNotation.className = 'notation rank';
                        rankNotation.textContent = 8 - displayRow;
                        square.appendChild(rankNotation);
                    }
                    
                    // Tambahkan pion jika ada
                    const piece = boardState[row][col];
                    if (piece) {
                        const pieceElement = document.createElement('div');
                        pieceElement.className = `piece ${piece.color}`;
                        pieceElement.textContent = piece.symbol;
                        pieceElement.dataset.row = row;
                        pieceElement.dataset.col = col;
                        pieceElement.draggable = currentPlayer === piece.color;
                        
                        pieceElement.addEventListener('dragstart', handleDragStart);
                        pieceElement.addEventListener('click', () => handleSquareClick(row, col));
                        
                        square.appendChild(pieceElement);
                    }
                    
                    square.addEventListener('dragover', handleDragOver);
                    square.addEventListener('dragenter', handleDragEnter);
                    square.addEventListener('dragleave', handleDragLeave);
                    square.addEventListener('drop', handleDrop);
                    square.addEventListener('click', () => handleSquareClick(row, col));
                    
                    chessboard.appendChild(square);
                }
            }
            
            // Highlight valid moves
            if (selectedSquare) {
                const [row, col] = selectedSquare;
                highlightSquare(row, col, 'highlight');
                
                for (const move of validMoves) {
                    const [destRow, destCol] = move;
                    const targetPiece = boardState[destRow][destCol];
                    
                    if (targetPiece) {
                        highlightSquare(destRow, destCol, 'capture-hint');
                    } else {
                        highlightSquare(destRow, destCol, 'move-hint');
                    }
                }
            }
        }

        // Highlight square
        function highlightSquare(row, col, className) {
            const displayRow = boardFlipped ? 7 - row : row;
            const displayCol = boardFlipped ? 7 - col : col;
            const square = chessboard.children[displayRow * 8 + displayCol];
            square.classList.add(className);
            
            // Auto remove highlight after some time
            setTimeout(() => {
                square.classList.remove(className);
            }, 3000);
        }

        // Handle drag and drop
        function handleDragStart(e) {
            if (!gameActive || (aiEnabled && currentPlayer === 'black')) return;
            
            const row = parseInt(e.target.dataset.row);
            const col = parseInt(e.target.dataset.col);
            selectedSquare = [row, col];
            validMoves = getValidMoves(row, col);
            
            e.dataTransfer.setData('text/plain', `${row},${col}`);
            setTimeout(() => {
                e.target.classList.add('dragging');
            }, 0);
        }

        function handleDragOver(e) {
            e.preventDefault();
        }

        function handleDragEnter(e) {
            e.preventDefault();
        }

        function handleDragLeave(e) {
        }

        function handleDrop(e) {
            e.preventDefault();
            if (!gameActive || (aiEnabled && currentPlayer === 'black')) return;
            
            const srcData = e.dataTransfer.getData('text/plain');
            const [srcRow, srcCol] = srcData.split(',').map(Number);
            const destRow = parseInt(e.target.closest('.square').dataset.row);
            const destCol = parseInt(e.target.closest('.square').dataset.col);
            
            // Validasi gerakan
            const isValid = validMoves.some(([r, c]) => r === destRow && c === destCol);
            if (isValid) {
                makeMove(srcRow, srcCol, destRow, destCol);
            }
            
            // Reset selection
            selectedSquare = null;
            validMoves = [];
            renderBoard();
        }

        // Handle square click
        function handleSquareClick(row, col) {
            if (!gameActive || (aiEnabled && currentPlayer === 'black')) return;
            
            const piece = boardState[row][col];
            
            // Jika sudah memilih pion dan klik kotak tujuan
            if (selectedSquare) {
                const [srcRow, srcCol] = selectedSquare;
                const isValid = validMoves.some(([r, c]) => r === row && c === col);
                
                if (isValid) {
                    makeMove(srcRow, srcCol, row, col);
                    selectedSquare = null;
                    validMoves = [];
                    renderBoard();
                    return;
                }
            }
            
            // Jika klik pion yang bisa digerakkan
            if (piece && piece.color === currentPlayer) {
                selectedSquare = [row, col];
                validMoves = getValidMoves(row, col);
                renderBoard();
            } else {
                selectedSquare = null;
                validMoves = [];
                renderBoard();
            }
        }

        // Dapatkan gerakan valid untuk pion tertentu
        function getValidMoves(row, col) {
            const piece = boardState[row][col];
            if (!piece || piece.color !== currentPlayer) return [];
            
            const moves = [];
            
            switch (piece.type) {
                case 'p': // Pion
                    const direction = piece.color === 'white' ? -1 : 1;
                    const startRow = piece.color === 'white' ? 6 : 1;
                    
                    // Gerakan maju
                    if (isInBoard(row + direction, col) && !boardState[row + direction][col]) {
                        moves.push([row + direction, col]);
                        
                        // Gerakan awal 2 langkah
                        if (row === startRow && !boardState[row + 2 * direction][col]) {
                            moves.push([row + 2 * direction, col]);
                        }
                    }
                    
                    // Makan diagonal
                    for (const dc of [-1, 1]) {
                        const newRow = row + direction;
                        const newCol = col + dc;
                        
                        if (isInBoard(newRow, newCol)) {
                            // Makan biasa
                            if (boardState[newRow][newCol] && boardState[newRow][newCol].color !== piece.color) {
                                moves.push([newRow, newCol]);
                            }
                            
                            // En passant
                            if (enPassantTarget && newRow === parseInt(enPassantTarget[1]) - 1 && 
                                String.fromCharCode(97 + newCol) === enPassantTarget[0]) {
                                moves.push([newRow, newCol]);
                            }
                        }
                    }
                    break;
                    
                case 'r': // Benteng
                    addStraightMoves(row, col, moves);
                    break;
                    
                case 'n': // Kuda
                    for (const [dr, dc] of [[-2,-1],[-2,1],[-1,-2],[-1,2],[1,-2],[1,2],[2,-1],[2,1]]) {
                        const newRow = row + dr;
                        const newCol = col + dc;
                        
                        if (isInBoard(newRow, newCol) && 
                            (!boardState[newRow][newCol] || boardState[newRow][newCol].color !== piece.color)) {
                            moves.push([newRow, newCol]);
                        }
                    }
                    break;
                    
                case 'b': // Gajah
                    addDiagonalMoves(row, col, moves);
                    break;
                    
                case 'q': // Menteri
                    addStraightMoves(row, col, moves);
                    addDiagonalMoves(row, col, moves);
                    break;
                    
                case 'k': // Raja
                    for (const [dr, dc] of [[-1,-1],[-1,0],[-1,1],[0,-1],[0,1],[1,-1],[1,0],[1,1]]) {
                        const newRow = row + dr;
                        const newCol = col + dc;
                        
                        if (isInBoard(newRow, newCol) && 
                     
