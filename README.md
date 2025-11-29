# rock-paper-scissor-frontend
This project is a simple and interactive Rock–Paper–Scissors game built using HTML, CSS, and JavaScript. The user can choose Rock, Paper, or Scissors, and the computer generates a random choice. Based on the selected options, the game displays the result (Win/Lose/Draw) and updates the score. 
 PROGRAM 
 <!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Rock Paper Scissors — Simple Game</title>
  <style>
    :root{--bg:#0f172a;--card:#0b1220;--accent:#06b6d4;--muted:#94a3b8;--win:#16a34a;--lose:#dc2626}
    *{box-sizing:border-box;font-family:Inter,Segoe UI,Arial;margin:0;padding:0}
    body{background:linear-gradient(180deg,#071029 0%,#071833 100%);color:#e6eef6;min-height:100vh;display:flex;align-items:center;justify-content:center;padding:24px}
    .card{background:rgba(255,255,255,0.03);width:720px;max-width:100%;padding:22px;border-radius:14px;box-shadow:0 10px 30px rgba(2,6,23,0.7)}
    header{display:flex;align-items:center;justify-content:space-between;margin-bottom:16px}
    h1{font-size:20px}
    .score{background:rgba(255,255,255,0.03);padding:8px 12px;border-radius:10px;color:var(--accent);font-weight:600}

    .arena{display:flex;gap:16px;align-items:center;justify-content:space-between}
    .choices{display:flex;gap:12px;flex:1;justify-content:center}
    .btn{background:linear-gradient(180deg,rgba(255,255,255,0.02),rgba(255,255,255,0.01));border:1px solid rgba(255,255,255,0.04);padding:14px 18px;border-radius:12px;cursor:pointer;min-width:110px;text-align:center}
    .btn:hover{transform:translateY(-4px)}
    .icon{font-size:28px;display:block;margin-bottom:6px}
    .status{flex-basis:240px;text-align:center}
    .result{font-weight:700;margin-top:6px}
    .muted{color:var(--muted);font-size:13px}

    .logs{margin-top:16px;background:rgba(255,255,255,0.02);padding:12px;border-radius:10px;min-height:44px}
    .controls{display:flex;gap:8px;justify-content:flex-end;margin-top:12px}
    .small{padding:8px 10px;font-size:13px}

    .win{color:var(--win)}
    .lose{color:var(--lose)}

    /* simple animation for pick flash */
    .flash{animation:flash 400ms ease}
    @keyframes flash{0%{transform:scale(1)}50%{transform:scale(1.06)}100%{transform:scale(1)}}

    @media (max-width:560px){.arena{flex-direction:column}.status{width:100%}}
  </style>
</head>
<body>
  <div class="card">
    <header>
      <h1>Rock • Paper • Scissors</h1>
      <div class="score">You: <span id="playerScore">0</span> — CPU: <span id="cpuScore">0</span></div>
    </header>

    <div class="arena">
      <div class="choices" role="group" aria-label="choose">
        <button class="btn" data-pick="rock" title="Rock">
          <span class="icon">✊</span>
          <span>Rock</span>
        </button>

        <button class="btn" data-pick="paper" title="Paper">
          <span class="icon">✋</span>
          <span>Paper</span>
        </button>

        <button class="btn" data-pick="scissors" title="Scissors">
          <span class="icon">✌️</span>
          <span>Scissors</span>
        </button>
      </div>

      <div class="status">
        <div class="muted">You chose</div>
        <div id="playerPick" style="font-size:28px;margin-top:6px">—</div>
        <div class="muted" style="margin-top:8px">CPU chose</div>
        <div id="cpuPick" style="font-size:28px;margin-top:6px">—</div>
        <div class="result" id="resultText">Make a pick</div>
      </div>
    </div>

    <div class="logs" id="log">First to 5 wins — try it!</div>

    <div class="controls">
      <button id="restart" class="small">Restart</button>
      <button id="resetScores" class="small">Reset Scores</button>
    </div>
  </div>

  <script>
    // --- Simple Rock-Paper-Scissors Game ---
    const buttons = document.querySelectorAll('.btn');
    const playerPickEl = document.getElementById('playerPick');
    const cpuPickEl = document.getElementById('cpuPick');
    const resultText = document.getElementById('resultText');
    const log = document.getElementById('log');
    const playerScoreEl = document.getElementById('playerScore');
    const cpuScoreEl = document.getElementById('cpuScore');

    let playerScore = 0, cpuScore = 0;
    const choices = ['rock','paper','scissors'];

    function cpuChoice(){
      return choices[Math.floor(Math.random()*choices.length)];
    }

    function asEmoji(pick){
      if(pick==='rock') return '✊ Rock';
      if(pick==='paper') return '✋ Paper';
      if(pick==='scissors') return '✌️ Scissors';
      return '—';
    }

    // decide winner: returns 'win' | 'lose' | 'draw'
    function decide(player, cpu){
      if(player===cpu) return 'draw';
      if(
        (player==='rock' && cpu==='scissors') ||
        (player==='paper' && cpu==='rock') ||
        (player==='scissors' && cpu==='paper')
      ) return 'win';
      return 'lose';
    }

    function updateScores(){
      playerScoreEl.textContent = playerScore;
      cpuScoreEl.textContent = cpuScore;
    }

    function appendLog(text){
      const now = new Date();
      const t = now.toLocaleTimeString();
      log.textContent = t + ' — ' + text;
    }

    function playRound(player){
      const cpu = cpuChoice();
      // show picks
      playerPickEl.textContent = asEmoji(player);
      cpuPickEl.textContent = asEmoji(cpu);

      // tiny flash animation
      playerPickEl.classList.add('flash');
      cpuPickEl.classList.add('flash');
      setTimeout(()=>{
        playerPickEl.classList.remove('flash');
        cpuPickEl.classList.remove('flash');
      },350);

      const outcome = decide(player,cpu);
      if(outcome==='win'){
        playerScore++;
        resultText.textContent = 'You Win!';
        resultText.classList.remove('lose');
        resultText.classList.add('win');
        appendLog('You beat CPU — ' + asEmoji(player) + ' vs ' + asEmoji(cpu));
      } else if(outcome==='lose'){
        cpuScore++;
        resultText.textContent = 'You Lose';
        resultText.classList.remove('win');
        resultText.classList.add('lose');
        appendLog('CPU beat you — ' + asEmoji(cpu) + ' vs ' + asEmoji(player));
      } else {
        resultText.textContent = "It's a Draw";
        resultText.classList.remove('win','lose');
        appendLog('Draw — ' + asEmoji(player) + ' vs ' + asEmoji(cpu));
      }

      updateScores();

      // optional: first to 5 ends the match
      if(playerScore>=5 || cpuScore>=5){
        const winner = playerScore>cpuScore ? 'You' : 'CPU';
        appendLog(winner + ' won the match! Reload or Reset to play again.');
        // disable buttons
        buttons.forEach(b=>b.disabled=true);
      }
    }

    buttons.forEach(b=>{
      b.addEventListener('click',e=>{
        const pick = b.getAttribute('data-pick');
        playRound(pick);
      });
    });

    document.getElementById('restart').addEventListener('click',()=>{
      // clear picks and enable
      playerPickEl.textContent = '—';
      cpuPickEl.textContent = '—';
      resultText.textContent = 'Make a pick';
      resultText.classList.remove('win','lose');
      buttons.forEach(b=>b.disabled=false);
      appendLog('Round restarted — play again');
    });

    document.getElementById('resetScores').addEventListener('click',()=>{
      playerScore = 0; cpuScore = 0; updateScores();
      appendLog('Scores reset');
      buttons.forEach(b=>b.disabled=false);
    });

    // accessibility: keyboard picks
    window.addEventListener('keydown',e=>{
      if(e.key === '1') playRound('rock');
      if(e.key === '2') playRound('paper');
      if(e.key === '3') playRound('scissors');
    });
  </script>
</body>
</html>
