<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Elite | Quantum Terminal</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --primary: #3b82f6; --bg: #010409; --accent: #1d4ed8; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: #f0f6fc; overflow-x: hidden; -webkit-user-select: none; }
        .glass { background: rgba(22, 27, 34, 0.85); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.05); border-radius: 28px; }
        .page { display: none; animation: slideIn 0.4s cubic-bezier(0.16, 1, 0.3, 1); }
        .active-page { display: block; }
        @keyframes slideIn { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }
        #wheel { width: 290px; height: 290px; border-radius: 50%; transition: transform 5s cubic-bezier(0.15, 0, 0.15, 1); border: 8px solid rgba(255,255,255,0.05); }
        #notif-container { position: fixed; top: 20px; left: 50%; transform: translateX(-50%); z-index: 10000; width: 90%; max-width: 350px; pointer-events: none; }
        .notif { background: rgba(13, 17, 23, 0.95); border: 1px solid rgba(59, 130, 246, 0.3); border-radius: 18px; padding: 12px 18px; margin-bottom: 10px; box-shadow: 0 15px 40px rgba(0,0,0,0.6); animation: notifIn 0.5s ease-out forwards; display: flex; align-items: center; gap: 14px; }
        @keyframes notifIn { from { opacity: 0; transform: translateY(-30px); } to { opacity: 1; transform: translateY(0); } }
        .nav-active { color: #3b82f6; background: rgba(59, 130, 246, 0.1); border-radius: 20px; }
        .admin-badge { background: linear-gradient(90deg, #3b82f6, #8b5cf6); padding: 3px 10px; border-radius: 6px; font-size: 8px; font-weight: 900; text-transform: uppercase; letter-spacing: 1px; }
        ::-webkit-scrollbar { width: 0; }
        .plan-card:active { transform: scale(0.97); }
    </style>
</head>
<body class="pb-28">

    <div id="notif-container"></div>

    <div id="admin-ui" class="hidden fixed inset-0 z-[20000] bg-[#010409] overflow-y-auto p-6">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-2xl font-black italic text-blue-500">VESTIFY <span class="text-white">COMMAND</span></h2>
            <button onclick="closeAdmin()" class="w-12 h-12 glass flex items-center justify-center text-white text-2xl">&times;</button>
        </div>
        <div class="space-y-6">
            <div class="glass p-6 border-blue-500/20">
                <p class="text-[10px] font-black uppercase opacity-40 mb-4 tracking-widest">Pending Requests</p>
                <div id="admin-tx-list" class="space-y-4"></div>
            </div>
            <div class="glass p-6">
                <p class="text-[10px] font-black uppercase opacity-40 mb-4 tracking-widest">Active Investors</p>
                <div id="admin-user-list" class="space-y-4"></div>
            </div>
        </div>
    </div>

    <main id="app-ui" class="hidden pt-8 px-6">
        <header class="flex items-center justify-between mb-8">
            <div class="flex items-center gap-4">
                <div class="relative">
                    <img id="user-photo" onclick="handleAdminTap()" class="w-12 h-12 rounded-2xl border-2 border-white/5 cursor-pointer">
                    <div class="absolute -bottom-1 -right-1 w-4 h-4 bg-green-500 border-4 border-[#010409] rounded-full"></div>
                </div>
                <div>
                    <div class="flex items-center gap-2">
                        <p id="user-name" class="text-xs font-black uppercase tracking-tight">Accessing...</p>
                        <span id="adm-tag" class="admin-badge hidden">Founder</span>
                    </div>
                    <p class="text-[8px] font-bold text-blue-500 uppercase tracking-[0.2em]">Singapore Mainnet</p>
                </div>
            </div>
            <button onclick="changePage('menu')" class="w-12 h-12 glass flex items-center justify-center text-blue-500"><i class="fa-solid fa-bars-staggered"></i></button>
        </header>

        <div id="p-home" class="page active-page">
            <div class="glass p-8 bg-gradient-to-br from-blue-600/10 via-transparent to-transparent mb-6 border-blue-500/20">
                <p class="text-[9px] font-black opacity-40 uppercase mb-2">Available Balance</p>
                <h3 class="text-5xl font-black italic mb-8 tracking-tighter" id="v-bal">₨ 0</h3>
                <div class="flex gap-3">
                    <button onclick="claimProfit()" class="flex-1 bg-white text-black py-4 rounded-2xl font-black text-[10px] uppercase shadow-2xl">Claim Profits</button>
                    <button onclick="changePage('finance')" class="flex-1 glass py-4 rounded-2xl font-black text-[10px] uppercase">Finance</button>
                </div>
            </div>
            
            <div class="grid grid-cols-2 gap-3 mb-8">
                <div class="glass p-4 text-center">
                    <p class="text-[8px] opacity-40 font-black uppercase">Pending</p>
                    <p id="v-pending" class="text-sm font-black text-blue-400">₨ 0</p>
                </div>
                <div class="glass p-4 text-center">
                    <p class="text-[8px] opacity-40 font-black uppercase">Active Nodes</p>
                    <p id="v-active-nodes" class="text-sm font-black text-blue-400">0</p>
                </div>
            </div>

            <h4 class="text-[10px] font-black uppercase opacity-30 mb-5 tracking-[0.3em]">Quantum Investment Nodes</h4>
            <div id="plans-grid" class="space-y-4 pb-12"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-2xl font-black italic uppercase mb-12">Lucky <span class="text-blue-500">Extraction</span></h2>
            <div class="relative inline-block mb-12">
                <div class="absolute -top-8 left-1/2 -translate-x-1/2 w-10 h-12 bg-red-600 z-50 shadow-[0_0_20px_rgba(220,38,38,0.5)]" style="clip-path: polygon(50% 100%, 0 0, 100% 0);"></div>
                <img id="wheel" src="IMG_20260515_012434.jpg" class="shadow-2xl">
            </div>
            <div class="max-w-xs mx-auto space-y-4">
                <button id="spin-btn" onclick="executeSpin()" class="w-full bg-blue-600 py-5 rounded-[2rem] font-black text-xs uppercase shadow-xl italic tracking-widest">Spin Now (1 Free/24h)</button>
                <p id="spin-timer" class="text-[8px] font-black text-blue-400 uppercase tracking-widest"></p>
            </div>
        </div>

        <div id="p-finance" class="page">
            <div class="flex gap-2 mb-8">
                <button onclick="setFin('dep')" id="f-dep" class="flex-1 py-4 rounded-2xl bg-blue-600 font-black text-[11px] uppercase transition-all">Deposit</button>
                <button onclick="setFin('wd')" id="f-wd" class="flex-1 py-4 rounded-2xl glass font-black text-[11px] uppercase opacity-40 transition-all">Withdraw</button>
            </div>
            <div id="fin-body" class="glass p-7 mb-10 border-blue-500/10"></div>
            <h4 class="text-[10px] font-black uppercase opacity-30 mb-5 tracking-widest">Terminal History</h4>
            <div id="history-list" class="space-y-3 pb-12"></div>
        </div>

        <div id="p-menu" class="page">
            <h2 class="text-3xl font-black italic uppercase mb-10">ELITE <span class="text-blue-500">ACCESS</span></h2>
            <div class="space-y-4">
                <div onclick="changePage('home')" class="glass p-7 flex items-center gap-6 active:bg-white/5"><i class="fa-solid fa-microchip text-blue-500"></i> <p class="font-bold">Main Console</p></div>
                <div onclick="logout()" class="p-7 flex items-center gap-6 text-red-500 mt-12 bg-red-500/5 rounded-[28px]"><i class="fa-solid fa-power-off"></i> <p class="font-bold">Shutdown Terminal</p></div>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-20 glass flex justify-around items-center z-[7000] border-t border-white/5">
        <button onclick="changePage('home')" class="nav-btn p-5 transition-all"><i class="fa-solid fa-layer-group text-lg"></i></button>
        <button onclick="changePage('spin')" class="nav-btn p-5 transition-all"><i class="fa-solid fa-dharmachakra text-lg"></i></button>
        <button onclick="changePage('finance')" class="nav-btn p-5 transition-all"><i class="fa-solid fa-wallet text-lg"></i></button>
    </nav>

    <script>
        // --- SECURE CONFIG ---
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore(), auth = firebase.auth(), provider = new firebase.auth.GoogleAuthProvider();

        let userObj = null, tapCount = 0, spinActive = false, currentRot = 0;
        const SPIN_WINS = { 0: 100, 45: 50, 90: 10, 135: 5, 180: 2, 225: 10, 270: 2, 315: 1 };
        const WIN_WEIGHTS = [0.01, 0.04, 0.15, 0.20, 0.20, 0.15, 0.15, 0.10]; 

        // --- AUTH & INIT ---
        auth.onAuthStateChanged(u => {
            if(u) {
                document.getElementById('app-ui').classList.remove('hidden');
                document.getElementById('bottom-nav').classList.remove('hidden');
                document.getElementById('user-photo').src = u.photoURL;
                document.getElementById('user-name').innerText = u.displayName;
                if(u.email === "khankhail@gmail.com") document.getElementById('adm-tag').classList.remove('hidden');
                sync(u.displayName);
                renderPlans();
                setFin('dep');
                startFakeNotifs();
                setInterval(updateLogic, 1000);
            } else {
                document.body.innerHTML += '<div class="fixed inset-0 flex items-center justify-center p-12 bg-[#010409]"><button onclick="login()" class="w-full max-w-xs bg-white text-black h-20 rounded-[2.5rem] font-black uppercase italic tracking-widest shadow-2xl">Connect Terminal</button></div>';
            }
        });

        async function login() { await auth.signInWithPopup(provider); }
        function logout() { auth.signOut().then(() => location.reload()); }

        function sync(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { name, balance: 0, plans: [], lastSpin: 0, lastClaim: Date.now() };
                document.getElementById('v-bal').innerText = "₨ " + userObj.balance.toLocaleString();
                document.getElementById('v-active-nodes').innerText = (userObj.plans || []).length;
            });
            db.collection("txs").where("user", "==", name).orderBy("time", "desc").onSnapshot(s => {
                let h = ""; s.forEach(d => {
                    const t = d.data();
                    const color = t.status === 'Approved' ? 'text-green-500' : (t.status === 'Rejected' ? 'text-red-500' : 'text-yellow-500');
                    h += `<div class="glass p-5 flex justify-between items-center"><p class="text-[10px] font-black uppercase">${t.type}</p><div class="text-right"><p class="text-xs font-black">₨ ${t.amount}</p><p class="text-[8px] font-black ${color}">${t.status}</p></div></div>`;
                });
                document.getElementById('history-list').innerHTML = h || `<p class="text-center text-[9px] opacity-20 py-4">No recent activities</p>`;
            });
        }

        // --- CORE SYSTEMS ---
        function renderPlans() {
            let h = ""; for(let i=1; i<=15; i++) {
                const cost = i * 200, daily = Math.floor(cost * 0.12);
                h += `
                <div class="glass p-6 flex justify-between items-center plan-card transition-all">
                    <div class="flex items-center gap-5">
                        <div class="w-12 h-12 bg-blue-600/10 rounded-2xl flex items-center justify-center text-blue-500"><i class="fa-solid fa-bolt text-sm"></i></div>
                        <div><h5 class="text-[10px] font-black uppercase italic">Cluster Node v.${i}</h5><p class="text-[8px] font-black text-green-500 uppercase">₨ ${daily}/Day ● 30D</p></div>
                    </div>
                    <button onclick="buyPlan(${cost}, ${daily})" class="bg-white text-black px-5 py-2.5 rounded-xl text-[9px] font-black uppercase">Buy ₨ ${cost}</button>
                </div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
        }

        async function buyPlan(c, d) {
            if(userObj.balance < c) return alert("Insufficient Liquidity!");
            await db.collection("users").doc(userObj.name).update({
                balance: firebase.firestore.FieldValue.increment(-c),
                plans: firebase.firestore.FieldValue.arrayUnion({ d, start: Date.now(), expiry: Date.now() + (30 * 86400000) })
            });
            showNotif("System", "Node deployment successful", "fa-check-circle text-green-500");
        }

        function updateLogic() {
            if(!userObj) return;
            let total = 0; const now = Date.now();
            (userObj.plans || []).forEach(p => { if(now < p.expiry) total += (p.d / 24) * ((now - (userObj.lastClaim || p.start)) / 3600000); });
            document.getElementById('v-pending').innerText = "₨ " + Math.max(0, Math.floor(total));
            
            const diff = now - (userObj.lastSpin || 0);
            document.getElementById('spin-timer').innerText = diff < 86400000 ? "LOCKED (SYNCING)" : "EXTRACTION READY";
        }

        async function claimProfit() {
            const now = Date.now(); let total = 0;
            (userObj.plans || []).forEach(p => { if(now < p.expiry) total += (p.d / 24) * ((now - (userObj.lastClaim || p.start)) / 3600000); });
            if(total < 1) return alert("Generation in progress...");
            await db.collection("users").doc(userObj.name).update({ balance: firebase.firestore.FieldValue.increment(total), lastClaim: now });
            showNotif("Profit", `₨ ${Math.floor(total)} added to wallet`, "fa-wallet text-green-500");
        }

        // --- SPIN LOGIC ---
        async function executeSpin() {
            if(spinActive || (Date.now() - (userObj.lastSpin || 0) < 86400000)) return;
            spinActive = true;
            const keys = Object.keys(SPIN_WINS);
            let r = Math.random(), sum = 0, stop = 0;
            for(let i=0; i<WIN_WEIGHTS.length; i++) { sum += WIN_WEIGHTS[i]; if(r <= sum) { stop = parseInt(keys[i]); break; } }
            currentRot += (3600 + stop - (currentRot % 360));
            document.getElementById('wheel').style.transform = `rotate(${currentRot}deg)`;
            setTimeout(async () => {
                spinActive = false;
                const win = SPIN_WINS[stop];
                await db.collection("users").doc(userObj.name).update({ balance: firebase.firestore.FieldValue.increment(win), lastSpin: Date.now() });
                showNotif("Elite Win", `Extracted ₨ ${win}`, "fa-gift text-yellow-500");
            }, 5500);
        }

        // --- FINANCE & ADMIN ---
        function setFin(t) {
            const b = document.getElementById('fin-body');
            document.getElementById('f-dep').className = t==='dep' ? "flex-1 py-4 rounded-2xl bg-blue-600 font-black text-[11px] uppercase" : "flex-1 py-4 rounded-2xl glass font-black text-[11px] uppercase opacity-40";
            document.getElementById('f-wd').className = t==='wd' ? "flex-1 py-4 rounded-2xl bg-red-600 font-black text-[11px] uppercase" : "flex-1 py-4 rounded-2xl glass font-black text-[11px] uppercase opacity-40";
            b.innerHTML = t === 'dep' ? `
                <div class="space-y-5">
                    <p class="text-center text-[10px] font-black text-blue-400">ADMIN WALLET: 03379827882 (Easypaisa)</p>
                    <input id="d-amt" type="number" placeholder="Amount (PKR)" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/5 outline-none">
                    <input id="d-tid" type="text" placeholder="Transaction ID" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/5 outline-none">
                    <button onclick="subTx('Deposit')" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase">Submit to Admin</button>
                </div>` : `
                <div class="space-y-5 text-center">
                    <p class="text-[9px] opacity-40 uppercase font-black">Min Payout: ₨ 500</p>
                    <input id="w-amt" type="number" placeholder="Enter Amount" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/5 outline-none">
                    <button onclick="subTx('Withdraw')" class="w-full bg-red-600 py-5 rounded-2xl font-black text-[10px] uppercase">Request Withdrawal</button>
                </div>`;
        }

        async function subTx(type) {
            const a = document.getElementById(type === 'Deposit' ? 'd-amt' : 'w-amt').value;
            const tid = type === 'Deposit' ? document.getElementById('d-tid').value : 'N/A';
            if(!a) return;
            await db.collection("txs").add({ user: userObj.name, type, amount: parseInt(a), tid, status: "Pending", time: Date.now() });
            alert("Sent! Wait for admin approval.");
        }

        function handleAdminTap() { tapCount++; if(tapCount >= 5) { const k = prompt("Master Key:"); if(k === "net204") loadAdmin(); tapCount = 0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        function loadAdmin() {
            document.getElementById('admin-ui').classList.remove('hidden');
            db.collection("txs").where("status", "==", "Pending").onSnapshot(s => {
                let h = ""; s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="glass p-5 border-white/5"><p class="text-[9px] font-black uppercase text-blue-400">${d.user}</p><p class="text-xl font-black my-2">₨ ${d.amount}</p><p class="text-[8px] opacity-30 mb-4">${d.type} | TID: ${d.tid}</p><div class="flex gap-2"><button onclick="approveTx('${doc.id}', '${d.user}', ${d.amount}, '${d.type}')" class="bg-green-600 flex-1 py-3 rounded-xl font-black text-[9px]">APPROVE</button><button onclick="rejectTx('${doc.id}')" class="bg-red-600 flex-1 py-3 rounded-xl font-black text-[9px]">REJECT</button></div></div>`;
                });
                document.getElementById('admin-tx-list').innerHTML = h || "No pending tasks";
            });
            db.collection("users").onSnapshot(s => {
                let h = ""; s.forEach(doc => {
                    const u = doc.data();
                    h += `<div class="glass p-5 flex justify-between items-center"><p class="text-[10px] font-black">${u.name}</p><p class="text-sm font-black text-green-500">₨ ${u.balance}</p><button onclick="editBal('${u.name}')" class="bg-blue-600 px-4 py-2 rounded-xl text-[8px] font-black">EDIT</button></div>`;
                });
        }

        async function approveTx(id, u, a, t) {
            await db.collection("txs").doc(id).update({ status: "Approved" });
            const val = t === "Deposit" ? a : -a;
            await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(val) });
        }
        async function rejectTx(id) { await db.collection("txs").doc(id).update({ status: "Rejected" }); }
        async function editBal(n) { const b = prompt("Add/Subtract Amount:"); if(b) await db.collection("users").doc(n).update({ balance: firebase.firestore.FieldValue.increment(parseInt(b)) }); }

        function startFakeNotifs() {
            const users = ["Akbar Mir", "Ali", "Zain", "Mehak", "Sara", "Hamza"];
            setInterval(() => {
                const u = users[Math.floor(Math.random()*users.length)];
                const acts = ["just deposited ₨ 2,000", "won ₨ 100 Jackpot!", "withdrew ₨ 1,500", "activated v.5 Node"];
                showNotif(u, acts[Math.floor(Math.random()*acts.length)], "fa-circle-check text-blue-500");
            }, 10000);
        }

        function showNotif(u, m, i) {
            const el = document.createElement('div'); el.className = "notif";
            el.innerHTML = `<i class="fa-solid ${i}"></i><div><p class="text-[10px] font-black">${u}</p><p class="text-[9px] opacity-60">${m}</p></div>`;
            document.getElementById('notif-container').appendChild(el);
            setTimeout(() => { el.style.opacity = '0'; setTimeout(() => el.remove(), 500); }, 4000);
        }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('nav-active'));
        }
    </script>
</body>
</html>
