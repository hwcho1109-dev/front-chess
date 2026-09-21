# front-chess
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>체스 (Chess)</title>
    <style>
        * { box-sizing: border-box; }
        
        body { 
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; 
            display: flex; 
            flex-direction: column; 
            align-items: center; 
            justify-content: center;
            height: 100vh;
            width: 100vw;
            background: radial-gradient(circle at center, #2c3e2e 0%, #171c18 60%, #0e120f 100%);
            background-attachment: fixed;
            color: #fff; 
            margin: 0; 
            padding: 0;
            overflow: hidden;
        }
        
        #home-screen { 
            display: flex; 
            flex-direction: column; 
            align-items: center; 
            justify-content: center; 
            height: 100%;
            width: 100%;
        }
        .menu-box { 
            background: rgba(45, 30, 24, 0.95); 
            padding: 3.5vh 3vw; 
            border-radius: 20px; 
            border: 3px solid #769656; 
            box-shadow: 0 15px 35px rgba(0,0,0,0.7), 0 0 20px rgba(118, 150, 86, 0.3); 
            display: flex; 
            flex-direction: column; 
            gap: 2.5vh; 
            width: min(420px, 90vw); 
            align-items: center; 
            backdrop-filter: blur(5px);
        }
        .menu-box label { font-size: max(18px, 2.6vh); font-weight: bold; }
        .menu-box select { width: 100%; padding: 1.5vh; font-size: max(16px, 2.2vh); font-weight: bold; border-radius: 8px; background: #1e1e1e; color: #fff; border: 2px solid #769656; }
        
        .home-rules-preview { background: rgba(24, 17, 14, 0.8); border: 2px dashed #769656; border-radius: 10px; padding: 1.8vh 1.5vw; width: 100%; font-size: max(14px, 1.9vh); line-height: 1.6; text-align: left; }
        .home-rules-preview div { margin-bottom: 0.6vh; }
        .home-rules-preview div:last-child { margin-bottom: 0; }
        
        .btn-main { width: 100%; padding: 1.8vh; font-size: max(18px, 2.5vh); font-weight: bold; background-color: #769656; color: white; border: none; border-radius: 8px; cursor: pointer; transition: 0.2s; box-shadow: 0 4px 10px rgba(0,0,0,0.3); }
        .btn-main:hover { background-color: #5c7743; transform: translateY(-2px); }

        #game-screen { 
            display: none; 
            flex-direction: column; 
            align-items: center; 
            justify-content: center;
            height: 100vh;
            width: 100vw;
            padding: 0.5vh 0;
        }
        #status { margin-bottom: 0.5vh; font-size: max(18px, 2.8vh); font-weight: bold; height: 3.5vh; text-shadow: 0 2px 4px rgba(0,0,0,0.8); display: flex; align-items: center; }
        
        .controls { display: flex; gap: 15px; margin-bottom: 0.6vh; }
        .btn-game { padding: 0.8vh 18px; font-size: max(14px, 2vh); font-weight: bold; border-radius: 8px; background: #3d2b1f; color: #fff; border: 2px solid #769656; display: flex; align-items: center; gap: 8px; cursor: pointer; transition: 0.2s; box-shadow: 0 4px 8px rgba(0,0,0,0.4); }
        .btn-game:hover { background: #5a402e; }

        :root {
            --sq-size: min(9vh, 11vw);
            --label-size: min(3vh, 3.5vw);
        }

        .captured-container { 
            width: calc(var(--sq-size) * 8 + var(--label-size) * 2); 
            height: 4vh; 
            background: rgba(45, 30, 24, 0.9); 
            border: 2px solid #3d2b1f; 
            border-radius: 8px; 
            padding: 2px 10px; 
            display: flex; 
            align-items: center; 
            gap: 4px; 
            margin-bottom: 0.5vh; 
            box-shadow: 0 4px 10px rgba(0,0,0,0.4); 
            overflow: hidden;
        }
        .captured-container img { height: 3vh; width: auto; filter: drop-shadow(0 1px 1px rgba(0,0,0,0.5)); }

        .board-container { display: flex; flex-direction: column; border: 4px solid #3d2b1f; box-shadow: 0 15px 35px rgba(0,0,0,0.8); position: relative; border-radius: 6px; overflow: hidden; }
        .row-container { display: flex; }
        
        .label { display: flex; justify-content: center; align-items: center; font-weight: bold; color: #d7ccc8; user-select: none; }

        .col-label { width: var(--sq-size); height: var(--label-size); background: #2d1e18; font-size: max(12px, 1.8vh); }
        .row-label { width: var(--label-size); height: var(--sq-size); background: #2d1e18; font-size: max(12px, 1.8vh); }
        .corner { width: var(--label-size); height: var(--label-size); background: #2d1e18; }

        #board { display: grid; grid-template-columns: repeat(8, var(--sq-size)); grid-template-rows: repeat(8, var(--sq-size)); position: relative; }
        .square { width: var(--sq-size); height: var(--sq-size); display: flex; justify-content: center; align-items: center; cursor: pointer; user-select: none; position: relative; }
        
        .white-sq { background-color: #eeeed2; }
        .black-sq { background-color: #769656; }
        
        .selected { background-color: #bbc12c !important; }
        .last-move-from { background-color: rgba(245, 246, 130, 0.6) !important; outline: 3px dashed #999; outline-offset: -3px; }
        .last-move-to { background-color: rgba(200, 205, 40, 0.8) !important; outline: 3px solid #555; outline-offset: -3px; }
        
        .highlight::after {
            content: ''; width: 32%; height: 32%; background: rgba(20, 85, 30, 0.6); border-radius: 50%; position: absolute;
        }

        .capturable {
            box-shadow: inset 0 0 0 4px #ff3333 !important;
            background-color: rgba(255, 51, 51, 0.35) !important;
        }

        .king-checked {
            background-color: #ff5500 !important;
            animation: pulse 1s infinite alternate;
        }

        .king-attacker {
            background-color: #cc0000 !important;
            box-shadow: inset 0 0 8px #fff;
        }

        @keyframes pulse {
            0% { box-shadow: inset 0 0 2px #fff; }
            100% { box-shadow: inset 0 0 14px #ff0000; }
        }

        .piece-img {
            width: 90%; height: 90%; pointer-events: none;
            filter: drop-shadow(0px 3px 3px rgba(0,0,0,0.4)); z-index: 2;
            transition: transform 0.3s cubic-bezier(0.25, 1, 0.5, 1);
        }

        .modal-overlay { display: none; position: fixed; top:0; left:0; width:100%; height:100%; background: rgba(0,0,0,0.75); justify-content: center; align-items: center; z-index: 100; backdrop-filter: blur(4px); }
        .modal-box { background: #2d1e18; padding: 3vh 2.5vw; border-radius: 16px; border: 3px solid #769656; display: flex; flex-direction: column; gap: 2vh; width: min(380px, 85vw); text-align: center; box-shadow: 0 10px 30px rgba(0,0,0,0.9); }
        .modal-box h3 { margin: 0; font-size: max(20px, 3vh); color: #769656; }

        .promotion-options { display: flex; justify-content: space-around; margin-top: 0.5vh; }
        .promotion-btn { background: #1e1e1e; border: 2px solid #769656; border-radius: 10px; cursor: pointer; padding: 6px; transition: 0.2s; }
        .promotion-btn:hover { background: #5c7743; transform: scale(1.1); }
        .promotion-btn img { width: max(40px, 6vh); height: max(40px, 6vh); }
    </style>
</head>
<body>

    <div id="home-screen">
        <div class="menu-box">
            <div style="width: 100%;">
                <label for="home-difficulty">AI 난이도 선택</label>
                <select id="home-difficulty" style="margin-top:1vh;">
                    <option value="easy">쉬움 (Random)</option>
                    <option value="medium" selected>보통 (Medium)</option>
                    <option value="hard">어려움 (Hard Alpha-Beta)</option>
                </select>
            </div>

            <div class="home-rules-preview">
                <div style="font-weight: bold; color: #ffeb3b; margin-bottom: 0.6vh; text-align: center; font-size: max(15px, 2.1vh);">📜 게임 룰 요약</div>
                <div>🟢 <b>폰 이동:</b> 전방 3방향 (직진/대각선 빈 칸)</div>
                <div>🔴 <b>폰 공격:</b> 전방 3방향 (직진/대각선 잡기)</div>
                <div style="color: #aaa; font-size: max(12px, 1.5vh); margin-top: 0.5vh; text-align: center;">* 첫 턴 대각선/직진 2칸 전진 허용 (경로 차단 시 이동 제한)</div>
            </div>

            <button class="btn-main" onclick="playAudio('click'); startGame();">게임 시작 ⚔️</button>
        </div>
    </div>

    <div id="promotion-modal" class="modal-overlay">
        <div class="modal-box" style="width: min(320px, 85vw);">
            <h3>👑 승급 기물 선택</h3>
            <p style="margin:0; font-size:15px; color:#ccc;">승급할 기물을 선택하세요.</p>
            <div class="promotion-options">
                <button class="promotion-btn" onclick="playAudio('click'); selectPromotion('Q')"><img id="prom-q" src="" alt="Queen"></button>
                <button class="promotion-btn" onclick="playAudio('click'); selectPromotion('R')"><img id="prom-r" src="" alt="Rook"></button>
                <button class="promotion-btn" onclick="playAudio('click'); selectPromotion('B')"><img id="prom-b" src="" alt="Bishop"></button>
                <button class="promotion-btn" onclick="playAudio('click'); selectPromotion('N')"><img id="prom-n" src="" alt="Knight"></button>
            </div>
            <button class="btn-main" style="background:#555; margin-top:0.8vh;" onclick="playAudio('click'); cancelPromotion();">취소</button>
        </div>
    </div>

    <div id="gameover-modal" class="modal-overlay">
        <div class="modal-box">
            <h3 id="gameover-title">🏆 게임 종료</h3>
            <p id="gameover-msg" style="font-size:17px; margin: 0.8vh 0; color:#fff;"></p>
            <button class="btn-main" onclick="playAudio('click'); restartGame();">다시하기</button>
            <button class="btn-main" style="background-color:#963d3d;" onclick="playAudio('click'); goHome();">홈으로</button>
        </div>
    </div>

    <div id="game-screen">
        <div id="status">당신의 차례입니다 (백색)</div>

        <div class="controls">
            <button class="btn-game" onclick="playAudio('click'); pauseGame();">⏸️ 일시정지</button>
            <button class="btn-game" onclick="playAudio('click'); undoMove();">↩️ 되돌리기</button>
        </div>

        <div id="captured-by-black" class="captured-container"></div>

        <div class="board-container">
            <div class="row-container">
                <div class="corner"></div>
                <div class="col-label label">A</div><div class="col-label label">B</div>
                <div class="col-label label">C</div><div class="col-label label">D</div>
                <div class="col-label label">E</div><div class="col-label label">F</div>
                <div class="col-label label">G</div><div class="col-label label">H</div>
                <div class="corner"></div>
            </div>
            
            <div class="row-container">
                <div id="left-labels" style="display:flex; flex-direction:column;"></div>
                <div id="board"></div>
                <div id="right-labels" style="display:flex; flex-direction:column;"></div>
            </div>

            <div class="row-container">
                <div class="corner"></div>
                <div class="col-label label">A</div><div class="col-label label">B</div>
                <div class="col-label label">C</div><div class="col-label label">D</div>
                <div class="col-label label">E</div><div class="col-label label">F</div>
                <div class="col-label label">G</div><div class="col-label label">H</div>
                <div class="corner"></div>
            </div>
        </div>

        <div id="captured-by-white" class="captured-container" style="margin-top:0.5vh; margin-bottom:0;"></div>
    </div>

    <div id="pause-modal" class="modal-overlay">
        <div class="modal-box" style="width: min(280px, 80vw);">
            <h3>⏸️ 일시정지</h3>
            <button class="btn-main" onclick="playAudio('click'); resumeGame();">계속하기</button>
            <button class="btn-main" onclick="playAudio('click'); restartGame();">다시하기</button>
            <button class="btn-main" style="background-color:#963d3d;" onclick="playAudio('click'); goHome();">홈으로</button>
        </div>
    </div>

<script>
let audioCtx = null;

function initAudio() {
    if (!audioCtx) {
        audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    }
}

function playAudio(type) {
    try {
        initAudio();
        if (audioCtx.state === 'suspended') {
            audioCtx.resume();
        }

        const now = audioCtx.currentTime;

        if (type === 'move') {
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.type = 'triangle';
            osc.frequency.setValueAtTime(150, now);
            osc.frequency.exponentialRampToValueAtTime(40, now + 0.12);
            gain.gain.setValueAtTime(0.4, now);
            gain.gain.exponentialRampToValueAtTime(0.01, now + 0.12);
            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.start(now);
            osc.stop(now + 0.12);
        } else if (type === 'capture') {
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.type = 'sawtooth';
            osc.frequency.setValueAtTime(320, now);
            osc.frequency.exponentialRampToValueAtTime(80, now + 0.15);
            gain.gain.setValueAtTime(0.6, now);
            gain.gain.exponentialRampToValueAtTime(0.01, now + 0.15);
            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.start(now);
            osc.stop(now + 0.15);
        } else if (type === 'check') {
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.type = 'sine';
            osc.frequency.setValueAtTime(587.33, now);
            osc.frequency.setValueAtTime(880, now + 0.1);
            gain.gain.setValueAtTime(0.3, now);
            gain.gain.exponentialRampToValueAtTime(0.01, now + 0.3);
            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.start(now);
            osc.stop(now + 0.3);
        } else if (type === 'click') {
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.type = 'sine';
            osc.frequency.setValueAtTime(400, now);
            gain.gain.setValueAtTime(0.2, now);
            gain.gain.exponentialRampToValueAtTime(0.01, now + 0.05);
            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.start(now);
            osc.stop(now + 0.05);
        }
    } catch(e) {}
}

const PIECE_IMAGES = {
    'P': 'https://upload.wikimedia.org/wikipedia/commons/thumb/4/45/Chess_plt45.svg/120px-Chess_plt45.svg.png',
    'R': 'https://upload.wikimedia.org/wikipedia/commons/thumb/7/72/Chess_rlt45.svg/120px-Chess_rlt45.svg.png',
    'N': 'https://upload.wikimedia.org/wikipedia/commons/thumb/7/70/Chess_nlt45.svg/120px-Chess_nlt45.svg.png',
    'B': 'https://upload.wikimedia.org/wikipedia/commons/thumb/b/b1/Chess_blt45.svg/120px-Chess_blt45.svg.png',
    'Q': 'https://upload.wikimedia.org/wikipedia/commons/thumb/1/15/Chess_qlt45.svg/120px-Chess_qlt45.svg.png',
    'K': 'https://upload.wikimedia.org/wikipedia/commons/thumb/4/42/Chess_klt45.svg/120px-Chess_klt45.svg.png',
    
    'p': 'https://upload.wikimedia.org/wikipedia/commons/thumb/c/c7/Chess_pdt45.svg/120px-Chess_pdt45.svg.png',
    'r': 'https://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Chess_rdt45.svg/120px-Chess_rdt45.svg.png',
    'n': 'https://upload.wikimedia.org/wikipedia/commons/thumb/e/ef/Chess_ndt45.svg/120px-Chess_ndt45.svg.png',
    'b': 'https://upload.wikimedia.org/wikipedia/commons/thumb/9/98/Chess_bdt45.svg/120px-Chess_bdt45.svg.png',
    'q': 'https://upload.wikimedia.org/wikipedia/commons/thumb/4/47/Chess_qdt45.svg/120px-Chess_qdt45.svg.png',
    'k': 'https://upload.wikimedia.org/wikipedia/commons/thumb/f/f0/Chess_kdt45.svg/120px-Chess_kdt45.svg.png'
};

const VALUES = { 'p': 100, 'n': 320, 'b': 330, 'r': 500, 'q': 900, 'k': 20000 };

const pawnPST_Black = [
    [ 0,  0,  0,  0,  0,  0,  0,  0],
    [50, 50, 50, 50, 50, 50, 50, 50],
    [20, 25, 30, 35, 35, 30, 25, 20],
    [10, 15, 25, 30, 30, 25, 15, 10],
    [ 5,  5, 20, 25, 25, 20,  5,  5],
    [ 0,  0, 10, 15, 15, 10,  0,  0],
    [ 5, 10, 10,-20,-20, 10, 10,  5],
    [ 0,  0,  0,  0,  0,  0,  0,  0]
];

const knightPST = [
    [-50,-40,-30,-30,-30,-30,-40,-50],
    [-40,-20,  0,  0,  0,  0,-20,-40],
    [-30,  0, 10, 15, 15, 10,  0,-30],
    [-30,  5, 15, 20, 20, 15,  5,-30],
    [-30,  0, 15, 20, 20, 15,  0,-30],
    [-30,  5, 10, 15, 15, 10,  5,-30],
    [-40,-20,  0,  5,  5,  0,-20,-40],
    [-50,-40,-30,-30,-30,-30,-40,-50]
];

let board = [];
let selectedSquare = null;
let validMoves = [];
let turn = 'w';
let lastMove = null;
let gameOver = false;
let isAnimating = false;
let history = [];
let pendingMove = null; 
let capturedByWhite = [];
let capturedByBlack = [];

function startGame() {
    initAudio();
    if (audioCtx && audioCtx.state === 'suspended') {
        audioCtx.resume();
    }
    document.getElementById('home-screen').style.display = 'none';
    document.getElementById('game-screen').style.display = 'flex';
    resetGameData();
}

function goHome() {
    document.getElementById('pause-modal').style.display = 'none';
    document.getElementById('gameover-modal').style.display = 'none';
    document.getElementById('game-screen').style.display = 'none';
    document.getElementById('home-screen').style.display = 'flex';
}

function pauseGame() {
    if(!gameOver && turn === 'w' && !isAnimating && !pendingMove) {
        document.getElementById('pause-modal').style.display = 'flex';
    }
}

function resumeGame() {
    document.getElementById('pause-modal').style.display = 'none';
}

function restartGame() {
    document.getElementById('pause-modal').style.display = 'none';
    document.getElementById('gameover-modal').style.display = 'none';
    resetGameData();
}

function resetGameData() {
    board = [
        ['r','n','b','q','k','b','n','r'],
        ['p','p','p','p','p','p','p','p'],
        ['.','.','.','.','.','.','.','.'],
        ['.','.','.','.','.','.','.','.'],
        ['.','.','.','.','.','.','.','.'],
        ['.','.','.','.','.','.','.','.'],
        ['P','P','P','P','P','P','P','P'],
        ['R','N','B','Q','K','B','N','R']
    ];
    selectedSquare = null;
    validMoves = [];
    turn = 'w';
    lastMove = null;
    gameOver = false;
    isAnimating = false;
    history = [];
    pendingMove = null;
    capturedByWhite = [];
    capturedByBlack = [];
    document.getElementById('status').innerText = '당신의 차례입니다 (백색)';
    setupLabels();
    renderBoard();
    renderCaptured();
}

function saveState() {
    history.push({
        board: board.map(row => [...row]),
        turn: turn,
        lastMove: lastMove ? JSON.parse(JSON.stringify(lastMove)) : null,
        gameOver: gameOver,
        capturedByWhite: [...capturedByWhite],
        capturedByBlack: [...capturedByBlack]
    });
}

function undoMove() {
    if (turn !== 'w' || history.length < 2 || gameOver || isAnimating || pendingMove) return;
    
    history.pop();
    let prevState = history.pop();
    
    board = prevState.board;
    turn = prevState.turn;
    lastMove = prevState.lastMove;
    gameOver = prevState.gameOver;
    capturedByWhite = prevState.capturedByWhite;
    capturedByBlack = prevState.capturedByBlack;
    selectedSquare = null;
    validMoves = [];

    let myCheck = getCheckStatus('w', board);
    document.getElementById('status').innerText = myCheck.isCheck ? '⚠️ 체크 당했습니다! 킹을 지키세요.' : '당신의 차례입니다 (백색)';
    renderBoard();
    renderCaptured();
}

function setupLabels() {
    const left = document.getElementById('left-labels');
    const right = document.getElementById('right-labels');
    left.innerHTML = ''; right.innerHTML = '';
    for (let i = 8; i >= 1; i--) {
        const l1 = document.createElement('div'); l1.className = 'row-label label'; l1.innerText = i;
        const l2 = document.createElement('div'); l2.className = 'row-label label'; l2.innerText = i;
        left.appendChild(l1); right.appendChild(l2);
    }
}

function renderCaptured() {
    const wDiv = document.getElementById('captured-by-white');
    const bDiv = document.getElementById('captured-by-black');
    wDiv.innerHTML = ''; bDiv.innerHTML = '';

    capturedByWhite.forEach(p => {
        const img = document.createElement('img');
        img.src = PIECE_IMAGES[p];
        wDiv.appendChild(img);
    });

    capturedByBlack.forEach(p => {
        const img = document.createElement('img');
        img.src = PIECE_IMAGES[p];
        bDiv.appendChild(img);
    });
}

function getCheckStatus(targetColor, currentBoard) {
    let kingChar = targetColor === 'w' ? 'K' : 'k';
    let kingPos = null;

    for (let r = 0; r < 8; r++) {
        for (let c = 0; c < 8; c++) {
            if (currentBoard[r][c] === kingChar) {
                kingPos = { r, c };
                break;
            }
        }
    }

    if (!kingPos) return { isCheck: false, kingPos: null, attackers: [] };

    let attackers = [];
    for (let r = 0; r < 8; r++) {
        for (let c = 0; c < 8; c++) {
            let piece = currentBoard[r][c];
            if (piece !== '.' && (targetColor === 'w' ? isBlack(piece) : isWhite(piece))) {
                let moves = getPseudoMoves(r, c, currentBoard);
                if (moves.some(m => m.r === kingPos.r && m.c === kingPos.c)) {
                    attackers.push({ r, c });
                }
            }
        }
    }

    return { isCheck: attackers.length > 0, kingPos, attackers };
}

function getLegalMoves(r, c, b) {
    const pseudoMoves = getPseudoMoves(r, c, b);
    const legalMoves = [];
    const color = isWhite(b[r][c]) ? 'w' : 'b';

    for (let m of pseudoMoves) {
        let tempBoard = b.map(row => [...row]);
        let p = tempBoard[r][c];
        tempBoard[r][c] = '.';
        if (p === 'P' && m.r === 0) p = 'Q';
        if (p === 'p' && m.r === 7) p = 'q';
        tempBoard[m.r][m.c] = p;

        if (!getCheckStatus(color, tempBoard).isCheck) {
            legalMoves.push(m);
        }
    }
    return legalMoves;
}

function getAllLegalMoves(color, b) {
    let allMoves = [];
    for (let r = 0; r < 8; r++) {
        for (let c = 0; c < 8; c++) {
            let p = b[r][c];
            if (p !== '.' && (color === 'w' ? isWhite(p) : isBlack(p))) {
                let moves = getLegalMoves(r, c, b);
                for (let m of moves) {
                    allMoves.push({ from: { r, c }, to: m });
                }
            }
        }
    }
    return allMoves;
}

function renderBoard() {
    const boardDiv = document.getElementById('board');
    boardDiv.innerHTML = '';

    const checkInfo = getCheckStatus(turn, board);

    for (let r = 0; r < 8; r++) {
        for (let c = 0; c < 8; c++) {
            const sq = document.createElement('div');
            sq.className = `square ${(r + c) % 2 === 0 ? 'white-sq' : 'black-sq'}`;
            sq.dataset.r = r;
            sq.dataset.c = c;
            
            if (lastMove) {
                if (lastMove.from.r === r && lastMove.from.c === c) sq.classList.add('last-move-from');
                if (lastMove.to.r === r && lastMove.to.c === c) sq.classList.add('last-move-to');
            }

            if (checkInfo.isCheck) {
                if (checkInfo.kingPos && checkInfo.kingPos.r === r && checkInfo.kingPos.c === c) {
                    sq.classList.add('king-checked');
                }
                if (checkInfo.attackers.some(a => a.r === r && a.c === c)) {
                    sq.classList.add('king-attacker');
                }
            }

            const p = board[r][c];
            if (p !== '.') {
                const img = document.createElement('img');
                img.src = PIECE_IMAGES[p];
                img.className = 'piece-img';
                sq.appendChild(img);
            }
            
            if (selectedSquare && selectedSquare.r === r && selectedSquare.c === c) {
                sq.classList.add('selected');
            }

            if (validMoves.some(m => m.r === r && m.c === c)) {
                if (p !== '.') {
                    sq.classList.add('capturable');
                } else {
                    sq.classList.add('highlight');
                }
            }
            
            sq.addEventListener('click', () => handleSquareClick(r, c));
            boardDiv.appendChild(sq);
        }
    }
}

function animateAndMove(from, to, promoPiece = 'Q', callback) {
    isAnimating = true;
    const fromIndex = from.r * 8 + from.c;
    const boardDiv = document.getElementById('board');
    const fromSq = boardDiv.children[fromIndex];
    const pieceImg = fromSq ? fromSq.querySelector('.piece-img') : null;

    const isCapture = board[to.r][to.c] !== '.';

    if (pieceImg) {
        const sqSize = fromSq.getBoundingClientRect().width;
        const deltaX = (to.c - from.c) * sqSize;
        const deltaY = (to.r - from.r) * sqSize;
        
        pieceImg.style.transform = `translate(${deltaX}px, ${deltaY}px)`;
        
        if (isCapture) playAudio('capture');
        else playAudio('move');

        setTimeout(() => {
            makeMove(from, to, promoPiece);
            isAnimating = false;
            if (callback) callback();
        }, 300);
    } else {
        if (isCapture) playAudio('capture');
        else playAudio('move');
        
        makeMove(from, to, promoPiece);
        isAnimating = false;
        if (callback) callback();
    }
}

function handleSquareClick(r, c) {
    if (turn !== 'w' || gameOver || isAnimating || pendingMove) return;

    if (selectedSquare) {
        const move = validMoves.find(m => m.r === r && m.c === c);
        if (move) {
            let piece = board[selectedSquare.r][selectedSquare.c];
            
            if (piece === 'P' && move.r === 0) {
                pendingMove = { from: selectedSquare, to: move };
                openPromotionModal('w');
                return;
            }

            saveState();
            let from = selectedSquare;
            selectedSquare = null;
            validMoves = [];
            
            animateAndMove(from, move, 'Q', () => {
                completePlayerTurn();
            });
            return;
        }
    }

    const piece = board[r][c];
    if (piece !== '.' && isWhite(piece)) {
        selectedSquare = { r, c };
        validMoves = getLegalMoves(r, c, board);
        playAudio('click');
        renderBoard();
    } else {
        selectedSquare = null;
        validMoves = [];
        renderBoard();
    }
}

function openPromotionModal(color) {
    document.getElementById('prom-q').src = PIECE_IMAGES[color === 'w' ? 'Q' : 'q'];
    document.getElementById('prom-r').src = PIECE_IMAGES[color === 'w' ? 'R' : 'r'];
    document.getElementById('prom-b').src = PIECE_IMAGES[color === 'w' ? 'B' : 'b'];
    document.getElementById('prom-n').src = PIECE_IMAGES[color === 'w' ? 'N' : 'n'];
    document.getElementById('promotion-modal').style.display = 'flex';
}

function cancelPromotion() {
    document.getElementById('promotion-modal').style.display = 'none';
    pendingMove = null;
    selectedSquare = null;
    validMoves = [];
    renderBoard();
}

function selectPromotion(pieceType) {
    document.getElementById('promotion-modal').style.display = 'none';
    if (pendingMove) {
        saveState();
        let from = pendingMove.from;
        let to = pendingMove.to;
        pendingMove = null;

        animateAndMove(from, to, pieceType, () => {
            completePlayerTurn();
        });
    }
}

function completePlayerTurn() {
    selectedSquare = null;
    validMoves = [];
    renderCaptured();

    if (checkGameOver('b')) return;

    turn = 'b';
    let nextCheck = getCheckStatus('b', board);
    if (nextCheck.isCheck) playAudio('check');

    document.getElementById('status').innerText = nextCheck.isCheck ? '⚠️ AI 킹 체크! AI 생각 중...' : 'AI가 생각 중입니다...';
    renderBoard();

    setTimeout(() => {
        requestAnimationFrame(() => {
            aiTurn();
        });
    }, 50);
}

function checkGameOver(currentTurn) {
    let allMoves = getAllLegalMoves(currentTurn, board);
    if (allMoves.length === 0) {
        gameOver = true;
        let isCheck = getCheckStatus(currentTurn, board).isCheck;
        let title = "🎮 게임 종료";
        let msg = "";

        if (isCheck) {
            playAudio('check');
            if (currentTurn === 'w') {
                title = "💀 패배...";
                msg = "AI(흑색)가 체크메이트로 승리했습니다.";
            } else {
                title = "🏆 승리!";
                msg = "축하합니다! AI(흑색)를 체크메이트했습니다.";
            }
        } else {
            title = "🤝 무승부";
            msg = "더 이상 둘 수 있는 수가 없습니다 (스테일메이트).";
        }

        document.getElementById('gameover-title').innerText = title;
        document.getElementById('gameover-msg').innerText = msg;
        document.getElementById('gameover-modal').style.display = 'flex';
        renderBoard();
        return true;
    }
    return false;
}

function isWhite(p) { return p !== '.' && p === p.toUpperCase(); }
function isBlack(p) { return p !== '.' && p === p.toLowerCase(); }

function getPseudoMoves(r, c, b) {
    const p = b[r][c];
    const moves = [];
    const dir = isWhite(p) ? -1 : 1;
    const isPawn = p.toLowerCase() === 'p';

    if (isPawn) {
        for (let dc of [-1, 0, 1]) {
            let nr = r + dir, nc = c + dc;
            
            if (inBounds(nr, nc)) {
                let target = b[nr][nc];
                
                if (target === '.' || (isWhite(p) ? isBlack(target) : isWhite(target))) {
                    moves.push({ r: nr, c: nc });
                    
                    const startRow = isWhite(p) ? 6 : 1;
                    if (r === startRow) {
                        let nnr = r + dir * 2;
                        let nnc = c + dc * 2;
                        
                        if (target === '.' && inBounds(nnr, nnc)) {
                            let doubleTarget = b[nnr][nnc];
                            if (doubleTarget === '.' || (isWhite(p) ? isBlack(doubleTarget) : isWhite(doubleTarget))) {
                                moves.push({ r: nnr, c: nnc });
                            }
                        }
                    }
                }
            }
        }
    } else {
        const type = p.toLowerCase();
        let dirs = [];
        if (type === 'n') dirs = [[-2,-1],[-2,1],[-1,-2],[-1,2],[1,-2],[1,2],[2,-1],[2,1]];
        else if (type === 'b') dirs = [[-1,-1],[-1,1],[1,-1],[1,1]];
        else if (type === 'r') dirs = [[-1,0],[1,0],[0,-1],[0,1]];
        else if (type === 'q' || type === 'k') dirs = [[-1,-1],[-1,1],[1,-1],[1,1],[-1,0],[1,0],[0,-1],[0,1]];

        for (let [dr, dc] of dirs) {
            let nr = r + dr, nc = c + dc;
            while (inBounds(nr, nc)) {
                if (b[nr][nc] === '.') {
                    moves.push({ r: nr, c: nc });
                    if (type === 'n' || type === 'k') break;
                } else {
                    if (isWhite(p) ? isBlack(b[nr][nc]) : isWhite(b[nr][nc])) {
                        moves.push({ r: nr, c: nc });
                    }
                    break;
                }
                nr += dr; nc += dc;
            }
        }
    }
    return moves;
}

function inBounds(r, c) { return r >= 0 && r < 8 && c >= 0 && c < 8; }

function makeMove(from, to, promoPiece = 'Q') {
    let p = board[from.r][from.c];
    let target = board[to.r][to.c];

    if (target !== '.') {
        if (isWhite(p)) capturedByWhite.push(target);
        else capturedByBlack.push(target);
    }

    board[from.r][from.c] = '.';
    
    if (p === 'P' && to.r === 0) p = promoPiece.toUpperCase();
    if (p === 'p' && to.r === 7) p = promoPiece.toLowerCase();
    
    board[to.r][to.c] = p;
    lastMove = { from: { r: from.r, c: from.c }, to: { r: to.r, c: to.c } };
}

function aiTurn() {
    saveState();
    const diff = document.getElementById('home-difficulty').value;
    const moves = getAllLegalMoves('b', board);
    if (moves.length === 0) return;

    let bestMove = null;

    if (diff === 'easy') {
        bestMove = moves[Math.floor(Math.random() * moves.length)];
    } else if (diff === 'medium') {
        let bestScore = -Infinity;
        for (let m of moves) {
            let tempBoard = simMove(board, m);
            let score = evaluateBoard(tempBoard);
            if (score > bestScore) {
                bestScore = score;
                bestMove = m;
            }
        }
    } else if (diff === 'hard') {
        moves.sort((a, b) => {
            let capA = board[a.to.r][a.to.c] !== '.' ? VALUES[board[a.to.r][a.to.c].toLowerCase()] : 0;
            let capB = board[b.to.r][b.to.c] !== '.' ? VALUES[board[b.to.r][b.to.c].toLowerCase()] : 0;
            return capB - capA;
        });

        let bestScore = -Infinity;
        let alpha = -Infinity;
        let beta = Infinity;

        for (let m of moves) {
            let tempBoard = simMove(board, m);
            let score = minimaxAlphaBeta(tempBoard, 3, alpha, beta, false);
            if (score > bestScore) {
                bestScore = score;
                bestMove = m;
            }
            alpha = Math.max(alpha, bestScore);
        }
    }

    if (bestMove) {
        animateAndMove(bestMove.from, bestMove.to, 'Q', () => {
            renderCaptured();
            if (checkGameOver('w')) return;

            turn = 'w';
            let myCheck = getCheckStatus('w', board);
            if (myCheck.isCheck) playAudio('check');
            
            document.getElementById('status').innerText = myCheck.isCheck ? '⚠️ 체크 당했습니다! 킹을 지키세요.' : '당신의 차례입니다 (백색)';
            renderBoard();
        });
    }
}

function simMove(b, move) {
    let temp = b.map(row => [...row]);
    let p = temp[move.from.r][move.from.c];
    temp[move.from.r][move.from.c] = '.';
    if (p === 'p' && move.to.r === 7) p = 'q';
    if (p === 'P' && move.to.r === 0) p = 'Q';
    temp[move.to.r][move.to.c] = p;
    return temp;
}

function minimaxAlphaBeta(b, depth, alpha, beta, isMaximizing) {
    if (depth === 0) return evaluateBoard(b);

    const color = isMaximizing ? 'b' : 'w';
    let moves = getAllLegalMoves(color, b);

    if (moves.length === 0) {
        if (getCheckStatus(color, b).isCheck) {
            return isMaximizing ? -50000 : 50000;
        }
        return 0;
    }

    moves.sort((m1, m2) => {
        let cap1 = b[m1.to.r][m1.to.c] !== '.' ? VALUES[b[m1.to.r][m1.to.c].toLowerCase()] : 0;
        let cap2 = b[m2.to.r][m2.to.c] !== '.' ? VALUES[b[m2.to.r][m2.to.c].toLowerCase()] : 0;
        return cap2 - cap1;
    });

    if (isMaximizing) {
        let maxEval = -Infinity;
        for (let m of moves) {
            let temp = simMove(b, m);
            let ev = minimaxAlphaBeta(temp, depth - 1, alpha, beta, false);
            maxEval = Math.max(maxEval, ev);
            alpha = Math.max(alpha, maxEval);
            if (beta <= alpha) break;
        }
        return maxEval;
    } else {
        let minEval = Infinity;
        for (let m of moves) {
            let temp = simMove(b, m);
            let ev = minimaxAlphaBeta(temp, depth - 1, alpha, beta, true);
            minEval = Math.min(minEval, ev);
            beta = Math.min(beta, minEval);
            if (beta <= alpha) break;
        }
        return minEval;
    }
}

function evaluateBoard(b) {
    let score = 0;
    for (let r = 0; r < 8; r++) {
        for (let c = 0; c < 8; c++) {
            let p = b[r][c];
            if (p !== '.') {
                let type = p.toLowerCase();
                let baseVal = VALUES[type];
                let pstVal = 0;

                if (type === 'p') {
                    pstVal = isBlack(p) ? pawnPST_Black[r][c] : pawnPST_Black[7 - r][c];
                } else if (type === 'n') {
                    pstVal = knightPST[r][c];
                }

                let total = baseVal + pstVal;
                score += isBlack(p) ? total : -total;
            }
        }
    }
    return score;
}
</script>
</body>
</html>
