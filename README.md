<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>SEMAKA Jackpot</title>
    <style>
        :root {
            --bg: #0a0a12; --card: #14141f; --accent: #b8a9ff; --accent2: #7c6fd4;
            --text: #e0e0e8; --sub: #666680; --green: #5ee7a0; --red: #f87171;
            --gold: #f0b90b; --radius: 16px; --border: #1e1e2e;
        }
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
            background: var(--bg); color: var(--text); min-height: 100vh;
            display: flex; justify-content: center; user-select: none;
            -webkit-tap-highlight-color: transparent; overflow: hidden;
        }
        .app { width: 100%; max-width: 420px; min-height: 100vh; display: flex; flex-direction: column; padding: 12px; }

        /* Верхняя панель */
        .top-bar { display: flex; align-items: center; gap: 10px; padding: 4px 0 8px; }
        .balance-badge {
            background: #14141f; border: 1px solid var(--border); border-radius: 20px;
            padding: 6px 14px; display: flex; align-items: center; gap: 6px; font-size: 13px;
        }
        .balance-badge svg { width: 16px; height: 16px; }
        .balance-amount { color: var(--gold); font-weight: 700; }
        .deposit-btn {
            margin-left: auto; background: var(--gold); color: #000; border: none;
            padding: 7px 16px; border-radius: 18px; font-weight: 700; font-size: 12px;
            cursor: pointer; white-space: nowrap;
        }
        .deposit-btn:active { opacity: 0.8; }

        /* Комнаты */
        .rooms { display: flex; gap: 6px; margin-bottom: 10px; }
        .room-tab {
            flex: 1; background: #10101a; border: 1px solid var(--border); color: #555;
            padding: 8px 4px; border-radius: 12px; font-size: 10px; cursor: pointer;
            text-align: center; transition: 0.2s; font-weight: 600;
        }
        .room-tab.active { background: #1a1a2e; color: var(--accent); border-color: var(--accent2); }
        .room-tab .limits { font-size: 8px; color: #555; margin-top: 2px; }
        .room-tab.active .limits { color: #888; }

        /* Заголовок джекпота */
        .jackpot-header { text-align: center; margin-bottom: 8px; }
        .jackpot-title { font-size: 18px; font-weight: 800; color: #fff; }
        .jackpot-subtitle { font-size: 11px; color: var(--sub); }
        .jackpot-bank { font-size: 26px; font-weight: 900; color: var(--gold); margin: 4px 0; }
        .jackpot-players { font-size: 11px; color: var(--sub); }

        /* Таймер */
        .timer-bar {
            background: #10101a; border-radius: 12px; height: 6px; margin: 10px 0;
            overflow: hidden; position: relative;
        }
        .timer-fill { height: 100%; background: var(--accent2); border-radius: 12px; transition: width 1s linear; }
        .timer-text { text-align: center; font-size: 11px; color: var(--sub); margin-bottom: 8px; }

        /* Лента аватарок */
        .slot-container {
            position: relative; width: 100%; height: 100px;
            overflow: hidden; margin: 8px 0;
        }
        .slot-pointer {
            position: absolute; top: 0; bottom: 0; left: 50%;
            transform: translateX(-50%); width: 3px; z-index: 10;
            background: var(--accent);
            box-shadow: 0 0 15px 3px rgba(184,169,255,0.6), 0 0 40px 8px rgba(184,169,255,0.3);
            border-radius: 2px;
        }
        .slot-pointer::after {
            content: ''; position: absolute; top: -6px; left: -5px;
            border-left: 7px solid transparent; border-right: 7px solid transparent;
            border-top: 10px solid var(--accent);
            filter: drop-shadow(0 0 6px rgba(184,169,255,0.8));
        }
        .slot-pointer::before {
            content: ''; position: absolute; bottom: -6px; left: -5px;
            border-left: 7px solid transparent; border-right: 7px solid transparent;
            border-bottom: 10px solid var(--accent);
            filter: drop-shadow(0 0 6px rgba(184,169,255,0.8));
        }
        .slot-track {
            display: flex; gap: 8px; position: absolute; left: 0; top: 8px;
            transition: transform 0.1s ease;
            will-change: transform;
        }
        .slot-item {
            flex-shrink: 0; width: 64px; height: 84px;
            display: flex; flex-direction: column; align-items: center; gap: 4px;
            transition: transform 0.2s;
        }
        .slot-avatar {
            width: 44px; height: 44px; border-radius: 50%;
            display: flex; align-items: center; justify-content: center;
            font-size: 14px; font-weight: 700; color: #fff;
            flex-shrink: 0;
        }
        .slot-chance { font-size: 9px; color: var(--sub); text-align: center; }
        .slot-amount { font-size: 8px; color: #555; }

        /* Блок ставки */
        .bet-area {
            display: flex; gap: 8px; align-items: center; margin-top: 8px;
        }
        .bet-input {
            flex: 1; background: #0f0f1a; border: 1px solid var(--border);
            color: #fff; padding: 10px 14px; border-radius: 12px; font-size: 14px;
            text-align: center;
        }
        .bet-input:focus { outline: none; border-color: var(--accent2); }
        .bet-btn {
            background: var(--accent2); color: #fff; border: none;
            padding: 10px 18px; border-radius: 12px; font-weight: 700; font-size: 18px;
            cursor: pointer; transition: 0.2s;
        }
        .bet-btn:active { background: #6a5fc0; }

        /* Инфо о ставке */
        .my-bet-info {
            display: flex; justify-content: space-between; font-size: 10px;
            color: var(--sub); margin-top: 4px; padding: 0 4px;
        }
        .my-bet-info span { color: #999; }

        /* Нижняя навигация */
        .nav {
            position: fixed; bottom: 0; left: 50%; transform: translateX(-50%);
            width: 100%; max-width: 420px; background: #0d0d18;
            display: flex; justify-content: space-around; padding: 6px 8px 18px;
            border-top: 1px solid var(--border); z-index: 100;
        }
        .nav-item {
            display: flex; flex-direction: column; align-items: center; gap: 2px;
            font-size: 9px; color: #444; cursor: pointer; transition: 0.2s;
            background: none; border: none; padding: 4px 8px; border-radius: 10px;
        }
        .nav-item.active { color: var(--accent); }
        .nav-icon { width: 20px; height: 20px; }
        .nav-icon svg { width: 100%; height: 100%; }
        .nav-item.active .nav-icon svg path,
        .nav-item.active .nav-icon svg circle,
        .nav-item.active .nav-icon svg rect { stroke: var(--accent); }

        .winner-flash { animation: flashGold 0.6s ease 3; }
        @keyframes flashGold {
            0%, 100% { background: transparent; }
            50% { background: rgba(240,185,11,0.15); }
        }
    </style>
</head>
<body>
    <div class="app" id="appContainer">
        <!-- Верхняя панель -->
        <div class="top-bar">
            <div class="balance-badge">
                <svg viewBox="0 0 24 24" fill="none" stroke="#f0b90b" stroke-width="2"><circle cx="12" cy="12" r="10"/><circle cx="12" cy="12" r="3"/></svg>
                <span class="balance-amount" id="balanceDisplay">0.0</span>
            </div>
            <button class="deposit-btn" onclick="alert('Пополнение')">Пополнить</button>
        </div>

        <!-- Комнаты -->
        <div class="rooms">
            <button class="room-tab active" onclick="switchRoom('bomj')">Бомж<div class="limits">1 – 50</div></button>
            <button class="room-tab" onclick="switchRoom('classic')">Классик<div class="limits">10 – 500</div></button>
            <button class="room-tab" onclick="switchRoom('major')">Мажор<div class="limits">250 – 5000</div></button>
            <button class="room-tab" onclick="switchRoom('hyena')">Гиена<div class="limits">5000 – 25000</div></button>
        </div>

        <!-- Джекпот -->
        <div class="jackpot-header">
            <div class="jackpot-title" id="roomTitle">Бомж</div>
            <div class="jackpot-subtitle">Общий банк</div>
            <div class="jackpot-bank" id="totalBank">0</div>
            <div class="jackpot-players" id="playerCount">Участников: 0</div>
        </div>

        <!-- Таймер -->
        <div class="timer-bar"><div class="timer-fill" id="timerFill" style="width: 100%"></div></div>
        <div class="timer-text" id="timerText">До розыгрыша: 0:30</div>

        <!-- Лента -->
        <div class="slot-container" id="slotContainer">
            <div class="slot-pointer"></div>
            <div class="slot-track" id="slotTrack"></div>
        </div>

        <!-- Блок ставки -->
        <div class="bet-area">
            <input class="bet-input" type="number" id="betAmount" placeholder="Сумма" min="1" inputmode="decimal">
            <button class="bet-btn" onclick="placeBet()">→</button>
        </div>
        <div class="my-bet-info">
            <span>Ставка: <strong id="myBetAmount">0</strong></span>
            <span>Шанс: <strong id="myChance">0%</strong></span>
        </div>

        <!-- Нижняя навигация -->
        <div class="nav">
            <button class="nav-item active" onclick="switchPage('jackpot')">
                <span class="nav-icon"><svg viewBox="0 0 24 24" fill="none" stroke="#555" stroke-width="2"><circle cx="12" cy="12" r="10"/><path d="M12 2v20"/></svg></span>Джекпот
            </button>
            <button class="nav-item" onclick="switchPage('tasks')">
                <span class="nav-icon"><svg viewBox="0 0 24 24" fill="none" stroke="#555" stroke-width="2"><rect x="3" y="3" width="18" height="18" rx="3"/><line x1="9" y1="9" x2="15" y2="9"/></svg></span>Задания
            </button>
            <button class="nav-item" onclick="switchPage('ref')">
                <span class="nav-icon"><svg viewBox="0 0 24 24" fill="none" stroke="#555" stroke-width="2"><circle cx="12" cy="8" r="4"/><path d="M4 20c0-4 4-7 8-7s8 3 8 7"/></svg></span>Друзья
            </button>
            <button class="nav-item" onclick="switchPage('top')">
                <span class="nav-icon"><svg viewBox="0 0 24 24" fill="none" stroke="#555" stroke-width="2"><polyline points="6 9 12 4 18 9"/><line x1="12" y1="4" x2="12" y2="20"/></svg></span>Топ
            </button>
            <button class="nav-item" onclick="switchPage('profile')">
                <span class="nav-icon"><svg viewBox="0 0 24 24" fill="none" stroke="#555" stroke-width="2"><circle cx="12" cy="8" r="4"/><path d="M4 20c0-4 4-7 8-7s8 3 8 7"/></svg></span>Профиль
            </button>
        </div>
    </div>

    <script>
        // Данные
        const ROOMS = {
            bomj: { name: 'Бомж', min: 1, max: 50, bank: 0, players: [], timer: 30, maxTimer: 30 },
            classic: { name: 'Классик', min: 10, max: 500, bank: 0, players: [], timer: 30, maxTimer: 30 },
            major: { name: 'Мажор', min: 250, max: 5000, bank: 0, players: [], timer: 30, maxTimer: 30 },
            hyena: { name: 'Гиена', min: 5000, max: 25000, bank: 0, players: [], timer: 30, maxTimer: 30 }
        };

        const AVATAR_COLORS = ['#f87171','#fb923c','#fbbf24','#a3e635','#34d399','#22d3ee','#60a5fa','#a78bfa','#f472b6','#e879f9','#fb7185','#38bdf8','#4ade80','#facc15','#c084fc'];
        const BOT_NAMES = ['CryptoKid','TapMonster','CoinHunter','GoldDigger','LuckyDuck','TonRider','SemakaFan','MegaTap','FastClick','BigWin','StarPlayer','NeonWolf','PixelKing','RocketBoy','DreamCatcher'];

        let currentRoom = 'bomj';
        let roomData = JSON.parse(JSON.stringify(ROOMS));
        let userBalance = 500;
        let myBet = 0;
        let isSpinning = false;
        let timerInterval = null;
        let spinAnimation = null;

        // Загрузка сохранений
        function loadData() {
            const saved = localStorage.getItem('semaka_jackpot');
            if (saved) {
                try {
                    const d = JSON.parse(saved);
                    userBalance = d.balance ?? 500;
                    myBet = d.myBet ?? 0;
                    if (d.rooms) roomData = d.rooms;
                } catch(e) {}
            }
            // Генерация ботов
            for (let room in roomData) {
                if (roomData[room].players.length === 0) generateBots(room);
            }
        }

        function saveData() {
            localStorage.setItem('semaka_jackpot', JSON.stringify({
                balance: userBalance,
                myBet: myBet,
                rooms: roomData
            }));
        }

        function generateBots(room) {
            const r = roomData[room];
            const count = 4 + Math.floor(Math.random() * 6);
            r.bank = 0;
            r.players = [];
            for (let i = 0; i < count; i++) {
                const bet = r.min + Math.floor(Math.random() * (r.max - r.min));
                r.players.push({
                    name: BOT_NAMES[Math.floor(Math.random() * BOT_NAMES.length)],
                    color: AVATAR_COLORS[Math.floor(Math.random() * AVATAR_COLORS.length)],
                    bet: bet,
                    isYou: false
                });
                r.bank += bet;
            }
            r.timer = r.maxTimer;
        }

        function getInitials(name) {
            return name.split(/(?=[A-Z])|[_ ]/).filter(Boolean).map(w => w[0]).join('').substring(0, 2).toUpperCase();
        }

        loadData();
        renderRoom();
        startTimer();

        // Отрисовка
        function renderRoom() {
            const r = roomData[currentRoom];
            document.getElementById('roomTitle').textContent = r.name;
            document.getElementById('totalBank').textContent = r.bank.toLocaleString();
            const totalPlayers = r.players.length + (myBet > 0 ? 1 : 0);
            document.getElementById('playerCount').textContent = 'Участников: ' + totalPlayers;

            const track = document.getElementById('slotTrack');
            let html = '';

            // Все игроки (боты + я)
            let allPlayers = [...r.players];
            if (myBet > 0) {
                allPlayers.push({
                    name: 'Вы',
                    color: '#b8a9ff',
                    bet: myBet,
                    isYou: true
                });
            }

            // Дублируем для бесшовной прокрутки
            const displayPlayers = [...allPlayers, ...allPlayers, ...allPlayers];

            const totalBet = r.bank + myBet;
            html = displayPlayers.map((p, i) => {
                const chance = totalBet > 0 ? ((p.bet / totalBet) * 100).toFixed(1) : '0.0';
                return `
                    <div class="slot-item">
                        <div class="slot-avatar" style="background:${p.color};${p.isYou?'box-shadow:0 0 12px rgba(184,169,255,0.6);':''}">
                            ${getInitials(p.name)}
                        </div>
                        <div class="slot-chance">${chance}%</div>
                        <div class="slot-amount">${p.bet}</div>
                    </div>
                `;
            }).join('');

            track.innerHTML = html;
            track.style.transition = 'none';
            track.style.transform = 'translateX(0px)';

            // Обновляем инфо о ставке
            document.getElementById('myBetAmount').textContent = myBet;
            const myTotalBet = r.bank + myBet;
            document.getElementById('myChance').textContent = myBet > 0 ? ((myBet / myTotalBet) * 100).toFixed(1) + '%' : '0%';

            updateBalance();
        }

        function updateBalance() {
            document.getElementById('balanceDisplay').textContent = userBalance.toLocaleString();
        }

        // Таймер
        function startTimer() {
            if (timerInterval) clearInterval(timerInterval);
            timerInterval = setInterval(() => {
                const r = roomData[currentRoom];
                if (r.timer > 0 && !isSpinning) {
                    r.timer--;
                    document.getElementById('timerFill').style.width = (r.timer / r.maxTimer * 100) + '%';
                    document.getElementById('timerText').textContent = 'До розыгрыша: 0:' + String(r.timer).padStart(2, '0');
                }
                if (r.timer <= 0 && !isSpinning) {
                    startSpin();
                }
            }, 1000);
        }

        // Вращение
        function startSpin() {
            if (isSpinning) return;
            const r = roomData[currentRoom];
            const allPlayers = [...r.players];
            if (myBet > 0) allPlayers.push({ name: 'Вы', color: '#b8a9ff', bet: myBet, isYou: true });
            if (allPlayers.length === 0) {
                r.timer = r.maxTimer;
                generateBots(currentRoom);
                renderRoom();
                return;
            }

            isSpinning = true;
            const totalBet = r.bank + myBet;
            // Выбор победителя с учётом шансов
            let winner;
            let rand = Math.random() * totalBet;
            let cumulative = 0;
            for (let p of allPlayers) {
                cumulative += p.bet;
                if (rand <= cumulative) { winner = p; break; }
            }
            if (!winner) winner = allPlayers[allPlayers.length - 1];

            const track = document.getElementById('slotTrack');
            const itemWidth = 72;
            const totalItems = allPlayers.length * 3;
            const winnerIndex = allPlayers.indexOf(winner);
            const targetIndex = allPlayers.length + winnerIndex;
            const targetX = -(targetIndex * itemWidth) + (window.innerWidth / 2) - 32;

            // Анимация
            track.style.transition = 'transform 4s cubic-bezier(0.1, 0.9, 0.2, 1)';
            track.style.transform = 'translateX(' + (targetX - 300) + 'px)';

            setTimeout(() => {
                track.style.transition = 'transform 2s cubic-bezier(0.1, 0.7, 0.1, 1)';
                track.style.transform = 'translateX(' + targetX + 'px)';
            }, 3500);

            setTimeout(() => {
                isSpinning = false;
                // Начисление выигрыша
                if (winner.isYou) {
                    userBalance += r.bank + myBet;
                    document.getElementById('appContainer').classList.add('winner-flash');
                    setTimeout(() => document.getElementById('appContainer').classList.remove('winner-flash'), 1800);
                }
                // Сброс
                myBet = 0;
                r.bank = 0;
                r.players = [];
                r.timer = r.maxTimer;
                generateBots(currentRoom);
                saveData();
                renderRoom();
            }, 6000);
        }

        // Ставка
        function placeBet() {
            if (isSpinning) return;
            const r = roomData[currentRoom];
            const amt = parseInt(document.getElementById('betAmount').value);
            if (!amt || amt < r.min) { alert('Минимальная ставка: ' + r.min); return; }
            if (amt > r.max) { alert('Максимальная ставка: ' + r.max); return; }
            if (amt > userBalance) { alert('Недостаточно средств'); return; }

            userBalance -= amt;
            myBet += amt;
            document.getElementById('betAmount').value = '';
            saveData();
            renderRoom();
        }

        // Переключение комнат
        function switchRoom(room) {
            if (isSpinning) return;
            currentRoom = room;
            myBet = 0;
            document.querySelectorAll('.room-tab').forEach((b, i) => {
                b.classList.toggle('active', ['bomj','classic','major','hyena'][i] === room);
            });
            if (roomData[room].players.length === 0) generateBots(room);
            renderRoom();
        }

        // Навигация
        function switchPage(page) {
            document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
            const idx = { jackpot: 0, tasks: 1, ref: 2, top: 3, profile: 4 };
            document.querySelectorAll('.nav-item')[idx[page]].classList.add('active');
            if (page === 'jackpot') renderRoom();
        }

        updateBalance();
        document.getElementById('betAmount').addEventListener('keydown', e => { if (e.key === 'Enter') placeBet(); });
    </script>
</body>
</html>
