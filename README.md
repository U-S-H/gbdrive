<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vestify Pro - Cloud Mining & Affiliate Network</title>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>

    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Plus Jakarta Sans', sans-serif; -webkit-tap-highlight-color: transparent; }
        body { background: #040814; color: #ffffff; min-height: 100vh; padding-bottom: 100px; overflow-x: hidden; }
        
        .bg-glow-1 { position: fixed; width: 350px; height: 350px; background: radial-gradient(circle, rgba(239, 68, 68, 0.12) 0%, rgba(0,0,0,0) 70%); top: -50px; left: -50px; z-index: -1; pointer-events: none; }
        .bg-glow-2 { position: fixed; width: 450px; height: 450px; background: radial-gradient(circle, rgba(56, 189, 248, 0.1) 0%, rgba(0,0,0,0) 70%); bottom: 100px; right: -100px; z-index: -1; pointer-events: none; }

        /* Auth Portal Elements */
        #authScreen, #secretAdminScreen { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: #040814; z-index: 99999; display: flex; align-items: center; justify-content: center; padding: 20px; overflow-y: auto; }
        .auth-container, .admin-container { background: rgba(255, 255, 255, 0.02); border: 1px solid rgba(255, 255, 255, 0.06); border-radius: 28px; padding: 35px 24px; width: 100%; max-width: 420px; text-align: center; backdrop-filter: blur(25px); box-shadow: 0 20px 50px rgba(0,0,0,0.5); }
        .auth-tabs { display: flex; gap: 10px; margin-bottom: 25px; background: rgba(255,255,255,0.03); padding: 6px; border-radius: 14px; }
        .auth-tab-btn { flex: 1; padding: 12px; border-radius: 10px; border: none; background: transparent; color: #94a3b8; font-weight: 700; cursor: pointer; transition: all 0.3s; }
        .auth-tab-btn.active { background: linear-gradient(135deg, #ef4444, #ff7e5f); color: white; box-shadow: 0 4px 15px rgba(239,68,68,0.3); }
        
        .google-auth-btn { width: 100%; padding: 14px; border-radius: 12px; border: 1px solid rgba(255,255,255,0.1); background: rgba(255,255,255,0.03); color: white; font-weight: 700; font-size: 14px; cursor: pointer; display: flex; align-items: center; justify-content: center; gap: 10px; margin-top: 15px; }

        /* Main System Framework */
        .vestify-header { padding: 18px 16px; background: rgba(11, 21, 40, 0.5); backdrop-filter: blur(15px); border-bottom: 1px solid rgba(255, 255, 255, 0.04); position: sticky; top: 0; z-index: 100; }
        .brand-row { display: flex; justify-content: space-between; align-items: center; }
        .logo-block { display: flex; align-items: center; gap: 12px; cursor: pointer; }
        .logo-icon { width: 40px; height: 40px; background: linear-gradient(135deg, #ef4444, #ff7e5f); border-radius: 12px; display: flex; align-items: center; justify-content: center; font-size: 18px; font-weight: 800; }
        .logo-text h1 { font-size: 18px; font-weight: 800; letter-spacing: 1px; }

        .stats-dashboard { background: rgba(255, 255, 255, 0.02); border: 1px solid rgba(255, 255, 255, 0.05); border-radius: 24px; padding: 22px; margin: 20px 16px; position: relative; }
        .live-tag { position: absolute; top: 12px; right: 16px; background: rgba(16, 185, 129, 0.15); color: #10b981; border: 1px solid rgba(16,185,129,0.3); font-size: 10px; padding: 2px 8px; border-radius: 20px; font-weight: 700; }
        
        .quick-actions { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; margin: 0 16px 20px 16px; }
        .action-btn { padding: 12px 6px; border-radius: 14px; border: none; font-size: 12px; font-weight: 700; color: white; cursor: pointer; display: flex; flex-direction: column; align-items: center; gap: 6px; background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.05); }
        .btn-deposit { color: #10b981; }
        .btn-withdraw { color: #3b82f6; }
        .btn-transfer { color: #eab308; }

        /* Custom Dynamic Grid Mapping */
        .plan-tabs-row { display: flex; gap: 8px; padding: 0 16px; margin-bottom: 12px; overflow-x: auto; white-space: nowrap; }
        .plan-tab-trigger { background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.05); padding: 8px 16px; border-radius: 20px; font-size: 12px; color: #94a3b8; font-weight: 700; cursor: pointer; }
        .plan-tab-trigger.active-tab { background: #ef4444; color: white; border-color: #ef4444; }

        .plans-grid { display: none; flex-direction: column; gap: 12px; padding: 0 16px; }
        .plans-grid.active-grid { display: flex; }
        
        /* Premium Card Layout With Rich Micro Graphics */
        .plan-card { background: rgba(15, 23, 42, 0.3); border: 1px solid rgba(255, 255, 255, 0.04); border-radius: 20px; padding: 16px; display: flex; align-items: center; gap: 14px; position: relative; }
        .plan-img-frame { width: 64px; height: 64px; border-radius: 14px; overflow: hidden; background: #1e293b; border: 1px solid rgba(255,255,255,0.08); display: flex; align-items: center; justify-content: center; }
        .plan-img-frame img { width: 100%; height: 100%; object-fit: cover; }
        .plan-details { flex: 1; }
        .plan-details h4 { font-size: 14px; font-weight: 700; color: white; }
        .plan-details p { font-size: 11px; color: #64748b; margin-top: 2px; }
        .plan-details span { color: #10b981; font-weight: 700; }
        
        .buy-plan-btn { padding: 10px 18px; background: linear-gradient(135deg, #ef4444, #ff7e5f); border: none; border-radius: 12px; color: white; font-weight: 700; font-size: 12px; cursor: pointer; }

        .app-view-page { display: none; padding: 0 16px; }
        .app-view-page.active-page { display: block; }
        .history-card { background: rgba(255,255,255,0.01); border: 1px solid rgba(255,255,255,0.04); padding: 14px; border-radius: 14px; margin-bottom: 8px; display: flex; justify-content: space-between; align-items: center; }

        .bottom-nav { position: fixed; bottom: 0; left: 0; width: 100%; height: 70px; background: rgba(4, 8, 20, 0.95); backdrop-filter: blur(20px); border-top: 1px solid rgba(255, 255, 255, 0.05); display: flex; justify-content: space-around; align-items: center; z-index: 999; }
        .nav-item { display: flex; flex-direction: column; align-items: center; gap: 4px; color: #64748b; text-decoration: none; font-size: 11px; font-weight: 600; cursor: pointer; }
        .nav-item.active { color: #ef4444; }

        .premium-modal { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(2, 4, 10, 0.85); backdrop-filter: blur(12px); z-index: 10000; display: flex; align-items: center; justify-content: center; opacity: 0; pointer-events: none; transition: opacity 0.3s; padding: 20px; }
        .premium-modal.modal-open { opacity: 1; pointer-events: auto; }
        .modal-content { background: #0b111e; border: 1px solid rgba(255, 255, 255, 0.08); width: 100%; max-width: 380px; border-radius: 24px; padding: 22px; text-align: center; position: relative; }
        .modal-input { width: 100%; padding: 12px; border-radius: 12px; border: 1px solid rgba(255,255,255,0.08); background: rgba(0,0,0,0.3); color: white; font-size: 13px; margin-top: 10px; outline: none; }
        .close-modal-btn { position: absolute; top: 14px; right: 14px; color: #64748b; font-size: 20px; cursor: pointer; }

        .reward-toast { position: fixed; top: 25px; left: 50%; transform: translateX(-50%) translateY(-100px); background: #ef4444; padding: 12px 20px; border-radius: 16px; z-index: 11000; font-weight: 700; font-size: 13px; transition: transform 0.3s ease; box-shadow: 0 10px 25px rgba(239,68,68,0.3); color: white; }
        .reward-toast.toast-show { transform: translateX(-50%) translateY(0); }
        .section-title { font-size:12px; color:#94a3b8; margin:20px 16px 10px 16px; text-transform:uppercase; letter-spacing: 0.5px; font-weight: 700; }
    </style>
</head>
<body>

    <div class="bg-glow-1"></div>
    <div class="bg-glow-2"></div>

    <div id="rewardToast" class="reward-toast">Notification Console</div>

    <div id="secretAdminScreen" style="display: none;">
        <div class="admin-container">
            <div style="color: #ef4444; font-size: 24px; margin-bottom: 10px;"><i class="fa-solid fa-user-shield"></i></div>
            <h3 style="color: white; font-weight: 800; margin-bottom: 5px;">Admin Terminal Access</h3>
            <p style="font-size: 11px; color: #64748b; margin-bottom: 20px;">Enter core master credentials</p>
            
            <input type="password" id="txtAdminSecretKey" class="modal-input" placeholder="Enter Master Passkey" style="margin-top: 0; text-align: center; font-size: 16px; letter-spacing: 2px;">
            
            <div style="display: flex; gap: 10px; margin-top: 20px;">
                <button class="buy-plan-btn" onclick="closeAdminTerminal()" style="flex: 1; background: rgba(255,255,255,0.05); color: #94a3b8;">Abort</button>
                <button class="buy-plan-btn" onclick="verifyAdminMasterPasskey()" style="flex: 1; background: linear-gradient(135deg, #ef4444, #b91c1c);">Unlock Core</button>
            </div>
        </div>
    </div>

    <div id="authScreen">
        <div class="auth-container">
            <h2 style="font-weight: 800; background: linear-gradient(135deg, #fff, #94a3b8); -webkit-background-clip: text; -webkit-text-fill-color: transparent;">VESTIFY</h2>
            <p style="font-size: 11px; color: #64748b; margin-bottom: 20px;">Advanced Mining Network Protocol</p>
            
            <div class="auth-tabs">
                <button id="tabLogin" class="auth-tab-btn active" onclick="switchAuthTab('login')">Sign In</button>
                <button id="tabRegister" class="auth-tab-btn" onclick="switchAuthTab('register')">Sign Up</button>
            </div>
            
            <input type="text" id="authUsername" class="modal-input" placeholder="Enter Unique Username" style="margin-top:0; display:none;">
            <input type="email" id="authEmail" class="modal-input" placeholder="Enter Registered Email">
            <input type="password" id="authPassword" class="modal-input" placeholder="Security Password">
            <input type="text" id="authReferralInput" class="modal-input" placeholder="Sponsor Link Tag (Optional)">
            
            <button class="buy-plan-btn" onclick="handleAuthAction()" style="width:100%; margin-top:20px; padding:14px; border-radius:12px; font-size:13px;">INITIALIZE MATRIX SYSTEM</button>
            
            <div style="margin: 15px 0; color: #64748b; font-size: 11px; font-weight: 700;">OR CONTINUE VIA SECURE TOKEN</div>
            
            <button class="google-auth-btn" onclick="executeGoogleAuthenticationLink()">
                <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/auth/google.svg" width="18" alt=""> Continue with Google
            </button>
        </div>
    </div>

    <div class="vestify-header">
        <div class="brand-row">
            <div class="logo-block" onclick="registerAdminLogoTap()">
                <div class="logo-icon"><i class="fa-solid fa-chart-line"></i></div>
                <div class="logo-text">
                    <h1>VESTIFY</h1>
                    <p style="font-size:10px; color:#ef4444;">Quantum Farm Arrays</p>
                </div>
            </div>
            <button class="buy-plan-btn" onclick="triggerSignOut()" style="background:rgba(239,68,68,0.1); color:#ef4444; font-size:11px; padding:6px 12px; border:1px solid rgba(239,68,68,0.2);">Disconnect</button>
        </div>
    </div>

    <div id="pageHome" class="app-view-page active-page">
        <div class="stats-dashboard">
            <span class="live-tag"><i class="fa-solid fa-bolt"></i> Cluster Online</span>
            <p style="font-size:12px; color:#64748b;">Available Scaled Wallet Assets</p>
            <h2 style="font-size:28px; margin-top:4px; font-weight:800; color:white;">Rs. <span id="assetDisplay">0.00</span></h2>
            <p style="font-size:11px; color:#94a3b8; margin-top:8px;">Active Running Nodes: <b id="minerDisplay" style="color:#ef4444;">0</b></p>
        </div>

        <div class="quick-actions">
            <button class="action-btn btn-deposit" onclick="openDepositModal()"><i class="fa-solid fa-wallet"></i>Deposit</button>
            <button class="action-btn btn-withdraw" onclick="openWithdrawModal()"><i class="fa-solid fa-money-bill-transfer"></i>Payout</button>
            <button class="action-btn btn-transfer" onclick="openTransferModal()"><i class="fa-solid fa-paper-plane"></i>Transfer</button>
        </div>

        <div class="section-title"><i class="fa-solid fa-microchip"></i> Hardware Cluster Farm Pods</div>
        <div class="plan-tabs-row">
            <div class="plan-tab-trigger active-tab" id="tbNorm" onclick="switchPlanCategory('gridNormal', 'tbNorm')">Micro Processors</div>
            <div class="plan-tab-trigger" id="tbSpec" onclick="switchPlanCategory('gridSpecial', 'tbSpec')">SSR Mega Farms</div>
            <div class="plan-tab-trigger" id="tbOffr" onclick="switchPlanCategory('gridOffers', 'tbOffr')">Enterprise Nodes</div>
        </div>

        <div class="plans-grid active-grid" id="gridNormal">
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-server" style="color:#ef4444; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify Micro VT-1</h4><p>Cost: <b>Rs. 300</b> | Yield: <span>Rs. 25/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify Micro VT-1', 300, 25)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-server" style="color:#ef4444; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify Micro VT-2</h4><p>Cost: <b>Rs. 600</b> | Yield: <span>Rs. 52/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify Micro VT-2', 600, 52)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-server" style="color:#ef4444; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify Micro VT-3</h4><p>Cost: <b>Rs. 1,000</b> | Yield: <span>Rs. 90/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify Micro VT-3', 1000, 90)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-server" style="color:#ef4444; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify Micro VT-4</h4><p>Cost: <b>Rs. 1,500</b> | Yield: <span>Rs. 140/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify Micro VT-4', 1500, 140)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-server" style="color:#ef4444; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify Micro VT-5</h4><p>Cost: <b>Rs. 2,000</b> | Yield: <span>Rs. 195/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify Micro VT-5', 2000, 195)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-server" style="color:#ef4444; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify Micro VT-6</h4><p>Cost: <b>Rs. 3,000</b> | Yield: <span>Rs. 300/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify Micro VT-6', 3000, 300)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-server" style="color:#ef4444; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify Micro VT-7</h4><p>Cost: <b>Rs. 4,000</b> | Yield: <span>Rs. 410/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify Micro VT-7', 4000, 410)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-server" style="color:#ef4444; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify Micro VT-8</h4><p>Cost: <b>Rs. 5,000</b> | Yield: <span>Rs. 530/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify Micro VT-8', 5000, 530)">Lease</button>
            </div>
        </div>

        <div class="plans-grid" id="gridSpecial">
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-cubes" style="color:#38bdf8; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify SSR Orla-1</h4><p>Cost: <b>Rs. 7,000</b> | Yield: <span>Rs. 760/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify SSR Orla-1', 7000, 760)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-cubes" style="color:#38bdf8; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify SSR Orla-2</h4><p>Cost: <b>Rs. 10,000</b> | Yield: <span>Rs. 1,120/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify SSR Orla-2', 10000, 1120)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-cubes" style="color:#38bdf8; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify SSR Orla-3</h4><p>Cost: <b>Rs. 15,000</b> | Yield: <span>Rs. 1,750/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify SSR Orla-3', 15000, 1750)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-cubes" style="color:#38bdf8; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify SSR Orla-4</h4><p>Cost: <b>Rs. 20,000</b> | Yield: <span>Rs. 2,400/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify SSR Orla-4', 20000, 2400)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-cubes" style="color:#38bdf8; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify SSR Orla-5</h4><p>Cost: <b>Rs. 25,000</b> | Yield: <span>Rs. 3,100/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify SSR Orla-5', 25000, 3100)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-cubes" style="color:#38bdf8; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify SSR Orla-6</h4><p>Cost: <b>Rs. 30,000</b> | Yield: <span>Rs. 3,850/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify SSR Orla-6', 30000, 3850)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-cubes" style="color:#38bdf8; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify SSR Orla-7</h4><p>Cost: <b>Rs. 40,000</b> | Yield: <span>Rs. 5,300/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify SSR Orla-7', 40000, 5300)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-cubes" style="color:#38bdf8; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify SSR Orla-8</h4><p>Cost: <b>Rs. 50,000</b> | Yield: <span>Rs. 6,800/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify SSR Orla-8', 50000, 6800)">Lease</button>
            </div>
        </div>

        <div class="plans-grid" id="gridOffers">
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-circle-nodes" style="color:#a855f7; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify Infinite-1</h4><p>Cost: <b>Rs. 65,000</b> | Yield: <span>Rs. 9,000/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify Infinite-1', 65000, 9000)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-circle-nodes" style="color:#a855f7; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify Infinite-2</h4><p>Cost: <b>Rs. 80,000</b> | Yield: <span>Rs. 11,500/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify Infinite-2', 80000, 11500)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-circle-nodes" style="color:#a855f7; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify Infinite-3</h4><p>Cost: <b>Rs. 100,000</b> | Yield: <span>Rs. 15,000/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify Infinite-3', 100000, 15000)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-circle-nodes" style="color:#a855f7; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify Infinite-4</h4><p>Cost: <b>Rs. 120,000</b> | Yield: <span>Rs. 18,500/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify Infinite-4', 120000, 18500)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-circle-nodes" style="color:#a855f7; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify Infinite-5</h4><p>Cost: <b>Rs. 150,000</b> | Yield: <span>Rs. 24,000/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify Infinite-5', 150000, 24000)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-circle-nodes" style="color:#a855f7; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify Infinite-6</h4><p>Cost: <b>Rs. 200,000</b> | Yield: <span>Rs. 33,000/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify Infinite-6', 200000, 33000)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-circle-nodes" style="color:#a855f7; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify Infinite-7</h4><p>Cost: <b>Rs. 250,000</b> | Yield: <span>Rs. 42,500/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify Infinite-7', 250000, 42500)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-img-frame"><i class="fa-solid fa-circle-nodes" style="color:#a855f7; font-size:24px;"></i></div>
                <div class="plan-details"><h4>Vestify Infinite-8</h4><p>Cost: <b>Rs. 300,000</b> | Yield: <span>Rs. 53,000/Day</span></p></div>
                <button class="buy-plan-btn" onclick="leaseHardwareNode('Vestify Infinite-8', 300000, 53000)">Lease</button>
            </div>
        </div>

        <div class="section-title"><i class="fa-solid fa-hourglass-half"></i> Daily Node Yield Distribution Track</div>
        <div class="history-card" style="margin: 0; background: rgba(239,68,68,0.03); border-color: rgba(239,68,68,0.2);">
            <div>
                <span style="font-size:13px; font-weight:700; color:#ef4444;">Block Cycle Countdown</span>
                <p style="font-size:11px; color:#64748b;">Realtime hardware synchronizer logic</p>
            </div>
            <span id="countdownTimerClock" style="font-size:15px; font-weight:800; color:#ef4444; letter-spacing:1px;">24:00:00</span>
        </div>
    </div>

    <div id="pageHistory" class="app-view-page">
        <div class="section-title" style="margin-left:0;"><i class="fa-solid fa-list"></i> Structural Ledger Logs</div>
        <div id="userHistoryRecordsList">Awaiting ledger cluster initialization stream loops...</div>
    </div>

    <div class="bottom-nav">
        <div class="nav-item active" id="nvHome" onclick="routeToView('pageHome', 'nvHome')"><i class="fa-solid fa-house"></i><span>Home</span></div>
        <div class="nav-item" id="nvHistory" onclick="routeToView('pageHistory', 'nvHistory')"><i class="fa-solid fa-receipt"></i><span>Ledger Logs</span></div>
    </div>

    <div id="depositModal" class="premium-modal">
        <div class="modal-content" style="max-width:400px; text-align:left;">
            <div class="close-modal-btn" onclick="closeModal('depositModal')">&times;</div>
            <h4 style="margin-bottom:12px; color:white; text-align:center;">Secure PKR Deposit Terminal</h4>
            
            <input type="number" id="pkrDepositInput" class="modal-input" placeholder="Enter Amount (PKR)" oninput="updateDepositCalculations()">
            
            <div style="background: rgba(16, 185, 129, 0.05); padding: 10px; border-radius: 12px; margin-top: 10px; font-size:12px;">
                <div style="display:flex; justify-content:space-between; color:#94a3b8;"><span>Promotional Boost (+8%):</span> <b id="lblBonusPKR" style="color:#10b981;">Rs. 0</b></div>
                <div style="display:flex; justify-content:space-between; color:white; font-weight:800; margin-top:4px;"><span>Credited Assets:</span> <b id="lblTotalCreditedPKR" style="color:#ef4444;">Rs. 0</b></div>
            </div>

            <div style="display: grid; grid-template-columns: repeat(3, 1fr); gap: 8px; margin-top: 12px;">
                <button id="chkEasyPaisa" class="action-btn" onclick="selectDepositGatewayChannel('EasyPaisa')" style="border-color:#10b981; font-size:10px;"><i class="fa-solid fa-mobile-screen" style="color:#10b981;"></i>EasyPaisa</button>
                <button id="chkJazzCash" class="action-btn" onclick="selectDepositGatewayChannel('JazzCash')" style="font-size:10px;"><i class="fa-solid fa-wallet" style="color:#eab308;"></i>JazzCash</button>
                <button id="chkUSDT" class="action-btn" onclick="selectDepositGatewayChannel('USDT')" style="font-size:10px;"><i class="fa-solid fa-circle-dollar-to-slot" style="color:#26a17b;"></i>Binance</button>
            </div>

            <div style="background:rgba(0,0,0,0.2); padding:10px; border-radius:10px; margin-top:12px; display:flex; justify-content:space-between; align-items:center;">
                <div>
                    <span id="lblAccountTitleType" style="font-size:10px; color:#64748b; text-transform:uppercase;">EasyPaisa Destination</span>
                    <h4 id="txtMerchantAccountNo" style="font-size:13px; color:white;">03379827882</h4>
                </div>
                <button class="plan-tab-trigger" onclick="copyMerchantAccountToken()" style="padding:2px 8px; font-size:10px;">Copy</button>
            </div>
            
            <div style="font-size:11px; margin-top:8px; display:flex; justify-content:space-between;"><span>Exact Amount Due:</span> <b id="txtPayableExactAmount" style="color:#ef4444;">Rs. 0</b></div>

            <input type="text" id="depositTxIDInput" class="modal-input" placeholder="Enter 11-12 Digit Reference ID (TID)">
            <button class="buy-plan-btn" onclick="submitPkrDepositToFirebase()" style="width:100%; margin-top:12px; padding:12px;">LOG PAYLOAD TRANSACTION</button>
        </div>
    </div>

    <div id="withdrawModal" class="premium-modal">
        <div class="modal-content">
            <div class="close-modal-btn" onclick="closeModal('withdrawModal')">&times;</div>
            <h4>Secure Capital Payout</h4>
            <input type="text" id="withdrawTitle" class="modal-input" placeholder="Account Holder Title">
            <input type="text" id="withdrawNumber" class="modal-input" placeholder="Target Bank/Wallet Number">
            <input type="number" id="withdrawAmount" class="modal-input" placeholder="Amount (PKR)">
            <button class="buy-plan-btn" onclick="dispatchWithdrawalToCloud()" style="width:100%; margin-top:12px; padding:12px; background:#3b82f6;">DISPATCH CASHOUT REQUEST</button>
        </div>
    </div>

    <div id="transferModal" class="premium-modal">
        <div class="modal-content">
            <div class="close-modal-btn" onclick="closeModal('transferModal')">&times;</div>
            <h4 style="color:#eab308;"><i class="fa-solid fa-paper-plane"></i> Wallet P2P Link</h4>
            <input type="text" id="transferTargetUsername" class="modal-input" placeholder="Recipient Unique Username">
            <input type="number" id="transferAmount" class="modal-input" placeholder="Amount (PKR)">
            <button class="buy-plan-btn" onclick="executeP2PInternalTransfer()" style="width:100%; margin-top:14px; padding:12px; background:linear-gradient(135deg, #eab308, #ca8a04);">EXECUTE P2P NODE LINK</button>
        </div>
    </div>

    <script>
        // Production Cloud Configuration Matrices
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
        let activeGatewayChannel = "EasyPaisa";
        let adminTapSequenceCounter = 0;
        const ADMIN_MASTER_PASSKEY = "Khankhail786"; 

        const paymentGatewayMerchantConfig = {
            "EasyPaisa": { number: "03379827882", title: "EasyPaisa Node Principal" },
            "JazzCash": { number: "03705519562", title: "JazzCash Node Principal" },
            "USDT": { number: "TRC20_CRYPTO_HASH_NETWORK_ROUTE", title: "Binance Destination Node (TRC20)" }
        };

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

        function registerAdminLogoTap() {
            adminTapSequenceCounter++;
            if (adminTapSequenceCounter === 3) {
                triggerToastDisplay("Admin Protocol Link: 2 steps left...");
            }
            if (adminTapSequenceCounter >= 5) {
                adminTapSequenceCounter = 0;
                document.getElementById('secretAdminScreen').style.display = 'flex';
                triggerToastDisplay("Admin core routing link opened!");
            }
        }

        function verifyAdminMasterPasskey() {
            const enteredKey = document.getElementById('txtAdminSecretKey').value;
            if(enteredKey === ADMIN_MASTER_PASSKEY) {
                triggerToastDisplay("Access granted. Opening Firestore Console arrays...");
                window.open("https://console.firebase.google.com/project/vestify-991f2/firestore/data", "_blank");
                closeAdminTerminal();
            } else {
                triggerToastDisplay("🚨 Access violation: Incorrect master terminal passkey!");
                document.getElementById('txtAdminSecretKey').value = "";
            }
        }

        function closeAdminTerminal() {
            document.getElementById('secretAdminScreen').style.display = 'none';
            document.getElementById('txtAdminSecretKey').value = "";
        }

        function switchAuthTab(mode) {
            currentAuthTabMode = mode;
            document.getElementById('tabLogin').classList.toggle('active', mode === 'login');
            document.getElementById('tabRegister').classList.toggle('active', mode === 'register');
            document.getElementById('authUsername').style.display = mode === 'register' ? 'block' : 'none';
        }

        function handleAuthAction() {
            const email = document.getElementById('authEmail').value.trim();
            const pass = document.getElementById('authPassword').value;
            const username = document.getElementById('authUsername').value.trim().toLowerCase();

            if(!email || !pass) { triggerToastDisplay("Parameters missing inside initialization channels."); return; }

            if(currentAuthTabMode === "register") {
                if(!username) { triggerToastDisplay("Please state a clean structural user tag."); return; }
                
                db.collection("users").where("username", "==", username).get().then(snap => {
                    if(!snap.empty) { throw "Username allocation trace locked on cluster servers."; }
                    return auth.createUserWithEmailAndPassword(email, pass);
                }).then(cred => {
                    return db.collection("users").doc(cred.user.uid).set({
                        uid: cred.user.uid, balance: 0.00, minersCount: 0, email: email, username: username,
                        timestamp: firebase.firestore.FieldValue.serverTimestamp()
                    });
                }).then(() => {
                    triggerToastDisplay("Registry matrix mapping fully compiled.");
                }).catch(e => triggerToastDisplay(e.message || e));
            } else {
                auth.signInWithEmailAndPassword(email, pass)
                    .then(() => triggerToastDisplay("Secure session token generated."))
                    .catch(e => triggerToastDisplay(e.message));
            }
        }

        function executeGoogleAuthenticationLink() {
            const provider = new firebase.auth.GoogleAuthProvider();
            auth.signInWithPopup(provider).then(result => {
                const user = result.user;
                const userRef = db.collection("users").doc(user.uid);
                
                userRef.get().then(doc => {
                    if(!doc.exists) {
                        let computedTag = "user_" + user.email.split('@')[0] + Math.floor(Math.random()*100);
                        userRef.set({
                            uid: user.uid, balance: 0.00, minersCount: 0, email: user.email, username: computedTag,
                            timestamp: firebase.firestore.FieldValue.serverTimestamp()
                        }).then(() => triggerToastDisplay("Google profile credentials registered."));
                    }
                });
            }).catch(err => triggerToastDisplay(err.message));
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

        function updateDepositCalculations() {
            let amt = parseFloat(document.getElementById('pkrDepositInput').value) || 0;
            let bonus = amt * 0.08;
            let total = amt + bonus;

            document.getElementById('lblBonusPKR').innerText = "Rs. " + bonus.toLocaleString();
            document.getElementById('lblTotalCreditedPKR').innerText = "Rs. " + total.toLocaleString();
            
            if(activeGatewayChannel === "USDT") {
                document.getElementById('txtPayableExactAmount').innerText = `$${(amt / 300).toFixed(2)} USDT (~Rs. ${amt})`;
            } else {
                document.getElementById('txtPayableExactAmount').innerText = "Rs. " + amt.toLocaleString();
            }
        }

        function selectDepositGatewayChannel(channel) {
            activeGatewayChannel = channel;
            document.querySelectorAll('#depositModal .action-btn').forEach(b => b.style.borderColor = "rgba(255,255,255,0.05)");
            
            if(channel === "EasyPaisa") document.getElementById('chkEasyPaisa').style.borderColor = "#10b981";
            if(channel === "JazzCash") document.getElementById('chkJazzCash').style.borderColor = "#eab308";
            if(channel === "USDT") document.getElementById('chkUSDT').style.borderColor = "#26a17b";

            const node = paymentGatewayMerchantConfig[channel];
            document.getElementById('lblAccountTitleType').innerText = node.title;
            document.getElementById('txtMerchantAccountNo').innerText = node.number;
            
            updateDepositCalculations();
        }

        function copyMerchantAccountToken() {
            let num = document.getElementById('txtMerchantAccountNo').innerText;
            navigator.clipboard.writeText(num);
            triggerToastDisplay("Merchant transfer hash string token saved.");
        }

        function submitPkrDepositToFirebase() {
            const inputVal = parseFloat(document.getElementById('pkrDepositInput').value);
            const tid = document.getElementById('depositTxIDInput').value.trim();

            if(isNaN(inputVal) || inputVal < 200) { triggerToastDisplay("Minimum threshold limit asset is Rs. 200."); return; }
            if(!tid || tid.length < 10) { triggerToastDisplay("Invalid system parameter tracking ID size."); return; }

            db.collection("deposits").add({
                userId: currentUserId, amount: inputVal, gatewayChannel: activeGatewayChannel,
                transactionId: tid, status: "Pending Admin Auditing", timestamp: firebase.firestore.FieldValue.serverTimestamp()
            }).then(() => {
                closeModal('depositModal'); triggerToastDisplay("Transaction routed to structural verification arrays.");
                document.getElementById('pkrDepositInput').value = ""; document.getElementById('depositTxIDInput').value = "";
            });
        }

        function leaseHardwareNode(name, price, dailyProfitValue) {
            if(userCurrentBalance < price) { triggerToastDisplay("Insufficient funds inside core node balance."); return; }
            db.collection("users").doc(currentUserId).update({
                balance: userCurrentBalance - price, minersCount: totalActiveCoresCount + 1
            }).then(() => {
                triggerToastDisplay(`Successfully leased ${name}! Cluster logic active.`);
            });
        }

        function dispatchWithdrawalToCloud() {
            const title = document.getElementById('withdrawTitle').value.trim();
            const targetNo = document.getElementById('withdrawNumber').value.trim();
            const amt = parseFloat(document.getElementById('withdrawAmount').value);

            if(!title || !targetNo || isNaN(amt) || amt > userCurrentBalance) { triggerToastDisplay("Invalid configuration limits."); return; }

            db.collection("users").doc(currentUserId).update({ balance: userCurrentBalance - amt }).then(() => {
                return db.collection("withdrawals").add({
                    userId: currentUserId, title: title, targetNo: targetNo, amount: amt, status: "pending",
                    timestamp: firebase.firestore.FieldValue.serverTimestamp()
                });
            }).then(() => { closeModal('withdrawModal'); triggerToastDisplay("Payout request buffered into queue loops."); });
        }

        function executeP2PInternalTransfer() {
            const targetUserField = document.getElementById('transferTargetUsername').value.trim().toLowerCase();
            const amountField = parseFloat(document.getElementById('transferAmount').value);

            if(!targetUserField || isNaN(amountField) || amountField <= 0) { triggerToastDisplay("Please refine parameter variables."); return; }
            if(amountField > userCurrentBalance) { triggerToastDisplay("Assets exceed account threshold data bounds."); return; }

            db.collection("users").where("username", "==", targetUserField).get().then(snapshot => {
                if(snapshot.empty) { throw "Destination user hash match not found inside cloud databases."; }
                const node = snapshot.docs[0];
                if(node.id === currentUserId) { throw "Self loop network parameters are blocked."; }

                const batch = db.batch();
                batch.update(db.collection("users").doc(currentUserId), { balance: userCurrentBalance - amountField });
                batch.update(db.collection("users").doc(node.id), { balance: (node.data().balance || 0) + amountField });
                return batch.commit();
            }).then(() => {
                closeModal('transferModal'); triggerToastDisplay(`Transferred Rs. ${amountField} seamlessly.`);
            }).catch(err => triggerToastDisplay(err));
        }

        function syncUserLedgerTracks() {
            db.collection("deposits").where("userId", "==", currentUserId).onSnapshot(snap => {
                let html = "";
                snap.forEach(doc => {
                    const d = doc.data();
                    html += `<div class="history-card"><div><b>Rs. ${d.amount}</b><p>${d.transactionId} (${d.gatewayChannel})</p></div><span>${d.status}</span></div>`;
                });
                document.getElementById('userHistoryRecordsList').innerHTML = html || "Financial log tracks void inside node blocks.";
            });
        }

        function initializeCountdownClockTicker() {
            setInterval(() => {
                const now = new Date();
                const h = 23 - now.getHours();
                const m = 59 - now.getMinutes();
                const s = 59 - now.getSeconds();
                document.getElementById('countdownTimerClock').innerText = 
                    `${h.toString().padStart(2, '0')}:${m.toString().padStart(2, '0')}:${s.toString().padStart(2, '0')}`;
            }, 1000);
        }

        function switchPlanCategory(gridId, tabId) {
            document.querySelectorAll('.plans-grid').forEach(g => g.classList.remove('active-grid'));
            document.querySelectorAll('.plan-tab-trigger').forEach(t => t.classList.remove('active-tab'));
            document.getElementById(gridId).classList.add('active-grid');
            document.getElementById(tabId).classList.add('active-tab');
        }

        function routeToView(pId, nId) {
            document.querySelectorAll('.app-view-page').forEach(p=>p.classList.remove('active-page'));
            document.querySelectorAll('.nav-item').forEach(n=>n.classList.remove('active'));
            document.getElementById(pId).classList.add('active-page'); document.getElementById(nId).classList.add('active');
        }

        function openDepositModal() { document.getElementById('depositModal').classList.add('modal-open'); selectDepositGatewayChannel('EasyPaisa'); }
        function openWithdrawModal() { document.getElementById('withdrawModal').classList.add('modal-open'); }
        function openTransferModal() { document.getElementById('transferModal').classList.add('modal-open'); }
        function closeModal(id) { document.getElementById(id).classList.remove('modal-open'); }

        function triggerToastDisplay(msg) {
            const t = document.getElementById('rewardToast'); t.innerText = msg; t.classList.add('toast-show');
            setTimeout(() => t.classList.remove('toast-show'), 2500);
        }
    </script>
</body>
</html>
