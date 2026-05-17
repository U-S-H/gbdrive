<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Vestify Network Pro - Premium Decentralized Asset Mining Engine</title>
    
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>

    <style>
        /* Matrix Base Elements Configuration */
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Plus Jakarta Sans', sans-serif; -webkit-tap-highlight-color: transparent; }
        body { background: #040814; color: #ffffff; min-height: 100vh; padding-bottom: 110px; overflow-x: hidden; position: relative; }
        
        /* Ultra Modern High Yield Visual Ambient Accents */
        .ambient-glow-alpha { position: fixed; width: 400px; height: 400px; background: radial-gradient(circle, rgba(239, 68, 68, 0.14) 0%, rgba(0,0,0,0) 75%); top: -100px; left: -100px; z-index: -1; pointer-events: none; }
        .ambient-glow-beta { position: fixed; width: 500px; height: 500px; background: radial-gradient(circle, rgba(56, 189, 248, 0.11) 0%, rgba(0,0,0,0) 75%); bottom: 50px; right: -150px; z-index: -1; pointer-events: none; }

        /* Custom Scrollbar Properties */
        ::-webkit-scrollbar { width: 4px; height: 4px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { background: rgba(255, 255, 255, 0.1); border-radius: 10px; }

        /* Security Portal Shield Layouts */
        #authScreen, #secretAdminScreen { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: #040814; z-index: 99999; display: flex; align-items: center; justify-content: center; padding: 20px; overflow-y: auto; }
        .auth-card-shield, .admin-card-shield { background: rgba(11, 22, 44, 0.4); border: 1px solid rgba(255, 255, 255, 0.06); border-radius: 32px; padding: 40px 24px; width: 100%; max-width: 420px; text-align: center; backdrop-filter: blur(30px); box-shadow: 0 30px 70px rgba(0,0,0,0.6); }
        
        .auth-tab-segment { display: flex; gap: 8px; margin-bottom: 25px; background: rgba(255,255,255,0.02); padding: 6px; border-radius: 16px; border: 1px solid rgba(255,255,255,0.04); }
        .auth-tab-action { flex: 1; padding: 14px; border-radius: 12px; border: none; background: transparent; color: #94a3b8; font-weight: 700; font-size: 13px; cursor: pointer; transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
        .auth-tab-action.active-tab-state { background: linear-gradient(135deg, #ef4444, #ff7e5f); color: #ffffff; box-shadow: 0 6px 20px rgba(239,68,68,0.35); }
        
        /* Unified System Input Controls */
        .sys-input-field { width: 100%; padding: 14px 16px; border-radius: 14px; border: 1px solid rgba(255,255,255,0.07); background: rgba(2, 5, 13, 0.6); color: #ffffff; font-size: 14px; margin-top: 12px; outline: none; transition: all 0.3s; font-weight: 500; }
        .sys-input-field:focus { border-color: #ef4444; box-shadow: 0 0 0 3px rgba(239, 68, 68, 0.15); background: rgba(2, 5, 13, 0.8); }
        .sys-input-field::placeholder { color: #475569; }

        .google-sso-trigger { width: 100%; padding: 14px; border-radius: 14px; border: 1px solid rgba(255,255,255,0.08); background: rgba(255,255,255,0.02); color: #ffffff; font-weight: 700; font-size: 14px; cursor: pointer; display: flex; align-items: center; justify-content: center; gap: 12px; margin-top: 16px; transition: background 0.3s; }
        .google-sso-trigger:hover { background: rgba(255,255,255,0.05); }

        /* Core Application Dashboard Structure */
        .vestify-navbar-header { padding: 16px; background: rgba(6, 11, 25, 0.6); backdrop-filter: blur(20px); border-bottom: 1px solid rgba(255, 255, 255, 0.04); position: sticky; top: 0; z-index: 1000; }
        .nav-brand-container { display: flex; justify-content: space-between; align-items: center; max-width: 1200px; margin: 0 auto; }
        .brand-logo-trigger { display: flex; align-items: center; gap: 14px; cursor: pointer; user-select: none; }
        .logo-graphic-node { width: 44px; height: 44px; background: linear-gradient(135deg, #ef4444, #ff7e5f); border-radius: 14px; display: flex; align-items: center; justify-content: center; font-size: 20px; font-weight: 800; color: white; box-shadow: 0 4px 15px rgba(239,68,68,0.25); }
        .brand-meta-title h1 { font-size: 20px; font-weight: 800; letter-spacing: 0.5px; color: #ffffff; }
        .brand-meta-title p { font-size: 10px; color: #ef4444; font-weight: 700; text-transform: uppercase; letter-spacing: 1px; margin-top: 1px; }

        /* Dynamic Wallet Metric Dashboard Panel */
        .metric-canvas-card { background: linear-gradient(145deg, rgba(15, 23, 42, 0.4), rgba(30, 41, 59, 0.1)); border: 1px solid rgba(255, 255, 255, 0.05); border-radius: 28px; padding: 24px; margin: 24px 16px; position: relative; overflow: hidden; box-shadow: 0 15px 35px rgba(0,0,0,0.3); }
        .active-status-beacon { position: absolute; top: 16px; right: 16px; background: rgba(16, 185, 129, 0.12); color: #10b981; border: 1px solid rgba(16,185,129,0.25); font-size: 10px; padding: 4px 10px; border-radius: 30px; font-weight: 700; display: flex; align-items: center; gap: 6px; text-transform: uppercase; letter-spacing: 0.5px; }
        .beacon-pulse { width: 6px; height: 6px; background: #10b981; border-radius: 50%; display: inline-block; animation: beaconBlink 2s infinite; }
        @keyframes beaconBlink { 0% { opacity: 0.4; } 50% { opacity: 1; } 100% { opacity: 0.4; } }

        /* Quick Gateway Action Elements */
        .gateway-actions-matrix { display: grid; grid-template-columns: repeat(3, 1fr); gap: 12px; margin: 0 16px 24px 16px; }
        .gateway-trigger-cell { padding: 14px 8px; border-radius: 16px; border: 1px solid rgba(255,255,255,0.04); font-size: 13px; font-weight: 700; color: #ffffff; cursor: pointer; display: flex; flex-direction: column; align-items: center; gap: 8px; background: rgba(255,255,255,0.02); transition: all 0.25s; }
        .gateway-trigger-cell:active { transform: scale(0.96); background: rgba(255,255,255,0.05); }
        .cell-deposit i { color: #10b981; font-size: 16px; }
        .cell-payout i { color: #3b82f6; font-size: 16px; }
        .cell-p2p i { color: #eab308; font-size: 16px; }

        /* Dynamic Grid Filtering Systems */
        .filter-ribbon-row { display: flex; gap: 10px; padding: 0 16px; margin-bottom: 16px; overflow-x: auto; white-space: nowrap; -webkit-overflow-scrolling: touch; }
        .filter-ribbon-row::-webkit-scrollbar { display: none; }
        .filter-node-btn { background: rgba(255,255,255,0.02); border: 1px solid rgba(255,255,255,0.05); padding: 10px 18px; border-radius: 30px; font-size: 12px; color: #94a3b8; font-weight: 700; cursor: pointer; transition: all 0.3s; }
        .filter-node-btn.active-node-state { background: #ef4444; color: #ffffff; border-color: #ef4444; box-shadow: 0 4px 15px rgba(239,68,68,0.3); }

        .hardware-cluster-grid { display: none; flex-direction: column; gap: 12px; padding: 0 16px; }
        .hardware-cluster-grid.active-cluster-grid { display: flex; }
        
        /* Premium Core Processing Array Cards */
        .hardware-pod-card { background: rgba(11, 18, 36, 0.4); border: 1px solid rgba(255, 255, 255, 0.04); border-radius: 22px; padding: 18px; display: flex; align-items: center; gap: 16px; position: relative; transition: border-color 0.3s; }
        .hardware-pod-card:hover { border-color: rgba(255, 255, 255, 0.08); }
        .pod-graphic-wrapper { width: 68px; height: 68px; border-radius: 16px; background: rgba(15, 23, 42, 0.8); border: 1px solid rgba(255,255,255,0.06); display: flex; align-items: center; justify-content: center; }
        .pod-meta-details { flex: 1; }
        .pod-meta-details h4 { font-size: 15px; font-weight: 700; color: #ffffff; letter-spacing: 0.3px; }
        .pod-meta-details p { font-size: 12px; color: #64748b; margin-top: 4px; }
        .pod-meta-details span { color: #10b981; font-weight: 700; }
        
        /* Core Executable Controls */
        .action-execution-btn { padding: 12px 20px; background: linear-gradient(135deg, #ef4444, #ff7e5f); border: none; border-radius: 14px; color: #ffffff; font-weight: 700; font-size: 13px; cursor: pointer; transition: all 0.3s; box-shadow: 0 4px 15px rgba(239,68,68,0.2); }
        .action-execution-btn:active { transform: scale(0.95); }

        /* Modular Routing Screen Enclosures */
        .app-view-enclosure { display: none; padding: 0 16px; max-width: 1200px; margin: 0 auto; }
        .app-view-enclosure.active-view-state { display: block; }
        .ledger-row-card { background: rgba(255,255,255,0.01); border: 1px solid rgba(255,255,255,0.04); padding: 16px; border-radius: 16px; margin-bottom: 10px; display: flex; justify-content: space-between; align-items: center; }

        /* Premium Floating Dock Leveler */
        .dock-navigation-deck { position: fixed; bottom: 0; left: 0; width: 100%; height: 76px; background: rgba(4, 8, 20, 0.93); backdrop-filter: blur(25px); border-top: 1px solid rgba(255, 255, 255, 0.05); display: flex; justify-content: space-around; align-items: center; z-index: 9999; }
        .dock-action-node { display: flex; flex-direction: column; align-items: center; gap: 5px; color: #64748b; text-decoration: none; font-size: 11px; font-weight: 600; cursor: pointer; transition: color 0.3s; width: 60px; text-align: center; }
        .dock-action-node i { font-size: 18px; }
        .dock-action-node.active-dock-state { color: #ef4444; }

        /* Secure Modular Popups Layer setup */
        .overlay-system-modal { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(2, 4, 10, 0.88); backdrop-filter: blur(15px); z-index: 20000; display: flex; align-items: center; justify-content: center; opacity: 0; pointer-events: none; transition: opacity 0.3s cubic-bezier(0.4, 0, 0.2, 1); padding: 20px; }
        .overlay-system-modal.modal-display-open { opacity: 1; pointer-events: auto; }
        .modal-vault-wrapper { background: #0b1222; border: 1px solid rgba(255, 255, 255, 0.07); width: 100%; max-width: 400px; border-radius: 28px; padding: 26px; text-align: center; position: relative; box-shadow: 0 20px 50px rgba(0,0,0,0.5); }
        .modal-dismiss-trigger { position: absolute; top: 16px; right: 16px; color: #64748b; font-size: 24px; cursor: pointer; transition: color 0.2s; }
        .modal-dismiss-trigger:hover { color: white; }

        /* Operational Toast Array Console */
        .toast-popup-console { position: fixed; top: 25px; left: 50%; transform: translateX(-50%) translateY(-120px); background: linear-gradient(135deg, #ef4444, #b91c1c); padding: 14px 24px; border-radius: 18px; z-index: 100000; font-weight: 700; font-size: 13px; transition: transform 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275); box-shadow: 0 12px 30px rgba(239,68,68,0.4); color: white; white-space: nowrap; text-align: center; }
        .toast-popup-console.toast-display-show { transform: translateX(-50%) translateY(0); }
        .section-header-tag { font-size: 12px; color: #94a3b8; margin: 24px 16px 12px 16px; text-transform: uppercase; letter-spacing: 0.8px; font-weight: 700; display: flex; align-items: center; gap: 8px; }
    </style>
</head>
<body>

    <div class="ambient-glow-alpha"></div>
    <div class="ambient-glow-beta"></div>

    <div id="rewardToast" class="toast-popup-console">System Notification Active</div>

    <div id="secretAdminScreen" style="display: none;">
        <div class="admin-card-shield">
            <div style="color: #ef4444; font-size: 32px; margin-bottom: 12px;"><i class="fa-solid fa-user-shield"></i></div>
            <h3 style="color: white; font-weight: 800; font-size: 20px;">Admin Console Access</h3>
            <p style="font-size: 12px; color: #64748b; margin-bottom: 24px;">Enter administrative credentials to bypass routing</p>
            
            <input type="password" id="txtAdminSecretKey" class="sys-input-field" placeholder="Enter Master Passkey" style="text-align: center; font-size: 18px; letter-spacing: 3px;">
            
            <div style="display: flex; gap: 12px; margin-top: 24px;">
                <button class="action-execution-btn" onclick="closeAdminTerminal()" style="flex: 1; background: rgba(255,255,255,0.04); color: #94a3b8; box-shadow: none;">Abort</button>
                <button class="action-execution-btn" onclick="verifyAdminMasterPasskey()" style="flex: 1; background: linear-gradient(135deg, #ef4444, #991b1b);">Unlock Matrix</button>
            </div>
        </div>
    </div>

    <div id="authScreen">
        <div class="auth-card-shield">
            <h2 style="font-weight: 800; font-size: 26px; letter-spacing: 1px; background: linear-gradient(135deg, #ffffff, #94a3b8); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin-bottom: 4px;">VESTIFY</h2>
            <p style="font-size: 12px; color: #64748b; margin-bottom: 24px;">High Yield Capital Mining Network</p>
            
            <div class="auth-tab-segment">
                <button id="tabLogin" class="auth-tab-action active-tab-state" onclick="switchAuthTab('login')">Sign In</button>
                <button id="tabRegister" class="auth-tab-action" onclick="switchAuthTab('register')">Sign Up</button>
            </div>
            
            <input type="text" id="authUsername" class="sys-input-field" placeholder="Create Unique Username" style="display:none;">
            <input type="email" id="authEmail" class="sys-input-field" placeholder="Enter Account Email">
            <input type="password" id="authPassword" class="sys-input-field" placeholder="Enter Access Password">
            <input type="text" id="authReferralInput" class="sys-input-field" placeholder="Sponsor Link ID (Optional)">
            
            <button class="action-execution-btn" onclick="handleAuthAction()" style="width: 100%; margin-top: 24px; padding: 16px; border-radius: 14px; font-size: 14px;">ESTABLISH LIVE SESSION</button>
            
            <div style="margin: 20px 0 15px 0; color: #475569; font-size: 11px; font-weight: 700; letter-spacing: 0.5px;">OR INTEGRATE TOKEN</div>
            
            <button class="google-sso-trigger" onclick="executeGoogleAuthenticationLink()">
                <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/auth/google.svg" width="18" alt="Google Token"> Authenticate via Google
            </button>
        </div>
    </div>

    <div class="vestify-navbar-header">
        <div class="nav-brand-container">
            <div class="brand-logo-trigger" onclick="registerAdminLogoTap()">
                <div class="logo-graphic-node"><i class="fa-solid fa-cube"></i></div>
                <div class="brand-meta-title">
                    <h1>VESTIFY</h1>
                    <p>Quantum Core Modules</p>
                </div>
            </div>
            <button class="action-execution-btn" onclick="triggerSignOut()" style="background: rgba(239, 68, 68, 0.08); color: #ef4444; font-size: 12px; padding: 8px 16px; border: 1px solid rgba(239, 68, 68, 0.2); box-shadow: none;">Disconnect</button>
        </div>
    </div>

    <div id="pageHome" class="app-view-enclosure active-view-state">
        <div class="metric-canvas-card">
            <span class="active-status-beacon"><span class="beacon-pulse"></span> Nodes Connected</span>
            <p style="font-size: 13px; color: #94a3b8; font-weight: 500;">Available Network Capital Balance</p>
            <h2 style="font-size: 32px; margin-top: 6px; font-weight: 800; color: #ffffff; letter-spacing: -0.5px;">Rs. <span id="assetDisplay">0.00</span></h2>
            <p style="font-size: 12px; color: #64748b; margin-top: 12px; font-weight: 500;">Active Hardware Arrays: <b id="minerDisplay" style="color: #ef4444; font-weight: 700;">0</b></p>
        </div>

        <div class="gateway-actions-matrix">
            <div class="gateway-trigger-cell cell-deposit" onclick="openDepositModal()"><i class="fa-solid fa-shield-heart"></i>Deposit</div>
            <div class="gateway-trigger-cell cell-payout" onclick="openWithdrawModal()"><i class="fa-solid fa-money-bill-trend-up"></i>Payout</div>
            <div class="gateway-trigger-cell cell-p2p" onclick="openTransferModal()"><i class="fa-solid fa-arrows-spin"></i>P2P Node</div>
        </div>

        <div class="section-header-tag"><i class="fa-solid fa-microchip" style="color: #ef4444;"></i> Available Scaled Grid Pools</div>
        <div class="filter-ribbon-row">
            <div class="filter-node-btn active-node-state" id="tbNorm" onclick="switchPlanCategory('gridNormal', 'tbNorm')">Micro Processors</div>
            <div class="filter-node-btn" id="tbSpec" onclick="switchPlanCategory('gridSpecial', 'tbSpec')">SSR Mega Farms</div>
            <div class="filter-node-btn" id="tbOffr" onclick="switchPlanCategory('gridOffers', 'tbOffr')">Enterprise Nodes</div>
        </div>

        <div class="hardware-cluster-grid active-cluster-grid" id="gridNormal">
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-server" style="color:#ef4444; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify Micro VT-1</h4><p>Cost: <b>Rs. 300</b> | Yield: <span>Rs. 25/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify Micro VT-1', 300, 25)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-server" style="color:#ef4444; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify Micro VT-2</h4><p>Cost: <b>Rs. 600</b> | Yield: <span>Rs. 52/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify Micro VT-2', 600, 52)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-server" style="color:#ef4444; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify Micro VT-3</h4><p>Cost: <b>Rs. 1,000</b> | Yield: <span>Rs. 90/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify Micro VT-3', 1000, 90)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-server" style="color:#ef4444; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify Micro VT-4</h4><p>Cost: <b>Rs. 1,500</b> | Yield: <span>Rs. 140/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify Micro VT-4', 1500, 140)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-server" style="color:#ef4444; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify Micro VT-5</h4><p>Cost: <b>Rs. 2,000</b> | Yield: <span>Rs. 195/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify Micro VT-5', 2000, 195)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-server" style="color:#ef4444; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify Micro VT-6</h4><p>Cost: <b>Rs. 3,000</b> | Yield: <span>Rs. 300/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify Micro VT-6', 3000, 300)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-server" style="color:#ef4444; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify Micro VT-7</h4><p>Cost: <b>Rs. 4,000</b> | Yield: <span>Rs. 410/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify Micro VT-7', 4000, 410)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-server" style="color:#ef4444; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify Micro VT-8</h4><p>Cost: <b>Rs. 5,000</b> | Yield: <span>Rs. 530/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify Micro VT-8', 5000, 530)">Lease</button>
            </div>
        </div>

        <div class="hardware-cluster-grid" id="gridSpecial">
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-cubes" style="color:#38bdf8; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify SSR Orla-1</h4><p>Cost: <b>Rs. 7,000</b> | Yield: <span>Rs. 760/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify SSR Orla-1', 7000, 760)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-cubes" style="color:#38bdf8; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify SSR Orla-2</h4><p>Cost: <b>Rs. 10,000</b> | Yield: <span>Rs. 1,120/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify SSR Orla-2', 10000, 1120)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-cubes" style="color:#38bdf8; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify SSR Orla-3</h4><p>Cost: <b>Rs. 15,000</b> | Yield: <span>Rs. 1,750/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify SSR Orla-3', 15000, 1750)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-cubes" style="color:#38bdf8; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify SSR Orla-4</h4><p>Cost: <b>Rs. 20,000</b> | Yield: <span>Rs. 2,400/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify SSR Orla-4', 20000, 2400)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-cubes" style="color:#38bdf8; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify SSR Orla-5</h4><p>Cost: <b>Rs. 25,000</b> | Yield: <span>Rs. 3,100/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify SSR Orla-5', 25000, 3100)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-cubes" style="color:#38bdf8; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify SSR Orla-6</h4><p>Cost: <b>Rs. 30,000</b> | Yield: <span>Rs. 3,850/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify SSR Orla-6', 30000, 3850)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-cubes" style="color:#38bdf8; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify SSR Orla-7</h4><p>Cost: <b>Rs. 40,000</b> | Yield: <span>Rs. 5,300/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify SSR Orla-7', 40000, 5300)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-cubes" style="color:#38bdf8; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify SSR Orla-8</h4><p>Cost: <b>Rs. 50,000</b> | Yield: <span>Rs. 6,800/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify SSR Orla-8', 50000, 6800)">Lease</button>
            </div>
        </div>

        <div class="hardware-cluster-grid" id="gridOffers">
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-circle-nodes" style="color:#a855f7; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify Infinite-1</h4><p>Cost: <b>Rs. 65,000</b> | Yield: <span>Rs. 9,000/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify Infinite-1', 65000, 9000)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-circle-nodes" style="color:#a855f7; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify Infinite-2</h4><p>Cost: <b>Rs. 80,000</b> | Yield: <span>Rs. 11,500/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify Infinite-2', 80000, 11500)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-circle-nodes" style="color:#a855f7; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify Infinite-3</h4><p>Cost: <b>Rs. 100,000</b> | Yield: <span>Rs. 15,000/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify Infinite-3', 100000, 15000)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-circle-nodes" style="color:#a855f7; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify Infinite-4</h4><p>Cost: <b>Rs. 120,000</b> | Yield: <span>Rs. 18,500/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify Infinite-4', 120000, 18500)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-circle-nodes" style="color:#a855f7; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify Infinite-5</h4><p>Cost: <b>Rs. 150,000</b> | Yield: <span>Rs. 24,000/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify Infinite-5', 150000, 24000)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-circle-nodes" style="color:#a855f7; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify Infinite-6</h4><p>Cost: <b>Rs. 200,000</b> | Yield: <span>Rs. 33,000/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify Infinite-6', 200000, 33000)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-circle-nodes" style="color:#a855f7; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify Infinite-7</h4><p>Cost: <b>Rs. 250,000</b> | Yield: <span>Rs. 42,500/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify Infinite-7', 250000, 42500)">Lease</button>
            </div>
            <div class="hardware-pod-card">
                <div class="pod-graphic-wrapper"><i class="fa-solid fa-circle-nodes" style="color:#a855f7; font-size:24px;"></i></div>
                <div class="pod-meta-details"><h4>Vestify Infinite-8</h4><p>Cost: <b>Rs. 300,000</b> | Yield: <span>Rs. 53,000/Day</span></p></div>
                <button class="action-execution-btn" onclick="leaseHardwareNode('Vestify Infinite-8', 300000, 53000)">Lease</button>
            </div>
        </div>

        <div class="section-header-tag"><i class="fa-solid fa-hourglass-half" style="color: #ef4444;"></i> Revenue Distribution Pipeline</div>
        <div class="ledger-row-card" style="margin: 0; background: rgba(239,68,68,0.02); border-color: rgba(239,68,68,0.15);">
            <div>
                <span style="font-size: 14px; font-weight: 700; color: #ef4444;">Block Cycle Dispatch Countdown</span>
                <p style="font-size: 11px; color: #64748b; margin-top: 2px;">Synchronized node array mining logic timer</p>
            </div>
            <span id="countdownTimerClock" style="font-size: 16px; font-weight: 800; color: #ef4444; letter-spacing: 1px;">24:00:00</span>
        </div>
    </div>

    <div id="pageHistory" class="app-view-enclosure">
        <div class="section-header-tag" style="margin-left: 0; padding-left: 0;"><i class="fa-solid fa-receipt"></i> Core Capital Ledger Invoices</div>
        <div id="userHistoryRecordsList" style="font-size: 13px; color: #64748b; line-height: 2;">Awaiting secure ledger pipeline stream compilation loops...</div>
    </div>

    <div class="dock-navigation-deck">
        <div class="dock-action-node active-dock-state" id="nvHome" onclick="routeToView('pageHome', 'nvHome')"><i class="fa-solid fa-grip"></i><span>Console</span></div>
        <div class="dock-action-node" id="nvHistory" onclick="routeToView('pageHistory', 'nvHistory')"><i class="fa-solid fa-chart-pie"></i><span>Ledger Logs</span></div>
    </div>

    <div id="depositModal" class="overlay-system-modal">
        <div class="modal-vault-wrapper" style="max-width: 420px; text-align: left;">
            <div class="modal-dismiss-trigger" onclick="closeModal('depositModal')">&times;</div>
            <h4 style="margin-bottom: 16px; color: #ffffff; text-align: center; font-weight: 800; font-size: 18px;">PKR Native Deposit Terminal</h4>
            
            <label style="font-size: 11px; color: #64748b; font-weight: 700; text-transform: uppercase;">Amount Input</label>
            <input type="number" id="pkrDepositInput" class="sys-input-field" placeholder="Enter Amount (PKR)" oninput="updateDepositCalculations()" style="margin-top: 6px;">
            
            <div style="background: rgba(16, 185, 129, 0.04); padding: 14px; border-radius: 16px; margin-top: 14px; border: 1px solid rgba(16, 185, 129, 0.1);">
                <div style="display: flex; justify-content: space-between; color: #94a3b8; font-size: 12px;"><span>Promo Boost Reward (+8%):</span> <b id="lblBonusPKR" style="color: #10b981;">Rs. 0</b></div>
                <div style="display: flex; justify-content: space-between; color: #ffffff; font-weight: 800; margin-top: 6px; font-size: 13px;"><span>Net Assets Credited:</span> <b id="lblTotalCreditedPKR" style="color: #ef4444;">Rs. 0</b></div>
            </div>

            <div style="display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; margin-top: 16px;">
                <button id="chkEasyPaisa" class="gateway-trigger-cell" onclick="selectDepositGatewayChannel('EasyPaisa')" style="border-color: #10b981; font-size: 11px;"><i class="fa-solid fa-mobile-retro" style="color: #10b981;"></i>EasyPaisa</button>
                <button id="chkJazzCash" class="gateway-trigger-cell" onclick="selectDepositGatewayChannel('JazzCash')" style="font-size: 11px;"><i class="fa-solid fa-money-check-dollar" style="color: #eab308;"></i>JazzCash</button>
                <button id="chkUSDT" class="gateway-trigger-cell" onclick="selectDepositGatewayChannel('USDT')" style="font-size: 11px;"><i class="fa-solid fa-coins" style="color: #26a17b;"></i>Binance</button>
            </div>

            <div style="background: rgba(2, 5, 13, 0.5); padding: 14px; border-radius: 16px; margin-top: 16px; display: flex; justify-content: space-between; align-items: center; border: 1px solid rgba(255,255,255,0.04);">
                <div>
                    <span id="lblAccountTitleType" style="font-size: 10px; color: #64748b; text-transform: uppercase; font-weight: 700; letter-spacing: 0.5px;">EasyPaisa Merchant Endpoint</span>
                    <h4 id="txtMerchantAccountNo" style="font-size: 14px; color: #ffffff; font-weight: 700; margin-top: 2px;">03379827882</h4>
                </div>
                <button class="filter-node-btn" onclick="copyMerchantAccountToken()" style="padding: 6px 14px; font-size: 11px; background: rgba(255,255,255,0.04);">Copy</button>
            </div>
            
            <div style="font-size: 12px; margin-top: 14px; display: flex; justify-content: space-between; font-weight: 600; padding: 0 2px;">
                <span style="color: #94a3b8;">Exact Structural Due:</span> 
                <b id="txtPayableExactAmount" style="color: #ef4444; font-weight: 700;">Rs. 0</b>
            </div>

            <input type="text" id="depositTxIDInput" class="sys-input-field" placeholder="Enter Transfer Tracking ID (TID)" style="margin-top: 14px;">
            <button class="action-execution-btn" onclick="submitPkrDepositToFirebase()" style="width: 100%; margin-top: 16px; padding: 14px; border-radius: 14px;">TRANSMIT METRIC DATA LOG</button>
        </div>
    </div>

    <div id="withdrawModal" class="overlay-system-modal">
        <div class="modal-vault-wrapper">
            <div class="modal-dismiss-trigger" onclick="closeModal('withdrawModal')">&times;</div>
            <h4 style="font-weight: 800; font-size: 18px; margin-bottom: 12px;">Secure Capital Cashout</h4>
            <p style="font-size: 12px; color: #64748b; margin-bottom: 16px;">Specify your target merchant details for settlement.</p>
            <input type="text" id="withdrawTitle" class="sys-input-field" placeholder="Account Title Beneficiary">
            <input type="text" id="withdrawNumber" class="sys-input-field" placeholder="Wallet Account Number (EasyPaisa/JazzCash)">
            <input type="number" id="withdrawAmount" class="sys-input-field" placeholder="Payout Value Asset (PKR)">
            <button class="action-execution-btn" onclick="dispatchWithdrawalToCloud()" style="width: 100%; margin-top: 20px; padding: 14px; background: #3b82f6;">DISPATCH CASHOUT BLOCK</button>
        </div>
    </div>

    <div id="transferModal" class="overlay-system-modal">
        <div class="modal-vault-wrapper">
            <div class="modal-dismiss-trigger" onclick="closeModal('transferModal')">&times;</div>
            <h4 style="color: #eab308; font-weight: 800; font-size: 18px; margin-bottom: 4px;"><i class="fa-solid fa-share-nodes"></i> P2P Asset Transfer</h4>
            <p style="font-size: 12px; color: #64748b; margin-bottom: 16px;">Move capital instantly inside internal node chains.</p>
            <input type="text" id="transferTargetUsername" class="sys-input-field" placeholder="Recipient Unique Username">
            <input type="number" id="transferAmount" class="sys-input-field" placeholder="Transfer Value Asset (PKR)">
            <button class="action-execution-btn" onclick="executeP2PInternalTransfer()" style="width: 100%; margin-top: 20px; padding: 14px; background: linear-gradient(135deg, #eab308, #ca8a04);">ROUTE INTERNAL LINK</button>
        </div>
    </div>

    <script>
        // Production Cloud Configuration Matrices Arrays
        const firebaseConfig = {
            apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g",
            authDomain: "vestify-991f2.firebaseapp.com",
            databaseURL: "https://vestify-991f2-default-rtdb.firebaseio.com",
            projectId: "vestify-991f2",
            storageBucket: "vestify-991f2.firebasestorage.app",
            messagingSenderId: "799007097733",
            appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec"
        };

        // Engine Initiation Sequence Loops
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

        // Realtime Security Auth State Listener Interceptor
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
            document.getElementById('tabLogin').classList.toggle('active-tab-state', mode === 'login');
            document.getElementById('tabRegister').classList.toggle('active-tab-state', mode === 'register');
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
            document.querySelectorAll('#depositModal .gateway-trigger-cell').forEach(b => b.style.borderColor = "rgba(255,255,255,0.05)");
            
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
                    html += `<div class="ledger-row-card"><div><b>Rs. ${d.amount}</b><p>${d.transactionId} (${d.gatewayChannel})</p></div><span style="font-weight:700; font-size:12px; color:#ef4444;">${d.status}</span></div>`;
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
            document.querySelectorAll('.hardware-cluster-grid').forEach(g => g.classList.remove('active-cluster-grid'));
            document.querySelectorAll('.filter-node-btn').forEach(t => t.classList.remove('active-node-state'));
            document.getElementById(gridId).classList.add('active-cluster-grid');
            document.getElementById(tabId).classList.add('active-node-state');
        }

        function routeToView(pId, nId) {
            document.querySelectorAll('.app-view-enclosure').forEach(p=>p.classList.remove('active-view-state'));
            document.querySelectorAll('.dock-action-node').forEach(n=>n.classList.remove('active-dock-state'));
            document.getElementById(pId).classList.add('active-view-state'); document.getElementById(nId).classList.add('active-dock-state');
        }

        function openDepositModal() { document.getElementById('depositModal').classList.add('modal-display-open'); selectDepositGatewayChannel('EasyPaisa'); }
        function openWithdrawModal() { document.getElementById('withdrawModal').classList.add('modal-display-open'); }
        function openTransferModal() { document.getElementById('transferModal').classList.add('modal-display-open'); }
        function closeModal(id) { document.getElementById(id).classList.remove('modal-display-open'); }

        function triggerToastDisplay(msg) {
            const t = document.getElementById('rewardToast'); t.innerText = msg; t.classList.add('toast-display-show');
            setTimeout(() => t.classList.remove('toast-display-show'), 2500);
        }
    </script>
</body>
</html>
