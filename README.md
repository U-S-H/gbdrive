<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Elite Terminal</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: #010409; color: white; margin: 0; padding: 0; overflow-x: hidden; }
        .glass { background: rgba(22, 27, 34, 0.8); backdrop-filter: blur(12px); border: 1px solid rgba(255,255,255,0.05); border-radius: 24px; }
        .page { display: none; padding: 20px; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        #login-screen { position: fixed; inset: 0; display: flex; flex-direction: column; align-items: center; justify-content: center; background: #010409; z-index: 10000; }
        #wheel { width: 280px; transition: transform 5s cubic-bezier(0.15, 0, 0.15, 1); border-radius: 50%; border: 5px solid rgba(255,255,255,0.1); }
        .nav-bar { position: fixed; bottom: 20px; left: 20px; right: 20px; height: 75px; display: flex; justify-content: space-around; align-items: center; z-index: 5000; border-top: 1px solid rgba(255,255,255,0.05); }
        .admin-btn { background: linear-gradient(90deg, #3b82f6, #1d4ed8); padding: 5px 12px; border-radius: 8px; font-size: 9px; font-weight: 800; text-transform: uppercase; }
    </style>
</head>
<body>

    <div id="login-screen">
        <h1 class="text-4xl font-extrabold italic mb-12 tracking-tighter">VESTIFY <span class="text-blue-500">ELITE</span></h1>
        <button onclick="login()" class="w-72 h-18 bg-white text-black font-black rounded-3xl uppercase tracking-widest shadow-2xl transition-transform active:scale-95">Connect Terminal</button>
        <p class="mt-6 text-[10px] opacity-30 uppercase tracking-widest">Institutional Grade Security</p>
    </div>

    <div id="admin-ui" class="hidden fixed inset-0 z-[11000] bg-[#010409] overflow-y-auto p-6">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-2xl font-black italic text-red-500">MASTER <span class="text-white">CONSOLE</span></h2>
            <button onclick="closeAdmin()" class="text-4xl">&times;</button>
        </div>
        <div class="space-y-6">
            <div class="glass p-6"><h4 class="text-[10px] font-black opacity-40 uppercase mb-4">Pending Transactions</h4><div id="adm-tx-list"></div></div>
            <div class="glass p-6"><h4 class="text-[10px] font-black opacity-40 uppercase mb-4">User Database</h4><div id="adm-user-list"></div></div>
        </div>
    </div>

    <main id="app-ui" class="hidden min-h-screen">
        <header class="p-6 flex justify-between items-center">
            <div class="flex items-center gap-4">
                <img id="user-photo" onclick="triggerAdmin()" class="w-12 h-12 rounded-2xl border-2 border-white/5 cursor-pointer">
                <div>
                    <div class="flex items-center gap-2">
                        <p id="user-name" class="text-xs font-black uppercase tracking-tight">Accessing...</p>
                        <span id="owner-tag" class="admin-btn hidden">Owner</span>
                    </div>
                    <p class="text-[8px] text-blue-500 font-bold uppercase tracking-widest">Quantum Node Live</p>
                </div>
            </div>
            <button onclick="showPage('menu')" class="w-12 h-12 glass flex items-center justify-center text-blue-500"><i class="fa-solid fa-bars-staggered"></i></button>
        </header>

        <div id="p-home" class="page active-page">
            <div class="glass p-8 mb-6 border-blue-500/20 bg-gradient-to-br from-blue-600/5 to-transparent">
                <p class="text-[10px] opacity-50 uppercase font-black mb-1">Total Assets</p>
                <h2 id="v-bal" class="text-5xl font-black italic mb-8 tracking-tighter">₨ 0</h2>
                <div class="flex gap-3">
                    <button onclick="claimProfit()" class="flex-1 bg-white text-black py-4 rounded-2xl font-black text-[10px] uppercase shadow-lg">Collect Profits</button>
                    <button onclick="showPage('finance')" class="flex-1 glass py-4 rounded-2xl font-black text-[10px] uppercase">Finance</button>
                </div>
            </div>
            <h4 class="text-[10px] font-black opacity-30 uppercase mb-4 tracking-[0.3em]">Mining Nodes</h4>
            <div id="plans-grid" class="space-y-4 pb-28"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-2xl font-black italic uppercase mb-12">Lucky <span class="text-blue-500">Extraction</span></h2>
            <div class="relative inline-block mb-10">
                <div class="absolute -top-6 left-1/2 -translate-x-1/2 w-8 h-10 bg-red-600 z-10 shadow-xl" style="clip-path: polygon(50% 100%, 0 0, 100% 0);"></div>
                <img id="wheel" src="IMG_20260515_012434.jpg" onerror="this.src='https://via.placeholder.com/300?text=Spin+Wheel'">
            </div>
            <button onclick="runSpin()" class="w-full max-w-xs mx-auto bg-blue-600 py-5 rounded-[2rem] font-black uppercase text-xs shadow-2xl italic">Start Spin</button>
        </div>

        <div id="p-finance" class="page">
            <div class="glass p-8 mb-8">
                <h3 class="text-xs font-black mb-6 uppercase tracking-widest text-blue-400">Secure Deposit</h3>
                <p class="text-[10px] font-bold mb-6 opacity-60">Easypaisa Admin: <span class="text-white">03379827882</span></p>
                <div class="space-y-4">
                    <input id="dep-amt" type="number" placeholder="Enter Amount" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/5 outline-none">
                    <input id="dep-tid" type="text" placeholder="Transaction ID (TID)" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/5 outline-none">
                    <button onclick="submitDeposit()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest">Submit Proof</button>
                </div>
            </div>
            <h4 class="text-[10px] font-black opacity-30 uppercase mb-4 tracking-widest">History</h4>
            <div id="tx-history" class="space-y-3 pb-28"></div>
        </div>

        <div id="p-menu" class="page">
            <div class="space-y-4">
                <div onclick="showPage('home')" class="glass p-6 flex items-center gap-5"><i class="fa-solid fa-microchip text-blue-500"></i> <p class="font-bold">Main Dashboard</p></div>
                <div onclick="logout()" class="p-6 flex items-center gap-5 text-red-500 bg-red-500/5 rounded-3xl"><i class="fa-solid fa-power-off"></i> <p class="font-bold">Shutdown Terminal</p></div>
            </div>
        </div>

        <nav class="nav-bar glass">
            <button onclick="showPage('home')" class="p-4 text-xl"><i class="fa-solid fa-layer-group"></i></button>
            <button onclick="showPage('spin')" class="p-4 text-xl"><i class="fa-solid fa-dharmachakra"></i></button>
            <button onclick="showPage('finance')" class="p-4 text-xl"><i class="fa-solid fa-wallet"></i></button>
        </nav>
    </main>

    <script>
        // --- 1. FIREBASE CONFIG ---
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore(), auth = firebase.auth();

        let userData = null, taps = 0, currentRot = 0;

        // --- 2. AUTHENTICATION ---
        auth.onAuthStateChanged(user => {
            if (user) {
                document.getElementById('login-screen').style.display = 'none';
                document.getElementById('app-ui').classList.remove('hidden');
                document.getElementById('user-photo').src = user.photoURL;
                document.getElementById('user-name').innerText = user.displayName;
                if(user.email === "khankhail@gmail.com") document.getElementById('owner-tag').classList.remove('hidden');
                syncData(user.displayName);
                renderMiningNodes();
            } else {
                document.getElementById('login-screen').style.display = 'flex';
                document.getElementById('app-ui').classList.add('hidden');
            }
        });

        function login() { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }
        function logout() { auth.signOut().then(() => location.reload()); }

        // --- 3. DATA SYNC ---
        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userData = doc.data() || { balance: 0, plans: [], lastClaim: Date.now() };
                document.getElementById('v-bal').innerText = "₨ " + (userData.balance || 0).toLocaleString();
            });
            db.collection("txs").where("user", "==", name).orderBy("time", "desc").limit(10).onSnapshot(s => {
                let h = ""; s.forEach(d => {
                    const t = d.data();
                    const color = t.status === "Approved" ? "text-green-500" : "text-yellow-500";
                    h += `<div class="glass p-5 flex justify-between items-center"><p class="text-[9px] font-black uppercase">Deposit</p><div class="text-right"><p class="text-xs font-black">₨ ${t.amount}</p><p class="text-[8px] font-black ${color}">${t.status}</p></div></div>`;
                });
                document.getElementById('tx-history').innerHTML = h || `<p class="text-center opacity-20 text-[9px] py-4">No records</p>`;
            });
        }

        // --- 4. APP LOGIC ---
        function renderMiningNodes() {
            let h = ""; for(let i=1; i<=10; i++) {
                let cost = i * 200;
                h += `<div class="glass p-6 flex justify-between items-center"><div><p class="text-[10px] font-black uppercase italic">Node v.${i}</p><p class="text-[8px] text-green-500 font-bold uppercase">₨ ${Math.floor(cost*0.12)}/Day</p></div><button onclick="buyNode(${cost})" class="bg-white text-black px-5 py-2.5 rounded-xl text-[9px] font-black uppercase">Buy ₨ ${cost}</button></div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
        }

        async function buyNode(c) {
            if(userData.balance < c) return alert("Low Liquidity!");
            await db.collection("users").doc(auth.currentUser.displayName).update({ balance: firebase.firestore.FieldValue.increment(-c) });
            alert("Mining Node Activated!");
        }

        function showPage(p) {
            document.querySelectorAll('.page').forEach(el => el.classList.remove('active-page'));
            document.getElementById('p-' + p).classList.add('active-page');
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amt').value;
            const t = document.getElementById('dep-tid').value;
            if(!a || !t) return alert("Fill details!");
            await db.collection("txs").add({ user: auth.currentUser.displayName, amount: parseInt(a), tid: t, status: "Pending", time: Date.now() });
            alert("Sent to Master Console!");
        }

        function runSpin() {
            currentRot += 1800 + Math.floor(Math.random() * 360);
            document.getElementById('wheel').style.transform = `rotate(${currentRot}deg)`;
            setTimeout(() => alert("Extraction Success! Check Balance."), 5500);
        }

        // --- 5. ADMIN SYSTEM ---
        function triggerAdmin() { taps++; if(taps >= 5) { const k = prompt("Master Key:"); if(k === "net204") loadAdmin(); taps = 0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        
        function loadAdmin() {
            document.getElementById('admin-ui').classList.remove('hidden');
            db.collection("txs").where("status", "==", "Pending").onSnapshot(s => {
                let h = ""; s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="glass p-5 mb-4"><p class="text-[10px] font-black text-blue-400">${d.user}</p><p class="text-xl font-black my-2 text-white">₨ ${d.amount}</p><p class="text-[8px] opacity-40 mb-4">TID: ${d.tid}</p><div class="flex gap-2"><button onclick="approveTx('${doc.id}', '${d.user}', ${d.amount})" class="bg-green-600 flex-1 py-3 rounded-xl font-black text-[9px]">APPROVE</button><button onclick="rejectTx('${doc.id}')" class="bg-red-600 flex-1 py-3 rounded-xl font-black text-[9px]">REJECT</button></div></div>`;
                });
                document.getElementById('adm-tx-list').innerHTML = h || "No pending requests";
            });
        }

        async function approveTx(id, u, a) {
            await db.collection("txs").doc(id).update({ status: "Approved" });
            await db.collection("users").doc(u).set({ balance: firebase.firestore.FieldValue.increment(a) }, { merge: true });
        }
        async function rejectTx(id) { await db.collection("txs").doc(id).update({ status: "Rejected" }); }

    </script>
</body>
</html>
