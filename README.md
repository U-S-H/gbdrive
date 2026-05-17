<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vestify - Crystal Cloud Mining Matrix</title>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>

    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Plus Jakarta Sans', sans-serif; -webkit-tap-highlight-color: transparent; }
        body { background: #060b19; color: #ffffff; min-height: 100vh; padding-bottom: 90px; overflow-x: hidden; }
        
        /* Neon Ambient Glows */
        .bg-glow-1 { position: fixed; width: 300px; height: 300px; background: radial-gradient(circle, rgba(255, 65, 108, 0.15) 0%, rgba(0,0,0,0) 70%); top: -50px; left: -50px; z-index: -1; pointer-events: none; }
        .bg-glow-2 { position: fixed; width: 400px; height: 400px; background: radial-gradient(circle, rgba(56, 189, 248, 0.12) 0%, rgba(0,0,0,0) 70%); bottom: 100px; right: -100px; z-index: -1; pointer-events: none; }

        /* Multi Auth Overlay Screen */
        #authScreen, #secretAdminScreen { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: #060b19; z-index: 99999; display: flex; align-items: center; justify-content: center; padding: 20px; overflow-y: auto; }
        .auth-container, .admin-container { background: rgba(255, 255, 255, 0.03); border: 1px solid rgba(255, 255, 255, 0.08); border-radius: 28px; padding: 30px 24px; width: 100%; max-width: 400px; text-align: center; backdrop-filter: blur(20px); }
        .auth-tabs { display: flex; gap: 10px; margin-bottom: 20px; }
        .auth-tab-btn { flex: 1; padding: 12px; border-radius: 12px; border: none; background: rgba(255,255,255,0.05); color: #94a3b8; font-weight: 700; cursor: pointer; }
        .auth-tab-btn.active { background: linear-gradient(135deg, #ff416c, #ff4b2b); color: white; }

        /* Main Structural Views */
        .vestify-header { padding: 20px 16px; background: rgba(11, 21, 40, 0.6); backdrop-filter: blur(15px); border-bottom: 1px solid rgba(255, 255, 255, 0.05); position: sticky; top: 0; z-index: 100; }
        .brand-row { display: flex; justify-content: space-between; align-items: center; }
        .logo-block { display: flex; align-items: center; gap: 12px; cursor: pointer; }
        .logo-icon { width: 42px; height: 42px; background: linear-gradient(135deg, #38bdf8, #0369a1); border-radius: 12px; display: flex; align-items: center; justify-content: center; box-shadow: 0 0 20px rgba(56, 189, 248, 0.4); font-size: 20px; font-weight: 800; color: white; }
        .logo-text h1 { font-size: 20px; font-weight: 800; letter-spacing: 1.5px; background: linear-gradient(to right, #ffffff, #a5b4fc); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

        /* Stats Console */
        .stats-dashboard { background: rgba(255, 255, 255, 0.02); border: 1px solid rgba(255, 255, 255, 0.06); border-radius: 24px; padding: 24px; display: flex; justify-content: space-between; align-items: center; margin: 20px 16px; position: relative; }
        .live-tag { position: absolute; top: 12px; right: 16px; background: rgba(16, 185, 129, 0.2); color: #10b981; border: 1px solid #10b981; font-size: 10px; padding: 2px 8px; border-radius: 20px; font-weight: 800; }
        .stat-box-left h2 { font-size: 28px; font-weight: 800; color: #ffffff; margin-top: 4px; }
        .stat-box-right { text-align: right; border-left: 1px solid rgba(255, 255, 255, 0.1); padding-left: 20px; }
        .stat-box-right h2 { font-size: 28px; font-weight: 800; color: #38bdf8; }

        .quick-actions { display: grid; grid-template-columns: repeat(2, 1fr); gap: 12px; margin: 0 16px 20px 16px; }
        .action-btn { padding: 14px; border-radius: 16px; border: none; font-size: 14px; font-weight: 700; color: white; cursor: pointer; display: flex; align-items: center; justify-content: center; gap: 8px; transition: transform 0.2s; }
        .btn-deposit { background: linear-gradient(135deg, #10b981, #059669); }
        .btn-withdraw { background: linear-gradient(135deg, #3b82f6, #1d4ed8); }
        .action-btn:active { transform: scale(0.97); }

        /* Cloud Fire Rig System Styles */
        .section-title { font-size: 14px; font-weight: 700; color: #94a3b8; margin: 25px 20px 12px 20px; text-transform: uppercase; letter-spacing: 1px; display: flex; align-items: center; gap: 8px; }
        .plans-grid { display: flex; flex-direction: column; gap: 14px; padding: 0 16px; }
        .plan-card { background: rgba(15, 23, 42, 0.4); border: 1px solid rgba(255, 255, 255, 0.05); border-radius: 22px; padding: 16px; display: flex; align-items: center; gap: 16px; position: relative; overflow: hidden; }
        .plan-rig-icon { width: 70px; height: 70px; background: rgba(255,255,255,0.03); border-radius: 16px; display: flex; align-items: center; justify-content: center; border: 1px solid rgba(255,255,255,0.08); font-size: 32px; color: #38bdf8; position: relative; }
        .plan-rig-icon::after { content: ''; position: absolute; width: 8px; height: 8px; background: #10b981; border-radius: 50%; top: 6px; right: 6px; box-shadow: 0 0 8px #10b981; }
        .plan-info { flex: 1; }
        .plan-info h4 { font-size: 16px; font-weight: 700; color: #ffffff; margin-bottom: 2px; }
        .plan-info p { font-size: 12px; color: #64748b; }
        .plan-info span { color: #10b981; font-weight: 700; }
        .buy-plan-btn { background: linear-gradient(135deg, #ff416c, #ff4b2b); border: none; color: white; padding: 10px 18px; font-weight: 700; font-size: 12px; border-radius: 12px; cursor: pointer; }

        /* Dynamic Pages Components */
        .app-view-page { display: none; padding: 0 16px; }
        .app-view-page.active-page { display: block; }
        .history-card { background: rgba(255,255,255,0.02); border: 1px solid rgba(255,255,255,0.05); padding: 14px; border-radius: 14px; margin-bottom: 10px; display: flex; justify-content: space-between; align-items: center; }
        .status-badge { padding: 4px 8px; border-radius: 6px; font-size: 11px; font-weight: 700; }
        .status-pending { background: rgba(234, 179, 8, 0.15); color: #eab308; }
        .status-success { background: rgba(16, 185, 129, 0.15); color: #10b981; }

        /* Custom Corporate Info Packs Styles */
        .info-card { background: rgba(255,255,255,0.02); border: 1px solid rgba(255,255,255,0.05); padding: 16px; border-radius: 18px; margin-bottom: 12px; }
        .info-card h5 { font-size: 14px; font-weight: 700; color: #38bdf8; margin-bottom: 6px; }
        .info-card p { font-size: 12px; color: #94a3b8; line-height: 1.6; }

        /* Bottom Persistent Navigation Row */
        .bottom-nav { position: fixed; bottom: 0; left: 0; width: 100%; height: 75px; background: rgba(6, 11, 23, 0.9); backdrop-filter: blur(20px); border-top: 1px solid rgba(255, 255, 255, 0.06); display: flex; justify-content: space-around; align-items: center; z-index: 999; padding-bottom: 8px; }
        .nav-item { display: flex; flex-direction: column; align-items: center; gap: 4px; color: #64748b; text-decoration: none; font-size: 11px; font-weight: 600; cursor: pointer; }
        .nav-item.active { color: #38bdf8; }

        /* Modals Frame Structural System */
        .premium-modal { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(3, 7, 18, 0.83); backdrop-filter: blur(10px); z-index: 10000; display: flex; align-items: center; justify-content: center; opacity: 0; pointer-events: none; transition: opacity 0.3s; padding: 20px; overflow-y: auto; }
        .premium-modal.modal-open { opacity: 1; pointer-events: auto; }
        .modal-content { background: linear-gradient(135deg, #0f172a 0%, #0b1324 100%); border: 1px solid rgba(255, 255, 255, 0.1); width: 100%; max-width: 380px; border-radius: 28px; padding: 24px; text-align: center; position: relative; }
        .close-modal-btn { position: absolute; top: 16px; right: 16px; width: 32px; height: 32px; background: rgba(255, 255, 255, 0.05); border: 1px solid rgba(255, 255, 255, 0.1); color: #94a3b8; border-radius: 50%; display: flex; align-items: center; justify-content: center; cursor: pointer; }
        .modal-input { width: 100%; padding: 14px; border-radius: 12px; border: 1px solid rgba(255,255,255,0.1); background: rgba(0,0,0,0.2); color: white; font-size: 14px; margin-top: 12px; outline: none; }
        .gateway-item { background: rgba(255, 255, 255, 0.03); border: 1px solid rgba(255, 255, 255, 0.08); border-radius: 14px; padding: 12px; display: flex; align-items: center; justify-content: space-between; cursor: pointer; margin-top: 8px; }
        .gateway-item.selected-gw { border-color: #10b981; background: rgba(16,185,129,0.05); }

        /* Global Toast Alert Display Box */
        .reward-toast { position: fixed; top: 30px; left: 50%; transform: translateX(-50%) translateY(-100px); background: linear-gradient(135deg, #10b981 0%, #059669 100%); border: 1px solid rgba(255,255,255,0.2); padding: 14px 24px; border-radius: 20px; box-shadow: 0 10px 30px rgba(16, 185, 129, 0.3); display: flex; align-items: center; gap: 10px; z-index: 11000; font-weight: 700; font-size: 14px; transition: transform 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275); pointer-events: none; }
        .reward-toast.toast-show { transform: translateX(-50%) translateY(0); }
        
        /* Admin System Layout Logs Table */
        .admin-log-sec { text-align: left; margin-top: 20px; background: rgba(0,0,0,0.2); padding: 12px; border-radius: 12px; max-height: 200px; overflow-y: auto; font-size: 11px; }
    </style>
</head>
<body>

    <div class="bg-glow-1"></div>
    <div class="bg-glow-2"></div>

    <div id="rewardToast" class="reward-toast">
        <i class="fa-solid fa-circle-check"></i><span id="toastMsg">Alert Tracker Notification</span>
    </div>

    <div id="secretAdminScreen" style="display:none;">
        <div class="admin-container" style="max-width: 600px;">
            <div class="close-modal-btn" onclick="closeAdminPanel()">&times;</div>
            <h2 style="color:#38bdf8;"><i class="fa-solid fa-user-shield"></i> Secret Core Admin Panel</h2>
            <p style="font-size:12px; color:#64748b; margin-bottom:15px;">Live Cloud Infrastructure Configuration</p>
            
            <div id="adminAuthLock">
                <input type="password" id="adminSecretKeyInput" class="modal-input" placeholder="Enter System Administrative Access Key">
                <button class="action-btn btn-deposit" onclick="verifySystemAdminPass()" style="width:100%; margin-top:12px;">Authorize Terminal</button>
            </div>

            <div id="adminConsoleData" style="display:none;">
                <div class="admin-log-sec">
                    <strong>Live Deposits Queue:</strong>
                    <div id="admDepositsList" style="margin-top:8px;">Loading payloads...</div>
                </div>
                <div class="admin-log-sec">
                    <strong>Live Withdrawals Queue:</strong>
                    <div id="admWithdrawsList" style="margin-top:8px;">Loading payloads...</div>
                </div>
                <div class="admin-log-sec">
                    <strong>Registered Investors Cloud:</strong>
                    <div id="admUsersList" style="margin-top:8px;">Loading users profile metrics...</div>
                </div>
            </div>
        </div>
    </div>

    <div id="authScreen">
        <div class="auth-container">
            <h2>VESTIFY MATRIX</h2>
            <p style="font-size: 12px; color: #64748b; margin-bottom: 20px;">Dual Engine Authentication Node</p>
            <div class="auth-tabs">
                <button id="tabLogin" class="auth-tab-btn active" onclick="switchAuthTab('login')">Login</button>
                <button id="tabRegister" class="auth-tab-btn" onclick="switchAuthTab('register')">Register</button>
            </div>
            <input type="email" id="authEmail" class="modal-input" placeholder="Email Account Registry" style="margin-top:0;">
            <input type="password" id="authPassword" class="modal-input" placeholder="System Security Password">
            <button class="action-btn btn-deposit" onclick="handleAuthAction()" style="width:100%; margin-top:20px; border-radius:12px;">PROCEED ENTRY</button>
            <div style="margin-top: 18px; color: #475569; font-size: 11px; font-weight:700;">OR VIA EXTERNAL NETWORK</div>
            <button class="action-btn" onclick="loginWithGoogle()" style="width:100%; margin-top:14px; border-radius:12px; background:#fff; color:#0f172a; font-weight:800; border:none;">
                <img src="https://upload.wikimedia.org/wikipedia/commons/5/53/Google_%22G%22_Logo.svg" alt="G" style="width:16px; margin-right:8px; display:inline-block; vertical-align:middle;">
                Sign In with Google
            </button>
        </div>
    </div>

    <div class="vestify-header">
        <div class="brand-row">
            <div class="logo-block" onclick="registerAdminLogoTap()">
                <div class="logo-icon"><i class="fa-solid fa-cubes-gradient"></i></div>
                <div class="logo-text">
                    <h1>VESTIFY</h1>
                    <p>✨ Cloud Fire Ecosystem ✨</p>
                </div>
            </div>
            <button class="buy-plan-btn" onclick="triggerSignOut()" style="background:rgba(255,255,255,0.04); color:#f43f5e; border:1px solid rgba(244,63,94,0.15);">Logout</button>
        </div>
    </div>

    <div id="pageHome" class="app-view-page active-page">
        <div class="stats-dashboard">
            <span class="live-tag"><i class="fa-solid fa-bolt"></i> Live Mining</span>
            <div class="stat-box-left">
                <p>Total Assets Equity</p>
                <h2>Rs. <span id="assetDisplay">0.00</span></h2>
            </div>
            <div class="stat-box-right">
                <p>Cloud Engines</p>
                <h2 id="minerDisplay">0</h2>
            </div>
        </div>

        <div class="quick-actions">
            <button class="action-btn btn-deposit" onclick="openDepositModal()"><i class="fa-solid fa-wallet"></i> Deposit</button>
            <button class="action-btn btn-withdraw" onclick="openWithdrawModal()"><i class="fa-solid fa-money-bill-transfer"></i> Payout</button>
        </div>

        <div class="section-title"><i class="fa-solid fa-microchip" style="color:#38bdf8;"></i> Cloud Fire Core Rig Engines</div>
        <div class="plans-grid">
            <div class="plan-card">
                <div class="plan-rig-icon"><i class="fa-solid fa-server"></i></div>
                <div class="plan-info">
                    <h4>Micro Pod Engine</h4>
                    <p>Allocation Cost: <strong>Rs. 200</strong> | 30 Days</p>
                    <p>Daily Revenue Yield: <span>Rs. 15.00</span></p>
                </div>
                <button class="buy-plan-btn" onclick="leaseInvestmentNode('Micro Pod Engine', 200, 15)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-rig-icon" style="color:#eab308;"><i class="fa-solid fa-layer-group"></i></div>
                <div class="plan-info">
                    <h4>Stratum Mega Rig</h4>
                    <p>Allocation Cost: <strong>Rs. 1,000</strong> | 30 Days</p>
                    <p>Daily Revenue Yield: <span>Rs. 80.00</span></p>
                </div>
                <button class="buy-plan-btn" onclick="leaseInvestmentNode('Stratum Mega Rig', 1000, 80)">Lease</button>
            </div>
            <div class="plan-card">
                <div class="plan-rig-icon" style="color:#a855f7;"><i class="fa-solid fa-network-wired"></i></div>
                <div class="plan-info">
                    <h4>Quantum Compute Matrix</h4>
                    <p>Allocation Cost: <strong>Rs. 5,000</strong> | 30 Days</p>
                    <p>Daily Revenue Yield: <span>Rs. 450.00</span></p>
                </div>
                <button class="buy-plan-btn" onclick="leaseInvestmentNode('Quantum Compute Matrix', 5000, 450)">Lease</button>
            </div>
        </div>
    </div>

    <div id="pageHistory" class="app-view-page">
        <div class="section-title"><i class="fa-solid fa-clock-rotate-left"></i> Your Operations Log Ledger</div>
        <div style="margin-bottom:15px; color:#64748b; font-size:12px;">Real-time execution logs for audit compliance:</div>
        <div id="userHistoryListRecords">Loading user transaction tracks...</div>
    </div>

    <div id="pageLegal" class="app-view-page">
        <div class="section-title"><i class="fa-solid fa-building-shield"></i> Corporate Information Profile</div>
        <div class="info-card">
            <h5>1. Operational Ecosystem Protocol</h5>
            <p>Vestify provides decentralized processing hardware simulation blocks. Capital deployed remains securely locked inside structural operational rig contracts for a sequence runtime lifecycle of exactly 30 days.</p>
        </div>
        <div class="info-card">
            <h5>2. Payout Rules & Financial Compliance</h5>
            <p>All withdrawal requests are cleared after automatic validation verification pipelines. Account titles must align precisely with destination addresses to pass verification filters.</p>
        </div>
        <div class="section-title"><i class="fa-solid fa-circle-question"></i> System Frequently Asked Questions</div>
        <div class="info-card">
            <h5>How long does verification audit take?</h5>
            <p>Deposit audits execute within a standard window of 1-6 hours depending upon reference network payload confirmations.</p>
        </div>
        <div class="info-card">
            <h5>Can multiple nodes operate concurrently?</h5>
            <p>Yes, users can spin up an unlimited configuration of concurrent mining pods on the same account profile.</p>
        </div>
    </div>

    <div class="bottom-nav">
        <div class="nav-item active" id="nvHome" onclick="navigateToViewportPage('pageHome', 'nvHome')"><i class="fa-solid fa-house"></i><span>Home</span></div>
        <div class="nav-item" id="nvHistory" onclick="navigateToViewportPage('pageHistory', 'nvHistory')"><i class="fa-solid fa-list-check"></i><span>History</span></div>
        <div class="nav-item" id="nvLegal" onclick="navigateToViewportPage('pageLegal', 'nvLegal')"><i class="fa-solid fa-shield-halved"></i><span>Profile Info</span></div>
    </div>

    <div id="depositModal" class="premium-modal">
        <div class="modal-content">
            <div class="close-modal-btn" onclick="closeDepositModal()">&times;</div>
            <h3 style="color:white; font-size:18px; font-weight:700;">Deposit Gateway Console</h3>
            <p style="color:#94a3b8; font-size:12px; margin-top:4px;">Deploy investment asset liquidity allocation</p>
            
            <div class="gateway-list">
                <div class="gateway-item" id="gwEP" onclick="markDepositGateway('EasyPaisa', 'gwEP')">
                    <span style="background:#10b981; padding:4px 8px; border-radius:6px; font-size:11px; font-weight:800;">EP</span>
                    <span style="color:white; font-size:13px; font-weight:600;">EasyPaisa Account: 03123456789</span>
                </div>
                <div class="gateway-item" id="gwJC" onclick="markDepositGateway('JazzCash', 'gwJC')">
                    <span style="background:#dc2626; padding:4px 8px; border-radius:6px; font-size:11px; font-weight:800;">JC</span>
                    <span style="color:white; font-size:13px; font-weight:600;">JazzCash Account: 03001234567</span>
                </div>
            </div>
            
            <input type="number" id="depositAmount" class="modal-input" placeholder="Enter Cash Amount (PKR)">
            <input type="text" id="depositTrx" class="modal-input" placeholder="Enter Transaction Reference TRX ID">
            
            <div style="text-align:left; margin-top:14px;">
                <label style="font-size:11px; color:#64748b; font-weight:700;">Upload Payment Screenshot Receipt:</label>
                <input type="file" id="depositFileReceipt" class="modal-input" accept="image/*" style="padding:10px;" onchange="convertReceiptToBase64()">
            </div>

            <button class="action-btn btn-deposit" onclick="submitDepositPayloadToCloud()" style="width:100%; margin-top:16px; border-radius:12px;">SUBMIT LEDGER LOG</button>
        </div>
    </div>

    <div id="withdrawModal" class="premium-modal">
        <div class="modal-content">
            <div class="close-modal-btn" onclick="closeWithdrawModal()">&times;</div>
            <h3 style="color:white; font-size:18px; font-weight:700;">Payout Cashout Node</h3>
            <p style="color:#94a3b8; font-size:12px; margin-top:4px;">Extract earned operational dividend assets</p>
            
            <input type="text" id="withdrawTitle" class="modal-input" placeholder="Recipient Account Title Name">
            <input type="text" id="withdrawNumber" class="modal-input" placeholder="Destination Mobile Account No">
            <input type="number" id="withdrawAmount" class="modal-input" placeholder="Extraction Amount (PKR)">
            
            <button class="action-btn btn-withdraw" onclick="submitWithdrawalPayloadToCloud()" style="width:100%; margin-top:18px; border-radius:12px;">DISPATCH PAYOUT REQUEST</button>
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
        const googleProvider = new firebase.auth.GoogleAuthProvider();

        let currentUserId = null;
        let currentAssets = 0.00;
        let totalMiners = 0;
        let currentAuthMode = "login";
        let selectedDepositGateway = "";
        let base64ReceiptStringOutput = "";
        let adminLogoTapSequenceCounter = 0;

        // Session Authentication Synchronization Hook
        auth.onAuthStateChanged((user) => {
            if (user) {
                currentUserId = user.uid;
                document.getElementById('authScreen').style.display = 'none';
                loadUserDataProfileRealtime();
                syncUserTransactionsHistoryLogs();
            } else {
                currentUserId = null;
                document.getElementById('authScreen').style.display = 'flex';
            }
        });

        function switchAuthTab(mode) {
            currentAuthMode = mode;
            document.getElementById('tabLogin').classList.toggle('active', mode === 'login');
            document.getElementById('tabRegister').classList.toggle('active', mode === 'register');
        }

        function handleAuthAction() {
            const email = document.getElementById('authEmail').value.trim();
            const password = document.getElementById('authPassword').value;
            if (!email || !password) { showRewardToast("All mandatory identity fields are required."); return; }

            if (currentAuthMode === "register") {
                auth.createUserWithEmailAndPassword(email, password)
                    .then((cred) => {
                        return db.collection("users").doc(cred.user.uid).set({
                            balance: 0.00,
                            minersCount: 0,
                            email: email,
                            registeredAt: firebase.firestore.FieldValue.serverTimestamp()
                        });
                    })
                    .then(() => showRewardToast("Account Security Protocol Established!"))
                    .catch(err => showRewardToast(err.message));
            } else {
                auth.signInWithEmailAndPassword(email, password)
                    .then(() => showRewardToast("Welcome back to Vestify Core Terminal!"))
                    .catch(err => showRewardToast(err.message));
            }
        }

        function loginWithGoogle() {
            auth.signInWithPopup(googleProvider)
                .then((result) => {
                    const user = result.user;
                    return db.collection("users").doc(user.uid).get().then((doc) => {
                        if (!doc.exists) {
                            return db.collection("users").doc(user.uid).set({
                                balance: 0.00,
                                minersCount: 0,
                                email: user.email,
                                name: user.displayName || "Premium Investor",
                                registeredAt: firebase.firestore.FieldValue.serverTimestamp()
                            });
                        }
                    });
                })
                .then(() => showRewardToast("Google Network Connected Successfully!"))
                .catch(err => showRewardToast(err.message));
        }

        function triggerSignOut() { auth.signOut().then(() => showRewardToast("Session pipeline disconnected.")); }

        function loadUserDataProfileRealtime() {
            if(!currentUserId) return;
            db.collection("users").doc(currentUserId).onSnapshot((doc) => {
                if (doc.exists) {
                    const data = doc.data();
                    currentAssets = data.balance || 0.00;
                    totalMiners = data.minersCount || 0;
                    document.getElementById('assetDisplay').innerText = currentAssets.toLocaleString('en-US', {minimumFractionDigits: 2});
                    document.getElementById('minerDisplay').innerText = totalMiners;
                }
            });
        }

        // Viewport Page Navigation Router
        function navigateToViewportPage(pageId, navId) {
            document.querySelectorAll('.app-view-page').forEach(p => p.classList.remove('active-page'));
            document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
            document.getElementById(pageId).classList.add('active-page');
            document.getElementById(navId).classList.add('active');
        }

        // Base64 Binary Stream Conversion Engine
        function convertReceiptToBase64() {
            const targetFile = document.getElementById('depositFileReceipt').files[0];
            const fileReaderHandle = new FileReader();
            fileReaderHandle.onloadend = function() {
                base64ReceiptStringOutput = fileReaderHandle.result;
                showRewardToast("Receipt Image Buffer Loaded! Base64 structural format ready.");
            }
            if(targetFile) { fileReaderHandle.readAsDataURL(targetFile); }
        }

        function markDepositGateway(gw, elemId) {
            selectedDepositGateway = gw;
            document.querySelectorAll('.gateway-item').forEach(i => i.classList.remove('selected-gw'));
            document.getElementById(elemId).classList.add('selected-gw');
            showRewardToast(`Verification Node Set: ${gw}`);
        }

        function submitDepositPayloadToCloud() {
            const amt = parseFloat(document.getElementById('depositAmount').value);
            const trx = document.getElementById('depositTrx').value.trim();
            if (!selectedDepositGateway || isNaN(amt) || amt <= 0 || !trx) { showRewardToast("Please fully fill all data nodes before submission."); return; }

            db.collection("deposits").add({
                userId: currentUserId,
                amount: amt,
                gateway: selectedDepositGateway,
                transactionId: trx,
                receiptBase64: base64ReceiptStringOutput || "No Document Image Uploaded",
                status: "Pending Admin Audit",
                timestamp: firebase.firestore.FieldValue.serverTimestamp()
            }).then(() => {
                closeDepositModal();
                showRewardToast("Audit request logged! Balance sync will execute upon authorization.");
                document.getElementById('depositAmount').value = "";
                document.getElementById('depositTrx').value = "";
                document.getElementById('depositFileReceipt').value = "";
                base64ReceiptStringOutput = "";
            });
        }

        function submitWithdrawalPayloadToCloud() {
            const title = document.getElementById('withdrawTitle').value.trim();
            const num = document.getElementById('withdrawNumber').value.trim();
            const amt = parseFloat(document.getElementById('withdrawAmount').value);

            if (!title || !num || isNaN(amt) || amt <= 0) { showRewardToast("All accounting parameters are mandatory."); return; }
            if (amt > currentAssets) { showRewardToast("Error: Capital liquidity bounds exceeded."); return; }

            db.collection("users").doc(currentUserId).update({ balance: currentAssets - amt }).then(() => {
                return db.collection("withdrawals").add({
                    userId: currentUserId,
                    accountTitle: title,
                    accountNumber: num,
                    amountRequested: amt,
                    status: "Awaiting Dispatch Allocation",
                    timestamp: firebase.firestore.FieldValue.serverTimestamp()
                });
            }).then(() => {
                closeWithdrawalModal();
                showRewardToast("Payout logged! Allocation pipeline running.");
                document.getElementById('withdrawTitle').value = "";
                document.getElementById('withdrawNumber').value = "";
                document.getElementById('withdrawAmount').value = "";
            });
        }

        function leaseInvestmentNode(planName, price, dailyYield) {
            if (currentAssets < price) { showRewardToast("Insufficient capital equity for this processing array."); return; }
            db.collection("users").doc(currentUserId).update({
                balance: currentAssets - price,
                minersCount: totalMiners + 1
            }).then(() => {
                return db.collection("activePlans").add({
                    userId: currentUserId,
                    planName: planName,
                    costPrice: price,
                    dailyYield: dailyYield,
                    status: "Active Compounding",
                    activatedAt: firebase.firestore.FieldValue.serverTimestamp()
                });
            }).then(() => {
                showRewardToast(`Rig Active: ${planName} processing network array online.`);
            }).catch(err => showRewardToast(err.message));
        }

        function syncUserTransactionsHistoryLogs() {
            if(!currentUserId) return;
            db.collection("deposits").where("userId", "==", currentUserId).onSnapshot((snap) => {
                let htmlOut = "";
                if(snap.empty) { htmlOut = `<p style="font-size:12px; color:#64748b;">No transactional sequence recorded.</p>`; }
                snap.forEach((doc) => {
                    const data = doc.data();
                    htmlOut += `
                        <div class="history-card">
                            <div>
                                <strong style="font-size:13px; color:#10b981;">+ Rs. ${data.amount}</strong>
                                <p style="font-size:10px; color:#64748b;">TRX: ${data.transactionId} (${data.gateway})</p>
                            </div>
                            <span class="status-badge status-pending">${data.status}</span>
                        </div>
                    `;
                });
                document.getElementById('userHistoryListRecords').innerHTML = htmlOut;
            });
        }

        // SECRET INTERFACE COMBINATORIAL TAP LOGIC TRIGGER
        function registerAdminLogoTap() {
            adminLogoTapSequenceCounter++;
            if(adminLogoTapSequenceCounter >= 4) {
                adminLogoTapSequenceCounter = 0;
                document.getElementById('secretAdminScreen').style.display = 'flex';
                showRewardToast("System Entry Terminal Connection Requested.");
            }
        }

        function verifySystemAdminPass() {
            const userEnteredKey = document.getElementById('adminSecretKeyInput').value;
            if(userEnteredKey === "net204") {
                document.getElementById('adminAuthLock').style.display = 'none';
                document.getElementById('adminConsoleData').style.display = 'block';
                showRewardToast("System Core Terminal Decrypted. Access Granted.");
                bootstrapSystemAdministrativeLiveViews();
            } else {
                showRewardToast("Access Denied: Faulty Authentication Key Certificate Sequence.");
            }
        }

        function bootstrapSystemAdministrativeLiveViews() {
            // Fetch live operational state parameters for admin review console
            db.collection("deposits").orderBy("timestamp", "desc").limit(10).onSnapshot(snap => {
                let out = "";
                snap.forEach(doc => {
                    const d = doc.data();
                    out += `<p style='border-bottom:1px solid #1e293b; padding:6px 0;'>User: ${d.userId}<br>Amt: <b>Rs. ${d.amount}</b> | TRX: ${d.transactionId}<br>Base64 Output Payload Buffer: <span style='color:#eab308; word-break:break-all;'>${d.receiptBase64 ? d.receiptBase64.substring(0,60) : 'None'}...</span></p>`;
                });
                document.getElementById('admDepositsList').innerHTML = out || "No active deposits queue.";
            });

            db.collection("withdrawals").orderBy("timestamp", "desc").limit(10).onSnapshot(snap => {
                let out = "";
                snap.forEach(doc => {
                    const w = doc.data();
                    out += `<p style='border-bottom:1px solid #1e293b; padding:6px 0;'>User: ${w.userId}<br>Amt: <b>Rs. ${w.amountRequested}</b><br>Target Channel: ${w.accountNumber} (${w.accountTitle})</p>`;
                });
                document.getElementById('admWithdrawsList').innerHTML = out || "No active withdrawals payload queue.";
            });

            db.collection("users").limit(15).onSnapshot(snap => {
                let out = "";
                snap.forEach(doc => {
                    const u = doc.data();
                    out += `<p style='border-bottom:1px solid #1e293b; padding:4px 0;'>ID: ${doc.id}<br>Email Address: ${u.email || 'OAuth Token Appended User'} | Balance Stack: <b>Rs. ${u.balance}</b></p>`;
                });
                document.getElementById('admUsersList').innerHTML = out || "No registered network engines profiles logs found.";
            });
        }

        function closeAdminPanel() { document.getElementById('secretAdminScreen').style.display = 'none'; }
        function openDepositModal() { document.getElementById('depositModal').classList.add('modal-open'); }
        function closeDepositModal() { document.getElementById('depositModal').classList.remove('modal-open'); }
        function openWithdrawModal() { document.getElementById('withdrawModal').classList.add('modal-open'); }
        function closeWithdrawModal() { document.getElementById('withdrawModal').classList.remove('modal-open'); }
        function closeWithdrawalModal() { closeWithdrawModal(); }

        function showRewardToast(message) {
            const toast = document.getElementById('rewardToast');
            document.getElementById('toastMsg').innerText = message;
            toast.classList.add('toast-show');
            setTimeout(() => { toast.classList.remove('toast-show'); }, 3000);
        }
    </script>
</body>
</html>
