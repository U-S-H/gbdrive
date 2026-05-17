<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vestify - Premium Mining Portal</title>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
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

        /* Ambient Glow Particles */
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

        .live-badge span {
            font-size: 11px;
            font-weight: 700;
            color: #10b981;
            letter-spacing: 0.5px;
        }

        /* Glassmorphism Balance Dashboard */
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
            font-size: 32px;
            font-weight: 800;
            color: #ffffff;
            letter-spacing: -0.5px;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .stat-box-left h2 span {
            font-size: 14px;
            color: #64748b;
            cursor: pointer;
        }

        .stat-box-right {
            text-align: right;
            border-left: 1px solid rgba(255, 255, 255, 0.1);
            padding-left: 30px;
        }

        .stat-box-right h2 {
            font-size: 32px;
            font-weight: 800;
            color: #38bdf8;
            text-shadow: 0 0 15px rgba(56, 189, 248, 0.2);
        }

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

        .info-bar i {
            color: #f97316;
            font-size: 18px;
            animation: bounce 2s infinite;
        }

        .info-bar p {
            font-size: 12px;
            color: #fdba74;
            line-height: 1.5;
            font-weight: 500;
        }

        /* Active Miners / Countdown section */
        .miner-status-container {
            margin: 0 16px 20px 16px;
            background: rgba(255, 255, 255, 0.01);
            border: 1px dashed rgba(255, 255, 255, 0.1);
            border-radius: 18px;
            padding: 20px;
            text-align: center;
        }

        .miner-status-title {
            font-size: 12px;
            font-weight: 700;
            color: #94a3b8;
            text-transform: uppercase;
            letter-spacing: 1px;
            margin-bottom: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 6px;
        }

        .miner-status-title i { color: #ff416c; }

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

        /* Action Claim Button */
        .btn-container {
            padding: 0 16px;
            margin-bottom: 25px;
        }

        .premium-btn {
            width: 100%;
            background: linear-gradient(135deg, #ff416c 0%, #ff4b2b 100%);
            border: none;
            outline: none;
            color: white;
            padding: 18px;
            font-size: 16px;
            font-weight: 700;
            letter-spacing: 1px;
            border-radius: 16px;
            cursor: pointer;
            box-shadow: 0 8px 25px rgba(255, 65, 108, 0.35);
            transition: all 0.3s ease;
            position: relative;
            overflow: hidden;
        }

        .premium-btn:active {
            transform: scale(0.98);
        }

        .premium-btn::after {
            content: '';
            position: absolute;
            top: -50%;
            left: -60%;
            width: 30%;
            height: 200%;
            background: rgba(255, 255, 255, 0.2);
            transform: rotate(30deg);
            animation: shine 4s infinite linear;
        }

        /* Bubble Rewards Mining Grid */
        .bubble-zone-title {
            font-size: 14px;
            font-weight: 700;
            color: #f1f5f9;
            margin: 0 20px 15px 20px;
            display: flex;
            align-items: center;
            gap: 8px;
        }
        .bubble-zone-title span { color: #38bdf8; }

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
            width: 75px;
            height: 75px;
            background: radial-gradient(circle at 30% 30%, rgba(255, 255, 255, 0.12) 0%, rgba(255, 255, 255, 0.03) 50%, rgba(56, 189, 248, 0.15) 100%);
            border: 1px solid rgba(255, 255, 255, 0.15);
            border-radius: 50%;
            backdrop-filter: blur(4px);
            -webkit-backdrop-filter: blur(4px);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            box-shadow: inset 0 4px 10px rgba(255, 255, 255, 0.1), 0 8px 20px rgba(0,0,0,0.2);
            animation: floatBubble 4s infinite ease-in-out;
            transition: all 0.4s ease;
        }

        .crypto-bubble:nth-child(2) { animation-delay: 0.7s; }
        .crypto-bubble:nth-child(3) { animation-delay: 1.4s; }
        .crypto-bubble:nth-child(4) { animation-delay: 2.1s; }
        .crypto-bubble:nth-child(5) { animation-delay: 2.8s; }
        .crypto-bubble:nth-child(6) { animation-delay: 3.5s; }

        .crypto-bubble:active {
            transform: scale(0.85);
            background: rgba(56, 189, 248, 0.3);
        }

        .crypto-bubble i {
            font-size: 20px;
            color: #38bdf8;
            margin-bottom: 4px;
        }

        .crypto-bubble span {
            font-size: 11px;
            font-weight: 700;
            color: #ffffff;
        }

        .crypto-bubble.bubble-claimed {
            opacity: 0.3;
            pointer-events: none;
            transform: scale(0.9);
            box-shadow: none;
            border-color: rgba(255,255,255,0.05);
        }

        /* Feature Cards Layer */
        .feature-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 14px;
            padding: 0 16px 20px 16px;
        }

        .feature-card {
            background: rgba(255, 255, 255, 0.03);
            border: 1px solid rgba(255, 255, 255, 0.06);
            border-radius: 20px;
            padding: 16px;
            display: flex;
            flex-direction: column;
            gap: 10px;
            cursor: pointer;
        }

        .feature-card:active { background: rgba(255, 255, 255, 0.08); }

        .card-icon-box {
            width: 40px;
            height: 40px;
            border-radius: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 18px;
        }

        .card-task .card-icon-box { background: rgba(251, 146, 60, 0.1); color: #fb923c; }
        .card-spin .card-icon-box { background: rgba(168, 85, 247, 0.1); color: #a855f7; }

        .feature-card h3 { font-size: 14px; font-weight: 700; color: #f1f5f9; }
        .feature-card p { font-size: 11px; color: #94a3b8; }

        /* Bottom Nav Bar */
        .bottom-nav {
            position: fixed;
            bottom: 0;
            left: 0;
            width: 100%;
            height: 75px;
            background: rgba(6, 11, 23, 0.9);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            border-top: 1px solid rgba(255, 255, 255, 0.06);
            display: flex;
            justify-content: space-around;
            align-items: center;
            z-index: 999;
            padding-bottom: 8px;
        }

        .nav-item {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 4px;
            color: #64748b;
            text-decoration: none;
            font-size: 11px;
            font-weight: 600;
            cursor: pointer;
        }

        .nav-item i { font-size: 20px; }
        .nav-item.active { color: #38bdf8; }

        .nav-item.active i {
            color: #38bdf8;
            text-shadow: 0 0 10px rgba(56, 189, 248, 0.4);
        }

        .nav-item-center {
            position: relative;
            top: -15px;
            width: 55px;
            height: 55px;
            background: linear-gradient(135deg, #38bdf8 0%, #2563eb 100%);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            box-shadow: 0 8px 20px rgba(37, 99, 235, 0.4);
            border: 4px solid #060b17;
        }
        .nav-item-center i { font-size: 22px; color: white; }

        /* POPUP MODALS */
        .premium-modal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(3, 7, 18, 0.8);
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
            z-index: 10000;
            display: flex;
            align-items: center;
            justify-content: center;
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.4s ease;
            padding: 20px;
        }

        .premium-modal.modal-open { opacity: 1; pointer-events: auto; }

        .modal-content {
            background: linear-gradient(135deg, #0f172a 0%, #0b1324 100%);
            border: 1px solid rgba(255, 255, 255, 0.1);
            width: 100%;
            max-width: 360px;
            border-radius: 28px;
            padding: 24px;
            text-align: center;
            position: relative;
            transform: scale(0.85);
            transition: transform 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
        }

        .premium-modal.modal-open .modal-content { transform: scale(1); }

        .close-modal-btn {
            position: absolute;
            top: 16px;
            right: 16px;
            width: 32px;
            height: 32px;
            background: rgba(255, 255, 255, 0.05);
            border: 1px solid rgba(255, 255, 255, 0.1);
            color: #94a3b8;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
        }

        /* Spin Wheel */
        .wheel-container {
            position: relative;
            width: 200px;
            height: 200px;
            margin: 20px auto;
        }

        .main-wheel {
            width: 100%;
            height: 100%;
            border-radius: 50%;
            border: 6px solid #1e293b;
            background: conic-gradient(
                #ff416c 0deg 60deg, 
                #38bdf8 60deg 120deg, 
                #a855f7 120deg 180deg, 
                #fb923c 180deg 240deg, 
                #10b981 240deg 300deg,
                #6366f1 300deg 360deg
            );
            transition: transform 4s cubic-bezier(0.1, 0.8, 0.1, 1);
        }

        .wheel-pointer {
            position: absolute;
            top: -10px;
            left: 50%;
            transform: translateX(-50%);
            width: 0;
            height: 0;
            border-left: 12px solid transparent;
            border-right: 12px solid transparent;
            border-top: 20px solid #ffffff;
            z-index: 10;
        }

        .wheel-center-pin {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 30px;
            height: 30px;
            background: white;
            border-radius: 50%;
            z-index: 5;
        }

        /* Payment Gateways */
        .gateway-list { display: flex; flex-direction: column; gap: 12px; margin-top: 20px; text-align: left; }
        .gateway-item {
            background: rgba(255, 255, 255, 0.03);
            border: 1px solid rgba(255, 255, 255, 0.08);
            border-radius: 16px;
            padding: 14px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            cursor: pointer;
        }
        .gateway-info { display: flex; align-items: center; gap: 12px; }
        .gateway-logo-box { width: 36px; height: 36px; border-radius: 10px; display: flex; align-items: center; justify-content: center; font-size: 16px; font-weight: bold; }
        
        .gw-easypaisa { background: #10b981; color: white; }
        .gw-jazzcash { background: #dc2626; color: white; }
        .gw-usdt { background: #26a17b; color: white; }

        .gateway-name h4 { font-size: 13px; font-weight: 600; color: #f1f5f9; }
        .gateway-name p { font-size: 10px; color: #64748b; }

        /* Toast Popup */
        .reward-toast {
            position: fixed;
            top: 30px;
            left: 50%;
            transform: translateX(-50%) translateY(-100px);
            background: linear-gradient(135deg, #10b981 0%, #059669 100%);
            border: 1px solid rgba(255,255,255,0.2);
            padding: 14px 24px;
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(16, 185, 129, 0.3);
            display: flex;
            align-items: center;
            gap: 10px;
            z-index: 11000;
            font-weight: 700;
            font-size: 14px;
            transition: transform 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            pointer-events: none;
        }
        .reward-toast.toast-show { transform: translateX(-50%) translateY(0); }

        /* Keyframe Animations */
        @keyframes pulse {
            0% { transform: scale(0.95); box-shadow: 0 0 0 0 rgba(16, 185, 129, 0.6); }
            70% { transform: scale(1); box-shadow: 0 0 0 8px rgba(16, 185, 129, 0); }
            100% { transform: scale(0.95); box-shadow: 0 0 0 0 rgba(16, 185, 129, 0); }
        }
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
        <span id="toastMsg">Claim Successful!</span>
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
            <p>Total Assets</p>
            <h2>$<span id="assetDisplay">0.00</span> <span class="fa-solid fa-chevron-down" onclick="openGatewayModal()"></span></h2>
        </div>
        <div class="stat-box-right">
            <p>Active Miners</p>
            <h2 id="minerDisplay">0</h2>
        </div>
    </div>

    <div class="info-bar">
        <i class="fa-solid fa-triangle-exclamation"></i>
        <p>Tap on any active glass bubble to instantly claim premium rewards. New machines activate cycles instantly.</p>
    </div>

    <div class="miner-status-container">
        <div class="miner-status-title">
            <i class="fa-solid fa-fire"></i> Active Miners Status & Cycle
        </div>
        <div id="timerPill" class="active-miner-pill">
            <i class="fa-solid fa-hourglass-half"></i> No Active Miners. Lease a setup below!
        </div>
    </div>

    <div class="btn-container">
        <button class="premium-btn" onclick="triggerMainClaim()">
            <i class="fa-solid fa-bolt" style="margin-right: 6px;"></i> CLAIM ALL ACTIVE REWARDS
        </button>
    </div>

    <div class="bubble-zone-title">
        <span><i class="fa-solid fa-cubes-blur"></i></span> Instant Rewards Mining Grid
    </div>
    <div class="bubble-wrapper">
        <div class="crypto-bubble" onclick="claimBubble(this, 0.15)">
            <i class="fa-brands fa-bitcoin"></i>
            <span>$0.15</span>
        </div>
        <div class="crypto-bubble" onclick="claimBubble(this, 0.25)">
            <i class="fa-brands fa-ethereum"></i>
            <span>$0.25</span>
        </div>
        <div class="crypto-bubble" onclick="claimBubble(this, 0.10)">
            <i class="fa-solid fa-litecoin-sign"></i>
            <span>$0.10</span>
        </div>
        <div class="crypto-bubble" onclick="claimBubble(this, 0.40)">
            <i class="fa-solid fa-gem"></i>
            <span>$0.40</span>
        </div>
        <div class="crypto-bubble" onclick="claimBubble(this, 0.05)">
            <i class="fa-solid fa-coins"></i>
            <span>$0.05</span>
        </div>
        <div class="crypto-bubble" onclick="claimBubble(this, 0.30)">
            <i class="fa-solid fa-bolt-lightning"></i>
            <span>$0.30</span>
        </div>
    </div>

    <div class="feature-grid">
        <div class="feature-card card-task" onclick="openGatewayModal()">
            <div class="card-icon-box"><i class="fa-solid fa-wallet"></i></div>
            <h3>Deposit Plans</h3>
            <p>Unlock premium models starting from 200 PKR.</p>
        </div>
        <div class="feature-card card-spin" onclick="openSpinModal()">
            <div class="card-icon-box"><i class="fa-solid fa-circle-notch"></i></div>
            <h3>Lucky Spin</h3>
            <p>Spin the randomized wheel to win free prizes.</p>
        </div>
    </div>

    <div class="bottom-nav">
        <a class="nav-item active">
            <i class="fa-solid fa-house"></i>
            <span>Home</span>
        </a>
        <a class="nav-item" onclick="openSpinModal()">
            <i class="fa-solid fa-star"></i>
            <span>Lucky Spin</span>
        </a>
        <a class="nav-item nav-item-center" onclick="triggerMainClaim()">
            <i class="fa-solid fa-circle-play"></i>
        </a>
        <a class="nav-item" onclick="openGatewayModal()">
            <i class="fa-solid fa-wallet"></i>
            <span>Deposit</span>
        </a>
        <a class="nav-item">
            <i class="fa-solid fa-user"></i>
            <span>Me</span>
        </a>
    </div>

    <div id="spinModal" class="premium-modal">
        <div class="modal-content">
            <div class="close-modal-btn" onclick="closeSpinModal()">&times;</div>
            <h3 style="font-size:18px; font-weight:700; color:white; margin-bottom:6px;">Lucky Spin Wheel</h3>
            <p style="font-size:12px; color:#94a3b8; margin-bottom:15px;">Test your luck for randomized rewards!</p>
            
            <div class="wheel-container">
                <div class="wheel-pointer"></div>
                <div class="wheel-center-pin"></div>
                <div id="spinWheelElement" class="main-wheel"></div>
            </div>

            <button class="premium-btn" id="spinBtn" onclick="startSpinEngine()" style="padding:12px; font-size:14px; margin-top:10px;">
                SPIN WHEEL NOW
            </button>
        </div>
    </div>

    <div id="gatewayModal" class="premium-modal">
        <div class="modal-content">
            <div class="close-modal-btn" onclick="closeGatewayModal()">&times;</div>
            <h3 style="font-size:18px; font-weight:700; color:white; margin-bottom:6px;">Secure Gateway Portal</h3>
            <p style="font-size:12px; color:#94a3b8;">Select your preferred automatic deposit gateway</p>
            
            <div class="gateway-list">
                <div class="gateway-item" onclick="triggerGatewayAction('EasyPaisa')">
                    <div class="gateway-info">
                        <div class="gateway-logo-box gw-easypaisa">EP</div>
                        <div class="gateway-name">
                            <h4>EasyPaisa Portal</h4>
                            <p>Instant Automatic Verification</p>
                        </div>
                    </div>
                    <i class="fa-solid fa-chevron-right" style="font-size:12px; color:#64748b;"></i>
                </div>

                <div class="gateway-item" onclick="triggerGatewayAction('JazzCash')">
                    <div class="gateway-info">
                        <div class="gateway-logo-box gw-jazzcash">JC</div>
                        <div class="gateway-name">
                            <h4>JazzCash Checkout</h4>
                            <p>Secure Standard Gateway</p>
                        </div>
                    </div>
                    <i class="fa-solid fa-chevron-right" style="font-size:12px; color:#64748b;"></i>
                </div>

                <div class="gateway-item" onclick="triggerGatewayAction('USDT TRC20')">
                    <div class="gateway-info">
                        <div class="gateway-logo-box gw-usdt"><i class="fa-solid fa-t"></i></div>
                        <div class="gateway-name">
                            <h4>USDT (TRC20)</h4>
                            <p>Global Decentralized Crypto</p>
                        </div>
                    </div>
                    <i class="fa-solid fa-chevron-right" style="font-size:12px; color:#64748b;"></i>
                </div>
            </div>
        </div>
    </div>

    <script>
        let currentAssets = 0.00;
        let totalMiners = 0;
        let countdownTime = 24 * 60 * 60; 
        let countdownInterval = null;
        let canSpin = true;

        function claimBubble(element, amount) {
            if (element.classList.contains('bubble-claimed')) return;

            element.classList.add('bubble-claimed');
            updateAssets(amount);
            showRewardToast(`Claim Successful! +$${amount.toFixed(2)}`);
            
            if (totalMiners === 0) {
                totalMiners = 1;
                document.getElementById('minerDisplay').innerText = totalMiners;
                startMiningCountdown();
            }
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
                let rewardValue = parseFloat(bubble.querySelector('span').innerText.replace('$', ''));
                gatheredReward += rewardValue;
            });

            updateAssets(gatheredReward);
            showRewardToast(`Total Claim Successful: +$${gatheredReward.toFixed(2)} 🎉`);
        }

        function updateAssets(val) {
            currentAssets += val;
            document.getElementById('assetDisplay').innerText = currentAssets.toFixed(2);
        }

        function showRewardToast(message) {
            const toast = document.getElementById('rewardToast');
            document.getElementById('toastMsg').innerText = message;
            toast.classList.add('toast-show');
            setTimeout(() => { toast.classList.remove('toast-show'); }, 3000);
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

                timerPill.innerHTML = `<i class="fa-solid fa-stopwatch" style="color:#38bdf8;"></i> Mining Cycle: ${hrs.toString().padStart(2,'0')}:${mins.toString().padStart(2,'0')}:${secs.toString().padStart(2,'0')}`;
            }, 1000);
        }

        function openSpinModal() { document.getElementById('spinModal').classList.add('modal-open'); }
        function closeSpinModal() { document.getElementById('spinModal').classList.remove('modal-open'); }
        
        function openGatewayModal() { document.getElementById('gatewayModal').classList.add('modal-open'); }
        function closeGatewayModal() { document.getElementById('gatewayModal').classList.remove('modal-open'); }

        function startSpinEngine() {
            if (!canSpin) {
                showRewardToast("Free entry used up! Try again in next cycle.");
                return;
            }
            canSpin = false;
            
            const wheel = document.getElementById('spinWheelElement');
            const randomDegrees = Math.floor(Math.random() * 360) + 1800; 
            wheel.style.transform = `rotate(${randomDegrees}deg)`;

            const winPrizes = ["$0.50 Bonus", "$1.00 Mega Win", "$0.10 Reward", "Free Miner Node", "$2.00 Platinum Drop", "$0.25 Light Drop"];
            const selectedPrize = winPrizes[Math.floor(Math.random() * winPrizes.length)];

            setTimeout(() => {
                closeSpinModal();
                showRewardToast(`Congratulations! You won: ${selectedPrize}`);
                if(selectedPrize.includes('$')) {
                    let winValue = parseFloat(selectedPrize.replace(/[^0-9.]/g, ''));
                    updateAssets(winValue);
                } else {
                    totalMiners += 1;
                    document.getElementById('minerDisplay').innerText = totalMiners;
                }
                setTimeout(() => { wheel.style.transform = 'rotate(0deg)'; canSpin = true; }, 500);
            }, 4100);
        }

        function triggerGatewayAction(gatewayName) {
            closeGatewayModal();
            showRewardToast(`${gatewayName} integration active. Processing transaction...`);
        }
    </script>
</body>
</html>
