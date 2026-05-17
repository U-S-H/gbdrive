<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vestify Pro - Cloud Mining Quantum Network</title>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>

    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Plus Jakarta Sans', sans-serif; -webkit-tap-highlight-color: transparent; }
        body { background: #040814; color: #ffffff; min-height: 100vh; padding-bottom: 100px; overflow-x: hidden; }
        
        .bg-glow-1 { position: fixed; width: 350px; height: 350px; background: radial-gradient(circle, rgba(255, 65, 108, 0.12) 0%, rgba(0,0,0,0) 70%); top: -50px; left: -50px; z-index: -1; pointer-events: none; }
        .bg-glow-2 { position: fixed; width: 450px; height: 450px; background: radial-gradient(circle, rgba(56, 189, 248, 0.1) 0%, rgba(0,0,0,0) 70%); bottom: 100px; right: -100px; z-index: -1; pointer-events: none; }

        /* Auth Screen Styling */
        #authScreen, #secretAdminScreen { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: #040814; z-index: 99999; display: flex; align-items: center; justify-content: center; padding: 20px; overflow-y: auto; }
        .auth-container, .admin-container { background: rgba(255, 255, 255, 0.02); border: 1px solid rgba(255, 255, 255, 0.06); border-radius: 28px; padding: 30px 20px; width: 100%; max-width: 420px; text-align: center; backdrop-filter: blur(25px); }
        .auth-tabs { display: flex; gap: 10px; margin-bottom: 20px; }
        .auth-tab-btn { flex: 1; padding: 12px; border-radius: 12px; border: none; background: rgba(255,255,255,0.04); color: #94a3b8; font-weight: 700; cursor: pointer; }
        .auth-tab-btn.active { background: linear-gradient(135deg, #ff416c, #ff4b2b); color: white; }

        /* Structure Header & Stats */
        .vestify-header { padding: 18px 16px; background: rgba(11, 21, 40, 0.5); backdrop-filter: blur(15px); border-bottom: 1px solid rgba(255, 255, 255, 0.04); position: sticky; top: 0; z-index: 100; }
        .brand-row { display: flex; justify-content: space-between; align-items: center; }
        .logo-block { display: flex; align-items: center; gap: 12px; cursor: pointer; }
        .logo-icon { width: 40px; height: 40px; background: linear-gradient(135deg, #ff416c, #ff4b2b); border-radius: 12px; display: flex; align-items: center; justify-content: center; font-size: 18px; font-weight: 800; }
        .logo-text h1 { font-size: 18px; font-weight: 800; letter-spacing: 1px; }

        .stats-dashboard { background: rgba(255, 255, 255, 0.02); border: 1px solid rgba(255, 255, 255, 0.05); border-radius: 24px; padding: 22px; margin: 20px 16px; position: relative; }
        .live-tag { position: absolute; top: 12px; right: 16px; background: rgba(16, 185, 129, 0.15); color: #10b981; border: 1px solid rgba(16,185,129,0.3); font-size: 10px; padding: 2px 8px; border-radius: 20px; font-weight: 700; }
        
        .quick-actions { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; margin: 0 16px 20px 16px; }
        .action-btn { padding: 12px 6px; border-radius: 14px; border: none; font-size: 12px; font-weight: 700; color: white; cursor: pointer; display: flex; flex-direction: column; align-items: center; gap: 6px; transition: transform 0.2s; background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.05); }
        .action-btn i { font-size: 16px; }
        .btn-deposit { color: #10b981; }
        .btn-withdraw { color: #3b82f6; }
        .btn-transfer { color: #eab308; }
        .action-btn:active { transform: scale(0.95); }

        /* Categorized Plan Nav Tabs */
        .plan-tabs-row { display: flex; gap: 8px; padding: 0 16px; margin-bottom: 12px; overflow-x: auto; white-space: nowrap; }
        .plan-tab-trigger { background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.05); padding: 8px 16px; border-radius: 20px; font-size: 12px; color: #94a3b8; font-weight: 700; cursor: pointer; }
        .plan-tab-trigger.active-tab { background: #38bdf8; color: #040814; border-color: #38bdf8; }

        .plans-grid { display: none; flex-direction: column; gap: 12px; padding: 0 16px; }
        .plans-grid.active-grid { display: flex; }
        .plan-card { background: rgba(15, 23, 42, 0.3); border: 1px solid rgba(255, 255, 255, 0.04); border-radius: 20px; padding: 14px; display: flex; align-items: center; gap: 14px; position: relative; }
        .plan-icon-frame { width: 56px; height: 56px; background: rgba(255,255,255,0.02); border-radius: 14px; display: flex; align-items: center; justify-content: center; font-size: 24px; color: #38bdf8; border: 1px solid rgba(255,255,255,0.06); }
        .plan-details { flex: 1; }
        .plan-details h4 { font-size: 14px; font-weight: 700; color: white; }
        .plan-details p { font-size: 11px; color: #64748b; margin-top: 1px; }
        .plan-details span { color: #10b981; font-weight: 700; }

        /* Dynamic Engine Runtime Panels */
        .app-view-page { display: none; padding: 0 16px; }
        .app-view-page.active-page { display: block; }
        .history-card { background: rgba(255,255,255,0.01); border: 1px solid rgba(255,255,255,0.04); padding: 12px; border-radius: 14px; margin-bottom: 8px; display: flex; justify-content: space-between; align-items: center; }

        /* Spin Lucky Engine Styles */
        .spin-box-card { background: rgba(255,255,255,0.02); border: 1px solid rgba(255,255,255,0.05); padding: 20px; border-radius: 24px; text-align: center; margin-top: 15px; }
        .wheel-outer { width: 160px; height: 160px; border: 4px dashed #38bdf8; border-radius: 50%; margin: 15px auto; display: flex; align-items: center; justify-content: center; font-weight: 800; font-size: 14px; color: #38bdf8; transition: transform 3s cubic-bezier(0.1, 0.8, 0.1, 1); }

        /* Persistent Navigation Control */
        .bottom-nav { position: fixed; bottom: 0; left: 0; width: 100%; height: 70px; background: rgba(4, 8, 20, 0.9); backdrop-filter: blur(20px); border-top: 1px solid rgba(255, 255, 255, 0.05); display: flex; justify-content: space-around; align-items: center; z-index: 999; }
        .nav-item { display: flex; flex-direction: column; align-items: center; gap: 4px; color: #64748b; text-decoration: none; font-size: 11px; font-weight: 600; cursor: pointer; }
        .nav-item.active { color: #38bdf8; }

        /* Universal Modals */
        .premium-modal { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(2, 4, 10, 0.85); backdrop-filter: blur(12px); z-index: 10000; display: flex; align-items: center; justify-content: center; opacity: 0; pointer-events: none; transition: opacity 0.3s; padding: 20px; overflow-y: auto; }
        .premium-modal.modal-open { opacity: 1; pointer-events: auto; }
        .modal-content { background: #0b111e; border: 1px solid rgba(255, 255, 255, 0.08); width: 100%; max-width: 380px; border-radius: 24px; padding: 22px; text-align: center; position: relative; }
        .modal-input { width: 100%; padding: 12px; border-radius: 12px; border: 1px solid rgba(255,255,255,0.08); background: rgba(0,0,0,0.3); color: white; font-size: 13px; margin-top: 10px; outline: none; }
        .close-modal-btn { position: absolute; top: 14px; right: 14px; color: #64748b; font-size: 20px; cursor: pointer; }

        /* Notification Toast Alert Display */
        .reward-toast { position: fixed; top: 25px; left: 50%; transform: translateX(-50%) translateY(-100px); background: #10b981; padding: 12px 20px; border-radius: 16px; z-index: 11000; font-weight: 700; font-size: 13px; transition: transform 0.3s ease; }
        .reward-toast.toast-show { transform: translateX(-50%) translateY(0); }
        .admin-log-sec { text-align: left; margin-top: 15px; background: rgba(0,0,0,0.3); padding: 10px; border-radius: 12px; max-height: 200px; overflow-y: auto; font-size: 11px; }
        .adm-btn { border: none; padding: 4px 8px; border-radius: 6px; color: white; font-weight: 700; cursor: pointer; margin-top: 5px; font-size: 11px; }
    </style>
</head>
<body>

    <div class="bg-glow-1"></div>
    <div class="bg-glow-2"></div>

    <div id="rewardToast" class="reward-toast">Alert Notification Node</div>

    <div id="secretAdminScreen" style="display:none;">
        <div class="admin-container" style="max-width: 500px;">
            <div class="close-modal-btn" onclick="closeAdminPanel()">&times;</div>
            <h3 style="color:#38bdf8; margin-bottom:10px;"><i class="fa-solid fa-user-shield"></i> System Control Center</h3>
            <div id="adminAuthLock">
                <input type="password" id="adminSecretKeyInput" class="modal-input" placeholder="Enter Administration Secret Token">
                <button class="buy-plan-btn" onclick="verifySystemAdminPass()" style="width:100%; margin-top:10px; padding:12px;">Authorize</button>
            </div>
            <div id="adminConsoleData" style="display:none;">
                <div class="admin-log-sec">
                    <strong>📥 Deposit Pipeline Queue:</strong>
                    <div id="admDepositsList" style="margin-top:5px;">Awaiting logs...</div>
                </div>
                <div class="admin-log-sec">
                    <strong>👥 Network Registered Users:</strong>
                    <div id="admUsersList" style="margin-top:5px;">Awaiting profiles...</div>
                </div>
            </div>
        </div>
    </div>

    <div id="authScreen">
        <div class="auth-container">
            <h2>VESTIFY PRO</h2>
            <p style="font-size: 11px; color: #64748b; margin-bottom: 15px;">Decentralized Mining Node Login</p>
            <div class="auth-tabs">
                <button id="tabLogin" class="auth-tab-btn active" onclick="switchAuthTab('login')">Sign In</button>
                <button id="tabRegister" class="auth-tab-btn" onclick="switchAuthTab('register')">Sign Up</button>
            </div>
            <input type="email" id="authEmail" class="modal-input" placeholder="Email Account Address" style="margin-top:0;">
            <input type="password" id="authPassword" class="modal-input" placeholder="Security Password Protocol">
            <button class="buy-plan-btn" onclick="handleAuthAction()" style="width:100%; margin-top:15px; padding:12px; border-radius:12px;">EXECUTE SYSTEM PORTAL</button>
        </div>
    </div>

    <div class="vestify-header">
        <div class="brand-row">
            <div class="logo-block" onclick="registerAdminLogoTap()">
                <div class="logo-icon"><i class="fa-solid fa-microchip"></i></div>
                <div class="logo-text">
                    <h1>VESTIFY PRO</h1>
                    <p style="font-size:10px; color:#38bdf8;">Cloud Fire Core Array</p>
                </div>
            </div>
            <button class="buy-plan-btn" onclick="triggerSignOut()" style="background:rgba(239,68,68,0.1); color:#ef4444; font-size:11px; padding:6px 12px;">Exit</button>
        </div>
    </div>

    <div id="pageHome" class="app-view-page active-page">
        <div class="stats-dashboard">
            <span class="live-tag"><i class="fa-solid fa-bolt"></i> Live Array</span>
            <p style="font-size:12px; color:#64748b;">Total Core Liquid Equity</p>
            <h2 style="font-size:26px; margin-top:4px;">Rs. <span id="assetDisplay">0.00</span></h2>
            <p style="font-size:11px; color:#94a3b8; margin-top:8px;">Active Processing Cores: <b id="minerDisplay" style="color:#38bdf8;">0</b></p>
        </div>

        <div class="quick-actions">
            <button class="action-btn btn-deposit" onclick="openDepositModal()"><i class="fa-solid fa-wallet"></i>Deposit</button>
            <button class="action-btn btn-withdraw" onclick="openWithdrawModal()"><i class="fa-solid fa-money-bill-transfer"></i>Payout</button>
            <button class="action-btn btn-transfer" onclick="openTransferModal()"><i class="fa-solid fa-paper-plane"></i>Transfer</button>
        </div>

        <div class="section-title" style="font-size:12px; color:#94a3b8; margin:20px 16px 10px 16px; text-transform:uppercase;"><i class="fa-solid fa-server"></i> Cloud Mining Sub-Stations</div>
        
        <div class="plan-tabs-row">
            <div class="plan-tab-trigger active-tab" id="tbNorm" onclick="switchPlanCategory('gridNormal', 'tbNorm')">Normal Pods</div>
            <div class="plan-tab-trigger" id="tbSpec" onclick="switchPlanCategory('gridSpecial', 'tbSpec')">Special Rigs</div>
            <div class="plan-tab-trigger" id="tbOffr" onclick="switchPlanCategory('gridOffers', 'tbOffr')">Exclusive Offers</div>
        </div>

        <div class="plans-grid active-grid" id="gridNormal">
            <div class="plan-card">
                <div class="plan-icon-frame"><i class="fa-solid fa-box"></i></div>
                <div class="plan-details"><h4>Pod Alpha-1</h4><p>Cost: <b>Rs. 200</b> | Yield: <span>Rs. 16/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseNode('Pod Alpha-1', 200, 16)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-icon-frame"><i class="fa-solid fa-box"></i></div>
                <div class="plan-details"><h4>Pod Alpha-2</h4><p>Cost: <b>Rs. 500</b> | Yield: <span>Rs. 42/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseNode('Pod Alpha-2', 500, 42)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-icon-frame"><i class="fa-solid fa-box"></i></div>
                <div class="plan-details"><h4>Pod Alpha-3</h4><p>Cost: <b>Rs. 800</b> | Yield: <span>Rs. 70/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseNode('Pod Alpha-3', 800, 70)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-icon-frame"><i class="fa-solid fa-box"></i></div>
                <div class="plan-details"><h4>Pod Alpha-4</h4><p>Cost: <b>Rs. 1,200</b> | Yield: <span>Rs. 105/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseNode('Pod Alpha-4', 1200, 105)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-icon-frame"><i class="fa-solid fa-box"></i></div>
                <div class="plan-details"><h4>Pod Alpha-5</h4><p>Cost: <b>Rs. 1,600</b> | Yield: <span>Rs. 145/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseNode('Pod Alpha-5', 1600, 145)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-icon-frame"><i class="fa-solid fa-box"></i></div>
                <div class="plan-details"><h4>Pod Alpha-6</h4><p>Cost: <b>Rs. 2,000</b> | Yield: <span>Rs. 185/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseNode('Pod Alpha-6', 2000, 185)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-icon-frame"><i class="fa-solid fa-box"></i></div>
                <div class="plan-details"><h4>Pod Alpha-7</h4><p>Cost: <b>Rs. 2,500</b> | Yield: <span>Rs. 235/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseNode('Pod Alpha-7', 2500, 235)">Lease</button>
            </div>
        </div>

        <div class="plans-grid" id="gridSpecial">
            <div class="plan-card">
                <div class="plan-icon-frame" style="color:#eab308;"><i class="fa-solid fa-layer-group"></i></div>
                <div class="plan-details"><h4>Rig Stratum-1</h4><p>Cost: <b>Rs. 4,000</b> | Yield: <span>Rs. 380/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseNode('Rig Stratum-1', 4000, 380)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-icon-frame" style="color:#eab308;"><i class="fa-solid fa-layer-group"></i></div>
                <div class="plan-details"><h4>Rig Stratum-2</h4><p>Cost: <b>Rs. 6,000</b> | Yield: <span>Rs. 580/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseNode('Rig Stratum-2', 6000, 580)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-icon-frame" style="color:#eab308;"><i class="fa-solid fa-layer-group"></i></div>
                <div class="plan-details"><h4>Rig Stratum-3</h4><p>Cost: <b>Rs. 9,000</b> | Yield: <span>Rs. 890/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseNode('Rig Stratum-3', 9000, 890)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-icon-frame" style="color:#eab308;"><i class="fa-solid fa-layer-group"></i></div>
                <div class="plan-details"><h4>Rig Stratum-4</h4><p>Cost: <b>Rs. 12,000</b> | Yield: <span>Rs. 1,220/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseNode('Rig Stratum-4', 12000, 1220)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-icon-frame" style="color:#eab308;"><i class="fa-solid fa-layer-group"></i></div>
                <div class="plan-details"><h4>Rig Stratum-5</h4><p>Cost: <b>Rs. 15,000</b> | Yield: <span>Rs. 1,550/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseNode('Rig Stratum-5', 15000, 1550)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-icon-frame" style="color:#eab308;"><i class="fa-solid fa-layer-group"></i></div>
                <div class="plan-details"><h4>Rig Stratum-6</h4><p>Cost: <b>Rs. 18,000</b> | Yield: <span>Rs. 1,900/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseNode('Rig Stratum-6', 18000, 1900)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-icon-frame" style="color:#eab308;"><i class="fa-solid fa-layer-group"></i></div>
                <div class="plan-details"><h4>Rig Stratum-7</h4><p>Cost: <b>Rs. 20,000</b> | Yield: <span>Rs. 2,150/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseNode('Rig Stratum-7', 20000, 2150)">Lease</button>
            </div>
        </div>

        <div class="plans-grid" id="gridOffers">
            <div class="plan-card">
                <div class="plan-icon-frame" style="color:#a855f7;"><i class="fa-solid fa-bolt-lightning"></i></div>
                <div class="plan-details"><h4>Matrix Overdrive-1</h4><p>Cost: <b>Rs. 30,000</b> | Yield: <span>Rs. 3,400/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseNode('Matrix Overdrive-1', 30,000, 3400)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-icon-frame" style="color:#a855f7;"><i class="fa-solid fa-bolt-lightning"></i></div>
                <div class="plan-details"><h4>Matrix Overdrive-2</h4><p>Cost: <b>Rs. 50,000</b> | Yield: <span>Rs. 6,000/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseNode('Matrix Overdrive-2', 50000, 6000)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-icon-frame" style="color:#a855f7;"><i class="fa-solid fa-bolt-lightning"></i></div>
                <div class="plan-details"><h4>Matrix Overdrive-3</h4><p>Cost: <b>Rs. 75,000</b> | Yield: <span>Rs. 9,500/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseNode('Matrix Overdrive-3', 75000, 9500)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-icon-frame" style="color:#a855f7;"><i class="fa-solid fa-bolt-lightning"></i></div>
                <div class="plan-details"><h4>Matrix Overdrive-4</h4><p>Cost: <b>Rs. 100,000</b> | Yield: <span>Rs. 13,500/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseNode('Matrix Overdrive-4', 100000, 13500)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-icon-frame" style="color:#a855f7;"><i class="fa-solid fa-bolt-lightning"></i></div>
                <div class="plan-details"><h4>Matrix Overdrive-5</h4><p>Cost: <b>Rs. 125,000</b> | Yield: <span>Rs. 17,500/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseNode('Matrix Overdrive-5', 125000, 17500)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-icon-frame" style="color:#a855f7;"><i class="fa-solid fa-bolt-lightning"></i></div>
                <div class="plan-details"><h4>Matrix Overdrive-6</h4><p>Cost: <b>Rs. 150,000</b> | Yield: <span>Rs. 22,000/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseNode('Matrix Overdrive-6', 150000, 22000)">Lease</button>
            </div>
        </div>

        <div class="section-title" style="font-size:12px; color:#94a3b8; margin:25px 16px 10px 16px;"><i class="fa-solid fa-hourglass-half"></i> System Dividend Distribution Tracker</div>
        <div class="history-card" style="margin: 0 16px; background: rgba(56,189,248,0.03); border-color: rgba(56,189,248,0.2);">
            <div>
                <span style="font-size:13px; font-weight:700; color:#38bdf8;">Next Block Network Payout</span>
                <p style="font-size:11px; color:#64748b;">Automated server cluster synchronization cycle</p>
            </div>
            <span id="countdownTimerClock" style="font-size:15px; font-weight:800; color:#38bdf8; letter-spacing:1px;">24:00:00</span>
        </div>

        <div class="spin-box-card" style="margin: 20px 16px;">
            <h4>🎉 Automated Lucky Core Bonus 🎉</h4>
            <p style="font-size:11px; color:#64748b; margin-top:2px;">Trigger daily randomized balance multiplier</p>
            <div class="wheel-outer" id="luckyBonusWheel">SPIN CORE</div>
            <button class="buy-plan-btn" onclick="triggerLuckyCoreSpin()" style="padding:10px 24px;">LAUNCH SPIN</button>
        </div>
    </div>

    <div id="pageHistory" class="app-view-page">
        <div class="section-title" style="margin-left:0;"><i class="fa-solid fa-list"></i> Real-time Transaction Sequences</div>
        <div id="userHistoryRecordsList">Awaiting security matrix data streams...</div>
    </div>

    <div id="pageLegal" class="app-view-page">
        <div class="section-title" style="margin-left:0;"><i class="fa-solid fa-user-shield"></i> Safety & Security Framework</div>
        <div style="background:rgba(255,255,255,0.02); border:1px solid rgba(255,255,255,0.05); padding:16px; border-radius:18px; font-size:12px; color:#94a3b8; line-height:1.6;">
            <p style="margin-bottom:10px;"><b style="color:white;">Privacy Protocol:</b> User financial payloads, identities, and receipt data loops remain protected within cloud hardware blocks.</p>
            <p><b style="color:white;">Mining Regulations:</b> Balance extraction nodes require mandatory admin verification to clean destination targets.</p>
        </div>
    </div>

    <div class="bottom-nav">
        <div class="nav-item active" id="nvHome" onclick="routeToView('pageHome', 'nvHome')"><i class="fa-solid fa-house"></i><span>Home</span></div>
        <div class="nav-item" id="nvHistory" onclick="routeToView('pageHistory', 'nvHistory')"><i class="fa-solid fa-receipt"></i><span>Ledger Logs</span></div>
        <div class="nav-item" id="nvLegal" onclick="routeToView('pageLegal', 'nvLegal')"><i class="fa-solid fa-shield-halved"></i><span>Compliance</span></div>
    </div>

    <div id="depositModal" class="premium-modal">
        <div class="modal-content">
            <div class="close-modal-btn" onclick="closeModal('depositModal')">&times;</div>
            <h4>Secure Deposit Pipeline</h4>
            <input type="number" id="depositAmount" class="modal-input" placeholder="Amount (PKR)">
            <input type="text" id="depositTrx" class="modal-input" placeholder="TRX Transaction Reference ID">
            <input type="file" id="depositFileReceipt" class="modal-input" accept="image/*" onchange="extractBase64Binary()">
            <button class="buy-plan-btn" onclick="dispatchDepositToCloud()" style="width:100%; margin-top:12px; padding:12px;">LOG PAYLOAD</button>
        </div>
    </div>

    <div id="withdrawModal" class="premium-modal">
        <div class="modal-content">
            <div class="close-modal-btn" onclick="closeModal('withdrawModal')">&times;</div>
            <h4>Secure Dividend Extraction</h4>
            <input type="text" id="withdrawTitle" class="modal-input" placeholder="Account Holder Title Name">
            <input type="text" id="withdrawNumber" class="modal-input" placeholder="Account Target Number">
            <input type="number" id="withdrawAmount" class="modal-input" placeholder="Amount (PKR)">
            <button class="buy-plan-btn" onclick="dispatchWithdrawalToCloud()" style="width:100%; margin-top:12px; padding:12px;" style="background:#3b82f6;">DISPATCH CASH</button>
        </div>
    </div>

    <div id="transferModal" class="premium-modal">
        <div class="modal-content">
            <div class="close-modal-btn" onclick="closeModal('transferModal')">&times;</div>
            <h4 style="color:#eab308;"><i class="fa-solid fa-paper-plane"></i> Instant Wallet P2P Transfer</h4>
            <p style="font-size:11px; color:#64748b; margin-top:4px;">Direct node synchronization without external billing</p>
            <input type="text" id="transferTargetEmail" class="modal-input" placeholder="Recipient Account Email / Username">
            <input type="number" id="transferAmount" class="modal-input" placeholder="Transfer Capital Amount (PKR)">
            <button class="buy-plan-btn" onclick="executeP2PInternalTransfer()" style="width:100%; margin-top:14px; padding:12px; background:linear-gradient(135deg, #eab308, #ca8a04);">EXECUTE TRANSFER LINK</button>
        </div>
    </div>

    <script>
        const firebaseConfig = {
            apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g",
            authDomain: "vestify-991f2.firebaseapp.com",
            databaseURL: "https://vestify-991f2-default-rtdb.firebaseio.com",
            projectId: "vestify-991f2",
            storageBucket: "vestify-991f2.firebasestorage.app",
            messagingSenderId: "799007097733",
            appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec"
        };

        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();

        let currentUserId = null;
        let userCurrentBalance = 0.00;
        let totalActiveCoresCount = 0;
        let currentAuthTabMode = "login";
        let base64ImageBufferStream = "";
        let adminPanelLogoClicksCount = 0;

        auth.onAuthStateChanged(user => {
            if (user) {
                currentUserId = user.uid;
                document.getElementById('authScreen').style.display = 'none';
                syncProfileRealtimeMetrics();
                syncUserLedgerTracks();
                initializeCountdownClockTicker();
            } else {
                currentUserId = null;
                document.getElementById('authScreen').style.display = 'flex';
            }
        });

        function switchAuthTab(mode) {
            currentAuthTabMode = mode;
            document.getElementById('tabLogin').classList.toggle('active', mode === 'login');
            document.getElementById('tabRegister').classList.toggle('active', mode === 'register');
        }

        function handleAuthAction() {
            const email = document.getElementById('authEmail').value.trim();
            const pass = document.getElementById('authPassword').value;
            if(!email || !pass) { triggerToastDisplay("Please pass complete parameters."); return; }

            if(currentAuthTabMode === "register") {
                auth.createUserWithEmailAndPassword(email, pass).then(cred => {
                    return db.collection("users").doc(cred.user.uid).set({
                        balance: 0.00, minersCount: 0, email: email, hasSpunToday: false
                    });
                }).then(() => triggerToastDisplay("Registry Protocol Saved!")).catch(e => triggerToastDisplay(e.message));
            } else {
                auth.signInWithEmailAndPassword(email, pass)
                    .then(() => triggerToastDisplay("Terminal Unlocked!"))
                    .catch(e => triggerToastDisplay(e.message));
            }
        }

        function triggerSignOut() { auth.signOut(); }

        function syncProfileRealtimeMetrics() {
            db.collection("users").doc(currentUserId).onSnapshot(doc => {
                if(doc.exists) {
                    const d = doc.data();
                    userCurrentBalance = d.balance || 0.00;
                    totalActiveCoresCount = d.minersCount || 0;
                    document.getElementById('assetDisplay').innerText = userCurrentBalance.toLocaleString('en-US',{minimumFractionDigits:2});
                    document.getElementById('minerDisplay').innerText = totalActiveCoresCount;
                }
            });
        }

        function switchPlanCategory(gridId, tabId) {
            document.querySelectorAll('.plans-grid').forEach(g => g.classList.remove('active-grid'));
            document.querySelectorAll('.plan-tab-trigger').forEach(t => t.classList.remove('active-tab'));
            document.getElementById(gridId).classList.add('active-grid');
            document.getElementById(tabId).classList.add('active-tab');
        }

        function extractBase64Binary() {
            const file = document.getElementById('depositFileReceipt').files[0];
            const reader = new FileReader();
            reader.onloadend = () => { base64ImageBufferStream = reader.result; triggerToastDisplay("Receipt Stream Compiled."); };
            if(file) reader.readAsDataURL(file);
        }

        function dispatchDepositToCloud() {
            const amt = parseFloat(document.getElementById('depositAmount').value);
            const trx = document.getElementById('depositTrx').value.trim();
            if(isNaN(amt) || !trx) { triggerToastDisplay("Invalid payload bounds."); return; }

            db.collection("deposits").add({
                userId: currentUserId, amount: amt, transactionId: trx,
                receiptBase64: base64ImageBufferStream || "Void", status: "pending",
                timestamp: firebase.firestore.FieldValue.serverTimestamp()
            }).then(() => {
                closeModal('depositModal'); triggerToastDisplay("Log queued for verification framework audit.");
                document.getElementById('depositAmount').value = ""; document.getElementById('depositTrx').value = "";
            });
        }

        function dispatchWithdrawalToCloud() {
            const title = document.getElementById('withdrawTitle').value.trim();
            const num = document.getElementById('withdrawNumber').value.trim();
            const amt = parseFloat(document.getElementById('withdrawAmount').value);

            if(!title || !num || isNaN(amt) || amt > userCurrentBalance) { triggerToastDisplay("Liquidity or target failure."); return; }

            db.collection("users").doc(currentUserId).update({ balance: userCurrentBalance - amt }).then(() => {
                return db.collection("withdrawals").add({
                    userId: currentUserId, title: title, targetNo: num, amount: amt, status: "pending",
                    timestamp: firebase.firestore.FieldValue.serverTimestamp()
                });
            }).then(() => { closeModal('withdrawModal'); triggerToastDisplay("Payout request buffered."); });
        }

        // PROFESSIONAL USERNAME / EMAIL INTERNAL P2P TRANSFER SYSTEM
        function executeP2PInternalTransfer() {
            const targetEmailInput = document.getElementById('transferTargetEmail').value.trim().toLowerCase();
            const transferAmt = parseFloat(document.getElementById('transferAmount').value);

            if(!targetEmailInput || isNaN(transferAmt) || transferAmt <= 0) { triggerToastDisplay("Please specify complete transfer details."); return; }
            if(transferAmt > userCurrentBalance) { triggerToastDisplay("Insufficient funds inside core asset equity."); return; }

            // Dynamic User Registry Matching Query Pipeline
            db.collection("users").where("email", "==", targetEmailInput).get().then(snapshot => {
                if(snapshot.empty) { throw "Destination user handle matching token not found."; }
                
                const targetUserDoc = snapshot.docs[0];
                const targetUid = targetUserDoc.id;
                const targetCurrentBal = targetUserDoc.data().balance || 0;

                if(targetUid === currentUserId) { throw "Internal self-loop routing loops are forbidden."; }

                // Atomic structural database execution block
                const batch = db.batch();
                batch.update(db.collection("users").doc(currentUserId), { balance: userCurrentBalance - transferAmt });
                batch.update(db.collection("users").doc(targetUid), { balance: targetCurrentBal + transferAmt });
                
                // Add tracking logs inside audit trail
                const logRef = db.collection("deposits").doc();
                batch.set(logRef, {
                    userId: currentUserId, amount: transferAmt, transactionId: "P2P-OUT-" + Math.floor(Math.random()*100000),
                    gateway: "Internal Transfer Out", status: "approved", timestamp: firebase.firestore.FieldValue.serverTimestamp()
                });

                const logInRef = db.collection("deposits").doc();
                batch.set(logInRef, {
                    userId: targetUid, amount: transferAmt, transactionId: "P2P-IN-" + Math.floor(Math.random()*100000),
                    gateway: "Internal Transfer In", status: "approved", timestamp: firebase.firestore.FieldValue.serverTimestamp()
                });

                return batch.commit();
            }).then(() => {
                closeModal('transferModal');
                triggerToastDisplay(`Transferred Rs. ${transferAmt} successfully to target payload!`);
                document.getElementById('transferTargetEmail').value = "";
                document.getElementById('transferAmount').value = "";
            }).catch(err => triggerToastDisplay(err));
        }

        function leaseNode(name, price, daily) {
            if(userCurrentBalance < price) { triggerToastDisplay("Asset bounds exceeded."); return; }
            db.collection("users").doc(currentUserId).update({
                balance: userCurrentBalance - price, minersCount: totalActiveCoresCount + 1
            }).then(() => {
                triggerToastDisplay(`Array leased: ${name} computing sequence live.`);
            });
        }

        function triggerLuckyCoreSpin() {
            db.collection("users").doc(currentUserId).get().then(doc => {
                if(doc.data().hasSpunToday) { triggerToastDisplay("Limit reached: 1 operational spin per interval."); return; }
                
                const prizes = [5, 10, 20, 50, 100];
                const winningVal = prizes[Math.floor(Math.random() * prizes.length)];
                const wheel = document.getElementById('luckyBonusWheel');
                
                wheel.style.transform = "rotate(1440deg)";
                setTimeout(() => {
                    db.collection("users").doc(currentUserId).update({
                        balance: userCurrentBalance + winningVal, hasSpunToday: true
                    }).then(() => {
                        triggerToastDisplay(`Victory! Credited +Rs. ${winningVal} to main matrix node.`);
                        wheel.style.transform = "rotate(0deg)";
                    });
                }, 3000);
            });
        }

        function syncUserLedgerTracks() {
            db.collection("deposits").where("userId", "==", currentUserId).onSnapshot(snap => {
                let html = "";
                snap.forEach(doc => {
                    const d = doc.data();
                    html += `<div class="history-card"><div><b>Rs. ${d.amount}</b><p>${d.transactionId}</p></div><span>${d.status}</span></div>`;
                });
                document.getElementById('userHistoryRecordsList').innerHTML = html || "Void records ledger pipeline.";
            });
        }

        function initializeCountdownClockTicker() {
            setInterval(() => {
                const now = new Date();
                const hours = 23 - now.getHours(); const mins = 59 - now.getMinutes(); const secs = 59 - now.getSeconds();
                document.getElementById('countdownTimerClock').innerText = `${hours.toString().padStart(2,'0')}:${mins.toString().padStart(2,'0')}:${secs.toString().padStart(2,'0')}`;
            }, 1000);
        }

        function registerAdminLogoTap() {
            adminPanelLogoClicksCount++; if(adminPanelLogoClicksCount >= 4) { adminPanelLogoClicksCount=0; document.getElementById('secretAdminScreen').style.display='flex'; }
        }

        function verifySystemAdminPass() {
            if(document.getElementById('adminSecretKeyInput').value === "net204") {
                document.getElementById('adminAuthLock').style.display = 'none';
                document.getElementById('adminConsoleData').style.display = 'block';
                executeAdminLiveConsoleFeeds();
            } else { triggerToastDisplay("Invalid Key Crypt."); }
        }

        function executeAdminLiveConsoleFeeds() {
            db.collection("deposits").where("status", "==", "pending").onSnapshot(snap => {
                let out = "";
                snap.forEach(doc => {
                    const d = doc.data();
                    out += `
                        <div style="border-bottom:1px solid rgba(255,255,255,0.05); padding:8px 0;">
                            User: ${d.userId} | Amt: <b>Rs. ${d.amount}</b><br>TRX ID: ${d.transactionId}<br>
                            <button class="adm-btn" style="background:#10b981;" onclick="adminProcessDeposit('${doc.id}', '${d.userId}', ${d.amount}, true)">Approve</button>
                            <button class="adm-btn" style="background:#ef4444;" onclick="adminProcessDeposit('${doc.id}', '${d.userId}', ${d.amount}, false)">Reject</button>
                        </div>
                    `;
                });
                document.getElementById('admDepositsList').innerHTML = out || "No pending operations payload channels.";
            });
        }

        function adminProcessDeposit(docId, uid, amt, isApproved) {
            if(isApproved) {
                db.collection("users").doc(uid).get().then(uDoc => {
                    const currentBal = uDoc.data().balance || 0;
                    return db.collection("users").doc(uid).update({ balance: currentBal + amt });
                }).then(() => { return db.collection("deposits").doc(docId).update({ status: "approved" }); });
            } else { db.collection("deposits").doc(docId).update({ status: "rejected" }); }
        }

        function routeToView(pId, nId) {
            document.querySelectorAll('.app-view-page').forEach(p=>p.classList.remove('active-page'));
            document.querySelectorAll('.nav-item').forEach(n=>n.classList.remove('active'));
            document.getElementById(pId).classList.add('active-page'); document.getElementById(nId).classList.add('active');
        }

        function openDepositModal() { document.getElementById('depositModal').classList.add('modal-open'); }
        function openWithdrawModal() { document.getElementById('withdrawModal').classList.add('modal-open'); }
        function openTransferModal() { document.getElementById('transferModal').classList.add('modal-open'); }
        function closeModal(id) { document.getElementById(id).classList.remove('modal-open'); }
        function closeAdminPanel() { document.getElementById('secretAdminScreen').style.display='none'; }

        function triggerToastDisplay(msg) {
            const t = document.getElementById('rewardToast'); t.innerText = msg; t.classList.add('toast-show');
            setTimeout(() => t.classList.remove('toast-show'), 2500);
        }
    </script>
</body>
</html>
