<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vestify - Premium Investment Portal</title>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>

    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Plus Jakarta Sans', sans-serif;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            background: linear-gradient(135deg, #060b19 0%, #0c162d 50%, #050914 100%);
            color: #ffffff;
            min-height: 100vh;
            padding-bottom: 90px;
            overflow-x: hidden;
        }

        /* Ambient Glow Background Particles */
        .bg-glow-1 {
            position: fixed;
            width: 300px;
            height: 300px;
            background: radial-gradient(circle, rgba(255, 65, 108, 0.15) 0%, rgba(0,0,0,0) 70%);
            top: -50px;
            left: -50px;
            z-index: -1;
            pointer-events: none;
        }
        .bg-glow-2 {
            position: fixed;
            width: 400px;
            height: 400px;
            background: radial-gradient(circle, rgba(56, 189, 248, 0.12) 0%, rgba(0,0,0,0) 70%);
            bottom: 100px;
            right: -100px;
            z-index: -1;
            pointer-events: none;
        }

        /* App Header */
        .vestify-header {
            padding: 20px 16px;
            background: rgba(11, 21, 40, 0.6);
            backdrop-filter: blur(15px);
            -webkit-backdrop-filter: blur(15px);
            border-bottom: 1px solid rgba(255, 255, 255, 0.05);
            position: sticky;
            top: 0;
            z-index: 100;
        }

        .brand-row {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }

        .logo-block {
            display: flex;
            align-items: center;
            gap: 12px;
        }

        .logo-icon {
            width: 42px;
            height: 42px;
            background: linear-gradient(135deg, #ff416c, #ff4b2b);
            border-radius: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            box-shadow: 0 0 20px rgba(255, 65, 108, 0.4);
            font-size: 20px;
            font-weight: 800;
            color: white;
            transform: rotate(-5deg);
        }

        .logo-text h1 {
            font-size: 20px;
            font-weight: 800;
            letter-spacing: 1.5px;
            background: linear-gradient(to right, #ffffff, #a5b4fc);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .logo-text p {
            font-size: 9px;
            color: #38bdf8;
            font-weight: 700;
            letter-spacing: 1px;
            text-transform: uppercase;
            margin-top: 1px;
        }

        .live-badge {
            background: rgba(16, 185, 129, 0.12);
            border: 1px solid rgba(16, 185, 129, 0.3);
            padding: 6px 14px;
            border-radius: 20px;
            display: flex;
            align-items: center;
            gap: 6px;
        }

        .live-dot {
            width: 8px;
            height: 8px;
            background-color: #10b981;
            border-radius: 50%;
            animation: pulse 1.6s infinite;
        }

        /* Glassmorphism Balance Dashboard (PKR Updated) */
        .stats-dashboard {
            background: rgba(255, 255, 255, 0.03);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            border: 1px solid rgba(255, 255, 255, 0.08);
            border-radius: 24px;
            padding: 24px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            box-shadow: 0 12px 40px rgba(0, 0, 0, 0.4);
            margin: 0 16px 20px 16px;
        }

        .stat-box-left p, .stat-box-right p {
            font-size: 13px;
            color: #94a3b8;
            font-weight: 500;
            margin-bottom: 6px;
        }

        .stat-box-left h2 {
            font-size: 28px;
            font-weight: 800;
            color: #ffffff;
            letter-spacing: -0.5px;
        }

        .stat-box-right {
            text-align: right;
            border-left: 1px solid rgba(255, 255, 255, 0.1);
            padding-left: 20px;
        }

        .stat-box-right h2 {
            font-size: 28px;
            font-weight: 800;
            color: #38bdf8;
            text-shadow: 0 0 15px rgba(56, 189, 248, 0.2);
        }

        /* Quick Action Shortcut Menu */
        .quick-actions {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 12px;
            margin: 0 16px 20px 16px;
        }

        .action-btn {
            padding: 14px;
            border-radius: 16px;
            border: none;
            outline: none;
            font-size: 14px;
            font-weight: 700;
            color: white;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
            transition: opacity 0.2s;
        }

        .btn-deposit { background: linear-gradient(135deg, #10b981 0%, #059669 100%); box-shadow: 0 6px 15px rgba(16, 185, 129, 0.25); }
        .btn-withdraw { background: linear-gradient(135deg, #3b82f6 0%, #1d4ed8 100%); box-shadow: 0 6px 15px rgba(59, 130, 246, 0.25); }
        .action-btn:active { opacity: 0.9; transform: scale(0.98); }

        /* Guidelines Notification Bar */
        .info-bar {
            background: linear-gradient(90deg, rgba(249, 115, 22, 0.08) 0%, rgba(249, 115, 22, 0.02) 100%);
            border: 1px solid rgba(249, 115, 22, 0.2);
            border-radius: 16px;
            padding: 14px 16px;
            margin: 0 16px 20px 16px;
            display: flex;
            align-items: center;
            gap: 12px;
        }

        .info-bar i { color: #f97316; font-size: 18px; animation: bounce 2s infinite; }
        .info-bar p { font-size: 11px; color: #fdba74; line-height: 1.5; font-weight: 500; }

        /* Miner Status / Countdown Section */
        .miner-status-container {
            margin: 0 16px 20px 16px;
            background: rgba(255, 255, 255, 0.01);
            border: 1px dashed rgba(255, 255, 255, 0.1);
            border-radius: 18px;
            padding: 18px;
            text-align: center;
        }

        .miner-status-title {
            font-size: 12px;
            font-weight: 700;
            color: #94a3b8;
            text-transform: uppercase;
            letter-spacing: 1px;
            margin-bottom: 12px;
        }

        .active-miner-pill {
            background: rgba(255, 255, 255, 0.04);
            border: 1px solid rgba(255, 255, 255, 0.06);
            padding: 12px;
            border-radius: 14px;
            display: inline-flex;
            align-items: center;
            gap: 8px;
            color: #cbd5e1;
            font-size: 13px;
        }

        /* Large Action Claim Button */
        .btn-container { padding: 0 16px; margin-bottom: 25px; }
        .premium-btn {
            width: 100%;
            background: linear-gradient(135deg, #ff416c 0%, #ff4b2b 100%);
            border: none;
            outline: none;
            color: white;
            padding: 18px;
            font-size: 15px;
            font-weight: 700;
            letter-spacing: 1px;
            border-radius: 16px;
            cursor: pointer;
            box-shadow: 0 8px 25px rgba(255, 65, 108, 0.35);
            position: relative;
            overflow: hidden;
        }
        .premium-btn::after {
            content: ''; position: absolute; top: -50%; left: -60%; width: 30%; height: 200%;
            background: rgba(255, 255, 255, 0.2); transform: rotate(30deg); animation: shine 4s infinite linear;
        }

        /* Bubble Rewards Grid (PKR Values Inside) */
        .bubble-zone-title { font-size: 14px; font-weight: 700; color: #f1f5f9; margin: 0 20px 15px 20px; }
        .bubble-zone-title span { color: #38bdf8; margin-right: 6px; }

        .bubble-wrapper {
            background: rgba(255, 255, 255, 0.02);
            border: 1px solid rgba(255, 255, 255, 0.04);
            border-radius: 24px;
            margin: 0 16px 25px 16px;
            padding: 24px;
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 20px;
            justify-items: center;
        }

        .crypto-bubble {
            width: 75px; height: 75px;
            background: radial-gradient(circle at 30% 30%, rgba(255, 255, 255, 0.12) 0%, rgba(255, 255, 255, 0.03) 50%, rgba(56, 189, 248, 0.15) 100%);
            border: 1px solid rgba(255, 255, 255, 0.15);
            border-radius: 50%; backdrop-filter: blur(4px); -webkit-backdrop-filter: blur(4px);
            display: flex; flex-direction: column; align-items: center; justify-content: center;
            cursor: pointer; box-shadow: inset 0 4px 10px rgba(255, 255, 255, 0.1), 0 8px 20px rgba(0,0,0,0.2);
            animation: floatBubble 4s infinite ease-in-out;
            transition: all 0.4s ease;
        }
        .crypto-bubble:nth-child(2) { animation-delay: 0.7s; }
        .crypto-bubble:nth-child(3) { animation-delay: 1.4s; }

        .crypto-bubble i { font-size: 18px; color: #38bdf8; margin-bottom: 4px; }
        .crypto-bubble span { font-size: 10px; font-weight: 700; color: #ffffff; }
        .crypto-bubble.bubble-claimed { opacity: 0.3; pointer-events: none; transform: scale(0.9); box-shadow: none; }

        /* Feature Extra Grid Layers */
        .feature-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 14px; padding: 0 16px 20px 16px; }
        .feature-card { background: rgba(255, 255, 255, 0.03); border: 1px solid rgba(255, 255, 255, 0.06); border-radius: 20px; padding: 16px; display: flex; flex-direction: column; gap: 10px; cursor: pointer; }
        .card-icon-box { width: 40px; height: 40px; border-radius: 12px; display: flex; align-items: center; justify-content: center; font-size: 18px; }
        .card-task .card-icon-box { background: rgba(251, 146, 60, 0.1); color: #fb923c; }
        .card-spin .card-icon-box { background: rgba(168, 85, 247, 0.1); color: #a855f7; }
        .feature-card h3 { font-size: 13px; font-weight: 700; color: #f1f5f9; }
        .feature-card p { font-size: 10px; color: #94a3b8; }

        /* Sticky Bottom Bar */
        .bottom-nav {
            position: fixed; bottom: 0; left: 0; width: 100%; height: 75px;
            background: rgba(6, 11, 23, 0.9); backdrop-filter: blur(20px); -webkit-backdrop-filter: blur(20px);
            border-top: 1px solid rgba(255, 255, 255, 0.06); display: flex; justify-content: space-around; align-items: center; z-index: 999; padding-bottom: 8px;
        }
        .nav-item { display: flex; flex-direction: column; align-items: center; gap: 4px; color: #64748b; text-decoration: none; font-size: 11px; font-weight: 600; cursor: pointer; }
        .nav-item.active { color: #38bdf8; }
        .nav-item-center { position: relative; top: -15px; width: 55px; height: 55px; background: linear-gradient(135deg, #38bdf8 0%, #2563eb 100%); border-radius: 50%; display: flex; align-items: center; justify-content: center; box-shadow: 0 8px 20px rgba(37, 99, 235, 0.4); border: 4px solid #060b17; }
        .nav-item-center i { font-size: 22px; color: white; }

        /* MODAL POPUPS STYLING */
        .premium-modal {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(3, 7, 18, 0.8); backdrop-filter: blur(10px); -webkit-backdrop-filter: blur(10px);
            z-index: 10000; display: flex; align-items: center; justify-content: center;
            opacity: 0; pointer-events: none; transition: opacity 0.4s ease; padding: 20px;
        }
        .premium-modal.modal-open { opacity: 1; pointer-events: auto; }
        .modal-content {
            background: linear-gradient(135deg, #0f172a 0%, #0b1324 100%); border: 1px solid rgba(255, 255, 255, 0.1);
            width: 100%; max-width: 360px; border-radius: 28px; padding: 24px; text-align: center; position: relative;
            transform: scale(0.85); transition: transform 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
        }
        .premium-modal.modal-open .modal-content { transform: scale(1); }
        .close-modal-btn { position: absolute; top: 16px; right: 16px; width: 32px; height: 32px; background: rgba(255, 255, 255, 0.05); border: 1px solid rgba(255, 255, 255, 0.1); color: #94a3b8; border-radius: 50%; display: flex; align-items: center; justify-content: center; cursor: pointer; }

        /* Input Fields for Transaction Forms */
        .modal-input {
            width: 100%; padding: 14px; border-radius: 12px; border: 1px solid rgba(255,255,255,0.1);
            background: rgba(0,0,0,0.2); color: white; font-size: 14px; margin-top: 12px; outline: none;
        }
        .modal-input:focus { border-color: #38bdf8; }

        /* Spin Wheel Visuals */
        .wheel-container { position: relative; width: 180px; height: 180px; margin: 15px auto; }
        .main-wheel { width: 100%; height: 100%; border-radius: 50%; border: 6px solid #1e293b; background: conic-gradient(#ff416c 0deg 60deg, #38bdf8 60deg 120deg, #a855f7 120deg 180deg, #fb923c 180deg 240deg, #10b981 240deg 300deg, #6366f1 300deg 360deg); transition: transform 4s cubic-bezier(0.1, 0.8, 0.1, 1); }
        .wheel-pointer { position: absolute; top: -8px; left: 50%; transform: translateX(-50%); width: 0; height: 0; border-left: 10px solid transparent; border-right: 10px solid transparent; border-top: 18px solid #ffffff; z-index: 10; }
        .wheel-center-pin { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); width: 26px; height: 26px; background: white; border-radius: 50%; z-index: 5; }

        /* Gateway Selection List */
        .gateway-list { display: flex; flex-direction: column; gap: 10px; margin-top: 15px; text-align: left; }
        .gateway-item { background: rgba(255, 255, 255, 0.03); border: 1px solid rgba(255, 255, 255, 0.08); border-radius: 14px; padding: 12px; display: flex; align-items: center; justify-content: space-between; cursor: pointer; }
        .gateway-info { display: flex; align-items: center; gap: 12px; }
        .gateway-logo-box { width: 36px; height: 36px; border-radius: 10px; display: flex; align-items: center; justify-content: center; font-size: 14px; font-weight: bold; }
        .gw-easypaisa { background: #10b981; color: white; }
        .gw-jazzcash { background: #dc2626; color: white; }

        /* Toast Popup Alerts */
        .reward-toast { position: fixed; top: 30px; left: 50%; transform: translateX(-50%) translateY(-100px); background: linear-gradient(135deg, #10b981 0%, #059669 100%); border: 1px solid rgba(255,255,255,0.2); padding: 14px 24px; border-radius: 20px; box-shadow: 0 10px 30px rgba(16, 185, 129, 0.3); display: flex; align-items: center; gap: 10px; z-index: 11000; font-weight: 700; font-size: 14px; transition: transform 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275); pointer-events: none; }
        .reward-toast.toast-show { transform: translateX(-50%) translateY(0); }

        @keyframes pulse { 0% { transform: scale(0.95); box-shadow: 0 0 0 0 rgba(16, 185, 129, 0.6); } 70% { transform: scale(1); box-shadow: 0 0 0 8px rgba(16, 185, 129, 0); } 100% { transform: scale(0.95); box-shadow: 0 0 0 0 rgba(16, 185, 129, 0); } }
        @keyframes bounce { 0%, 100% { transform: translateY(0); } 50% { transform: translateY(-4px); } }
        @keyframes shine { 0% { left: -60%; } 15% { left: 130%; } 100% { left: 130%; } }
        @keyframes floatBubble { 0%, 100% { transform: translateY(0px) scale(1); } 50% { transform: translateY(-8px) scale(1.03); } }
    </style>
</head>
<body>

    <div class="bg-glow-1"></div>
    <div class="bg-glow-2"></div>

    <div id="rewardToast" class="reward-toast">
        <i class="fa-solid fa-circle-check"></i>
        <span id="toastMsg">Action Updated!</span>
    </div>

    <div class="vestify-header">
        <div class="brand-row">
            <div class="logo-block">
                <div class="logo-icon">V</div>
                <div class="logo-text">
                    <h1>VESTIFY</h1>
                    <p>✨ Premium Investment Portal ✨</p>
                </div>
            </div>
            <div class="live-badge">
                <div class="live-dot"></div>
                <span>LIVE</span>
            </div>
        </div>
    </div>

    <div class="stats-dashboard">
        <div class="stat-box-left">
            <p>Total Balance</p>
            <h2>Rs. <span id="assetDisplay">0.00</span></h2>
        </div>
        <div class="stat-box-right">
            <p>Active Nodes</p>
            <h2 id="minerDisplay">0</h2>
        </div>
    </div>

    <div class="quick-actions">
        <button class="action-btn btn-deposit" onclick="openDepositModal()"><i class="fa-solid fa-wallet"></i> Deposit</button>
        <button class="action-btn btn-withdraw" onclick="openWithdrawModal()"><i class="fa-solid fa-money-bill-transfer"></i> Withdraw</button>
    </div>

    <div class="info-bar">
        <i class="fa-solid fa-triangle-exclamation"></i>
        <p>Aastore & GB region fast automatic routing enabled. Tap active bubbles or lease nodes starting from 200 PKR.</p>
    </div>

    <div class="miner-status-container">
        <div class="miner-status-title"><i class="fa-solid fa-fire"></i> Active Cycles Remaining</div>
        <div id="timerPill" class="active-miner-pill"><i class="fa-solid fa-hourglass-half"></i> No Active Miners found.</div>
    </div>

    <div class="btn-container">
        <button class="premium-btn" onclick="triggerMainClaim()"><i class="fa-solid fa-bolt" style="margin-right: 6px;"></i> CLAIM ALL REWARDS</button>
    </div>

    <div class="bubble-zone-title"><span><i class="fa-solid fa-cubes-blur"></i></span> Instant Rewards Mining Grid (PKR)</div>
    <div class="bubble-wrapper">
        <div class="crypto-bubble" onclick="claimBubble(this, 45)">
            <i class="fa-brands fa-bitcoin"></i><span>Rs. 45</span>
        </div>
        <div class="crypto-bubble" onclick="claimBubble(this, 70)">
            <i class="fa-brands fa-ethereum"></i><span>Rs. 70</span>
        </div>
        <div class="crypto-bubble" onclick="claimBubble(this, 30)">
            <i class="fa-solid fa-litecoin-sign"></i><span>Rs. 30</span>
        </div>
        <div class="crypto-bubble" onclick="claimBubble(this, 110)">
            <i class="fa-solid fa-gem"></i><span>Rs. 110</span>
        </div>
        <div class="crypto-bubble" onclick="claimBubble(this, 15)">
            <i class="fa-solid fa-coins"></i><span>Rs. 15</span>
        </div>
        <div class="crypto-bubble" onclick="claimBubble(this, 85)">
            <i class="fa-solid fa-bolt-lightning"></i><span>Rs. 85</span>
        </div>
    </div>

    <div class="feature-grid">
        <div class="feature-card card-task" onclick="openDepositModal()">
            <div class="card-icon-box"><i class="fa-solid fa-layer-group"></i></div>
            <h3>Premium Plans</h3>
            <p>Lease high yield packages starting from 200 PKR.</p>
        </div>
        <div class="feature-card card-spin" onclick="openSpinModal()">
            <div class="card-icon-box"><i class="fa-solid fa-circle-notch"></i></div>
            <h3>Lucky Wheel</h3>
            <p>Spin the lucky wheel to trigger custom rewards.</p>
        </div>
    </div>

    <div class="bottom-nav">
        <a class="nav-item active"><i class="fa-solid fa-house"></i><span>Home</span></a>
        <a class="nav-item" onclick="openSpinModal()"><i class="fa-solid fa-star"></i><span>Lucky Spin</span></a>
        <a class="nav-item nav-item-center" onclick="triggerMainClaim()"><i class="fa-solid fa-circle-play"></i></a>
        <a class="nav-item" onclick="openDepositModal()"><i class="fa-solid fa-wallet"></i><span>Deposit</span></a>
        <a class="nav-item" onclick="openWithdrawModal()"><i class="fa-solid fa-money-bill-transfer"></i><span>Withdraw</span></a>
    </div>

    <div id="depositModal" class="premium-modal">
        <div class="modal-content">
            <div class="close-modal-btn" onclick="closeDepositModal()">&times;</div>
            <h3 style="color:white; font-size:18px; font-weight:700; margin-bottom:10px;">Deposit Portal</h3>
            <p style="color:#94a3b8; font-size:12px;">Select gateway & enter amount to recharge account.</p>
            
            <div class="gateway-list">
                <div class="gateway-item" onclick="selectGateway('EasyPaisa')">
                    <div class="gateway-info"><div class="gateway-logo-box gw-easypaisa">EP</div><h4>EasyPaisa Account</h4></div>
                </div>
                <div class="gateway-item" onclick="selectGateway('JazzCash')">
                    <div class="gateway-info"><div class="gateway-logo-box gw-jazzcash">JC</div><h4>JazzCash Account</h4></div>
                </div>
            </div>
            
            <input type="number" id="depositAmount" class="modal-input" placeholder="Enter Amount (Min 200 PKR)" min="200">
            <input type="text" id="depositTrx" class="modal-input" placeholder="Enter 3737/8558 TRX ID">
            <button class="premium-btn" onclick="submitDeposit()" style="padding:12px; font-size:14px; margin-top:15px;">CONFIRM DEPOSIT</button>
        </div>
    </div>

    <div id="withdrawModal" class="premium-modal">
        <div class="modal-content">
            <div class="close-modal-btn" onclick="closeWithdrawModal()">&times;</div>
            <h3 style="color:white; font-size:18px; font-weight:700; margin-bottom:10px;">Withdraw Funds</h3>
            <p style="color:#94a3b8; font-size:12px;">Get instant routing payout to your local provider</p>
            
            <input type="text" id="withdrawTitle" class="modal-input" placeholder="Account Holder Name">
            <input type="text" id="withdrawNumber" class="modal-input" placeholder="EasyPaisa/JazzCash Number">
            <input type="number" id="withdrawAmount" class="modal-input" placeholder="Amount to Withdraw (PKR)">
            <button class="premium-btn" onclick="submitWithdrawal()" style="padding:12px; font-size:14px; margin-top:15px; background:linear-gradient(135deg, #3b82f6 0%, #1d4ed8 100%); box-shadow:0 5px 15px rgba(59,130,246,0.3);">REQUEST PAYOUT</button>
        </div>
    </div>

    <div id="spinModal" class="premium-modal">
        <div class="modal-content">
            <div class="close-modal-btn" onclick="closeSpinModal()">&times;</div>
            <h3 style="font-size:18px; font-weight:700; color:white; margin-bottom:6px;">Lucky Spin Wheel</h3>
            <div class="wheel-container">
                <div class="wheel-pointer"></div><div class="wheel-center-pin"></div>
                <div id="spinWheelElement" class="main-wheel"></div>
            </div>
            <button class="premium-btn" id="spinBtn" onclick="startSpinEngine()" style="padding:12px; font-size:14px; margin-top:10px;">SPIN NOW</button>
        </div>
    </div>


    <script>
        // TODO: Paste your official Firebase Web App configuration inside this object
        const firebaseConfig = {
            apiKey: "YOUR_API_KEY_HERE",
            authDomain: "YOUR_AUTH_DOMAIN_HERE",
            projectId: "YOUR_PROJECT_ID_HERE",
            storageBucket: "YOUR_STORAGE_BUCKET_HERE",
            messagingSenderId: "YOUR_MESSAGING_SENDER_ID_HERE",
            appId: "YOUR_APP_ID_HERE"
        };

        // Initialize Firebase Instantly
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();

        // Local Reactivity State Variables
        let currentUserId = "dummy_user_session"; // Replaced dynamically on genuine Auth state changes
        let currentAssets = 0.00;
        let totalMiners = 0;
        let selectedDepositGateway = "";
        let countdownTime = 24 * 60 * 60;
        let countdownInterval = null;
        let canSpin = true;

        // Firebase Sync Engine Integration
        function syncUserDataToFirebase() {
            if(!db) return;
            db.collection("users").doc(currentUserId).set({
                balance: currentAssets,
                miners: totalMiners,
                lastUpdated: firebase.firestore.FieldValue.serverTimestamp()
            }, { merge: true })
            .then(() => console.log("Database synchronized successfully."))
            .catch(err => console.error("Firebase sync error: ", err));
        }

        function loadUserDataFromFirebase() {
            db.collection("users").doc(currentUserId).get().then((doc) => {
                if(doc.exists) {
                    const data = doc.data();
                    currentAssets = data.balance || 0.00;
                    totalMiners = data.miners || 0;
                    document.getElementById('assetDisplay').innerText = currentAssets.toFixed(2);
                    document.getElementById('minerDisplay').innerText = totalMiners;
                    if(totalMiners > 0) startMiningCountdown();
                }
            });
        }

        // Authentication Hook Simulator
        auth.onAuthStateChanged((user) => {
            if(user) {
                currentUserId = user.uid;
                loadUserDataFromFirebase();
            } else {
                // If no user session found, create an anonymous session for zero friction
                auth.signInAnonymously().catch(err => console.log("Anonymous Auth disabled or configuration required. fallback active."));
            }
        });

        // Bubble Claim Logic Engine
        function claimBubble(element, amount) {
            if (element.classList.contains('bubble-claimed')) return;
            element.classList.add('bubble-claimed');
            updateAssets(amount);
            showRewardToast(`Claim Successful! +Rs. ${amount}`);
            
            if (totalMiners === 0) {
                totalMiners = 1;
                document.getElementById('minerDisplay').innerText = totalMiners;
                startMiningCountdown();
            }
            syncUserDataToFirebase();
        }

        function triggerMainClaim() {
            let availableBubbles = document.querySelectorAll('.crypto-bubble:not(.bubble-claimed)');
            if (availableBubbles.length === 0) {
                showRewardToast("No active rewards to claim right now!");
                return;
            }
            let gatheredReward = 0;
            availableBubbles.forEach(bubble => {
                bubble.classList.add('bubble-claimed');
                let rewardValue = parseFloat(bubble.querySelector('span').innerText.replace('Rs. ', ''));
                gatheredReward += rewardValue;
            });
            updateAssets(gatheredReward);
            showRewardToast(`Total Collected: +Rs. ${gatheredReward} 🎉`);
            syncUserDataToFirebase();
        }

        function updateAssets(val) {
            currentAssets += val;
            document.getElementById('assetDisplay').innerText = currentAssets.toFixed(2);
        }

        // Modals Management Wrappers
        function openDepositModal() { document.getElementById('depositModal').classList.add('modal-open'); }
        function closeDepositModal() { document.getElementById('depositModal').classList.remove('modal-open'); }
        function openWithdrawModal() { document.getElementById('withdrawModal').classList.add('modal-open'); }
        function closeWithdrawModal() { document.getElementById('withdrawModal').classList.remove('modal-open'); }
        function openSpinModal() { document.getElementById('spinModal').classList.add('modal-open'); }
        function closeSpinModal() { document.getElementById('spinModal').classList.remove('modal-open'); }

        function selectGateway(gateway) {
            selectedDepositGateway = gateway;
            showRewardToast(`Selected Gateway: ${gateway}`);
        }

        // Functional Secure Deposit Processor
        function submitDeposit() {
            const amount = parseFloat(document.getElementById('depositAmount').value);
            const trxId = document.getElementById('depositTrx').value.trim();

            if(!selectedDepositGateway) { showRewardToast("Please select a gateway first!"); return; }
            if(isNaN(amount) || amount < 200) { showRewardToast("Minimum recharge threshold is 200 PKR."); return; }
            if(!trxId) { showRewardToast("Please enter a valid Transaction TRX ID!"); return; }

            // Push pending deposit tracking logs directly to Firebase database
            db.collection("deposits").add({
                userId: currentUserId,
                amount: amount,
                gateway: selectedDepositGateway,
                transactionId: trxId,
                status: "Pending Approval",
                timestamp: firebase.firestore.FieldValue.serverTimestamp()
            }).then(() => {
                closeDepositModal();
                showRewardToast("Deposit request log uploaded! Admin review active.");
                // Clean input fields
                document.getElementById('depositAmount').value = "";
                document.getElementById('depositTrx').value = "";
            });
        }

        // Functional Payout Request Processor
        function submitWithdrawal() {
            const name = document.getElementById('withdrawTitle').value.trim();
            const number = document.getElementById('withdrawNumber').value.trim();
            const amount = parseFloat(document.getElementById('withdrawAmount').value);

            if(!name || !number || isNaN(amount)) { showRewardToast("All input parameters are mandatory."); return; }
            if(amount > currentAssets) { showRewardToast("Insufficient balance error!"); return; }

            currentAssets -= amount;
            document.getElementById('assetDisplay').innerText = currentAssets.toFixed(2);

            db.collection("withdrawals").add({
                userId: currentUserId,
                accountName: name,
                accountNumber: number,
                payoutAmount: amount,
                status: "Processing Payout",
                timestamp: firebase.firestore.FieldValue.serverTimestamp()
            }).then(() => {
                closeWithdrawalModal();
                showRewardToast("Withdrawal locked into pipeline successfully!");
                syncUserDataToFirebase();
                // Clean inputs
                document.getElementById('withdrawTitle').value = "";
                document.getElementById('withdrawNumber').value = "";
                document.getElementById('withdrawAmount').value = "";
            });
        }

        // Gamified Lucky Spin Engine
        function startSpinEngine() {
            if (!canSpin) { showRewardToast("Free entry used. Try again in next cycle."); return; }
            canSpin = false;
            const wheel = document.getElementById('spinWheelElement');
            const randomDegrees = Math.floor(Math.random() * 360) + 1800;
            wheel.style.transform = `rotate(${randomDegrees}deg)`;

            const winPrizes = ["Rs. 50 Bonus", "Rs. 200 Mega Win", "Rs. 20 Reward", "Free Miner Node", "Rs. 500 Jackpot", "Rs. 10 Payout"];
            const selectedPrize = winPrizes[Math.floor(Math.random() * winPrizes.length)];

            setTimeout(() => {
                closeSpinModal();
                showRewardToast(`Congratulations! You won: ${selectedPrize}`);
                if(selectedPrize.includes('Rs.')) {
                    let winValue = parseFloat(selectedPrize.replace(/[^0-9.]/g, ''));
                    updateAssets(winValue);
                } else {
                    totalMiners += 1;
                    document.getElementById('minerDisplay').innerText = totalMiners;
                }
                syncUserDataToFirebase();
                setTimeout(() => { wheel.style.transform = 'rotate(0deg)'; canSpin = true; }, 500);
            }, 4100);
        }

        function startMiningCountdown() {
            if (countdownInterval) clearInterval(countdownInterval);
            const timerPill = document.getElementById('timerPill');
            countdownInterval = setInterval(() => {
                if (countdownTime <= 0) {
                    clearInterval(countdownInterval);
                    timerPill.innerHTML = `<i class="fa-solid fa-circle-check" style="color:#10b981;"></i> Mining Cycle Ready!`;
                    return;
                }
                countdownTime--;
                let hrs = Math.floor(countdownTime / 3600);
                let mins = Math.floor((countdownTime % 3600) / 60);
                let secs = countdownTime % 60;
                timerPill.innerHTML = `<i class="fa-solid fa-stopwatch" style="color:#38bdf8;"></i> Cycle: ${hrs.toString().padStart(2,'0')}:${mins.toString().padStart(2,'0')}:${secs.toString().padStart(2,'0')}`;
            }, 1000);
        }

        function showRewardToast(message) {
            const toast = document.getElementById('rewardToast');
            document.getElementById('toastMsg').innerText = message;
            toast.classList.add('toast-show');
            setTimeout(() => { toast.classList.remove('toast-show'); }, 3000);
        }
    </script>
</body>
</html>
