<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
  <meta name="google-site-verification" content="UABA0hyUZsT881HyI7wKmlKBNLNexFkkQrrTSzmxyDM" />
  <title>DOPAMINE RUSH - 爽快サウンドの無料ブラウザパズルゲーム</title>
  <meta name="description" content="爽快なブロック配置音とコンボ音を楽しめる無料ブラウザゲーム『DOPAMINE RUSH』。スマホやPCですぐに遊べるパズルゲーム！">
  <meta property="og:title" content="DOPAMINE RUSH">
  <meta property="og:description" content="爽快効果音のブラウザパズルゲーム">
  <meta property="og:type" content="website">

  <style>
    * {
      box-sizing: border-box;
      user-select: none;
      -webkit-user-select: none;
      touch-action: manipulation;
    }

    body {
      margin: 0;
      padding: 10px;
      background-color: #0d0f12;
      color: #ffffff;
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: flex-start;
      min-height: 100vh;
      overflow-y: auto;
    }

    #game-container {
      width: 100%;
      max-width: 420px;
      height: 600px;
      background: #161b22;
      border: 2px solid #30363d;
      border-radius: 16px;
      display: flex;
      flex-direction: column;
      position: relative;
      overflow: hidden;
      box-shadow: 0 10px 30px rgba(0,0,0,0.8);
      flex-shrink: 0;
    }

    .shake {
      animation: shake-anim 0.2s ease-in-out;
    }

    @keyframes shake-anim {
      0% { transform: translate(0, 0); }
      25% { transform: translate(-8px, 5px); }
      50% { transform: translate(8px, -5px); }
      75% { transform: translate(-5px, -5px); }
      100% { transform: translate(0, 0); }
    }

    header {
      padding: 10px 15px;
      text-align: center;
      background: rgba(0, 0, 0, 0.4);
      border-bottom: 1px solid #30363d;
    }

    h1 {
      margin: 0 0 5px 0;
      font-size: 1.3rem;
      letter-spacing: 2px;
      color: #00f2fe;
      text-shadow: 0 0 10px rgba(0, 242, 254, 0.6);
    }

    .hiscore-bar {
      font-size: 0.75rem;
      color: #ffd700;
      margin-bottom: 8px;
      font-weight: bold;
      letter-spacing: 1px;
    }

    .stats-bar {
      display: flex;
      justify-content: space-around;
      align-items: center;
    }

    .stat-box {
      display: flex;
      flex-direction: column;
      align-items: center;
    }

    .stat-value {
      font-size: 1.3rem;
      font-weight: bold;
      font-family: monospace;
    }

    .stat-label {
      font-size: 0.65rem;
      color: #8b949e;
    }

    #time-display.spurt {
      color: #ff0055;
      animation: pulse 0.4s infinite alternate;
      text-shadow: 0 0 12px #ff0055;
    }

    @keyframes pulse {
      0% { transform: scale(1); }
      100% { transform: scale(1.25); }
    }

    #combo-display {
      transition: transform 0.1s ease;
    }

    .combo-bump {
      transform: scale(1.4);
      color: #ff007f !important;
      text-shadow: 0 0 15px #ff007f;
    }

    #play-field {
      flex: 1;
      position: relative;
      background: radial-gradient(circle, #1a2332 0%, #0d0f12 100%);
    }

    .target {
      position: absolute;
      border-radius: 50%;
      cursor: pointer;
      display: flex;
      align-items: center;
      justify-content: center;
      font-weight: 900;
      font-size: 0.85rem;
      transform: scale(0);
      animation: pop-in 0.15s forwards ease-out;
      border: 2px solid rgba(255, 255, 255, 0.6);
      box-shadow: 
        inset -5px -5px 12px rgba(0, 0, 0, 0.6),
        inset 5px 5px 10px rgba(255, 255, 255, 0.8),
        0 8px 15px rgba(0, 0, 0, 0.5);
      transition: transform 0.05s ease;
    }

    .target:active {
      transform: scale(0.9) !important;
    }

    @keyframes pop-in {
      to { transform: scale(1); }
    }

    .target-gold {
      background: radial-gradient(circle at 35% 35%, #ffffff, #ffd700 60%, #b8860b 100%);
      color: #3d2b00;
      border-color: #fff;
      text-shadow: 0 1px 0 rgba(255, 255, 255, 0.8);
      box-shadow: 
        inset -5px -5px 12px rgba(139, 69, 19, 0.6),
        inset 5px 5px 10px rgba(255, 255, 255, 0.9),
        0 0 20px #ffd700,
        0 8px 15px rgba(0, 0, 0, 0.5);
    }

    .target-bomb {
      background: radial-gradient(circle at 35% 35%, #ff7799, #ff0055 60%, #880022 100%);
      color: #fff;
      border-color: #ff99bb;
      box-shadow: 
        inset -5px -5px 12px rgba(0, 0, 0, 0.8),
        inset 5px 5px 10px rgba(255, 255, 255, 0.7),
        0 0 20px #ff0055,
        0 8px 15px rgba(0, 0, 0, 0.5);
    }

    .overlay {
      position: absolute;
      inset: 0;
      background: rgba(13, 15, 18, 0.92);
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 20px;
      z-index: 10;
    }

    .hidden { display: none !important; }

    .btn {
      background: linear-gradient(135deg, #00f2fe, #4facfe);
      border: none;
      color: #000;
      padding: 12px 32px;
      font-size: 1.1rem;
      font-weight: bold;
      border-radius: 30px;
      cursor: pointer;
      box-shadow: 0 0 15px rgba(0, 242, 254, 0.4);
      margin-top: 10px;
    }

    .btn:active { transform: scale(0.95); }

    .rank-text {
      font-size: 3.5rem;
      font-weight: 900;
      margin: 5px 0;
      background: linear-gradient(135deg, #ffd700, #ff8c00);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }

    .new-record {
      color: #ff007f;
      font-weight: bold;
      font-size: 0.9rem;
      animation: pulse 0.5s infinite alternate;
    }

    .history-box {
      margin-top: 10px;
      background: rgba(255, 255, 255, 0.05);
      border-radius: 8px;
      padding: 8px;
      width: 100%;
      font-size: 0.75rem;
      color: #8b949e;
    }

    .name-input {
      background: #0d0f12;
      border: 1px solid #30363d;
      color: #fff;
      padding: 8px 12px;
      border-radius: 8px;
      font-size: 1rem;
      text-align: center;
      margin-bottom: 10px;
      width: 80%;
      max-width: 200px;
    }

    #online-ranking-panel {
      width: 100%;
      max-width: 420px;
      background: #161b22;
      border: 2px solid #30363d;
      border-radius: 16px;
      padding: 12px 15px;
      margin-top: 15px;
      box-shadow: 0 10px 30px rgba(0,0,0,0.8);
    }

    #online-ranking-panel h3 {
      margin: 0 0 8px 0;
      font-size: 0.9rem;
      color: #ffd700;
      letter-spacing: 1px;
      text-align: center;
    }

    .online-rank-row {
      display: flex;
      justify-content: space-between;
      padding: 5px 8px;
      border-bottom: 1px solid #21262d;
      font-size: 0.8rem;
      color: #c9d1d9;
    }

    .online-rank-row:last-child {
      border-bottom: none;
    }
  </style>
</head>
<body>

<div id="game-container">
  <header>
    <h1>⚡ DOPAMINE RUSH</h1>
    <div class="hiscore-bar">👑 HIGH SCORE: <span id="top-hiscore">0</span></div>
    
    <div class="stats-bar">
      <div class="stat-box">
        <div id="score-display" class="stat-value" style="color: #00f2fe;">0</div>
        <div class="stat-label">SCORE</div>
      </div>
      <div class="stat-box">
        <div id="combo-display" class="stat-value" style="color: #ff9900;">0</div>
        <div class="stat-label">COMBO</div>
      </div>
      <div class="stat-box">
        <div id="time-display" class="stat-value" style="color: #ffd700;">20.0</div>
        <div class="stat-label">TIME</div>
      </div>
    </div>
  </header>

  <div id="play-field"></div>

  <div id="start-screen" class="overlay">
    <h2 style="color: #00f2fe; margin-bottom: 5px;">準備OK？</h2>
    <input type="text" id="player-name" class="name-input" placeholder="プレイヤー名を入力" maxlength="10">
    <p style="font-size: 0.8rem; color: #8b949e; text-align: center; line-height: 1.4; margin: 0 0 10px 0;">
      ターゲットを瞬時にタップ！<br>
      🟡 金: ボーナス&タイム回復(+1秒)<br>
      💣 爆弾: コンボ消滅&タイム減算(-2秒)
    </p>
    <button class="btn" id="start-btn">START</button>
  </div>

  <div id="result-screen" class="overlay hidden">
    <h3 style="margin: 0; color: #8b949e;">RESULT</h3>
    <div id="new-record-badge" class="new-record hidden">🎉 NEW HIGH SCORE! 🎉</div>
    <div id="rank-display" class="rank-text">S</div>
    
    <div style="font-size: 1.2rem; margin-bottom: 3px;">
      SCORE: <span id="final-score" style="color: #00f2fe; font-weight: bold;">0</span>
    </div>
    <div style="font-size: 0.85rem; color: #ffd700; margin-bottom: 8px;">
      BEST: <span id="result-hiscore">0</span>
    </div>

    <button class="btn" id="retry-btn">RETRY</button>
    
    <div class="history-box">
      <div>直近の記録:</div>
      <div id="history-list" style="margin-top: 3px; color: #c9d1d9;"></div>
    </div>
  </div>
</div>

<div id="online-ranking-panel">
  <h3>🏆 世界のオンラインランキング</h3>
  <div id="online-ranking-list" style="color: #8b949e; text-align: center; font-size: 0.8rem;">読み込み中...</div>
</div>

<script type="module">
  import { initializeApp } from "https://www.gstatic.com/firebasejs/9.22.0/firebase-app.js";
  import { getFirestore, collection, addDoc, query, orderBy, limit, onSnapshot, serverTimestamp } from "https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore.js";

  const firebaseConfig = {
    apiKey: "AIzaSyDN5Y2t4I18nJPN-xCULDMKwG...",
    authDomain: "adpgjn.firebaseapp.com",
    projectId: "adpgjn",
    storageBucket: "adpgjn.firebasestorage.app",
    messagingSenderId: "693644624750",
    appId: "1:693644624750:web:71986adc378711e9f451f2"
  };

  const app = initializeApp(firebaseConfig);
  const db = getFirestore(app);

  const GAME_TIME = 20.0;
  let score = 0;
  let combo = 0;
  let timeLeft = GAME_TIME;
  let isPlaying = false;
  let timerId = null;
  let targetTimeoutId = null;

  let audioCtx = null;

  const blockBlastScale = [
    261.63, 329.63, 392.00, 523.25, 659.25, 783.99, 1046.50, 1318.51, 1567.98
  ];

  function unlockAudio() {
    if (!audioCtx) {
      audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    }
    if (audioCtx.state === 'suspended') {
      audioCtx.resume();
    }
  }

  function playBlockTapSound(now) {
    const bufferSize = audioCtx.sampleRate * 0.02;
    const buffer = audioCtx.createBuffer(1, bufferSize, audioCtx.sampleRate);
    const data = buffer.getChannelData(0);
    for (let i = 0; i < bufferSize; i++) {
      data[i] = Math.random() * 2 - 1;
    }

    const noise = audioCtx.createBufferSource();
    noise.buffer = buffer;

    const filter = audioCtx.createBiquadFilter();
    filter.type = 'bandpass';
    filter.frequency.setValueAtTime(800, now);
    filter.Q.setValueAtTime(3, now);

    const noiseGain = audioCtx.createGain();
    noiseGain.gain.setValueAtTime(0.35, now);
    noiseGain.gain.exponentialRampToValueAtTime(0.001, now + 0.02);

    noise.connect(filter);
    filter.connect(noiseGain);
    noiseGain.connect(audioCtx.destination);

    noise.start(now);

    const popOsc = audioCtx.createOscillator();
    const popGain = audioCtx.createGain();

    popOsc.type = 'sine';
    popOsc.frequency.setValueAtTime(320, now);
    popOsc.frequency.exponentialRampToValueAtTime(80, now + 0.03);

    popGain.gain.setValueAtTime(0.4, now);
    popGain.gain.exponentialRampToValueAtTime(0.001, now + 0.03);

    popOsc.connect(popGain);
    popGain.connect(audioCtx.destination);

    popOsc.start(now);
    popOsc.stop(now + 0.03);
  }

  function playMarimbaNote(freq, time, volume = 0.25) {
    const osc = audioCtx.createOscillator();
    const gain = audioCtx.createGain();

    osc.type = 'triangle';
    osc.frequency.setValueAtTime(freq, time);

    gain.gain.setValueAtTime(volume, time);
    gain.gain.exponentialRampToValueAtTime(0.0001, time + 0.3);

    osc.connect(gain);
    gain.connect(audioCtx.destination);

    osc.start(time);
    osc.stop(time + 0.3);
  }

  function playSound(type) {
    if (!audioCtx) return;
    if (audioCtx.state === 'suspended') {
      audioCtx.resume();
    }

    const now = audioCtx.currentTime;

    if (type === 'hit') {
      playBlockTapSound(now);

      const baseIdx = Math.min(combo % (blockBlastScale.length - 2), blockBlastScale.length - 3);
      const noteCount = Math.min(3 + Math.floor(combo / 2), 5);

      for (let i = 0; i < noteCount; i++) {
        const freq = blockBlastScale[baseIdx + (i % 3)];
        playMarimbaNote(freq * (1 + Math.floor(i / 3) * 0.5), now + 0.02 + i * 0.04, 0.22);
      }

    } else if (type === 'gold') {
      playBlockTapSound(now);
      [1046.50, 1318.51, 1567.98, 2093.00].forEach((freq, i) => {
        playMarimbaNote(freq, now + 0.02 + i * 0.05, 0.3);
      });

    } else if (type === 'bomb') {
      const osc = audioCtx.createOscillator();
      const gain = audioCtx.createGain();
      osc.type = 'triangle';
      osc.frequency.setValueAtTime(140, now);
      osc.frequency.exponentialRampToValueAtTime(50, now + 0.18);
      gain.gain.setValueAtTime(0.4, now);
      gain.gain.exponentialRampToValueAtTime(0.001, now + 0.18);
      osc.connect(gain);
      gain.connect(audioCtx.destination);
      osc.start(now);
      osc.stop(now + 0.18);

    } else if (type === 'finish') {
      [523.25, 659.25, 783.99, 1046.50].forEach((freq, i) => {
        playMarimbaNote(freq, now + i * 0.08, 0.3);
      });
    }
  }

  const container = document.getElementById('game-container');
  const field = document.getElementById('play-field');
  const scoreDisplay = document.getElementById('score-display');
  const comboDisplay = document.getElementById('combo-display');
  const timeDisplay = document.getElementById('time-display');
  const topHiscore = document.getElementById('top-hiscore');
  
  const startScreen = document.getElementById('start-screen');
  const resultScreen = document.getElementById('result-screen');
  const finalScoreDisplay = document.getElementById('final-score');
  const resultHiscore = document.getElementById('result-hiscore');
  const rankDisplay = document.getElementById('rank-display');
  const newRecordBadge = document.getElementById('new-record-badge');
  const historyList = document.getElementById('history-list');
  const playerNameInput = document.getElementById('player-name');
  const onlineRankingList = document.getElementById('online-ranking-list');

  document.getElementById('start-btn').addEventListener('pointerdown', () => {
    unlockAudio();
    startGame();
  });

  document.getElementById('retry-btn').addEventListener('pointerdown', () => {
    unlockAudio();
    startGame();
  });

  updateHighScoreDisplay();

  function updateHighScoreDisplay() {
    const high = localStorage.getItem('dr_highscore') || '0';
    topHiscore.textContent = parseInt(high, 10).toLocaleString();
    resultHiscore.textContent = parseInt(high, 10).toLocaleString();
  }

  function startGame() {
    const playerName = playerNameInput.value.trim() || '名無しさん';
    localStorage.setItem('dr_player_name', playerName);

    score = 0;
    combo = 0;
    timeLeft = GAME_TIME;
    isPlaying = true;

    scoreDisplay.textContent = '0';
    comboDisplay.textContent = '0';
    timeDisplay.textContent = GAME_TIME.toFixed(1);
    timeDisplay.classList.remove('spurt');
    newRecordBadge.classList.add('hidden');

    startScreen.classList.add('hidden');
    resultScreen.classList.add('hidden');

    let lastTime = performance.now();
    timerId = setInterval(() => {
      const now = performance.now();
      timeLeft -= (now - lastTime) / 1000;
      lastTime = now;

      if (timeLeft <= 0) {
        timeLeft = 0;
        endGame(playerName);
      }

      if (timeLeft <= 5.0 && !timeDisplay.classList.contains('spurt')) {
        timeDisplay.classList.add('spurt');
      }

      timeDisplay.textContent = Math.max(0, timeLeft).toFixed(1);
    }, 50);

    spawnTarget();
  }

  function spawnTarget() {
    if (!isPlaying) return;
    field.innerHTML = '';

    const target = document.createElement('div');
    const size = 65;
    const maxX = field.clientWidth - size;
    const maxY = field.clientHeight - size;

    const x = Math.floor(Math.random() * maxX);
    const y = Math.floor(Math.random() * maxY);

    const rand = Math.random();
    let type = 'normal';
    if (rand < 0.15) type = 'gold';
    else if (rand < 0.30) type = 'bomb';

    target.className = `target target-${type}`;
    target.style.width = `${size}px`;
    target.style.height = `${size}px`;
    target.style.left = `${x}px`;
    target.style.top = `${y}px`;

    if (type === 'normal') {
      const hue = Math.floor(Math.random() * 360);
      target.style.background = `radial-gradient(circle at 35% 35%, hsl(${hue}, 100%, 80%), hsl(${hue}, 100%, 50%) 60%, hsl(${hue}, 100%, 30%) 100%)`;
      target.style.boxShadow = `
        inset -5px -5px 12px rgba(0, 0, 0, 0.6),
        inset 5px 5px 10px rgba(255, 255, 255, 0.8),
        0 0 15px hsl(${hue}, 100%, 50%),
        0 8px 15px rgba(0, 0, 0, 0.5)
      `;
      target.style.color = '#fff';
      target.style.textShadow = '0 1px 3px rgba(0,0,0,0.8)';
      target.textContent = 'TAP';
    } else if (type === 'gold') {
      target.textContent = '+TIME';
    } else if (type === 'bomb') {
      target.textContent = '💣';
    }

    target.addEventListener('pointerdown', (e) => {
      e.stopPropagation();
      unlockAudio();
      handleHit(type);
    });

    field.appendChild(target);

    const speed = Math.max(400, 900 - combo * 15);
    targetTimeoutId = setTimeout(() => {
      if (isPlaying) {
        if (type !== 'bomb') combo = 0;
        updateUI();
        spawnTarget();
      }
    }, speed);
  }

  function handleHit(type) {
    clearTimeout(targetTimeoutId);

    if (type === 'bomb') {
      combo = 0;
      timeLeft = Math.max(0, timeLeft - 2.0);
      playSound('bomb');
      triggerVibrate([100, 50, 100]);
      triggerShake();
    } else {
      combo++;
      let gained = (type === 'gold') ? 500 : 100;
      score += gained + combo * 20;

      if (type === 'gold') {
        timeLeft = Math.min(GAME_TIME, timeLeft + 1.0);
        playSound('gold');
        triggerVibrate(80);
      } else {
        playSound('hit');
        triggerVibrate(30);
      }

      comboDisplay.classList.remove('combo-bump');
      void comboDisplay.offsetWidth;
      comboDisplay.classList.add('combo-bump');
    }

    updateUI();
    spawnTarget();
  }

  function updateUI() {
    scoreDisplay.textContent = score.toLocaleString();
    comboDisplay.textContent = combo;
  }

  function triggerShake() {
    container.classList.remove('shake');
    void container.offsetWidth;
    container.classList.add('shake');
  }

  function triggerVibrate(pattern) {
    if ('vibrate' in navigator) navigator.vibrate(pattern);
  }

  async function endGame(playerName) {
    isPlaying = false;
    clearInterval(timerId);
    clearTimeout(targetTimeoutId);
    field.innerHTML = '';

    playSound('finish');
    triggerVibrate([150, 100, 150]);

    const isNewRecord = saveRecord(score);
    if (isNewRecord) {
      newRecordBadge.classList.remove('hidden');
    }

    const rank = getRank(score);
    finalScoreDisplay.textContent = score.toLocaleString();
    rankDisplay.textContent = rank;
    
    updateHighScoreDisplay();
    renderHistory();

    resultScreen.classList.remove('hidden');

    try {
      await addDoc(collection(db, "rankings"), {
        name: playerName,
        score: score,
        createdAt: serverTimestamp()
      });
    } catch (e) {
      console.error("スコア送信エラー: ", e);
    }
  }

  function getRank(s) {
    if (s >= 12000) return 'SSS';
    if (s >= 8000)  return 'S';
    if (s >= 5000)  return 'A';
    if (s >= 3000)  return 'B';
    return 'C';
  }

  function saveRecord(s) {
    const high = parseInt(localStorage.getItem('dr_highscore') || '0', 10);
    let isNew = false;
    if (s > high) {
      localStorage.setItem('dr_highscore', s.toString());
      isNew = true;
    }

    const history = JSON.parse(localStorage.getItem('dr_history') || '[]');
    history.unshift(s);
    if (history.length > 3) history.pop();
    localStorage.setItem('dr_history', JSON.stringify(history));

    return isNew;
  }

  function renderHistory() {
    const history = JSON.parse(localStorage.getItem('dr_history') || '[]');
    historyList.innerHTML = history.map((sc, i) => `<div>#${i+1} : ${sc.toLocaleString()} pt</div>`).join('');
  }

  const savedName = localStorage.getItem('dr_player_name');
  if (savedName) playerNameInput.value = savedName;

  const q = query(collection(db, "rankings"), orderBy("score", "desc"), limit(5));
  onSnapshot(q, (snapshot) => {
    let html = "";
    let rank = 1;
    snapshot.forEach((doc) => {
      const data = doc.data();
      html += `<div class="online-rank-row"><span><b>#${rank}</b> ${escapeHtml(data.name)}</span><span><b>${data.score.toLocaleString()}</b> pt</span></div>`;
      rank++;
    });
    onlineRankingList.innerHTML = html || "まだ記録はありません";
  });

  function escapeHtml(str) {
    return str.replace(/[&'`<>"]/g, (match) => ({
      '&': '&amp;', '\'': '&#x27;', '`': '&#x60;', '<': '&lt;', '>': '&gt;', '"': '&quot;'
    }[match]));
  }
</script>
</body>
</html>
