<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
  <title>DOPAMINE RUSH - オンラインランキング対応</title>
  <style>
    * { box-sizing: border-box; user-select: none; -webkit-user-select: none; touch-action: manipulation; }
    body {
      margin: 0; padding: 0; background-color: #0d0f12; color: #ffffff;
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      display: flex; flex-direction: column; align-items: center; justify-content: flex-start;
      min-height: 100vh; overflow-y: auto; padding-bottom: 20px;
    }
    #game-container {
      width: 100%; max-width: 420px; background: #161b22; border: 2px solid #30363d;
      border-radius: 16px; display: flex; flex-direction: column; position: relative;
      overflow: hidden; box-shadow: 0 10px 30px rgba(0,0,0,0.8); margin-top: 10px;
    }
    .shake { animation: shake-anim 0.2s ease-in-out; }
    @keyframes shake-anim {
      0% { transform: translate(0, 0); }
      25% { transform: translate(-8px, 5px); }
      50% { transform: translate(8px, -5px); }
      75% { transform: translate(-5px, -5px); }
      100% { transform: translate(0, 0); }
    }
    header { padding: 10px 15px; text-align: center; background: rgba(0, 0, 0, 0.4); border-bottom: 1px solid #30363d; }
    h1 { margin: 0 0 5px 0; font-size: 1.3rem; letter-spacing: 2px; color: #00f2fe; text-shadow: 0 0 10px rgba(0, 242, 254, 0.6); }
    .stats-bar { display: flex; justify-content: space-around; align-items: center; margin-top: 5px; }
    .stat-box { display: flex; flex-direction: column; align-items: center; }
    .stat-value { font-size: 1.2rem; font-weight: bold; font-family: monospace; }
    .stat-label { font-size: 0.65rem; color: #8b949e; }
    #time-display.spurt { color: #ff0055; animation: pulse 0.4s infinite alternate; text-shadow: 0 0 12px #ff0055; }
    @keyframes pulse { 0% { transform: scale(1); } 100% { transform: scale(1.25); } }
    #play-field { width: 100%; height: 320px; position: relative; background: radial-gradient(circle, #1a2332 0%, #0d0f12 100%); }
    .target {
      position: absolute; border-radius: 50%; cursor: pointer; display: flex;
      align-items: center; justify-content: center; font-weight: 900; font-size: 0.85rem;
      transform: scale(0); animation: pop-in 0.15s forwards ease-out;
      border: 2px solid rgba(255, 255, 255, 0.6);
      box-shadow: inset -5px -5px 12px rgba(0, 0, 0, 0.6), inset 5px 5px 10px rgba(255, 255, 255, 0.8), 0 8px 15px rgba(0, 0, 0, 0.5);
    }
    @keyframes pop-in { to { transform: scale(1); } }
    .target-gold { background: radial-gradient(circle at 35% 35%, #ffffff, #ffd700 60%, #b8860b 100%); color: #3d2b00; }
    .target-bomb { background: radial-gradient(circle at 35% 35%, #ff7799, #ff0055 60%, #880022 100%); color: #fff; }
    .overlay {
      position: absolute; inset: 0; background: rgba(13, 15, 18, 0.95);
      display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 20px; z-index: 10;
    }
    .hidden { display: none !important; }
    .btn {
      background: linear-gradient(135deg, #00f2fe, #4facfe); border: none; color: #000;
      padding: 10px 28px; font-size: 1rem; font-weight: bold; border-radius: 30px; cursor: pointer;
      box-shadow: 0 0 15px rgba(0, 242, 254, 0.4); margin-top: 10px;
    }
    input[type="text"] {
      padding: 8px 12px; border-radius: 8px; border: 1px solid #30363d; background: #0d0f12;
      color: #fff; font-size: 1rem; text-align: center; margin-bottom: 10px; width: 80%; max-width: 200px;
    }
    /* オンラインランキング表示エリアのデザイン */
    #online-ranking {
      width: 100%; max-width: 420px; background: #161b22; border: 2px solid #30363d;
      border-radius: 16px; padding: 15px; margin-top: 15px; box-shadow: 0 10px 30px rgba(0,0,0,0.8);
    }
    #online-ranking h3 { margin: 0 0 10px 0; font-size: 1rem; color: #ffd700; text-align: center; }
    .rank-row { display: flex; justify-content: space-between; padding: 6px 10px; border-bottom: 1px solid #21262d; font-size: 0.85rem; }
    .rank-row:last-child { border-bottom: none; }
  </style>
</head>
<body>

<div id="game-container">
  <header>
    <h1>⚡ DOPAMINE RUSH</h1>
    <div class="stats-bar">
      <div class="stat-box"><div id="score-display" class="stat-value" style="color: #00f2fe;">0</div><div class="stat-label">SCORE</div></div>
      <div class="stat-box"><div id="combo-display" class="stat-value" style="color: #ff9900;">0</div><div class="stat-label">COMBO</div></div>
      <div class="stat-box"><div id="time-display" class="stat-value" style="color: #ffd700;">20.0</div><div class="stat-label">TIME</div></div>
    </div>
  </header>

  <div id="play-field"></div>

  <!-- スタート画面 -->
  <div id="start-screen" class="overlay">
    <h2 style="color: #00f2fe; margin-bottom: 5px;">オンライン対戦準備OK？</h2>
    <input type="text" id="player-name" placeholder="プレイヤー名を入力" maxlength="10">
    <p style="font-size: 0.75rem; color: #8b949e; text-align: center; line-height: 1.4; margin: 5px 0 15px 0;">
      🟡 金: タイム回復(+1秒)<br>💣 爆弾: タイム減算(-2秒)
    </p>
    <button class="btn" id="start-btn">START</button>
  </div>

  <!-- リザルト画面 -->
  <div id="result-screen" class="overlay hidden">
    <h3 style="margin: 0 0 5px 0; color: #8b949e;">RESULT</h3>
    <div style="font-size: 1.5rem; margin-bottom: 10px;">
      SCORE: <span id="final-score" style="color: #00f2fe; font-weight: bold;">0</span>
    </div>
    <button class="btn" id="retry-btn">もう一度プレイ</button>
  </div>
</div>

<!-- 世界中の人と競えるオンラインランキング表示エリア -->
<div id="online-ranking">
  <h3>🏆 世界のオンラインランキング</h3>
  <div id="ranking-list" style="color: #8b949e; text-align: center; font-size: 0.85rem;">読み込み中...</div>
</div>

<!-- Firebase SDKの読み込み -->
<script type="module">
  import { initializeApp } from "https://www.gstatic.com/firebasejs/9.22.0/firebase-app.js";
  import { getFirestore, collection, addDoc, query, orderBy, limit, onSnapshot, serverTimestamp } from "https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore.js";

  // ★あなたのFirebase設定（鍵）をここにセットしています
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
  let score = 0, combo = 0, timeLeft = GAME_TIME, isPlaying = false;
  let timerId = null, targetTimeoutId = null;
  let audioCtx = null;

  const field = document.getElementById('play-field');
  const scoreDisplay = document.getElementById('score-display');
  const comboDisplay = document.getElementById('combo-display');
  const timeDisplay = document.getElementById('time-display');
  const startScreen = document.getElementById('start-screen');
  const resultScreen = document.getElementById('result-screen');
  const finalScoreDisplay = document.getElementById('final-score');
  const playerNameInput = document.getElementById('player-name');
  const rankingList = document.getElementById('ranking-list');

  function unlockAudio() {
    if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    if (audioCtx.state === 'suspended') audioCtx.resume();
  }

  document.getElementById('start-btn').addEventListener('pointerdown', () => {
    unlockAudio();
    startGame();
  });

  document.getElementById('retry-btn').addEventListener('pointerdown', () => {
    resultScreen.classList.add('hidden');
    startScreen.classList.remove('hidden');
  });

  function startGame() {
    const name = playerNameInput.value.trim() || '名無しさん';
    localStorage.setItem('dr_player_name', name);

    score = 0; combo = 0; timeLeft = GAME_TIME; isPlaying = true;
    scoreDisplay.textContent = '0';
    comboDisplay.textContent = '0';
    timeDisplay.textContent = GAME_TIME.toFixed(1);
    timeDisplay.classList.remove('spurt');

    startScreen.classList.add('hidden');
    resultScreen.classList.add('hidden');

    let lastTime = performance.now();
    timerId = setInterval(() => {
      const now = performance.now();
      timeLeft -= (now - lastTime) / 1000;
      lastTime = now;

      if (timeLeft <= 0) {
        timeLeft = 0;
        endGame(name);
      }
      if (timeLeft <= 5.0) timeDisplay.classList.add('spurt');
      timeDisplay.textContent = Math.max(0, timeLeft).toFixed(1);
    }, 50);

    spawnTarget();
  }

  function spawnTarget() {
    if (!isPlaying) return;
    field.innerHTML = '';

    const target = document.createElement('div');
    const size = 60;
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
      target.style.background = `radial-gradient(circle at 35% 35%, hsl(${hue}, 100%, 80%), hsl(${hue}, 100%, 50%) 60%)`;
      target.textContent = 'TAP';
    } else if (type === 'gold') {
      target.textContent = '+TIME';
    } else if (type === 'bomb') {
      target.textContent = '💣';
    }

    target.addEventListener('pointerdown', (e) => {
      e.stopPropagation();
      handleHit(type);
    });

    field.appendChild(target);

    const speed = Math.max(400, 900 - combo * 15);
    targetTimeoutId = setTimeout(() => {
      if (isPlaying) {
        if (type !== 'bomb') combo = 0;
        comboDisplay.textContent = combo;
        spawnTarget();
      }
    }, speed);
  }

  function handleHit(type) {
    clearTimeout(targetTimeoutId);
    if (type === 'bomb') {
      combo = 0;
      timeLeft = Math.max(0, timeLeft - 2.0);
    } else {
      combo++;
      let gained = (type === 'gold') ? 500 : 100;
      score += gained + combo * 20;
      if (type === 'gold') timeLeft = Math.min(GAME_TIME, timeLeft + 1.0);
    }
    scoreDisplay.textContent = score.toLocaleString();
    comboDisplay.textContent = combo;
    spawnTarget();
  }

  async function endGame(playerName) {
    isPlaying = false;
    clearInterval(timerId);
    clearTimeout(targetTimeoutId);
    field.innerHTML = '';

    finalScoreDisplay.textContent = score.toLocaleString();
    resultScreen.classList.remove('hidden');

    // ★Firebaseへスコアを送信する処理
    try {
      await addDoc(collection(db, "rankings"), {
        name: playerName,
        score: score,
        createdAt: serverTimestamp()
      });
    } catch (e) {
      console.error("ランキング送信エラー: ", e);
    }
  }

  // ★リアルタイムでランキングを取得して画面に反映する処理
  const q = query(collection(db, "rankings"), orderBy("score", "desc"), limit(5));
  onSnapshot(q, (snapshot) => {
    let html = "";
    let rank = 1;
    snapshot.forEach((doc) => {
      const data = doc.data();
      html += `<div class="rank-row"><span><b>#${rank}</b> ${escapeHtml(data.name)}</span><span><b>${data.score.toLocaleString()}</b> pt</span></div>`;
      rank++;
    });
    rankingList.innerHTML = html || "まだ記録はありません";
  });

  function escapeHtml(str) {
    return str.replace(/[&'`<>"]/g, (match) => ({
      '&': '&amp;', '\'': '&#x27;', '`': '&#x60;', '<': '&lt;', '>': '&gt;', '"': '&quot;'
    }[match]));
  }

  // 保存されていた名前があれば自動入力
  const savedName = localStorage.getItem('dr_player_name');
  if (savedName) playerNameInput.value = savedName;
</script>

</body>
</html>
