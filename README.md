<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Dopamine Rush - Online Ranking</title>
  <style>
    body { background: #0d0d1a; color: #fff; font-family: sans-serif; text-align: center; margin: 0; padding: 10px; display: flex; flex-direction: column; align-items: center; height: 100vh; overflow-y: auto; }
    h1 { font-size: 20px; margin: 10px 0; background: linear-gradient(45deg, #ff007f, #00ffff); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
    .hud { display: flex; gap: 20px; margin-bottom: 10px; font-size: 16px; }
    .grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; width: 100%; max-width: 300px; aspect-ratio: 1; margin-bottom: 10px; }
    .panel { background: #16213e; border: 2px solid #0f3460; border-radius: 12px; display: flex; align-items: center; justify-content: center; cursor: pointer; font-size: 24px; font-weight: bold; }
    .panel.active { background: #ff007f; box-shadow: 0 0 15px #ff007f; }
    input { padding: 8px; border-radius: 5px; border: none; margin-bottom: 10px; width: 80%; max-width: 200px; text-align: center; }
    button { padding: 8px 20px; background: #ff007f; color: #fff; border: none; border-radius: 5px; font-weight: bold; cursor: pointer; margin-bottom: 15px; }
    button:disabled { background: #555; cursor: not-allowed; }
    #ranking { width: 100%; max-width: 300px; background: #1a1a2e; padding: 10px; border-radius: 10px; text-align: left; font-size: 14px; }
    .rank-item { display: flex; justify-content: space-between; margin-bottom: 5px; border-bottom: 1px solid #2a2a4e; padding-bottom: 3px; }
  </style>
</head>
<body>

  <h1>⚡ DOPAMINE RUSH ⚡</h1>
  <div class="hud">
    <div>スコア: <span id="score">0</span></div>
    <div>残り時間: <span id="time">15</span>秒</div>
  </div>
  
  <input type="text" id="nameInput" placeholder="プレイヤー名を入力">
  <br>
  <button id="startBtn">スタート！</button>

  <div class="grid" id="grid"></div>

  <h3>🏆 オンラインランキング</h3>
  <div id="ranking">ロード中...</div>

  <script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/9.22.0/firebase-app.js";
    import { getFirestore, collection, addDoc, query, orderBy, limit, onSnapshot, serverTimestamp } from "https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore.js";

    // あなたのFirebase設定
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

    const grid = document.getElementById('grid');
    const scoreDisplay = document.getElementById('score');
    const timeDisplay = document.getElementById('time');
    const startBtn = document.getElementById('startBtn');
    const nameInput = document.getElementById('nameInput');
    const rankingDiv = document.getElementById('ranking');

    let score = 0;
    let timeLeft = 15;
    let timer = null;
    let activeIndex = null;
    let gamePlaying = false;
    let panels = [];

    // パネルを9個生成
    for (let i = 0; i < 9; i++) {
        const p = document.createElement('div');
        p.className = 'panel';
        p.addEventListener('click', () => {
            if (!gamePlaying) return;
            if (i === activeIndex) {
                score += 10;
                scoreDisplay.textContent = score;
                activateRandomPanel(); // すぐ次のパネルへ
            }
        });
        grid.appendChild(p);
        panels.push(p);
    }

    function activateRandomPanel() {
        if (activeIndex !== null) {
            panels[activeIndex].classList.remove('active');
        }
        let nextIndex;
        do {
            nextIndex = Math.floor(Math.random() * 9);
        } while (nextIndex === activeIndex);
        
        activeIndex = nextIndex;
        panels[activeIndex].classList.add('active');
    }

    // スタートボタンを押したときの処理
    startBtn.addEventListener('click', async () => {
        const playerName = nameInput.value.trim();
        if (!playerName) {
            alert('名前を入力してください！');
            return;
        }

        // 初期化
        score = 0;
        timeLeft = 15;
        scoreDisplay.textContent = score;
        timeDisplay.textContent = timeLeft;
        startBtn.disabled = true;
        nameInput.disabled = true;
        gamePlaying = true;

        activateRandomPanel();

        // タイマー開始
        timer = setInterval(async () => {
            timeLeft--;
            timeDisplay.textContent = timeLeft;

            if (timeLeft <= 0) {
                clearInterval(timer);
                if (activeIndex !== null) {
                    panels[activeIndex].classList.remove('active');
                    activeIndex = null;
                }
                gamePlaying = false;
                startBtn.disabled = false;
                nameInput.disabled = false;

                alert(`ゲーム終了！スコアは ${score} 点でした！`);

                // Firebaseにスコアを送信
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
        }, 1000);
    });

    // リアルタイムランキングの取得
    const q = query(collection(db, "rankings"), orderBy("score", "desc"), limit(5));
    onSnapshot(q, (snapshot) => {
        let html = "";
        let rank = 1;
        snapshot.forEach((doc) => {
            const data = doc.data();
            html += `<div class="rank-item"><span>${rank}. ${data.name}</span><span>${data.score}点</span></div>`;
            rank++;
        });
        rankingDiv.innerHTML = html || "まだ記録はありません";
    });
  </script>
</body>
</html>
