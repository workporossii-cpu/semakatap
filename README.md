<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>SEMAKA Coin</title>
    <style>
        :root {
            --bg: #121218; --card: #1c1c24; --accent: #b8a9ff; --accent2: #7c6fd4;
            --text: #e8e8f0; --sub: #8888a0; --green: #6ee7b7; --red: #fca5a5;
            --orange: #f0b90b; --blue: #60a5fa; --radius: 18px; --border: #2a2a35;
        }
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
            background: var(--bg); color: var(--text); min-height: 100vh;
            display: flex; justify-content: center; user-select: none;
            -webkit-tap-highlight-color: transparent; overflow-x: hidden;
        }
        .app { width: 100%; max-width: 420px; min-height: 100vh; display: flex; flex-direction: column; padding: 16px 12px 10px; }
        .header { display: flex; align-items: center; gap: 8px; padding: 4px 0 10px; }
        .profile-pic { width: 36px; height: 36px; border-radius: 50%; background: #2a2a38; display: flex; align-items: center; justify-content: center; font-size: 15px; flex-shrink: 0; cursor: pointer; color: #c4b5fd; font-weight: 600; }
        .balance-block { flex: 1; display: flex; align-items: center; gap: 8px; }
        .balance-info { display: flex; flex-direction: column; }
        .balance-label { font-size: 9px; color: var(--sub); letter-spacing: 2px; text-transform: uppercase; }
        .balance-amount { font-size: 22px; font-weight: 700; color: #fff; letter-spacing: -0.3px; line-height: 1; transition: all 0.3s ease; }
        .balance-amount.pop { animation: balancePop 0.3s ease; }
        @keyframes balancePop { 0% { transform: scale(1); } 50% { transform: scale(1.2); color: #f0b90b; } 100% { transform: scale(1); } }
        .withdraw-header-btn {
            background: #2a2a3a; color: #c4b5fd; border: 1px solid #3a3a55;
            padding: 7px 12px; border-radius: 12px; font-size: 11px; font-weight: 500;
            cursor: pointer; white-space: nowrap; transition: 0.2s;
            display: flex; align-items: center; gap: 4px;
        }
        .withdraw-header-btn:active { background: #35354d; }
        .withdraw-header-btn svg { width: 14px; height: 14px; }
        .daily-btn {
            background: #23232e; border: 1px solid var(--border); color: #c4b5fd;
            padding: 7px 12px; border-radius: 14px; font-size: 11px; font-weight: 500;
            cursor: pointer; white-space: nowrap; transition: 0.2s;
            display: flex; align-items: center; gap: 5px;
        }
        .daily-btn:active { background: #2e2e3d; }
        .daily-btn:disabled { opacity: 0.3; cursor: default; }
        .daily-btn svg { width: 16px; height: 16px; }
        .tap-container { flex: 1; display: flex; flex-direction: column; align-items: center; justify-content: flex-start; gap: 14px; padding: 6px 0 10px; }
        .tap-counter { font-size: 12px; color: var(--sub); background: #1a1a24; padding: 7px 16px; border-radius: 20px; border: 1px solid var(--border); }
        .tap-counter span { color: #c4b5fd; font-weight: 600; }
        .coin-wrapper { position: relative; }
        .coin-outer-glow {
            position: absolute; width: 290px; height: 290px; border-radius: 50%;
            top: -40px; left: -40px; pointer-events: none; z-index: 0; transition: all 0.5s ease;
        }
        .coin-outer-glow.active { box-shadow: 0 0 70px 18px rgba(184,169,255,0.10), 0 0 140px 35px rgba(184,169,255,0.05); }
        .coin-outer-glow.limit { box-shadow: 0 0 70px 18px rgba(252,165,165,0.10), 0 0 140px 35px rgba(252,165,165,0.05); }
        .coin-glow {
            position: absolute; width: 230px; height: 230px; border-radius: 50%;
            background: radial-gradient(circle, rgba(184,169,255,0.15) 0%, transparent 70%);
            top: -15px; left: -15px; pointer-events: none; z-index: 0;
            animation: pulse 3s ease-in-out infinite;
        }
        @keyframes pulse { 0%, 100% { transform: scale(1); opacity: 0.5; } 50% { transform: scale(1.15); opacity: 0.9; } }
        .coin {
            width: 260px; height: 260px; border-radius: 50%;
            background: #23232e; border: 3px solid #3a3a50;
            box-shadow: 0 0 0 7px #1a1a24, 0 22px 70px rgba(0,0,0,0.7);
            display: flex; align-items: center; justify-content: center;
            cursor: pointer; transition: transform 0.06s ease; position: relative; z-index: 1;
        }
        .coin:active { transform: scale(0.87); }
        .coin.blocked { border-color: #fca5a5; box-shadow: 0 0 0 7px #1a1a24, 0 22px 70px rgba(0,0,0,0.7), 0 0 40px rgba(252,165,165,0.30); }
        .coin-text { font-size: 24px; font-weight: 700; color: #c4b5fd; letter-spacing: 3px; }
        .block-timer { font-size: 12px; color: #fca5a5; text-align: center; min-height: 18px; }
        .tap-plus { position: fixed; font-size: 26px; font-weight: 700; color: #c4b5fd; pointer-events: none; z-index: 200; animation: floatUp 0.8s ease-out forwards; }
        @keyframes floatUp { 0% { opacity: 1; transform: translateY(0) scale(0.7); } 30% { opacity: 1; transform: translateY(-50px) scale(1.3); } 100% { opacity: 0; transform: translateY(-130px) scale(0.8); } }
        .coin-particle { position: fixed; width: 8px; height: 8px; border-radius: 50%; background: #c4b5fd; pointer-events: none; z-index: 199; animation: particleFly 0.6s ease-out forwards; }
        @keyframes particleFly { 0% { opacity: 1; transform: translate(0,0) scale(1); } 100% { opacity: 0; transform: translate(var(--dx), var(--dy)) scale(0); } }
        .progress-bar { width: 100%; max-width: 280px; height: 6px; background: #1e1e2a; border-radius: 6px; overflow: hidden; }
        .progress-fill { height: 100%; background: linear-gradient(90deg, #7c6fd4, #b8a9ff); border-radius: 6px; transition: width 0.4s ease; }
        .progress-fill::after { content: ''; position: absolute; right: 0; top: 0; width: 20px; height: 100%; background: rgba(255,255,255,0.3); border-radius: 6px; animation: progressGlow 1.5s ease-in-out infinite; }
        @keyframes progressGlow { 0%, 100% { opacity: 0.3; } 50% { opacity: 1; } }
        .reward-hint { font-size: 11px; color: var(--sub); }
        .reward-hint span { color: #c4b5fd; font-weight: 600; }
        .support-inline { text-align: center; padding: 4px 0; position: fixed; bottom: 76px; left: 50%; transform: translateX(-50%); width: 100%; max-width: 420px; z-index: 99; }
        .support-inline a { color: #777; text-decoration: none; font-size: 10px; display: inline-flex; align-items: center; gap: 3px; }
        .support-inline a:active { color: #c4b5fd; }
        .nav { position: fixed; bottom: 0; left: 50%; transform: translateX(-50%); width: 100%; max-width: 420px; background: #14141c; display: flex; justify-content: space-around; padding: 5px 6px 20px; border-top: 1px solid var(--border); z-index: 100; }
        .nav-item { display: flex; flex-direction: column; align-items: center; gap: 2px; font-size: 9px; color: #555; cursor: pointer; transition: 0.2s; background: none; border: none; padding: 4px 6px; border-radius: 12px; }
        .nav-item.active { color: #c4b5fd; }
        .nav-icon { width: 20px; height: 20px; }
        .nav-icon svg { width: 100%; height: 100%; }
        .nav-item.active .nav-icon svg path,
        .nav-item.active .nav-icon svg circle,
        .nav-item.active .nav-icon svg rect,
        .nav-item.active .nav-icon svg line,
        .nav-item.active .nav-icon svg polyline { stroke: #c4b5fd; }
        .page { display: none; flex: 1; overflow-y: auto; padding-bottom: 85px; }
        .page.active { display: block; animation: fadeIn 0.2s ease; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }
        .card { background: var(--card); border-radius: var(--radius); padding: 16px; margin-bottom: 10px; border: 1px solid var(--border); }
        .card h3 { font-size: 14px; margin-bottom: 12px; color: #c4b5fd; font-weight: 600; }
        .task-item { display: flex; justify-content: space-between; align-items: center; padding: 10px 0; border-bottom: 1px solid #22223a; }
        .task-item:last-child { border-bottom: none; }
        .task-name { font-size: 13px; }
        .task-reward { font-size: 12px; color: #9d8fff; font-weight: 600; margin-top: 2px; }
        .task-link-hint { font-size: 10px; color: var(--sub); margin-top: 2px; }
        .task-btn { background: #fff; color: #000; border: none; padding: 8px 14px; border-radius: 14px; font-weight: 600; font-size: 11px; cursor: pointer; transition: 0.2s; white-space: nowrap; }
        .task-btn:active { background: #ddd; }
        .task-btn:disabled { cursor: default; pointer-events: none; }
        .task-btn.done { background: #10b981; color: #fff; }
        .leaderboard-item { display: flex; align-items: center; gap: 10px; padding: 9px 0; border-bottom: 1px solid #22223a; }
        .leaderboard-rank { font-size: 15px; font-weight: 700; width: 24px; color: #9d8fff; text-align: center; }
        .ref-link-box { background: #15151f; padding: 10px; border-radius: 12px; font-size: 10px; word-break: break-all; color: #777; margin: 8px 0; border: 1px solid var(--border); font-family: monospace; }
        .btn { background: #2a2a3a; color: #c4b5fd; border: 1px solid #3a3a55; padding: 12px 22px; border-radius: 14px; font-weight: 500; font-size: 13px; cursor: pointer; width: 100%; margin-top: 6px; transition: 0.2s; }
        .btn:active { background: #35354d; }
        .btn:disabled { opacity: 0.4; cursor: default; pointer-events: none; }
        .btn.green { background: #065f46; color: #6ee7b7; border-color: #047857; }
        .btn.red { background: #450a0a; color: #fca5a5; border-color: #7f1d1d; }
        .btn.white { background: #fff; color: #000; border: none; font-weight: 600; }
        .withdraw-input, .withdraw-select { width: 100%; background: #15151f; border: 1px solid var(--border); color: #fff; padding: 12px 13px; border-radius: 12px; font-size: 13px; margin: 5px 0; transition: border 0.2s; }
        .withdraw-input:focus, .withdraw-select:focus { outline: none; border-color: #7c6fd4; }
        .withdraw-select { appearance: none; -webkit-appearance: none; background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='12' height='12' viewBox='0 0 12 12'%3E%3Cpath d='M6 8L1 3h10z' fill='%23888'/%3E%3C/svg%3E"); background-repeat: no-repeat; background-position: right 14px center; }
        .fee-info { font-size: 10px; color: var(--sub); margin: 3px 0; }
        .fee-info span { color: #fca5a5; }
        .total-info { font-size: 13px; color: #fff; margin: 6px 0; text-align: center; }
        .total-info strong { color: #6ee7b7; }
        .stat-row { display: flex; justify-content: space-between; padding: 7px 0; font-size: 12px; }
        .stat-row span:last-child { color: #ccc; }
        .ref-count { font-size: 28px; font-weight: 700; text-align: center; margin: 4px 0; color: #c4b5fd; }
        .ref-bonus-text { font-size: 12px; color: var(--sub); text-align: center; margin-bottom: 4px; line-height: 1.4; }
        .history-item { display: flex; justify-content: space-between; align-items: center; padding: 9px 0; border-bottom: 1px solid #22223a; font-size: 12px; }
        .history-item:last-child { border-bottom: none; }
        .history-status { font-size: 10px; padding: 3px 9px; border-radius: 8px; font-weight: 500; white-space: nowrap; }
        .history-status.processing { background: #1e293b; color: #60a5fa; }
        .history-status.paid { background: #064e3b; color: #6ee7b7; }
        .history-status.rejected { background: #450a0a; color: #fca5a5; }
        .modal-overlay { display: none; position: fixed; top: 0; left: 0; right: 0; bottom: 0; background: rgba(0,0,0,0.75); z-index: 300; align-items: center; justify-content: center; }
        .modal-overlay.active { display: flex; }
        .modal { background: #1c1c24; border-radius: var(--radius); padding: 20px; width: 92%; max-width: 400px; max-height: 88vh; overflow-y: auto; border: 1px solid var(--border); }
        .modal h3 { font-size: 15px; color: #c4b5fd; margin-bottom: 14px; font-weight: 600; }
        .modal-close { float: right; background: none; border: none; color: #888; font-size: 20px; cursor: pointer; padding: 0 4px; }
        .captcha-question { font-size: 17px; text-align: center; margin: 14px 0; color: #fff; }
        .captcha-input { width: 100%; background: #15151f; border: 1px solid var(--border); color: #fff; padding: 12px 13px; border-radius: 12px; font-size: 15px; text-align: center; margin: 6px 0; }
        .captcha-input:focus { outline: none; border-color: #7c6fd4; }
        .tabs { display: flex; gap: 4px; margin-bottom: 12px; flex-wrap: wrap; }
        .tab-btn { flex: 1; min-width: fit-content; background: #1e1e2a; border: 1px solid var(--border); color: #888; padding: 7px 5px; border-radius: 10px; font-size: 10px; cursor: pointer; text-align: center; transition: 0.2s; white-space: nowrap; display: flex; align-items: center; justify-content: center; gap: 3px; }
        .tab-btn.active { background: #2a2a3a; color: #c4b5fd; border-color: #3a3a55; }
        .tab-btn svg { width: 14px; height: 14px; }
        .admin-badge { background: #f0b90b; color: #000; font-size: 9px; padding: 2px 7px; border-radius: 8px; font-weight: 700; margin-left: 5px; }
        .log-item { font-size: 10px; padding: 5px 0; border-bottom: 1px solid #1a1a24; color: #999; }
        .log-item span { color: #ccc; font-weight: 500; }
        .profile-tap-hint { font-size: 9px; color: #444; text-align: center; margin-top: 2px; user-select: none; }
        .profile-stat-clickable { cursor: default; user-select: none; }
        .hidden { display: none !important; }
    </style>
</head>
<body>
    <div class="app">
        <div class="header">
            <div class="profile-pic" onclick="switchPage('profile')">
                <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="#c4b5fd" stroke-width="2"><circle cx="12" cy="8" r="4"/><path d="M4 20c0-4 4-7 8-7s8 3 8 7"/></svg>
            </div>
            <div class="balance-block">
                <div class="balance-info">
                    <div class="balance-label">Баланс</div>
                    <div class="balance-amount" id="balanceDisplay">0.0</div>
                </div>
                <button class="withdraw-header-btn" id="withdrawHeaderBtn" onclick="openWithdrawModal()">
                    <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="2" y="4" width="20" height="16" rx="3"/><circle cx="12" cy="12" r="4"/><line x1="2" y1="7" x2="22" y2="7"/></svg> Вывод
                </button>
            </div>
            <button class="daily-btn" id="dailyBonusBtn" onclick="claimDailyBonus()">
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M20 12v8a2 2 0 01-2 2H6a2 2 0 01-2-2v-8"/><path d="M12 2v18"/><path d="M4 8l8-6 8 6"/><rect x="8" y="12" width="2" height="4"/><rect x="14" y="12" width="2" height="4"/></svg> Бонус
            </button>
        </div>

        <div class="page active" id="tapPage">
            <div class="tap-container">
                <div class="tap-counter">Кликов: <span id="tapCountDisplay">0</span> / 200</div>
                <div class="progress-bar"><div class="progress-fill" id="tapProgress" style="width: 0%"></div></div>
                <div class="coin-wrapper">
                    <div class="coin-outer-glow active" id="coinOuterGlow"></div>
                    <div class="coin-glow"></div>
                    <div class="coin" id="coin" onclick="handleTap(event)"><span class="coin-text">SEMAKA</span></div>
                </div>
                <div class="block-timer" id="blockTimer"></div>
                <div class="reward-hint">+<span id="rewardPerTap">0.5</span> за клик</div>
            </div>
        </div>

        <div class="page" id="tasksPage"><div class="card"><h3>Задания</h3><div id="tasksList"></div></div></div>
        <div class="page" id="refPage">
            <div class="card">
                <h3>Рефералы</h3><div class="ref-count" id="refCount">0</div>
                <p style="color:var(--sub);font-size:12px;text-align:center;">приглашённых друзей</p>
                <div class="ref-bonus-text">Приглашай друзей и зарабатывай<br><strong style="color:#c4b5fd;">10% с каждого клика</strong></div>
                <div class="stat-row"><span>Бонус за друга:</span><span style="color:#c4b5fd;">+10% к кликам +5 монет</span></div>
                <p style="font-size:11px;color:var(--sub);margin-top:10px;">Твоя ссылка:</p>
                <div class="ref-link-box" id="refLink">https://t.me/semaka_tapbot?start=ref_USER</div>
                <button class="btn" id="copyRefBtn" onclick="copyRefLink()">Скопировать</button>
            </div>
        </div>
        <div class="page" id="topPage">
            <div class="card">
                <div class="tabs">
                    <button class="tab-btn active" onclick="switchTopTab('taps')">
                        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="6 9 12 4 18 9"/><line x1="12" y1="4" x2="12" y2="20"/></svg> Клики
                    </button>
                    <button class="tab-btn" onclick="switchTopTab('refs')">
                        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="8" r="4"/><path d="M4 20c0-4 4-7 8-7s8 3 8 7"/></svg> Рефералы
                    </button>
                </div>
                <div id="leaderboard"></div>
            </div>
        </div>
        <div class="page" id="profilePage">
            <div class="card">
                <h3>Профиль</h3>
                <div class="stat-row"><span>Имя:</span><span id="profileName">-</span></div>
                <div class="stat-row"><span>Telegram ID:</span><span id="profileId">-</span></div>
                <div class="stat-row"><span>Баланс:</span><span id="profileBalance">0.0</span></div>
                <div class="stat-row profile-stat-clickable" id="adminSecretProfile"><span>Кликов сегодня:</span><span id="profileTaps">0</span></div>
                <div class="profile-tap-hint" id="profileTapHint"></div>
                <div class="stat-row"><span>Рефералов:</span><span id="profileRefs">0</span></div>
            </div>
        </div>

        <div class="support-inline">
            <a href="https://t.me/user_live" target="_blank">
                <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="10"/><path d="M8 12s2-4 4-4 4 4 4 4-2 4-4 4-4-4-4-4"/><line x1="12" y1="10" x2="12" y2="14"/></svg> Поддержка
            </a>
        </div>

        <div class="nav">
            <button class="nav-item active" onclick="switchPage('tap')"><span class="nav-icon"><svg viewBox="0 0 24 24" fill="none" stroke="#555" stroke-width="2"><circle cx="12" cy="12" r="10"/><circle cx="12" cy="12" r="3"/></svg></span>Кликер</button>
            <button class="nav-item" onclick="switchPage('tasks')"><span class="nav-icon"><svg viewBox="0 0 24 24" fill="none" stroke="#555" stroke-width="2"><rect x="3" y="3" width="18" height="18" rx="3"/><line x1="9" y1="9" x2="15" y2="9"/><line x1="9" y1="13" x2="15" y2="13"/></svg></span>Задания</button>
            <button class="nav-item" onclick="switchPage('ref')"><span class="nav-icon"><svg viewBox="0 0 24 24" fill="none" stroke="#555" stroke-width="2"><circle cx="12" cy="8" r="4"/><path d="M4 20c0-4 4-7 8-7s8 3 8 7"/></svg></span>Друзья</button>
            <button class="nav-item" onclick="switchPage('top')"><span class="nav-icon"><svg viewBox="0 0 24 24" fill="none" stroke="#555" stroke-width="2"><polyline points="6 9 12 4 18 9"/><line x1="12" y1="4" x2="12" y2="20"/></svg></span>Топ</button>
            <button class="nav-item" onclick="switchPage('profile')"><span class="nav-icon"><svg viewBox="0 0 24 24" fill="none" stroke="#555" stroke-width="2"><circle cx="12" cy="8" r="4"/><path d="M4 20c0-4 4-7 8-7s8 3 8 7"/></svg></span>Профиль</button>
        </div>
    </div>

    <!-- Withdraw Modal -->
    <div class="modal-overlay" id="withdrawModal">
        <div class="modal">
            <button class="modal-close" onclick="closeWithdrawModal()">&times;</button>
            <h3>Вывод средств</h3>
            <p style="font-size:13px;color:var(--sub);margin-bottom:8px;">Минимум: 1500 монет</p>
            <input class="withdraw-input" type="number" id="withdrawAmount" placeholder="Сумма" min="1500" inputmode="decimal" oninput="updateWithdrawInfo()">
            <select class="withdraw-select" id="withdrawMethod" onchange="onMethodChange()">
                <option value="">Выберите способ вывода</option>
                <option value="sbp">СБП (Система быстрых платежей)</option>
                <option value="card">Банковская карта</option>
                <option value="usdt">USDT TRC-20</option>
                <option value="ton">TON</option>
                <option value="cryptobot">Crypto Bot</option>
            </select>
            <div id="methodFields" class="hidden">
                <div id="sbpFields" class="hidden">
                    <select class="withdraw-select" id="bankSelect"><option value="">Выберите банк</option><option>Сбербанк</option><option>Т-Банк</option><option>ВТБ</option><option>Альфа-Банк</option><option>Райффайзен</option><option>Газпромбанк</option><option>Почта Банк</option></select>
                    <input class="withdraw-input" type="text" id="dynamicWalletInput" placeholder="Введите номер телефона">
                </div>
                <div id="cardFields" class="hidden">
                    <select class="withdraw-select" id="cardType"><option value="">Тип карты</option><option>VISA</option><option>Mastercard</option><option>Мир</option></select>
                    <input class="withdraw-input" type="text" id="dynamicWalletInput" placeholder="Введите номер карты">
                </div>
                <div id="usdtFields" class="hidden">
                    <input class="withdraw-input" type="text" id="dynamicWalletInput" placeholder="Введите адрес кошелька TRC-20">
                </div>
                <div id="tonFields" class="hidden">
                    <input class="withdraw-input" type="text" id="dynamicWalletInput" placeholder="Введите адрес кошелька в сети TON">
                </div>
                <div id="cryptobotFields" class="hidden">
                    <select class="withdraw-select" id="cryptoCurrency"><option value="">В какой монете желаете получить чек?</option><option>USDT</option><option>TON</option></select>
                    <input class="withdraw-input" type="text" id="dynamicWalletInput" placeholder="Введите ваш username Telegram">
                </div>
            </div>
            <div id="feeDetails" class="hidden"><div class="fee-info" id="feeText"></div><div class="total-info">К получению: <strong id="totalAmount">0</strong> монет</div></div>
            <button class="btn" id="withdrawSubmitBtn" onclick="requestWithdraw()">Вывести</button>
            <p style="font-size:11px;color:var(--sub);margin-top:6px;text-align:center;" id="withdrawMsg"></p>
            <div style="margin-top:16px;"><h3 style="font-size:13px;">История выплат</h3><div id="withdrawHistory" style="max-height:180px;overflow-y:auto;"><p style="color:var(--sub);font-size:11px;text-align:center;">Нет истории</p></div></div>
        </div>
    </div>

    <!-- Captcha Modal -->
    <div class="modal-overlay" id="captchaModal">
        <div class="modal"><h3>Проверка</h3><p style="font-size:12px;color:var(--sub);text-align:center;">Подтверди, что ты не бот</p><div class="captcha-question" id="captchaQuestion"></div><input class="captcha-input" type="number" id="captchaInput" placeholder="Ответ" inputmode="numeric"><button class="btn" id="captchaSubmitBtn" onclick="submitCaptcha()">Подтвердить</button><p style="font-size:10px;color:var(--red);margin-top:6px;text-align:center;min-height:14px;" id="captchaMsg"></p></div>
    </div>

    <!-- Admin Login Modal -->
    <div class="modal-overlay" id="adminLoginModal">
        <div class="modal"><button class="modal-close" onclick="closeAdminLogin()">&times;</button><h3>Доступ</h3><input class="withdraw-input" type="password" id="adminPassword" placeholder="Пароль"><button class="btn" onclick="adminLogin()">Войти</button><p style="font-size:10px;color:var(--red);margin-top:6px;text-align:center;min-height:14px;" id="adminLoginMsg"></p></div>
    </div>

    <script>
        const STORAGE_KEY='semaka_v15',ADMIN_PASSWORD='admin123',MIN_TAP_INTERVAL=100,CAPTCHA_EVERY=40,BLOCK_DURATION=3000,MAX_FAST_TAPS=5;
        function getDefaultData(){return {balance:0,tapsToday:0,lastTapDate:new Date().toDateString(),dailyBonusClaimed:false,lastDailyDate:'',referrals:[],referralCode:'ref_'+Math.random().toString(36).substr(2,8),completedTasks:[],userId:'',tgId:'',tgUsername:'',withdrawHistory:[],isAdmin:false};}
        function getGlobalStore(){let s=localStorage.getItem('semaka_global_v10');if(!s){s={users:{},withdrawRequests:[],adminLogs:[],totalTaps:{},tasks:[{id:'task_default',name:'Подписаться на канал',reward:100,link:'https://t.me/example',description:'Перейди и подпишись'}],broadcasts:[]};localStorage.setItem('semaka_global_v10',JSON.stringify(s));}else s=JSON.parse(s);return s;}
        function saveGlobalStore(s){localStorage.setItem('semaka_global_v10',JSON.stringify(s));}
        let data=getDefaultData(),globalStore=getGlobalStore();
        let lastTapTime=0,fastTapCount=0,isBlocked=false,tapsSinceCaptcha=0,captchaResolve=null,isProcessingAction=false,currentTopTab='taps',currentAdminTab='requests',adminHoldTimer=null,adminHoldActive=false;
        let currentWithdrawMethod='';
        function initTelegramData(){if(window.Telegram&&window.Telegram.WebApp){const tg=window.Telegram.WebApp;tg.ready();const u=tg.initDataUnsafe?.user;if(u){data.tgId=u.id.toString();data.userId='tg_'+u.id;data.tgUsername=u.username||(u.first_name+(u.last_name?' '+u.last_name:''));}}if(!data.userId){data.userId='user_'+Math.random().toString(36).substr(2,8);data.tgUsername='User_'+data.userId.substring(5,11);}if(!data.tgId)data.tgId=data.userId;}
        function initUserInGlobal(){if(!globalStore.users[data.userId]){globalStore.users[data.userId]={id:data.userId,tgId:data.tgId,username:data.tgUsername,balance:data.balance,taps:0,referralCode:data.referralCode,referrals:[],banned:false};globalStore.totalTaps[data.userId]=0;saveGlobalStore(globalStore);}else{if(data.tgUsername)globalStore.users[data.userId].username=data.tgUsername;if(data.tgId)globalStore.users[data.userId].tgId=data.tgId;}}
        function loadData(){const saved=localStorage.getItem(STORAGE_KEY);if(saved){try{data=JSON.parse(saved);const today=new Date().toDateString();if(data.lastTapDate!==today){data.tapsToday=0;data.lastTapDate=today;data.dailyBonusClaimed=false;}}catch(e){data=getDefaultData();}}initTelegramData();globalStore=getGlobalStore();initUserInGlobal();if(globalStore.users[data.userId])data.balance=globalStore.users[data.userId].balance;const p=new URLSearchParams(window.location.search);const r=p.get('start');if(r&&r.startsWith('ref_')&&r!==data.referralCode&&!data.referrals.includes(r))addReferral(r);}
        function saveData(){if(globalStore.users[data.userId]){globalStore.users[data.userId].balance=data.balance;globalStore.users[data.userId].referrals=data.referrals;globalStore.users[data.userId].username=data.tgUsername||globalStore.users[data.userId].username;}saveGlobalStore(globalStore);localStorage.setItem(STORAGE_KEY,JSON.stringify(data));}
        loadData();

        function updateGlowState(){const g=document.getElementById('coinOuterGlow');g.classList.remove('active','limit');if(data.tapsToday>=200)g.classList.add('limit');else g.classList.add('active');}
        function spawnParticles(x,y){const colors=['#c4b5fd','#b8a9ff','#9d8fff','#f0b90b','#fff'];for(let i=0;i<10;i++){const p=document.createElement('div');p.className='coin-particle';const a=Math.random()*Math.PI*2,d=30+Math.random()*60;p.style.setProperty('--dx',Math.cos(a)*d+'px');p.style.setProperty('--dy',Math.sin(a)*d+'px');p.style.left=x+'px';p.style.top=y+'px';p.style.background=colors[Math.floor(Math.random()*colors.length)];p.style.width=(5+Math.random()*10)+'px';p.style.height=p.style.width;document.body.appendChild(p);setTimeout(()=>p.remove(),600);}}
        async function handleTap(event){
            if(isBlocked||isProcessingAction||data.tapsToday>=200){if(data.tapsToday>=200)shakeElement(document.getElementById('coin'));return;}
            const now=Date.now();
            if(lastTapTime>0&&(now-lastTapTime)<MIN_TAP_INTERVAL){fastTapCount++;if(fastTapCount>=MAX_FAST_TAPS){blockUser('Слишком быстро');addLog('bot_block','Блок: '+data.userId);return;}}else fastTapCount=Math.max(0,fastTapCount-1);
            lastTapTime=now;tapsSinceCaptcha++;if(tapsSinceCaptcha>=CAPTCHA_EVERY){tapsSinceCaptcha=0;if(!await showCaptcha()){blockUser('Провал капчи');addLog('captcha_fail','Капча: '+data.userId);return;}}
            data.tapsToday++;let reward=Math.round((0.5+data.referrals.length*0.1*0.5)*10)/10;
            data.balance=Math.round((data.balance+reward)*10)/10;globalStore.totalTaps[data.userId]=(globalStore.totalTaps[data.userId]||0)+1;
            saveData();updateGlowState();
            const coin=document.getElementById('coin'),rect=coin.getBoundingClientRect();
            let cx=event.touches?.[0]?.clientX||event.clientX||rect.left+rect.width/2;
            let cy=event.touches?.[0]?.clientY||event.clientY||rect.top+rect.height/2;
            spawnParticles(cx,cy);
            const plus=document.createElement('div');plus.className='tap-plus';plus.textContent='+'+reward.toFixed(1);plus.style.left=cx+'px';plus.style.top=(cy-30)+'px';document.body.appendChild(plus);setTimeout(()=>plus.remove(),800);
            animateBalance();updateUI();
        }
        function shakeElement(el){el.style.transform='translateX(-6px)';setTimeout(()=>el.style.transform='translateX(6px)',50);setTimeout(()=>el.style.transform='translateX(-4px)',100);setTimeout(()=>el.style.transform='',150);}
        function blockUser(reason){isBlocked=true;document.getElementById('coin').classList.add('blocked');const t=document.getElementById('blockTimer');let r=3;t.textContent=reason+' — подожди 3 сек';const c=setInterval(()=>{r--;if(r<=0){clearInterval(c);t.textContent='';document.getElementById('coin').classList.remove('blocked');isBlocked=false;fastTapCount=0;}else t.textContent=reason+' — подожди '+r+' сек';},1000);}
        function animateBalance(){const el=document.getElementById('balanceDisplay');el.classList.remove('pop');void el.offsetWidth;el.classList.add('pop');}
        function generateCaptcha(){const a=Math.floor(Math.random()*20)+1,b=Math.floor(Math.random()*20)+1,ops=['+','-','×'],op=ops[Math.floor(Math.random()*3)];let ans;switch(op){case'+':ans=a+b;break;case'-':ans=Math.max(a,b)-Math.min(a,b);break;case'×':ans=a*b;break;}return{question:(op==='-'?`${Math.max(a,b)} ${op} ${Math.min(a,b)}`:`${a} ${op} ${b}`)+' = ?',answer:ans};}
        function showCaptcha(){return new Promise(resolve=>{const c=generateCaptcha();document.getElementById('captchaQuestion').textContent=c.question;document.getElementById('captchaInput').value='';document.getElementById('captchaMsg').textContent='';document.getElementById('captchaModal').classList.add('active');document.getElementById('captchaInput').focus();captchaResolve={resolve,answer:c.answer};});}
        function submitCaptcha(){if(isProcessingAction)return;isProcessingAction=true;const btn=document.getElementById('captchaSubmitBtn');btn.disabled=true;const ans=parseInt(document.getElementById('captchaInput').value);if(isNaN(ans)){document.getElementById('captchaMsg').textContent='Введи число';btn.disabled=false;isProcessingAction=false;return;}if(captchaResolve&&ans===captchaResolve.answer){document.getElementById('captchaModal').classList.remove('active');captchaResolve.resolve(true);captchaResolve=null;}else{document.getElementById('captchaMsg').textContent='Неверно';document.getElementById('captchaInput').value='';document.getElementById('captchaInput').focus();}btn.disabled=false;isProcessingAction=false;}
        document.getElementById('captchaInput').addEventListener('keydown',e=>{if(e.key==='Enter')submitCaptcha();});
        function claimDailyBonus(){if(isProcessingAction)return;isProcessingAction=true;const today=new Date().toDateString();if(data.dailyBonusClaimed&&data.lastDailyDate===today){isProcessingAction=false;return;}data.dailyBonusClaimed=true;data.lastDailyDate=today;data.balance=Math.round((data.balance+0.1)*10)/10;saveData();animateBalance();updateUI();setTimeout(()=>{isProcessingAction=false;},500);}
        function renderTasks(){const c=document.getElementById('tasksList');if(!c)return;const tasks=globalStore.tasks||[];if(tasks.length===0){c.innerHTML='<p style="color:var(--sub);text-align:center;padding:20px;">Пока нет заданий</p>';return;}c.innerHTML=tasks.map(t=>{const done=data.completedTasks.includes(t.id);return`<div class="task-item"><div><div class="task-name">${t.name}</div><div class="task-reward">+${t.reward} монет</div>${t.description?`<div class="task-link-hint">${t.description}</div>`:''}${t.link?`<div class="task-link-hint">${t.link}</div>`:''}</div><button class="task-btn ${done?'done':''}" ${done?'disabled':''} onclick="completeTask('${t.id}',${t.reward},'${t.link||''}')">${done?'Выполнено':'Выполнить'}</button></div>`;}).join('');}
        function completeTask(id,reward,link){if(isProcessingAction||data.completedTasks.includes(id))return;isProcessingAction=true;if(link)window.open(link,'_blank');data.completedTasks.push(id);data.balance=Math.round((data.balance+reward)*10)/10;saveData();addLog('task_complete',`Задание ${id}: ${data.userId}`);renderTasks();animateBalance();updateUI();setTimeout(()=>{isProcessingAction=false;},500);}
        function addReferral(refCode){if(refCode===data.referralCode||data.referrals.includes(refCode))return;data.referrals.push(refCode);data.balance=Math.round((data.balance+5)*10)/10;for(let uid in globalStore.users){if(globalStore.users[uid].referralCode===refCode){globalStore.users[uid].balance=Math.round((globalStore.users[uid].balance+5)*10)/10;if(!globalStore.users[uid].referrals.includes(data.userId))globalStore.users[uid].referrals.push(data.userId);}}saveData();addLog('referral',`Новый реферал: ${data.userId}`);}
        function copyRefLink(){if(isProcessingAction)return;isProcessingAction=true;navigator.clipboard?.writeText(document.getElementById('refLink').textContent).then(()=>{}).catch(()=>{});const btn=document.getElementById('copyRefBtn');btn.textContent='Скопировано!';setTimeout(()=>{btn.textContent='Скопировать';isProcessingAction=false;},1000);}
        function switchTopTab(tab){currentTopTab=tab;document.querySelectorAll('#topPage .tab-btn').forEach((b,i)=>b.classList.toggle('active',(tab==='taps'&&i===0)||(tab==='refs'&&i===1)));renderLeaderboard();}
        function renderLeaderboard(){const c=document.getElementById('leaderboard');if(!c)return;let users=[];for(let uid in globalStore.users)users.push({id:uid,name:(globalStore.users[uid].username||uid).substring(0,16),taps:globalStore.totalTaps[uid]||0,refs:(globalStore.users[uid].referrals||[]).length,isYou:uid===data.userId});
            const podium=(i)=>{if(i===0)return'<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="#f0b90b" stroke-width="2"><path d="M4 20h16"/><path d="M8 20V12"/><path d="M16 20V12"/><path d="M12 20V4"/></svg>';if(i===1)return'<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="#aaa" stroke-width="2"><path d="M4 20h16"/><path d="M8 20V12"/><path d="M16 20V12"/><path d="M12 20V8"/></svg>';if(i===2)return'<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="#cd7f32" stroke-width="2"><path d="M4 20h16"/><path d="M8 20V14"/><path d="M16 20V14"/><path d="M12 20V10"/></svg>';return`<span style="font-size:13px;color:#666;">${i+1}</span>`;};
            if(currentTopTab==='taps'){users.sort((a,b)=>b.taps-a.taps);c.innerHTML='<h3 style="margin-bottom:10px;">Топ по кликам</h3>'+users.slice(0,10).map((u,i)=>`<div class="leaderboard-item" style="${u.isYou?'background:#1f1f30;border-radius:10px;padding:5px 8px;margin:2px 0;':''}"><span class="leaderboard-rank">${podium(i)}</span><span style="flex:1;">${u.name}${u.isYou?' — вы':''}</span><span style="color:#9d8fff;">${u.taps} кликов</span></div>`).join('')||'<p style="color:var(--sub);text-align:center;">Пока пусто</p>';}
            else{users.sort((a,b)=>b.refs-a.refs);c.innerHTML='<h3 style="margin-bottom:10px;">Топ по рефералам</h3>'+users.slice(0,10).map((u,i)=>`<div class="leaderboard-item" style="${u.isYou?'background:#1f1f30;border-radius:10px;padding:5px 8px;margin:2px 0;':''}"><span class="leaderboard-rank">${podium(i)}</span><span style="flex:1;">${u.name}${u.isYou?' — вы':''}</span><span style="color:#9d8fff;">${u.refs} друзей</span></div>`).join('')||'<p style="color:var(--sub);text-align:center;">Пока пусто</p>';}}

        function onMethodChange(){
            const m=document.getElementById('withdrawMethod').value;currentWithdrawMethod=m;
            const mf=document.getElementById('methodFields'),fd=document.getElementById('feeDetails');
            document.getElementById('sbpFields').classList.add('hidden');document.getElementById('cardFields').classList.add('hidden');
            document.getElementById('usdtFields').classList.add('hidden');document.getElementById('tonFields').classList.add('hidden');
            document.getElementById('cryptobotFields').classList.add('hidden');
            if(!m){mf.classList.add('hidden');fd.classList.add('hidden');}else{mf.classList.remove('hidden');fd.classList.remove('hidden');}
            if(m==='sbp')document.getElementById('sbpFields').classList.remove('hidden');
            else if(m==='card')document.getElementById('cardFields').classList.remove('hidden');
            else if(m==='usdt')document.getElementById('usdtFields').classList.remove('hidden');
            else if(m==='ton')document.getElementById('tonFields').classList.remove('hidden');
            else if(m==='cryptobot')document.getElementById('cryptobotFields').classList.remove('hidden');
            updateWithdrawInfo();
        }
        function updateWithdrawInfo(){
            const amt=parseFloat(document.getElementById('withdrawAmount').value)||0,m=document.getElementById('withdrawMethod').value;
            const fe=document.getElementById('feeText'),te=document.getElementById('totalAmount'),fd=document.getElementById('feeDetails');
            if(!m||amt<1500){fd.classList.add('hidden');return;}fd.classList.remove('hidden');
            let fee=0,ft='';
            if(m==='sbp'||m==='card'||m==='cryptobot'){fee=0;ft='Без комиссии';}
            else if(m==='ton'){fee=5;ft='Комиссия сети: ~5 монет';}
            else if(m==='usdt'){fee=Math.round(amt*0.05+350);ft='Комиссия: 350₽ + 5% (~'+fee+' монет)';}
            fe.innerHTML=ft;te.textContent=Math.max(0,amt-fee).toFixed(0);
        }
        function getActiveWalletInput(){
            const m=currentWithdrawMethod;
            if(m==='sbp')return document.querySelector('#sbpFields #dynamicWalletInput');
            if(m==='card')return document.querySelector('#cardFields #dynamicWalletInput');
            if(m==='usdt')return document.querySelector('#usdtFields #dynamicWalletInput');
            if(m==='ton')return document.querySelector('#tonFields #dynamicWalletInput');
            if(m==='cryptobot')return document.querySelector('#cryptobotFields #dynamicWalletInput');
            return null;
        }
        function openWithdrawModal(){if(isProcessingAction)return;document.getElementById('withdrawModal').classList.add('active');document.getElementById('withdrawMsg').textContent='';document.getElementById('withdrawAmount').value='';document.getElementById('withdrawMethod').value='';document.getElementById('methodFields').classList.add('hidden');document.getElementById('feeDetails').classList.add('hidden');currentWithdrawMethod='';renderWithdrawHistory();}
        function closeWithdrawModal(){document.getElementById('withdrawModal').classList.remove('active');}
        function renderWithdrawHistory(){const h=data.withdrawHistory||[],c=document.getElementById('withdrawHistory');if(h.length===0){c.innerHTML='<p style="color:var(--sub);font-size:11px;text-align:center;">Нет истории</p>';return;}c.innerHTML=h.slice().reverse().map(r=>`<div class="history-item"><div><div style="font-size:11px;">${r.amount} монет — ${r.method||'Способ'}</div><div style="font-size:9px;color:var(--sub);">${r.date}</div></div><span class="history-status ${r.status}">${r.status==='processing'?'В обработке':r.status==='paid'?'Выплачено':r.status==='rejected'?'Отклонено':r.status}</span></div>`).join('');}
        function requestWithdraw(){
            if(isProcessingAction)return;isProcessingAction=true;const btn=document.getElementById('withdrawSubmitBtn');btn.disabled=true;
            const amt=parseFloat(document.getElementById('withdrawAmount').value),method=document.getElementById('withdrawMethod').value;
            const walletInput=getActiveWalletInput(),wallet=walletInput?walletInput.value.trim():'';
            const bank=document.getElementById('bankSelect')?.value||'',card=document.getElementById('cardType')?.value||'',crypto=document.getElementById('cryptoCurrency')?.value||'';
            const msg=document.getElementById('withdrawMsg');
            if(!amt||amt<1500){msg.textContent='Минимум 1500 монет';btn.disabled=false;isProcessingAction=false;return;}
            if(amt>data.balance){msg.textContent='Недостаточно средств';btn.disabled=false;isProcessingAction=false;return;}
            if(!method){msg.textContent='Выберите способ вывода';btn.disabled=false;isProcessingAction=false;return;}
            if(method==='sbp'&&!bank){msg.textContent='Выберите банк';btn.disabled=false;isProcessingAction=false;return;}
            if(method==='card'&&!card){msg.textContent='Выберите тип карты';btn.disabled=false;isProcessingAction=false;return;}
            if(method==='cryptobot'&&!crypto){msg.textContent='Выберите валюту чека';btn.disabled=false;isProcessingAction=false;return;}
            if(method==='cryptobot'&&!wallet){msg.textContent='Введите ваш username Telegram';btn.disabled=false;isProcessingAction=false;return;}
            if(method==='usdt'&&!wallet){msg.textContent='Введите адрес кошелька TRC-20';btn.disabled=false;isProcessingAction=false;return;}
            if(method==='ton'&&!wallet){msg.textContent='Введите адрес кошелька TON';btn.disabled=false;isProcessingAction=false;return;}
            if(method==='sbp'&&!wallet){msg.textContent='Введите номер телефона';btn.disabled=false;isProcessingAction=false;return;}
            if(method==='card'&&!wallet){msg.textContent='Введите номер карты';btn.disabled=false;isProcessingAction=false;return;}
            let fee=0;if(method==='ton')fee=5;else if(method==='usdt')fee=Math.round(amt*0.05+350);
            if(amt-fee<=0){msg.textContent='Сумма меньше комиссии';btn.disabled=false;isProcessingAction=false;return;}
            let desc='';if(method==='sbp')desc=`СБП (${bank||'Банк'}) → ${wallet}`;else if(method==='card')desc=`Карта ${card||'VISA'} → ${wallet}`;else if(method==='cryptobot')desc=`Crypto Bot (${crypto||'Валюта'}) → ${wallet}`;else if(method==='usdt')desc=`USDT TRC-20 → ${wallet}`;else if(method==='ton')desc=`TON → ${wallet}`;
            const req={id:'w_'+Date.now(),userId:data.userId,amount:amt,method:desc,wallet:wallet,status:'processing',date:new Date().toLocaleString('ru-RU'),adminNote:''};
            if(!data.withdrawHistory)data.withdrawHistory=[];data.withdrawHistory.push(req);
            if(!globalStore.withdrawRequests)globalStore.withdrawRequests=[];globalStore.withdrawRequests.push(req);
            data.balance=Math.round((data.balance-amt)*10)/10;saveData();addLog('withdraw_request',`Вывод ${amt}: ${desc}`);
            msg.textContent='Заявка принята';msg.style.color='var(--green)';
            document.getElementById('withdrawAmount').value='';document.getElementById('withdrawMethod').value='';
            document.getElementById('methodFields').classList.add('hidden');document.getElementById('feeDetails').classList.add('hidden');
            currentWithdrawMethod='';renderWithdrawHistory();animateBalance();updateUI();
            setTimeout(()=>{btn.disabled=false;isProcessingAction=false;},1500);
        }

        function startAdminHold(){if(data.isAdmin||adminHoldActive)return;adminHoldActive=true;const hint=document.getElementById('profileTapHint');let r=11;adminHoldTimer=setInterval(()=>{r--;if(r<=0){clearInterval(adminHoldTimer);adminHoldTimer=null;adminHoldActive=false;hint.textContent='';document.getElementById('adminLoginModal').classList.add('active');document.getElementById('adminPassword').value='';}else if(r<=5)hint.textContent='Держи '+r+' сек...';},1000);}
        function stopAdminHold(){if(adminHoldTimer){clearInterval(adminHoldTimer);adminHoldTimer=null;}adminHoldActive=false;document.getElementById('profileTapHint').textContent='';}
        function closeAdminLogin(){document.getElementById('adminLoginModal').classList.remove('active');}
        function adminLogin(){if(document.getElementById('adminPassword').value===ADMIN_PASSWORD){data.isAdmin=true;saveData();document.getElementById('adminLoginModal').classList.remove('active');switchPage('admin');renderAdminPanel();addLog('admin_login','Администратор вошёл');}else document.getElementById('adminLoginMsg').textContent='Неверный пароль';}
        function switchAdminTab(tab){currentAdminTab=tab;document.querySelectorAll('#adminPage .tab-btn').forEach((b,i)=>b.classList.toggle('active',(tab==='requests'&&i===0)||(tab==='logs'&&i===1)||(tab==='users'&&i===2)||(tab==='tasks'&&i===3)||(tab==='balance'&&i===4)||(tab==='broadcast'&&i===5)||(tab==='edituser'&&i===6)));renderAdminPanel();}
        function renderAdminPanel(){
            const c=document.getElementById('adminContent');if(!c)return;
            if(currentAdminTab==='requests'){const reqs=globalStore.withdrawRequests||[];c.innerHTML=reqs.length===0?'<p style="color:var(--sub);text-align:center;padding:20px;">Нет заявок</p>':reqs.slice().reverse().map(r=>`<div class="history-item" style="flex-wrap:wrap;"><div style="width:100%;"><div style="display:flex;justify-content:space-between;"><span style="font-size:11px;"><strong>${r.amount} монет</strong> — ${r.method}</span><span class="history-status ${r.status}">${r.status==='processing'?'В обработке':r.status==='paid'?'Выплачено':r.status==='rejected'?'Отклонено':r.status}</span></div><div style="font-size:9px;color:var(--sub);">ID: ${r.userId} | ${r.date}</div>${r.status==='processing'?`<div style="margin-top:5px;display:flex;gap:5px;"><button class="btn green" style="padding:5px 10px;font-size:10px;" onclick="processWithdraw('${r.id}','paid')">Выплачено</button><button class="btn red" style="padding:5px 10px;font-size:10px;" onclick="processWithdraw('${r.id}','rejected')">Отказать</button></div>`:`<div style="font-size:9px;color:var(--sub);">${r.adminNote||''}</div>`}</div></div>`).join('');}
            else if(currentAdminTab==='logs'){const logs=globalStore.adminLogs||[];c.innerHTML=logs.length===0?'<p style="color:var(--sub);text-align:center;padding:20px;">Логи пусты</p>':logs.slice().reverse().slice(0,50).map(l=>`<div class="log-item"><span>[${l.date}]</span> ${l.action} — ${l.details}</div>`).join('');}
            else if(currentAdminTab==='users'){const ids=Object.keys(globalStore.users);c.innerHTML=ids.length===0?'<p style="color:var(--sub);text-align:center;padding:20px;">Нет пользователей</p>':ids.map(uid=>{const u=globalStore.users[uid];return`<div class="history-item"><div><div style="font-size:11px;">${(u.username||uid).substring(0,20)} ${u.banned?'[БАН]':''}</div><div style="font-size:9px;color:var(--sub);">ID: ${u.tgId||uid} | Баланс: ${u.balance.toFixed(1)}</div></div><button class="btn ${u.banned?'':'red'}" style="padding:5px 8px;font-size:9px;width:auto;" onclick="toggleBan('${uid}')">${u.banned?'Разбанить':'Бан'}</button></div>`;}).join('');}
            else if(currentAdminTab==='tasks'){const tasks=globalStore.tasks||[];c.innerHTML=`<div style="margin-bottom:14px;"><h4 style="color:#c4b5fd;margin-bottom:6px;">Добавить задание</h4><input class="withdraw-input" type="text" id="newTaskName" placeholder="Название"><input class="withdraw-input" type="number" id="newTaskReward" placeholder="Награда"><input class="withdraw-input" type="text" id="newTaskLink" placeholder="Ссылка"><input class="withdraw-input" type="text" id="newTaskDesc" placeholder="Описание"><button class="btn white" onclick="addTask()">Создать</button></div><h4 style="color:#c4b5fd;margin-bottom:6px;">Текущие</h4>${tasks.length===0?'<p style="color:var(--sub);">Нет заданий</p>':tasks.map(t=>`<div class="history-item"><div><div style="font-size:11px;">${t.name} — <span style="color:#9d8fff;">+${t.reward}</span></div></div><button class="btn red" style="padding:4px 7px;font-size:9px;width:auto;" onclick="deleteTask('${t.id}')">Удалить</button></div>`).join('')}`;}
            else if(currentAdminTab==='balance'){c.innerHTML=`<h4 style="color:#c4b5fd;margin-bottom:6px;">Выдача / Снятие</h4><input class="withdraw-input" type="text" id="balanceUserId" placeholder="ID пользователя"><input class="withdraw-input" type="number" id="balanceAmount" placeholder="Сумма (отрицательная — снятие)"><button class="btn" onclick="adjustBalance()">Применить</button><button class="btn red" onclick="adjustBalanceAll()" style="margin-top:4px;">Всем +5</button><p style="font-size:10px;color:var(--sub);margin-top:4px;" id="balanceMsg"></p>`;}
            else if(currentAdminTab==='broadcast'){c.innerHTML=`<h4 style="color:#c4b5fd;margin-bottom:6px;">Массовая рассылка</h4><textarea class="withdraw-input" id="broadcastMessage" placeholder="Текст сообщения" rows="3"></textarea><button class="btn" onclick="sendBroadcast()">Отправить всем</button><p style="font-size:10px;color:var(--sub);margin-top:4px;" id="broadcastMsg"></p>`;}
            else if(currentAdminTab==='edituser'){c.innerHTML=`<h4 style="color:#c4b5fd;margin-bottom:6px;">Редактировать пользователя</h4><input class="withdraw-input" type="text" id="editUserId" placeholder="ID пользователя"><input class="withdraw-input" type="text" id="editUserName" placeholder="Новое имя"><button class="btn" onclick="editUserName()">Сменить имя</button><p style="font-size:10px;color:var(--sub);margin-top:4px;" id="editUserMsg"></p>`;}
        }
        function addTask(){const n=document.getElementById('newTaskName').value.trim(),r=parseInt(document.getElementById('newTaskReward').value),l=document.getElementById('newTaskLink').value.trim(),d=document.getElementById('newTaskDesc').value.trim();if(!n||!r||r<1)return;globalStore.tasks.push({id:'task_'+Date.now(),name:n,reward:r,link:l,description:d});saveGlobalStore(globalStore);addLog('task_added',`Задание: ${n}`);renderAdminPanel();renderTasks();}
        function deleteTask(id){globalStore.tasks=(globalStore.tasks||[]).filter(t=>t.id!==id);saveGlobalStore(globalStore);addLog('task_deleted',`Удалено ${id}`);renderAdminPanel();renderTasks();}
        function processWithdraw(id,status){const req=globalStore.withdrawRequests.find(r=>r.id===id);if(req){req.status=status;req.adminNote=status==='paid'?'Выплачено':'Отклонено';}const uid=req?.userId;for(let k in localStorage){if(k.startsWith('semaka_v15')){try{const ud=JSON.parse(localStorage.getItem(k));if(ud.userId===uid&&ud.withdrawHistory){const hi=ud.withdrawHistory.find(h=>h.id===id);if(hi){hi.status=status;hi.adminNote=req.adminNote;localStorage.setItem(k,JSON.stringify(ud));}}}catch(e){}}}saveGlobalStore(globalStore);addLog('withdraw_'+status,`Заявка ${id}`);renderAdminPanel();}
        function toggleBan(uid){if(globalStore.users[uid]){globalStore.users[uid].banned=!globalStore.users[uid].banned;saveGlobalStore(globalStore);addLog(globalStore.users[uid].banned?'ban':'unban',`Пользователь ${uid}`);renderAdminPanel();}}
        function adjustBalance(){const uid=document.getElementById('balanceUserId').value.trim();const amt=parseFloat(document.getElementById('balanceAmount').value);const msg=document.getElementById('balanceMsg');if(!uid||isNaN(amt)){msg.textContent='Укажи ID и сумму';return;}if(globalStore.users[uid]){globalStore.users[uid].balance=Math.round((globalStore.users[uid].balance+amt)*10)/10;saveGlobalStore(globalStore);addLog('balance_adjust',`${uid}: ${amt>0?'+'+amt:amt}`);msg.textContent='Готово';msg.style.color='var(--green)';}else{msg.textContent='Не найден';msg.style.color='var(--red)';}}
        function adjustBalanceAll(){for(let uid in globalStore.users){globalStore.users[uid].balance=Math.round((globalStore.users[uid].balance+5)*10)/10;}saveGlobalStore(globalStore);addLog('balance_all','Всем +5');document.getElementById('balanceMsg').textContent='Всем +5';document.getElementById('balanceMsg').style.color='var(--green)';}
        function sendBroadcast(){const msg=document.getElementById('broadcastMessage').value.trim();if(!msg){document.getElementById('broadcastMsg').textContent='Введи текст';return;}if(!globalStore.broadcasts)globalStore.broadcasts=[];globalStore.broadcasts.push({message:msg,date:new Date().toLocaleString('ru-RU')});saveGlobalStore(globalStore);addLog('broadcast',`Рассылка: ${msg.substring(0,30)}...`);document.getElementById('broadcastMsg').textContent='Рассылка сохранена';document.getElementById('broadcastMsg').style.color='var(--green)';document.getElementById('broadcastMessage').value='';}
        function editUserName(){const uid=document.getElementById('editUserId').value.trim();const name=document.getElementById('editUserName').value.trim();const msg=document.getElementById('editUserMsg');if(!uid||!name){msg.textContent='Заполни оба поля';return;}if(globalStore.users[uid]){globalStore.users[uid].username=name;saveGlobalStore(globalStore);addLog('edit_user',`${uid} переименован в ${name}`);msg.textContent='Имя изменено';msg.style.color='var(--green)';}else{msg.textContent='Пользователь не найден';msg.style.color='var(--red)';}}
        function addLog(action,details){if(!globalStore.adminLogs)globalStore.adminLogs=[];globalStore.adminLogs.push({action,details,date:new Date().toLocaleString('ru-RU')});if(globalStore.adminLogs.length>200)globalStore.adminLogs=globalStore.adminLogs.slice(-200);saveGlobalStore(globalStore);}

        function switchPage(page){
            document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
            document.querySelectorAll('.nav-item').forEach(n=>n.classList.remove('active'));
            const map={tap:'tapPage',tasks:'tasksPage',ref:'refPage',top:'topPage',profile:'profilePage',admin:'adminPage'};
            const t=document.getElementById(map[page]);if(t)t.classList.add('active');
            const nm={tap:0,tasks:1,ref:2,top:3,profile:4};
            if(nm[page]!==undefined)document.querySelectorAll('.nav-item')[nm[page]].classList.add('active');
            if(page==='tasks')renderTasks();if(page==='top')renderLeaderboard();if(page==='profile')updateProfilePage();if(page==='admin'&&data.isAdmin)renderAdminPanel();
        }
        function updateProfilePage(){
            document.getElementById('profileName').textContent=data.tgUsername||(data.userId.substring(0,10)+'...');
            document.getElementById('profileId').textContent=data.tgId||data.userId;
            document.getElementById('profileBalance').textContent=data.balance.toFixed(1);
            document.getElementById('profileTaps').textContent=data.tapsToday;
            document.getElementById('profileRefs').textContent=data.referrals.length;
        }
        function updateUI(){
            document.getElementById('balanceDisplay').textContent=data.balance.toFixed(1);
            document.getElementById('tapCountDisplay').textContent=data.tapsToday;
            document.getElementById('tapProgress').style.width=(data.tapsToday/200*100)+'%';
            document.getElementById('refCount').textContent=data.referrals.length;
            document.getElementById('refLink').textContent='https://t.me/semaka_tapbot?start='+data.referralCode;
            document.getElementById('rewardPerTap').textContent=(Math.round((0.5+data.referrals.length*0.1*0.5)*10)/10).toFixed(1);
            updateGlowState();
            const today=new Date().toDateString();
            const btn=document.getElementById('dailyBonusBtn');
            if(data.dailyBonusClaimed&&data.lastDailyDate===today){btn.disabled=true;btn.innerHTML='<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M20 12v8a2 2 0 01-2 2H6a2 2 0 01-2-2v-8"/><path d="M12 2v18"/><path d="M4 8l8-6 8 6"/><rect x="8" y="12" width="2" height="4"/><rect x="14" y="12" width="2" height="4"/></svg> Готово';}
            else{btn.disabled=false;btn.innerHTML='<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M20 12v8a2 2 0 01-2 2H6a2 2 0 01-2-2v-8"/><path d="M12 2v18"/><path d="M4 8l8-6 8 6"/><rect x="8" y="12" width="2" height="4"/><rect x="14" y="12" width="2" height="4"/></svg> Бонус';}
        }

        document.getElementById('withdrawModal').addEventListener('click',function(e){if(e.target===this)closeWithdrawModal();});
        const adminSecret=document.getElementById('adminSecretProfile');
        adminSecret.addEventListener('touchstart',function(e){e.preventDefault();startAdminHold();});
        adminSecret.addEventListener('touchend',stopAdminHold);adminSecret.addEventListener('touchcancel',stopAdminHold);
        adminSecret.addEventListener('mousedown',function(e){e.preventDefault();startAdminHold();});
        adminSecret.addEventListener('mouseup',stopAdminHold);adminSecret.addEventListener('mouseleave',stopAdminHold);

        updateUI();renderTasks();updateProfilePage();renderLeaderboard();updateGlowState();
    </script>
</body>
</html>
