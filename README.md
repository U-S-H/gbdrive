<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vestify - Premium Mining Portal</title>
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
        .bg-glow-1 { position: fixed; width: 300px; height: 300px; background: radial-gradient(circle, rgba(255, 65, 108, 0.15) 0%, rgba(0,0,0,0) 70%); top: -50px; left: -50px; z-index: -1; pointer-events: none; }
        .bg-glow-2 { position: fixed; width: 400px; height: 400px; background: radial-gradient(circle, rgba(56, 189, 248, 0.12) 0%, rgba(0,0,0,0) 70%); bottom: 100px; right: -100px; z-index: -1; pointer-events: none; }

        /* Auth Screen Styling */
        #authScreen {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: #060b19; z-index: 99999; display: flex; align-items: center; justify-content: center; padding: 20px;
        }
        .auth-container {
            background: rgba(255, 255, 255, 0.03); border: 1px solid rgba(255, 255, 255, 0.08);
            border-radius: 28px; padding: 30px 24px; width: 100%; max-width: 360px; text-align: center;
            backdrop-filter: blur(20px); -webkit-backdrop-filter: blur(20px);
        }
        .auth-tabs { display: flex; gap: 10px; margin-bottom: 20px; }
        .auth-tab-btn { flex: 1; padding: 12px; border-radius: 12px; border: none; background: rgba(255,255,255,0.05); color: #94a3b8; font-weight: 700; cursor: pointer; }
        .auth-tab-btn.active { background: linear-gradient(135deg, #ff416c, #ff4b2b); color: white; }

        /* Main App Header */
        .vestify-header {
            padding: 20px 16px; background: rgba(11, 21, 40, 0.6); backdrop-filter: blur(15px);
            -webkit-backdrop-filter: blur(15px); border-bottom: 1px solid rgba(255, 255, 255, 0.05);
            position: sticky; top: 0; z-index: 100;
        }
        .brand-row { display: flex; justify-content: space-between; align-items: center; }
        .logo-block { display: flex; align-items: center; gap: 12px; }
        .logo-icon { width: 42px; height: 42px; background: linear-gradient(135deg, #ff416c, #ff4b2b); border-radius: 12px; display: flex; align-items: center; justify-content: center; box-shadow: 0 0 20px rgba(255, 65, 108, 0.4); font-size: 20px; font-weight: 800; color: white; transform: rotate(-5deg); }
        .logo-text h1 { font-size: 20px; font-weight: 800; letter-spacing: 1.5px; background: linear-gradient(to right, #ffffff, #a5b4fc); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        .logo-text p { font-size: 9px; color: #38bdf8; font-weight: 700; letter-spacing: 1px; text-transform: uppercase; }

        /* Dashboard View */
        .stats-dashboard {
            background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(20px); -webkit-backdrop-filter: blur(20px);
            border: 1px solid rgba(255, 255, 255, 0.08); border-radius: 24px; padding: 24px;
            display: flex; justify-content: space-between; align-items: center; margin: 20px 16px;
        }
        .stat-box-left h2 { font-size: 28px; font-weight: 800; color: #ffffff; }
        .stat-box-right { text-align: right; border-left: 1px solid rgba(255, 255, 255, 0.1); padding-left: 20px; }
        .stat-box-right h2 { font-size: 28px; font-weight: 800; color: #38bdf8; }

        .quick-actions { display: grid; grid-template-columns: repeat(2, 1fr); gap: 12px; margin: 0 16px 20px 16px; }
        .action-btn { padding: 14px; border-radius: 16px; border: none; font-size: 14px; font-weight: 700; color: white; cursor: pointer; display: flex; align-items: center; justify-content: center; gap: 8px; }
        .btn-deposit { background: linear-gradient(135deg, #10b981 0%, #059669 100%); }
        .btn-withdraw { background: linear-gradient(135deg, #3b82f6 0%, #1d4ed8 100%); }

        /* Investment Cards Grid */
        .investment-section-title { font-size: 14px; font-weight: 700; color: #f1f5f9; margin: 0 20px 15px 20px; }
        .plans-grid { display: flex; flex-direction: column; gap: 14px; padding: 0 16px 20px 16px; }
        .plan-card {
            background: rgba(255, 255, 255, 0.02); border: 1px solid rgba(255, 255, 255, 0.06);
            border-radius: 20px; padding: 18px; display: flex; justify-content: space-between; align-items: center;
        }
        .plan-info h4 { font-size: 16px; color: #ffffff; font-weight: 700; margin-bottom: 4px; }
        .plan-info p { font-size: 12px; color: #94a3b8; }
        .plan-info span { color: #10b981; font-weight: 700; }
        .buy-plan-btn { background: linear-gradient(135deg, #ff416c, #ff4b2b); border: none; color: white; padding: 10px 16px; font-weight: 700; font-size: 12px; border-radius: 12px; cursor: pointer; }

        /* Bottom Persistent Navigation */
        .bottom-nav { position: fixed; bottom: 0; left: 0; width: 100%; height: 75px; background: rgba(6, 11, 23, 0.9); backdrop-filter: blur(20px); border-top: 1px solid rgba(255, 255, 255, 0.06); display: flex; justify-content: space-around; align-items: center; z-index: 999; padding-bottom: 8px; }
        .nav-item { display: flex; flex-direction: column; align-items: center; gap: 4px; color: #64748b; text-decoration: none; font-size: 11px; font-weight: 600; cursor: pointer; }
        .nav-item.active { color: #38bdf8; }

        /* Modals and Form Controls */
        .premium-modal { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(3, 7, 18, 0.8); backdrop-filter: blur(10px); z-index: 10000; display: flex; align-items: center; justify-content: center; opacity: 0; pointer-events: none; transition: opacity 0.3s; padding: 20px; }
        .premium-modal.modal-open { opacity: 1; pointer-events: auto; }
        .modal-content { background: linear-gradient(135deg, #0f172a 0%, #0b1324 100%); border: 1px solid rgba(255, 255, 255, 0.1); width: 100%; max-width: 360px; border-radius: 28px; padding: 24px; text-align: center; position: relative; }
        .close-modal-btn { position: absolute; top: 16px; right: 16px; width: 32px; height: 32px; background: rgba(255, 255, 255, 0.05); border: 1px solid rgba(255, 255, 255, 0.1); color: #94a3b8; border-radius: 50%; display: flex; align-items: center; justify-content: center; cursor: pointer; }
        .modal-input { width: 100%; padding: 14px; border-radius: 12px; border: 1px solid rgba(255,255,255,0.1); background: rgba(0,0,0,0.2); color: white; font-size: 14px; margin-top: 12px; outline: none; }
        .modal-input:focus { border-color: #38bdf8; }
        
        .gateway-list { display: flex; flex-direction: column; gap: 10px; margin-top: 15px; text-align: left; }
        .gateway-item { background: rgba(255, 255, 255, 0.03); border: 1px solid rgba(255, 255, 255, 0.08); border-radius: 14px; padding: 12px; display: flex; align-items: center; justify-content: space-between; cursor: pointer; }
        .gw-easypaisa { background: #10b981; color: white; padding: 6px; border-radius: 6px; font-weight: bold; }
        .gw-jazzcash { background: #dc2626; color: white; padding: 6px; border-radius: 6px; font-weight: bold; }

        /* Toast Popup Alerts */
        .reward-toast { position: fixed; top: 30px; left: 50%; transform: translateX(-50%) translateY(-100px); background: linear-gradient(135deg, #10b981 0%, #059669 100%); border: 1px solid rgba(255,255,255,0.2); padding: 14px 24px; border-radius: 20px; box-shadow: 0 10px 30px rgba(16, 185, 129, 0.3); display: flex; align-items: center; gap: 10px; z-index: 11000; font-weight: 700; font-size: 14px; transition: transform 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275); pointer-events: none; }
        .reward-toast.toast-show { transform: translateX(-50%) translateY(0); }
    </style>
</head>
<body>

    <div class="bg-glow-1"></div>
    <div class="bg-glow-2"></div>

    <div id="rewardToast" class="reward-toast">
        <i class="fa-solid fa-circle-check"></i>
        <span id="toastMsg">Action Alert!</span>
    </div>

    <div id="authScreen">
        <div class="auth-container">
            <h2 style="margin-bottom: 5px; background: linear-gradient(to right, #ffffff, #38bdf8); -webkit-background-clip: text; -webkit-text-fill-color: transparent;">VESTIFY PORTAL</h2>
            <p style="font-size: 12px; color: #64748b; margin-bottom: 20px;">Secure Real Investment Portal</p>
            
            <div class="auth-tabs">
                <button id="tabLogin" class="auth-tab-btn active" onclick="switchAuthTab('login')">Login</button>
                <button id="tabRegister" class="auth-tab-btn" onclick="switchAuthTab('register')">Register</button>
            </div>

            <input type="email" id="authEmail" class="modal-input" placeholder="Your Email Address" style="margin-top:0;">
            <input type="password" id="authPassword" class="modal-input" placeholder="Secure Password">
            
            <button class="action-btn btn-deposit" id="authSubmitBtn" onclick="handleAuthAction()" style="width:100%; margin-top:20px; border-radius:12px;">PROCEED SECURELY</button>
        </div>
    </div>

    <div class="vestify-header">
        <div class="brand-row">
            <div class="logo-block">
                <div class="logo-icon">V</div>
                <div class="logo-text">
                    <h1>VESTIFY</h1>
                    <p>✨ Real Revenue Protocol ✨</p>
                </div>
            </div>
            <button class="buy-plan-btn" onclick="triggerSignOut()" style="background: rgba(255,255,255,0.05); color: #f43f5e; border: 1px solid rgba(244,63,94,0.2);">Logout</button>
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

    <div class="investment-section-title"><i class="fa-solid fa-layer-group" style="color:#ff416c; margin-right:6px;"></i> Tiered Node Premium Packages</div>
    <div class="plans-grid">
        <div class="plan-card">
            <div class="plan-info">
                <h4>Starter Miner Plan</h4>
                <p>Cost: <strong>Rs. 200</strong> | Loop: 30 Days</p>
                <p>Daily Profit: <span>Rs. 15.00</span></p>
            </div>
            <button class="buy-plan-btn" onclick="leaseInvestmentNode('Starter Plan', 200, 15)">Lease</button>
        </div>

        <div class="plan-card">
            <div class="plan-info">
                <h4>Bronze Yield Node</h4>
                <p>Cost: <strong>Rs. 1,000</strong> | Loop: 30 Days</p>
                <p>Daily Profit: <span>Rs. 80.00</span></p>
            </div>
            <button class="buy-plan-btn" onclick="leaseInvestmentNode('Bronze Plan', 1000, 80)">Lease</button>
        </div>

        <div class="plan-card">
            <div class="plan-info">
                <h4>Silver Master Engine</h4>
                <p>Cost: <strong>Rs. 5,000</strong> | Loop: 30 Days</p>
                <p>Daily Profit: <span>Rs. 450.00</span></p>
            </div>
            <button class="buy-plan-btn" onclick="leaseInvestmentNode('Silver Plan', 5000, 450)">Lease</button>
        </div>
    </div>

    <div class="bottom-nav">
        <a class="nav-item active"><i class="fa-solid fa-house"></i><span>Home</span></a>
        <a class="nav-item" onclick="openDepositModal()"><i class="fa-solid fa-wallet"></i><span>Deposit</span></a>
        <a class="nav-item" onclick="openWithdrawModal()"><i class="fa-solid fa-money-bill-transfer"></i><span>Withdraw</span></a>
    </div>

    <div id="depositModal" class="premium-modal">
        <div class="modal-content">
            <div class="close-modal-btn" onclick="closeDepositModal()">&times;</div>
            <h3 style="color:white; font-size:18px; font-weight:700; margin-bottom:10px;">Deposit Gateway</h3>
            <p style="color:#94a3b8; font-size:12px;">Send money to <strong>03456789101</strong> then add details:</p>
            
            <div class="gateway-list">
                <div class="gateway-item" onclick="selectGateway('EasyPaisa')">
                    <div class="gateway-info"><span class="gw-easypaisa">EP</span> <span style="color:white; font-size:13px; margin-left:10px;">EasyPaisa Automatic</span></div>
                </div>
                <div class="gateway-item" onclick="selectGateway('JazzCash')">
                    <div class="gateway-info"><span class="gw-jazzcash">JC</span> <span style="color:white; font-size:13px; margin-left:10px;">JazzCash Standard</span></div>
                </div>
            </div>
            
            <input type="number" id="depositAmount" class="modal-input" placeholder="Amount (Min 200 PKR)">
            <input type="text" id="depositTrx" class="modal-input" placeholder="Enter Transaction TRX ID">
            <button class="premium-btn" onclick="submitDeposit()" style="width:100%; padding:14px; border:none; border-radius:12px; margin-top:15px; color:white; font-weight:700;">SUBMIT TO AUDIT</button>
        </div>
    </div>

    <div id="withdrawModal" class="premium-modal">
        <div class="modal-content">
            <div class="close-modal-btn" onclick="closeWithdrawModal()">&times;</div>
            <h3 style="color:white; font-size:18px; font-weight:700; margin-bottom:10px;">Request Payout</h3>
            
            <input type="text" id="withdrawTitle" class="modal-input" placeholder="Account Title Name">
            <input type="text" id="withdrawNumber" class="modal-input" placeholder="EasyPaisa/JazzCash Account No">
            <input type="number" id="withdrawAmount" class="modal-input" placeholder="Withdrawal Amount (PKR)">
            <button class="premium-btn" onclick="submitWithdrawal()" style="width:100%; padding:14px; border:none; border-radius:12px; margin-top:15px; color:white; font-weight:700; background:linear-gradient(135deg, #3b82f6, #1d4ed8);">REQUEST CASH OUT</button>
        </div>
    </div>


    <script>
        // Sweetie, aapka actual configuration block safely embedded hai!
        const firebaseConfig = {
            apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g",
            authDomain: "vestify-991f2.firebaseapp.com",
            databaseURL: "https://vestify-991f2-default-rtdb.firebaseio.com",
            projectId: "vestify-991f2",
            storageBucket: "vestify-991f2.firebasestorage.app",
            messagingSenderId: "799007097733",
            appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec"
        };

        // Initialize Firebase SDK Instances
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();

        // Local Reactivity State Variables
        let currentUserId = null;
        let currentAssets = 0.00;
        let totalMiners = 0;
        let currentAuthMode = "login";
        let selectedDepositGateway = "";

        // Authentication State Listener Hook
        auth.onAuthStateChanged((user) => {
            if (user) {
                currentUserId = user.uid;
                document.getElementById('authScreen').style.display = 'none';
                loadUserDataFromFirebase();
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

            if (!email || !password) { showRewardToast("All credentials fields are required!"); return; }

            if (currentAuthMode === "register") {
                auth.createUserWithEmailAndPassword(email, password)
                    .then((cred) => {
                        return db.collection("users").doc(cred.user.uid).set({
                            balance: 0.00,
                            minersCount: 0,
                            registeredAt: firebase.firestore.FieldValue.serverTimestamp()
                        });
                    })
                    .then(() => showRewardToast("Account Registered Successfully! Account active."))
                    .catch(err => showRewardToast(err.message));
            } else {
                auth.signInWithEmailAndPassword(email, password)
                    .then(() => showRewardToast("Welcome back to Vestify Premium!"))
                    .catch(err => showRewardToast(err.message));
            }
        }

        function triggerSignOut() {
            auth.signOut().then(() => showRewardToast("Session closed successfully."));
        }

        // Live Cloud Synchronization Engines
        function loadUserDataFromFirebase() {
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

        // Tiered Investment Lease Logic
        function leaseInvestmentNode(planName, price, dailyYield) {
            if (currentAssets < price) {
                showRewardToast(`Insufficient balance! This node requires ${price} PKR.`);
                return;
            }

            // Deduct locally and commit node records live to cloud database logs
            db.collection("users").doc(currentUserId).update({
                balance: currentAssets - price,
                minersCount: totalMiners + 1
            }).then(() => {
                return db.collection("activePlans").add({
                    userId: currentUserId,
                    planName: planName,
                    costPrice: price,
                    dailyYield: dailyYield,
                    status: "Active Running",
                    activatedAt: firebase.firestore.FieldValue.serverTimestamp()
                });
            }).then(() => {
                showRewardToast(`${planName} active! Yield cycle tracking locked.`);
            }).catch(err => showRewardToast(err.message));
        }

        // Modal Views Toggle Systems
        function openDepositModal() { document.getElementById('depositModal').classList.add('modal-open'); }
        function closeDepositModal() { document.getElementById('depositModal').classList.remove('modal-open'); }
        function openWithdrawModal() { document.getElementById('withdrawModal').classList.add('modal-open'); }
        function closeWithdrawModal() { document.getElementById('withdrawModal').classList.remove('modal-open'); }

        function selectGateway(gw) {
            selectedDepositGateway = gw;
            showRewardToast(`Gateway target locked: ${gw}`);
        }

        function submitDeposit() {
            const amt = parseFloat(document.getElementById('depositAmount').value);
            const trx = document.getElementById('depositTrx').value.trim();

            if (!selectedDepositGateway) { showRewardToast("Please select a valid payment channel."); return; }
            if (isNaN(amt) || amt < 200) { showRewardToast("Minimum package requirement threshold is 200 PKR."); return; }
            if (!trx) { showRewardToast("Valid Transaction tracking ID code is mandatory."); return; }

            db.collection("deposits").add({
                userId: currentUserId,
                amount: amt,
                gateway: selectedDepositGateway,
                transactionId: trx,
                status: "Pending Admin Verification",
                timestamp: firebase.firestore.FieldValue.serverTimestamp()
            }).then(() => {
                closeDepositModal();
                showRewardToast("Deposit log pushed! Balance credit follows verification.");
                document.getElementById('depositAmount').value = "";
                document.getElementById('depositTrx').value = "";
            });
        }

        function submitWithdrawal() {
            const title = document.getElementById('withdrawTitle').value.trim();
            const num = document.getElementById('withdrawNumber').value.trim();
            const amt = parseFloat(document.getElementById('withdrawAmount').value);

            if (!title || !num || isNaN(amt) || amt <= 0) { showRewardToast("All validation fields are structural requirements."); return; }
            if (amt > currentAssets) { showRewardToast("Limit error: Insufficient financial equity."); return; }

            db.collection("users").doc(currentUserId).update({
                balance: currentAssets - amt
            }).then(() => {
                return db.collection("withdrawals").add({
                    userId: currentUserId,
                    accountTitle: title,
                    accountNumber: num,
                    amountRequested: amt,
                    status: "Awaiting Payout Pipeline",
                    timestamp: firebase.firestore.FieldValue.serverTimestamp()
                });
            }).then(() => {
                closeWithdrawalModal();
                showRewardToast("Withdrawal locked in. Payment processing initiated!");
                document.getElementById('withdrawTitle').value = "";
                document.getElementById('withdrawNumber').value = "";
                document.getElementById('withdrawAmount').value = "";
            });
        }

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
