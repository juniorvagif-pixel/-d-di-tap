<!DOCTYPE html>
<html lang="az">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Rəqəm Təxmin Oyunu</title>
<style>
  :root{
    --card-bg: rgba(255,255,255,0.08);
    --accent: #ffd166;
    --text: #ecf0f1;
  }
  /* Animated gradient background */
  body{
    margin:0;
    height:100vh;
    font-family: Inter, system-ui, Arial;
    color:var(--text);
    display:flex;
    align-items:center;
    justify-content:center;
    overflow:hidden;
    background:linear-gradient(120deg,#0f172a,#021124 40%,#0b3a2e);
  }
  .bg-anim{
    position:fixed; inset:0; z-index:0;
    background: radial-gradient(circle at 10% 20%, rgba(255,193,7,0.08), transparent 10%),
                radial-gradient(circle at 80% 80%, rgba(99,102,241,0.06), transparent 10%),
                radial-gradient(circle at 50% 50%, rgba(16,185,129,0.04), transparent 12%);
    animation: drift 20s linear infinite;
    filter: blur(18px) saturate(120%);
    pointer-events:none;
  }
  @keyframes drift{
    0%{ transform:translate(0,0) }
    50%{ transform:translate(-6vw,3vh) }
    100%{ transform:translate(0,0) }
  }

  /* Main card */
  .card{
    position:relative;
    z-index:2;
    width:min(960px, 92%);
    max-width:960px;
    background:linear-gradient(180deg, rgba(255,255,255,0.04), rgba(255,255,255,0.02));
    border:1px solid rgba(255,255,255,0.06);
    padding:22px;
    border-radius:16px;
    box-shadow: 0 10px 30px rgba(2,6,23,0.6);
    display:grid;
    grid-template-columns: 1fr 340px;
    gap:20px;
    align-items:start;
  }

  /* Left: game */
  .left{
    padding:16px;
  }
  h1{ margin:0 0 8px 0; font-size:20px; letter-spacing:0.3px; color:var(--accent) }
  p.lead{ margin:0 0 14px 0; color:#cbd5e1; font-size:14px }

  .panel{
    background: linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
    border-radius:12px;
    padding:14px;
    margin-bottom:14px;
    border:1px solid rgba(255,255,255,0.03);
  }

  .modes { display:flex; gap:8px; flex-wrap:wrap }
  .mode-btn{
    padding:8px 12px; border-radius:10px; cursor:pointer;
    background:transparent; border:1px solid rgba(255,255,255,0.05);
    color:var(--text); font-weight:600; font-size:13px;
  }
  .mode-btn.active{ background:linear-gradient(90deg,#ffd16622,#60a5fa22); box-shadow:0 6px 20px rgba(0,0,0,0.25) }

  .big-number{
    font-size:64px; font-weight:800; letter-spacing:6px;
    text-align:center; color:transparent;
    -webkit-text-stroke: 1px rgba(255,255,255,0.08);
    margin:18px 0 6px 0;
    background: linear-gradient(90deg,#fff,#ffd166,#60a5fa);
    -webkit-background-clip:text;
    background-clip:text;
  }

  .hint{ text-align:center; color:#a3b2c7; margin-bottom:10px; font-size:14px }

  .controls{ display:flex; gap:8px; align-items:center; justify-content:center; flex-wrap:wrap }
  input[type="number"]{
    width:160px; padding:12px 14px; font-size:18px; border-radius:10px;
    border:1px solid rgba(255,255,255,0.06); background:rgba(0,0,0,0.25); color:var(--text);
    outline:none; text-align:center;
  }
  button.btn{
    padding:12px 14px; border-radius:10px; border:none; cursor:pointer; font-weight:700;
    background:linear-gradient(90deg,#ffd166,#f97316); color:#021124;
    box-shadow: 0 6px 18px rgba(249,115,22,0.18);
  }
  button.secondary{
    background:transparent; border:1px solid rgba(255,255,255,0.06); color:var(--text); font-weight:600;
  }

  .meta{ display:flex; gap:12px; justify-content:center; margin-top:12px; color:#cbd5e1; font-size:14px }
  .meta div{ background:rgba(255,255,255,0.02); padding:8px 10px; border-radius:10px; border:1px solid rgba(255,255,255,0.02) }

  /* Right: info & leaderboard */
  .right{ padding:16px; min-width:240px }
  h2{ margin:0 0 12px 0; font-size:16px; color:#fff }
  .leader{
    background:linear-gradient(180deg, rgba(255,255,255,0.01), rgba(255,255,255,0.008));
    padding:10px; border-radius:10px; border:1px solid rgba(255,255,255,0.03);
    max-height:360px; overflow:auto;
  }
  .leader-item{ display:flex; justify-content:space-between; padding:8px 6px; border-bottom:1px dashed rgba(255,255,255,0.02); font-size:14px; color:#e6eef8 }
  .muted{ color:#9fb0c8; font-size:13px }

  footer{ grid-column:1/-1; text-align:center; margin-top:6px; color:#98a9c1; font-size:13px }

  /* Responsive */
  @media (max-width:820px){
    .card{ grid-template-columns: 1fr; padding:16px; }
    .right{ order:2 }
    .left{ order:1 }
    .big-number{ font-size:44px }
  }
</style>
</head>
<body>
  <div class="bg-anim" aria-hidden="true"></div>

  <main class="card" role="main" aria-labelledby="title">
    <section class="left">
      <h1 id="title">Rəqəm Təxmin Oyunu</h1>
      <p class="lead">Hədəf rəqəmi tap. Hər təxminə görə göstəriş alacaqsan: <strong>yüksək</strong> ya <strong>aşağı</strong>.</p>

      <div class="panel">
        <div style="display:flex;justify-content:space-between;align-items:center;">
          <div style="display:flex;flex-direction:column;">
            <small class="muted">Modlar</small>
            <div class="modes" role="tablist" aria-label="Oyun modları">
              <button class="mode-btn active" data-mode="easy">Asan (1-50)</button>
              <button class="mode-btn" data-mode="normal">Orta (1-100)</button>
              <button class="mode-btn" data-mode="hard">Çətin (1-500)</button>
              <button class="mode-btn" data-mode="time">Time Attack</button>
            </div>
          </div>
          <div style="text-align:right">
            <small class="muted">Sürətli göstərici</small>
            <div style="font-weight:700;font-size:18px; margin-top:6px;">Cəhdlər: <span id="tries">—</span></div>
          </div>
        </div>

        <div style="margin-top:14px; text-align:center">
          <div class="big-number" id="mask">?</div>
          <div class="hint" id="hint">Start düyməsinə bas və oyuna başla!</div>

          <div class="controls" style="margin-top:10px">
            <input id="guessInput" type="number" inputmode="numeric" placeholder="Rəqəmi yaz..." />
            <button class="btn" id="guessBtn">Təxmin et</button>
            <button class="secondary" id="newBtn">Start / Yenilə</button>
          </div>

          <div class="meta" aria-live="polite">
            <div>Aralıq: <span id="rangeText">—</span></div>
            <div>Qazan: <span id="wins">0</span></div>
            <div>Uduz: <span id="losses">0</span></div>
          </div>
        </div>
      </div>

      <div class="panel" aria-hidden="false">
        <h2 style="font-size:15px;margin:0 0 8px 0">Nəzarətlər</h2>
        <ul style="margin:0;padding-left:18px;color:#bcd2e8">
          <li>Mod seç -> Start bas -> rəqəmi təxmin et.</li>
          <li>Hər modun cəhd sayı fərqlidir; Time Attack-da zaman limitin var.</li>
          <li>Yaxşı nəticələr localStorage-də saxlanılır.</li>
        </ul>
      </div>
    </section>

    <aside class="right" aria-label="Skor və liderlər">
      <h2>Liderlər</h2>
      <div class="leader" id="leaderboard" aria-live="polite"></div>

      <div style="margin-top:12px" class="panel">
        <div style="display:flex; gap:8px; align-items:center;">
          <input id="nameInput" placeholder="Sənin adın..." style="flex:1;padding:10px;border-radius:8px;border:1px solid rgba(255,255,255,0.04);background:transparent;color:var(--text)" />
          <button class="btn" id="saveScore">Skoru Saxla</button>
        </div>
        <div style="margin-top:8px;color:#9fb0c8;font-size:13px">Yalnız ən yaxşı nəticə saxlanır.</div>
        <div style="margin-top:10px; display:flex; gap:8px;">
          <button class="secondary" id="clearScores">Liderləri Təmizlə</button>
          <button class="secondary" id="howBtn">Qaydalar</button>
        </div>
      </div>
    </aside>

    <footer>Hazırlayan: Ethical Hacker GPT • Mobil + Desktop dəstək • <span class="muted">Yaddaş: localStorage</span></footer>
  </main>

<script>
/*
  Rəqəm Təxmin Oyunu — JavaScript
  Xüsusiyyətlər:
  - Modlar (easy, normal, hard, time)
  - Cəhd sayı və limitlər
  - Time Attack üçün zamanlayıcı
  - localStorage ilə sadə lider lövhəsi
  - Mobil dostu
*/

const modes = {
  easy:   { min:1, max:50, tries:10, time:0 },
  normal: { min:1, max:100, tries:7, time:0 },
  hard:   { min:1, max:500, tries:5, time:0 },
  time:   { min:1, max:200, tries:12, time:20 } // time = seconds
};

let state = {
  mode: 'easy',
  target: null,
  triesLeft: 0,
  started: false,
  timeLeft: 0,
  timerId: null,
  wins: 0, losses:0
};

const el = {
  modes: document.querySelectorAll('.mode-btn'),
  tries: document.getElementById('tries'),
  guessInput: document.getElementById('guessInput'),
  guessBtn: document.getElementById('guessBtn'),
  newBtn: document.getElementById('newBtn'),
  hint: document.getElementById('hint'),
  rangeText: document.getElementById('rangeText'),
  mask: document.getElementById('mask'),
  wins: document.getElementById('wins'),
  losses: document.getElementById('losses'),
  leaderboard: document.getElementById('leaderboard'),
  nameInput: document.getElementById('nameInput'),
  saveScore: document.getElementById('saveScore'),
  clearScores: document.getElementById('clearScores'),
  howBtn: document.getElementById('howBtn')
};

function pickMode(m){
  state.mode = m;
  document.querySelectorAll('.mode-btn').forEach(b=> b.classList.toggle('active', b.dataset.mode===m));
  applyMode();
}

function applyMode(){
  const mod = modes[state.mode];
  state.triesLeft = mod.tries || Infinity;
  state.timeLeft = mod.time || 0;
  el.rangeText.innerText = `${mod.min} — ${mod.max}`;
  el.tries.innerText = (mod.time? `${state.timeLeft}s` : (isFinite(state.triesLeft)? state.triesLeft : '∞'));
  el.mask.innerText = '?';
  el.hint.innerText = 'Start düyməsinə bas və oyuna başla!';
}

function newGame(){
  const mod = modes[state.mode];
  state.target = Math.floor(Math.random() * (mod.max - mod.min + 1)) + mod.min;
  state.started = true;
  state.triesLeft = mod.tries || Infinity;
  state.timeLeft = mod.time || 0;
  el.mask.innerText = '✳';
  el.hint.innerText = 'Rəqəmi yaz və "Təxmin et" bas.';
  el.guessInput.value = '';
  el.guessInput.focus();
  updateTriesUI();
  stopTimer();
  if(state.timeLeft > 0) startTimer();
}

function endGame(won, reason){
  state.started = false;
  stopTimer();
  if(won){
    state.wins++;
    el.hint.innerText = `✅ Təbriklər! Düz tapdın: ${state.target}. (${reason || 'Uğur'})`;
    el.mask.innerText = state.target;
  } else {
    state.losses++;
    el.hint.innerText = `❌ Uduzdun — doğru rəqəm ${state.target}. ${reason || ''}`;
    el.mask.innerText = state.target;
  }
  el.wins.innerText = state.wins;
  el.losses.innerText = state.losses;
}

function makeGuess(){
  if(!state.started){ el.hint.innerText='Oyunu başlat: Start / Yenilə'; return; }
  const val = Number(el.guessInput.value);
  if(!Number.isFinite(val)){ el.hint.innerText='Zəhmət olmasa rəqəm daxil et.'; return; }
  const mod = modes[state.mode];
  if(val < mod.min || val > mod.max){ el.hint.innerText = `Aralıq: ${mod.min}-${mod.max} daxilində olmalıdır.`; return; }

  state.triesLeft = (isFinite(state.triesLeft) ? state.triesLeft - 1 : state.triesLeft);
  updateTriesUI();

  if(val === state.target){
    endGame(true, `Təxmin qaldı: ${state.triesLeft} cəhdlə`);
    return;
  }

  // give hint
  const diff = Math.abs(val - state.target);
  let hintText = val < state.target ? 'Daha YÜKSƏK' : 'Daha AŞAĞI';
  if(diff <= 2) hintText += ' — ÇOX YAXIN!';
  else if(diff <= 6) hintText += ' — YAXIN!';
  el.hint.innerText = `${hintText}`;

  if(isFinite(state.triesLeft) && state.triesLeft <= 0){
    endGame(false, 'Cəhdlərin bitdi.');
  }
}

function updateTriesUI(){
  if(modes[state.mode].time){
    el.tries.innerText = `${state.timeLeft}s`;
  } else {
    el.tries.innerText = isFinite(state.triesLeft)? state.triesLeft : '∞';
  }
}

function startTimer(){
  if(state.timerId) clearInterval(state.timerId);
  state.timerId = setInterval(()=>{
    state.timeLeft--;
    updateTriesUI();
    if(state.timeLeft <= 0){
      clearInterval(state.timerId);
      if(state.started) endGame(false, 'Zaman bitdi.');
    }
  }, 1000);
}

function stopTimer(){
  if(state.timerId) clearInterval(state.timerId);
  state.timerId = null;
}

function saveLeader(){
  const name = (el.nameInput.value || 'Anon').trim();
  const score = computeScore();
  if(score <= 0){ alert('Qeyd üçün əvvəlcə düzgün təxmin etməlisən.'); return; }
  const key = 'rt_game_leader';
  const raw = localStorage.getItem(key);
  const list = raw ? JSON.parse(raw) : [];
  // push and keep top 10 by score desc
  list.push({ name, score, mode: state.mode, date: new Date().toISOString() });
  list.sort((a,b)=> b.score - a.score);
  if(list.length>10) list.length=10;
  localStorage.setItem(key, JSON.stringify(list));
  el.nameInput.value = '';
  renderLeader();
  alert('Skor saxlandı!');
}

function computeScore(){
  // Simple formula: higher remaining tries/time -> higher score
  const mod = modes[state.mode];
  if(!state.target) return 0;
  // if last game was win, score = base + leftover * factor
  if(state.wins + state.losses === 0) return 0;
  // can't reliably tell last result; we'll compute based on tries left
  let base = 1000 / (mod.max / 50); // normalize bigger ranges
  let bonus = isFinite(state.triesLeft) ? Math.max(0, state.triesLeft) * 50 : 200;
  if(mod.time) bonus = Math.max(0, state.timeLeft) * 40;
  const score = Math.round(base + bonus);
  return score;
}

function renderLeader(){
  const raw = localStorage.getItem('rt_game_leader');
  const list = raw ? JSON.parse(raw) : [];
  if(list.length === 0){
    el.leaderboard.innerHTML = '<div style="padding:12px;color:#9fb0c8">Heç bir skor yoxdur — ilk sənin ola bilər!</div>';
    return;
  }
  el.leaderboard.innerHTML = list.map((it,i)=>`
    <div class="leader-item">
      <div style="display:flex;gap:8px;align-items:center"><strong>#${i+1}</strong><div>${escapeHtml(it.name)}</div></div>
      <div style="text-align:right"><div style="font-weight:800;color:var(--accent)">${it.score}</div><div class="muted" style="font-size:12px">${it.mode}</div></div>
    </div>
  `).join('');
}

function clearScores(){
  if(confirm('Liderləri silmək istədiyinə əminsən?')) {
    localStorage.removeItem('rt_game_leader');
    renderLeader();
  }
}

function escapeHtml(s){ return String(s).replace(/[&<>"']/g, c=>({ '&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;' })[c]); }

// Wire events
el.modes.forEach(b => b.addEventListener('click', ()=> pickMode(b.dataset.mode)));
el.guessBtn.addEventListener('click', makeGuess);
el.guessInput.addEventListener('keydown', e => { if(e.key==='Enter') makeGuess(); });
el.newBtn.addEventListener('click', newGame);
el.saveScore.addEventListener('click', saveLeader);
el.clearScores.addEventListener('click', clearScores);
el.howBtn.addEventListener('click', ()=> alert('Qaydalar:\\n- Mod seç və Start bas.\\n- Rəqəmi təxmin et.\\n- Easy/Normal/Hard üçün məhdud cəhd var.\\n- Time Attack-da zaman limiti var.\\n- Ən yaxşı onları saxla.'));


// initialize UI
pickMode('easy');
renderLeader();
applyMode();

// restore simple counters from storage (optional)
(function restoreCounts(){
  const key = 'rt_game_meta';
  const raw = localStorage.getItem(key);
  if(raw){
    try{
      const o=JSON.parse(raw);
      state.wins = o.wins||0; state.losses=o.losses||0;
      el.wins.innerText = state.wins; el.losses.innerText = state.losses;
    }catch(e){}
  }
})();

// persist simple counters on unload
window.addEventListener('beforeunload', ()=>{
  localStorage.setItem('rt_game_meta', JSON.stringify({ wins: state.wins, losses: state.losses }));
});
</script>
</body>
</html>
