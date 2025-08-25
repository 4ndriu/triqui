<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Triqui (Tic‑Tac‑Toe)</title>
  <style>
    :root{
      --bg: #0f1220;
      --panel: #151a2e;
      --accent: #7c9cff;
      --accent-2: #30e3b3;
      --text: #e6e9f2;
      --muted: #9aa3b2;
      --danger: #ff6b6b;
      --shadow: 0 20px 40px rgba(0,0,0,.35);
      --radius: 16px;
    }
    *{box-sizing:border-box}
    body{
      margin:0;min-height:100svh;display:grid;place-items:center;
      font-family:system-ui,-apple-system,Segoe UI,Roboto,Ubuntu,Inter,Arial,sans-serif;
      background: radial-gradient(1200px 800px at 10% 10%, #182043 0%, #0f1220 55%),
                  radial-gradient(900px 600px at 90% 90%, #182043 0%, #0f1220 60%);
      color:var(--text);
    }
    .card{
      width:min(92vw, 720px);
      background: linear-gradient(180deg, rgba(255,255,255,.04), rgba(255,255,255,.02));
      border:1px solid rgba(255,255,255,.08);
      border-radius:var(--radius);
      box-shadow:var(--shadow);
      padding:clamp(16px, 3vw, 28px);
    }
    header{
      display:flex;align-items:center;justify-content:space-between;gap:12px;flex-wrap:wrap;
      margin-bottom:16px;
    }
    h1{font-size:clamp(22px,3.2vw,28px);margin:0;letter-spacing:.2px}
    .sub{color:var(--muted);font-size:14px}

    .controls{display:flex;gap:10px;flex-wrap:wrap}
    .btn{appearance:none;border:1px solid rgba(255,255,255,.12);color:var(--text);
         background:#11162a;padding:10px 14px;border-radius:12px;cursor:pointer;
         font-weight:600;letter-spacing:.2px;transition:.18s transform,.18s filter,.18s background}
    .btn:hover{filter:brightness(1.1)}
    .btn:active{transform:translateY(1px)}
    .btn.primary{background:linear-gradient(180deg, #1c2447, #121732);border-color:#2b3566}
    .btn.ghost{background:#0e1327}

    .row{display:flex;gap:12px;align-items:center;flex-wrap:wrap}
    .label{color:var(--muted);font-weight:600}

    .score{
      display:grid;grid-template-columns:repeat(3,1fr);gap:12px;margin:12px 0 8px
    }
    .score .tile{
      background:var(--panel);border:1px solid rgba(255,255,255,.08);border-radius:14px;
      padding:12px;display:grid;place-items:center;text-align:center
    }
    .score .tile b{font-size:20px}
    .score .tile span{color:var(--muted);font-size:12px}

    .board{
      width:min(92vw, 420px);
      aspect-ratio:1/1;
      margin:18px auto 10px;
      display:grid;grid-template-columns:repeat(3,1fr);gap:10px;
    }
    .cell{
      display:grid;place-items:center;cursor:pointer;height:100%;
      border-radius:20px;border:1px solid rgba(255,255,255,.1);
      background:linear-gradient(160deg, #121832, #0c1023);
      box-shadow:inset 0 0 0 2px rgba(255,255,255,.02);
      font-size:clamp(42px, 10vw, 64px);
      font-weight:800;letter-spacing:1px;color:var(--text);
      transition:transform .18s, background .18s, box-shadow .18s;
    }
    .cell:hover{transform:translateY(-1px);box-shadow:inset 0 0 0 2px rgba(255,255,255,.06)}
    .cell:disabled{cursor:not-allowed;opacity:.9}
    .cell.x{color:var(--accent)}
    .cell.o{color:var(--accent-2)}
    .cell.win{background:linear-gradient(160deg, #16224a, #123d35); box-shadow:0 8px 20px rgba(0,0,0,.45)}

    .status{ text-align:center; font-weight:700; margin:6px 0 14px;}
    .status small{display:block;color:var(--muted);font-weight:500}

    .footer{display:flex;justify-content:center;margin-top:8px}

    /* Toggle */
    .toggle{
      display:inline-flex;align-items:center;gap:8px;user-select:none
    }
    .switch{position:relative;width:52px;height:28px;border-radius:999px;background:#0e1427;
      border:1px solid rgba(255,255,255,.12);cursor:pointer;transition:.2s background}
    .switch::after{content:"";position:absolute;inset:2px;left:2px;width:24px;height:24px;border-radius:999px;
      background:#ffffff1a;backdrop-filter:blur(4px);transition:transform .22s cubic-bezier(.2,.7,.2,1)}
    .switch[aria-checked="true"]{background:#1b2447}
    .switch[aria-checked="true"]::after{transform:translateX(24px)}

    @media (max-width:480px){
      .score{grid-template-columns:repeat(3,1fr)}
    }
  </style>
</head>
<body>
  <div class="card" role="application" aria-label="Triqui">
    <header>
      <div>
        <h1>Triqui</h1>
        <div class="sub">Juega contra otra persona o contra la CPU.</div>
      </div>
      <div class="controls">
        <button class="btn primary" id="btnReiniciar" title="Vaciar el tablero y continuar la partida">Reiniciar</button>
        <button class="btn ghost" id="btnResetScores" title="Poner marcador en cero">Borrar marcador</button>
      </div>
    </header>

    <div class="row" style="justify-content:space-between">
      <div class="toggle" id="modeToggle">
        <span class="label">Modo:</span>
        <button class="switch" id="switchMode" role="switch" aria-checked="false" aria-label="Cambiar modo"></button>
        <span id="modeLabel">Jugador vs Jugador</span>
      </div>
      <div class="row">
        <span class="label">Empieza:</span>
        <select id="starter" class="btn ghost" aria-label="Quién empieza">
          <option value="X">X</option>
          <option value="O">O</option>
        </select>
      </div>
    </div>

    <div class="score" aria-live="polite">
      <div class="tile"><b id="scoreX">0</b><span>Victorias X</span></div>
      <div class="tile"><b id="scoreEmpates">0</b><span>Empates</span></div>
      <div class="tile"><b id="scoreO">0</b><span>Victorias O</span></div>
    </div>

    <div class="status" id="status">Turno de <span id="turn">X</span><small>Haz clic en una casilla</small></div>

    <div class="board" id="board" role="grid" aria-label="Tablero de 3 por 3">
      <!-- Celdas generadas por JS -->
    </div>

    <div class="footer">
      <small class="sub">Accesible con teclado: usa Tab y Enter/Espacio.</small>
    </div>
  </div>

  <script>
  (function(){
    const boardEl = document.getElementById('board');
    const statusEl = document.getElementById('status');
    const turnEl = document.getElementById('turn');
    const btnReiniciar = document.getElementById('btnReiniciar');
    const btnResetScores = document.getElementById('btnResetScores');
    const switchMode = document.getElementById('switchMode');
    const modeLabel = document.getElementById('modeLabel');
    const starterSel = document.getElementById('starter');
    const scoreXEl = document.getElementById('scoreX');
    const scoreOEl = document.getElementById('scoreO');
    const scoreEmpatesEl = document.getElementById('scoreEmpates');

    const WIN_LINES = [
      [0,1,2],[3,4,5],[6,7,8], // filas
      [0,3,6],[1,4,7],[2,5,8], // columnas
      [0,4,8],[2,4,6]          // diagonales
    ];

    let board = Array(9).fill(null);
    let turn = 'X';
    let gameOver = false;
    let vsCPU = false;
    let scores = { X:0, O:0, D:0 };

    // Crear celdas como botones accesibles
    const cells = [];
    for(let i=0;i<9;i++){
      const btn = document.createElement('button');
      btn.className = 'cell';
      btn.type = 'button';
      btn.setAttribute('data-idx', i);
      btn.setAttribute('role','gridcell');
      btn.setAttribute('aria-label', 'Casilla vacía');
      btn.addEventListener('click', onCellClick);
      boardEl.appendChild(btn);
      cells.push(btn);
    }

    btnReiniciar.addEventListener('click', newRound);
    btnResetScores.addEventListener('click', ()=>{scores={X:0,O:0,D:0}; renderScores();});
    switchMode.addEventListener('click', ()=>{
      vsCPU = !vsCPU;
      switchMode.setAttribute('aria-checked', String(vsCPU));
      modeLabel.textContent = vsCPU ? 'Jugador vs CPU' : 'Jugador vs Jugador';
      newRound();
    });
    starterSel.addEventListener('change', ()=>{ newRound(); });

    function onCellClick(e){
      const idx = +e.currentTarget.dataset.idx;
      if(gameOver || board[idx]) return;
      place(idx, turn);
      const result = checkEnd();
      if(result) return finish(result);

      if(vsCPU){
        // turno CPU
        disableBoard(true);
        setTimeout(()=>{
          const move = bestMove(board, turn==='X'?'O':'X');
          if(move!=null) place(move, turn);
          const res2 = checkEnd();
          disableBoard(false);
          if(res2) return finish(res2);
        }, 250);
      }
    }

    function place(idx, player){
      board[idx] = player;
      cells[idx].textContent = player;
      cells[idx].classList.add(player.toLowerCase());
      cells[idx].setAttribute('aria-label', `Casilla con ${player}`);
      cells[idx].disabled = true;
      turn = player==='X'?'O':'X';
      turnEl.textContent = turn;
    }

    function checkEnd(){
      for(const [a,b,c] of WIN_LINES){
        if(board[a] && board[a]===board[b] && board[a]===board[c]){
          return { type:'win', player: board[a], line:[a,b,c] };
        }
      }
      if(board.every(Boolean)) return { type:'draw' };
      return null;
    }

    function finish({type, player, line}){
      gameOver = true;
      if(type==='win'){
        statusEl.innerHTML = `¡Ganó <span class="${player.toLowerCase()}">${player}</span>!`;
        scores[player]++;
        for(const i of line) cells[i].classList.add('win');
      }else{
        statusEl.textContent = 'Empate';
        scores.D++;
      }
      renderScores();
      disableBoard(true);
    }

    function disableBoard(disabled){
      cells.forEach(c=>c.disabled = disabled || Boolean(c.textContent));
    }

    function renderScores(){
      scoreXEl.textContent = scores.X;
      scoreOEl.textContent = scores.O;
      scoreEmpatesEl.textContent = scores.D;
    }

    function newRound(){
      // Reiniciar tablero pero mantener marcador
      board = Array(9).fill(null);
      gameOver = false;
      turn = starterSel.value;
      turnEl.textContent = turn;
      statusEl.innerHTML = `Turno de <span id="turnInner">${turn}</span><small>Haz clic en una casilla</small>`;
      cells.forEach(c=>{
        c.textContent='';
        c.classList.remove('x','o','win');
        c.disabled=false;
        c.setAttribute('aria-label', 'Casilla vacía');
      });
      // Si CPU y empieza O, y seleccionaste que empiece O, que la CPU juegue si corresponde
      if(vsCPU && turn==='O'){
        disableBoard(true);
        setTimeout(()=>{
          const move = bestMove(board, 'O');
          place(move, 'O');
          disableBoard(false);
        }, 250);
      }
    }

    // --- IA minimax (imparable) ---
    function bestMove(state, ai){
      const human = ai==='X' ? 'O' : 'X';
      const empty = state.map((v,i)=>v?null:i).filter(v=>v!==null);
      if(empty.length===0) return null;

      let best = { idx: null, score: -Infinity };
      for(const idx of empty){
        state[idx] = ai;
        const score = minimax(state, /*depth*/0, /*isMax*/false, ai, human);
        state[idx] = null;
        if(score > best.score){ best = { idx, score }; }
      }
      return best.idx;
    }

    function minimax(state, depth, isMax, ai, human){
      const outcome = evaluate(state, ai, human);
      if(outcome !== null) return outcome - depth * 0.01; // pequeño sesgo a ganar rápido

      const moves = state.map((v,i)=>v?null:i).filter(v=>v!==null);
      if(isMax){
        let best = -Infinity;
        for(const i of moves){
          state[i] = ai;
          best = Math.max(best, minimax(state, depth+1, false, ai, human));
          state[i] = null;
        }
        return best;
      }else{
        let best = Infinity;
        for(const i of moves){
          state[i] = human;
          best = Math.min(best, minimax(state, depth+1, true, ai, human));
          state[i] = null;
        }
        return best;
      }
    }

    function evaluate(s, ai, human){
      // Devuelve +10 si gana AI, -10 si gana humano, 0 en empate, null si el juego sigue
      const lines = WIN_LINES;
      for(const [a,b,c] of lines){
        if(s[a] && s[a]===s[b] && s[a]===s[c]){
          return s[a]===ai ? 10 : -10;
        }
      }
      if(s.every(Boolean)) return 0;
      return null;
    }

    // iniciar
    newRound();
  })();
  </script>
</body>
</html>
