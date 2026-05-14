<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Elite | Quantum Institutional Terminal</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --primary: #3b82f6; --bg: #020617; --card: #0f172a; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: #f8fafc; overflow-x: hidden; -webkit-user-select: none; }
        .glass { background: rgba(15, 23, 42, 0.7); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.05); border-radius: 28px; }
        .page { display: none; animation: slideUp 0.4s cubic-bezier(0.16, 1, 0.3, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        #wheel { width: 280px; height: 280px; transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); border-radius: 50%; border: 6px solid rgba(255,255,255,0.03); box-shadow: 0 0 50px rgba(59, 130, 246, 0.2); }
        .nav-active { color: #3b82f6; position: relative; }
        .nav-active::after { content: ''; position: absolute; bottom: -10px; width: 4px; height: 4px; background: #3b82f6; border-radius: 50%; }
        ::-webkit-scrollbar { width: 0; }
        .shimmer { background: linear-gradient(90deg, transparent, rgba(255,255,255,0.05), transparent); background-size: 200% 100%; animation: shimmer 2s infinite; }
        @keyframes shimmer { 0% { background-position: -200% 0; } 100% { background-position: 200% 0; } }
    </style>
</head>
<body class="min-h-screen pb-32">

    <div id="loader" class="fixed inset-0 z-[99999] bg-[#020617] flex flex-col items-center justify-center">
        <div class="relative">
            <div class="w-20 h-20 border-2 border-blue-500/20 border-t-blue-500 rounded-full animate-spin"></div>
            <i class="fa-solid fa-shield-halved absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 text-blue-500 text-xl"></i>
        </div>
        <p class="mt-6 text-[10px] font-black uppercase tracking-[0.4em] text-blue-500/50">Secure Syncing...</p>
    </div>

    <section id="auth-ui" class="hidden fixed inset-0 z-[8000] bg-[#020617] flex flex-col items-center justify-center p-10 text-center">
        <div onclick="handleLogoTap()" class="w-24 h-24 bg-blue-600 rounded-[2.5rem] flex items-center justify-center mb-10 rotate-3 shadow-[0_20px_60px_rgba(59,130,246,0.4)]">
            <i class="fa-solid fa-shield-halved text-white text-4xl"></i>
        </div>
        <h1 class="text-4xl font-black italic tracking-tighter uppercase">Vestify <span class="text-blue-500">Elite</span></h1>
        <p class="text-[9px] opacity-30 uppercase tracking-[0.5em] mt-2 mb-20">Quantum Asset Management</p>
        <button onclick="login()" class="w-full max-w-xs bg-white text-black py-5 rounded-3xl font-black uppercase text-[11px] tracking-widest active:scale-95 transition-all">Institutional Access</button>
    </section>

    <main id="app-ui" class="hidden pt-12 px-6">
        
        <div class="flex items-center justify-between mb-10">
            <div class="flex items-center gap-4">
                <div class="w-12 h-12 glass overflow-hidden border border-white/10">
                    <img id="user-photo" src="" class="w-full h-full object-cover">
                </div>
                <div>
                    <h2 id="user-name" class="font-black text-sm uppercase italic">Quantum User</h2>
                    <p class="text-[8px] font-bold text-green-500 uppercase tracking-widest">● Server Online</p>
                </div>
            </div>
            <button onclick="logout()" class="w-11 h-11 glass flex items-center justify-center text-red-500 active:scale-90 transition-transform"><i class="fa-solid fa-power-off text-sm"></i></button>
        </div>

        <div id="p-home" class="page active-page">
            <div class="glass p-8 mb-6 bg-gradient-to-br from-blue-600/20 to-transparent border-blue-500/10">
                <p class="text-[9px] font-black opacity-40 uppercase tracking-[0.2em] mb-1">Available Liquidity</p>
                <h3 class="text-5xl font-black italic tracking-tighter mb-6" id="v-bal">₨ 0.00</h3>
                <div class="grid grid-cols-2 gap-3">
                    <button onclick="claimDaily()" class="bg-white text-black py-3.5 rounded-2xl font-black text-[10px] uppercase">Claim Profit</button>
                    <button onclick="changePage('finance')" class="glass bg-white/5 py-3.5 rounded-2xl font-black text-[10px] uppercase">Withdraw</button>
                </div>
            </div>

            <div class="grid grid-cols-3 gap-3 mb-8">
                <div class="glass p-4 text-center">
                    <p class="text-[7px] opacity-40 uppercase font-black mb-1">Salary</p>
                    <p class="text-xs font-black text-blue-400" id="v-salary">₨ 0</p>
                </div>
                <div class="glass p-4 text-center">
                    <p class="text-[7px] opacity-40 uppercase font-black mb-1">Bonus</p>
                    <p class="text-xs font-black text-green-400" id="v-bonus">₨ 0</p>
                </div>
                <div class="glass p-4 text-center">
                    <p class="text-[7px] opacity-40 uppercase font-black mb-1">Team</p>
                    <p class="text-xs font-black" id="v-team">0</p>
                </div>
            </div>

            <h4 class="text-[10px] font-black uppercase opacity-30 mb-4 tracking-widest">Active Mining Clusters</h4>
            <div id="active-clusters" class="space-y-4"></div>
        </div>

        <div id="p-market" class="page">
            <div class="flex justify-between items-end mb-6">
                <h2 class="text-3xl font-black italic uppercase">Market</h2>
                <span class="text-[9px] bg-blue-600 px-3 py-1 rounded-full font-black">20+ NODES</span>
            </div>
            <div id="market-grid" class="grid gap-4 pb-10"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black italic mb-2 uppercase">Lucky Terminal</h2>
            <p class="text-[10px] text-blue-500 uppercase font-bold italic mb-10 tracking-[0.2em]">Quantum Reward Extraction</p>
            <div class="relative inline-block mb-12">
                <div class="absolute -top-6 left-1/2 -translate-x-1/2 w-10 h-12 bg-red-600 z-50 shadow-xl" style="clip-path: polygon(50% 100%, 0 0, 100% 0);"></div>
                <img id="wheel" src="IMG_20260515_012434.jpg" alt="Spin Terminal">
            </div>
            <div class="max-w-xs mx-auto space-y-4 px-4">
                <button id="spin-btn" onclick="executeSpin()" class="w-full bg-blue-600 py-5 rounded-3xl font-black uppercase text-xs italic shadow-[0_15px_40px_rgba(59,130,246,0.3)]">Execute Spin</button>
                <div class="glass p-3 flex justify-between items-center px-6">
                    <p class="text-[9px] font-black uppercase opacity-40">Next Cycle In</p>
                    <p id="spin-timer" class="text-[10px] font-black text-blue-400 italic">READY</p>
                </div>
            </div>
        </div>

        <div id="p-support" class="page">
            <h2 class="text-3xl font-black italic mb-6 uppercase">Help Desk</h2>
            <div id="chat-container" class="glass h-[400px] overflow-y-auto p-5 mb-4 flex flex-col gap-4">
                </div>
            <div class="flex gap-2">
                <input id="chat-input" type="text" placeholder="Direct message to admin..." class="flex-1 bg-white/5 border border-white/5 rounded-2xl p-4 text-xs outline-none focus:border-blue-500/50 transition-all">
                <button onclick="sendUserMsg()" class="w-14 h-14 bg-blue-600 rounded-2xl flex items-center justify-center active:scale-90 transition-all"><i class="fa-solid fa-paper-plane"></i></button>
            </div>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-3xl font-black italic mb-8 uppercase">Banking</h2>
            <div class="glass p-6 space-y-4">
                <div class="flex gap-2">
                    <button onclick="toggleFinance('dep')" id="btn-f-dep" class="flex-1 py-3.5 rounded-xl bg-blue-600 font-black text-[10px] uppercase">Deposit</button>
                    <button onclick="toggleFinance('wd')" id="btn-f-wd" class="flex-1 py-3.5 rounded-xl glass bg-white/5 font-black text-[10px] uppercase">Withdraw</button>
                </div>
                <div id="finance-box" class="space-y-4 pt-4">
                    </div>
            </div>
        </div>

    </main>

    <div id="admin-ui" class="hidden fixed inset-0 z-[10000] bg-[#01040a] p-8 overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-2xl font-black italic text-blue-500">MASTER TERMINAL</h2>
            <button onclick="closeAdmin()" class="text-4xl font-light">&times;</button>
        </div>
        <div class="space-y-6">
            <div class="glass p-6">
                <h4 class="text-[10px] font-black uppercase opacity-40 mb-4">Support Requests</h4>
                <div id="admin-chat-list" class="space-y-3"></div>
            </div>
            <div class="glass p-6">
                <h4 class="text-[10px] font-black uppercase opacity-40 mb-4">Server Actions</h4>
                <div class="grid grid-cols-2 gap-3">
                    <button onclick="toggleMaint()" class="bg-red-600 py-3 rounded-xl font-black text-[9px] uppercase">Maintenance</button>
                    <button onclick="clearHistory()" class="bg-slate-800 py-3 rounded-xl font-black text-[9px] uppercase">Clear Logs</button>
                </div>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-20 glass flex justify-around items-center z-[7000] border-t border-white/5">
        <button onclick="changePage('home')" class="nav-btn flex flex-col items-center p-3 nav-active" data-page="home"><i class="fa-solid fa-home-alt"></i></button>
        <button onclick="changePage('market')" class="nav-btn flex flex-col items-center p-3" data-page="market"><i class="fa-solid fa-grid-2"></i></button>
        <button onclick="changePage('spin')" class="nav-btn flex flex-col items-center p-3" data-page="spin"><i class="fa-solid fa-dharmachakra"></i></button>
        <button onclick="changePage('support')" class="nav-btn flex flex-col items-center p-3" data-page="support"><i class="fa-solid fa-headset"></i></button>
        <button onclick="changePage('finance')" class="nav-btn flex flex-col items-center p-3" data-page="finance"><i class="fa-solid fa-wallet"></i></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();
        const provider = new firebase.auth.GoogleAuthProvider();

        let userObj = null, spinActive = false, rot = 0, tapCount = 0;

        const PLANS = [
            { id: 'p1', name: 'Elite Start', price: 200, daily: 25, days: 10, special: true },
            { id: 'p2', name: 'Alpha Node', price: 500, daily: 45, days: 30, special: false },
            { id: 'p3', name: 'Beta Cluster', price: 1000, daily: 100, days: 30, special: false },
            { id: 'p4', name: 'Gamma Link', price: 2500, daily: 260, days: 30, special: false },
            { id: 'p5', name: 'Delta Core', price: 5000, daily: 550, days: 30, special: false },
            { id: 'p6', name: 'Zeta Mining', price: 10000, daily: 1200, days: 30, special: false },
            { id: 'p7', name: 'Omega Hub', price: 50000, daily: 7000, days: 30, special: true }
        ];

        // Winning mapping from your image segments
        const SPIN_WINS = { 0: 100, 45: 50, 90: 10, 135: 5, 180: 2, 225: 10, 270: 2, 315: 1 };

        window.onload = () => {
            setTimeout(() => {
                document.getElementById('loader').style.opacity = '0';
                setTimeout(() => document.getElementById('loader').style.display = 'none', 500);
                auth.onAuthStateChanged(u => u ? initApp(u) : document.getElementById('auth-ui').classList.remove('hidden'));
            }, 2000);
        };

        async function login() { await auth.signInWithPopup(provider); }
        function logout() { auth.signOut().then(() => location.reload()); }

        function initApp(u) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('user-photo').src = u.photoURL;
            document.getElementById('user-name').innerText = u.displayName;
            syncUserData(u.displayName);
            renderMarket();
            loadMessages();
        }

        function syncUserData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { name: name, balance: 0, salary: 0, bonus: 0, team: 0, lastSpin: 0, activePlans: [] };
                document.getElementById('v-bal').innerText = "₨ " + userObj.balance.toLocaleString();
                document.getElementById('v-salary').innerText = "₨ " + (userObj.salary || 0);
                document.getElementById('v-bonus').innerText = "₨ " + (userObj.bonus || 0);
                document.getElementById('v-team').innerText = (userObj.team || 0);
                updateSpinTimer();
            });
        }

        function renderMarket() {
            let h = '';
            PLANS.forEach(p => {
                h += `
                <div class="glass p-5 flex justify-between items-center ${p.special ? 'border-blue-500/30 bg-blue-600/5' : ''}">
                    <div>
                        <h5 class="font-black uppercase text-xs italic">${p.name}</h5>
                        <p class="text-[9px] text-green-400 font-bold">₨ ${p.daily}/day ● ${p.days} Days</p>
                    </div>
                    <div class="text-right">
                        <p class="text-xs font-black mb-2">₨ ${p.price}</p>
                        <button onclick="buyPlan('${p.id}')" class="bg-white text-black px-4 py-1.5 rounded-lg font-black text-[9px] uppercase">Activate</button>
                    </div>
                </div>`;
            });
            document.getElementById('market-grid').innerHTML = h;
        }

        async function buyPlan(id) {
            const p = PLANS.find(x => x.id === id);
            if(userObj.balance < p.price) return alert("Insufficient Liquidity!");
            await db.collection("users").doc(userObj.name).update({
                balance: userObj.balance - p.price,
                activePlans: firebase.firestore.FieldValue.arrayUnion({
                    id: p.id, name: p.name, daily: p.daily, expiry: Date.now() + (p.days * 86400000)
                })
            });
            alert("Node Activated!");
        }

        // --- Spin System ---
        async function executeSpin() {
            if(spinActive) return;
            const diff = Date.now() - (userObj.lastSpin || 0);
            if(diff < 86400000) return alert("Next cycle in 24 hours!");

            spinActive = true;
            const keys = Object.keys(SPIN_WINS);
            // Weighted Logic (Jackpot 100 has lower weight)
            const weights = [1, 5, 10, 20, 25, 15, 15, 9]; 
            const stopDeg = parseInt(keys[weightedRandom(weights)]);
            
            rot += (3600 + stopDeg - (rot % 360));
            document.getElementById('wheel').style.transform = `rotate(${rot}deg)`;

            setTimeout(async () => {
                spinActive = false;
                const win = SPIN_WINS[stopDeg];
                await db.collection("users").doc(userObj.name).update({
                    balance: userObj.balance + win,
                    lastSpin: Date.now()
                });
                alert(`Extraction Successful: ₨ ${win} credited.`);
            }, 4000);
        }

        function weightedRandom(w) { 
            let sum = w.reduce((a,b) => a+b, 0), r = Math.random()*sum;
            for(let i=0; i<w.length; i++) { if(r < w[i]) return i; r -= w[i]; }
        }

        function updateSpinTimer() {
            const diff = Date.now() - (userObj.lastSpin || 0);
            const el = document.getElementById('spin-timer');
            if(diff < 86400000) {
                const rem = 86400000 - diff;
                const h = Math.floor(rem/3600000), m = Math.floor((rem%3600000)/60000);
                el.innerText = `${h}h ${m}m Remaining`;
            } else { el.innerText = "READY"; }
        }

        // --- Support Desk ---
        async function sendUserMsg() {
            const m = document.getElementById('chat-input').value;
            if(!m) return;
            await db.collection("support").add({ user: userObj.name, msg: m, reply: "", time: Date.now() });
            document.getElementById('chat-input').value = "";
        }

        function loadMessages() {
            db.collection("support").where("user", "==", userObj.name).onSnapshot(snap => {
                let h = '';
                snap.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="bg-blue-600/20 self-end p-4 rounded-2xl rounded-tr-none max-w-[85%] text-[11px]">${d.msg}</div>`;
                    if(d.reply) h += `<div class="bg-white/5 self-start p-4 rounded-2xl rounded-tl-none max-w-[85%] text-[11px] border border-white/5"><span class="text-blue-500 font-bold block mb-1 uppercase text-[8px]">Admin Response</span>${d.reply}</div>`;
                });
                const container = document.getElementById('chat-container');
                container.innerHTML = h;
                container.scrollTop = container.scrollHeight;
            });
        }

        // --- Master Admin Logic ---
        function handleLogoTap() { 
            tapCount++; 
            if(tapCount >= 5) { 
                const k = prompt("Quantum Access Key:"); 
                if(k === "net204") {
                    document.getElementById('admin-ui').classList.remove('hidden');
                    loadAdminRequests();
                }
                tapCount = 0; 
            } 
        }

        function loadAdminRequests() {
            db.collection("support").where("reply", "==", "").onSnapshot(snap => {
                let h = '';
                snap.forEach(doc => {
                    h += `<div class="glass p-4"><p class="text-[9px] font-black text-blue-500 mb-1">${doc.data().user}</p>
                        <p class="text-xs mb-3">${doc.data().msg}</p>
                        <div class="flex gap-2">
                            <input id="ans-${doc.id}" type="text" placeholder="Reply..." class="flex-1 bg-black p-2 rounded text-[10px]">
                            <button onclick="adminReply('${doc.id}')" class="bg-blue-600 px-4 rounded text-[9px] font-black uppercase">Send</button>
                            <button onclick="adminDel('${doc.id}')" class="text-red-500 px-2"><i class="fa-solid fa-trash text-xs"></i></button>
                        </div>
                    </div>`;
                });
                document.getElementById('admin-chat-list').innerHTML = h || '<p class="text-center opacity-20 py-10 text-[10px]">NO PENDING REQUESTS</p>';
            });
        }

        async function adminReply(id) {
            const r = document.getElementById('ans-'+id).value;
            if(!r) return;
            await db.collection("support").doc(id).update({ reply: r });
        }

        async function adminDel(id) { await db.collection("support").doc(id).delete(); }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        // --- Navigation ---
        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-btn').forEach(b => b.classList.toggle('nav-active', b.dataset.page === p));
        }

        function toggleFinance(t) {
            const box = document.getElementById('finance-box');
            const isDep = t === 'dep';
            document.getElementById('btn-f-dep').className = isDep ? "flex-1 py-3.5 rounded-xl bg-blue-600 font-black text-[10px] uppercase" : "flex-1 py-3.5 rounded-xl glass bg-white/5 font-black text-[10px] uppercase text-white/40";
            document.getElementById('btn-f-wd').className = !isDep ? "flex-1 py-3.5 rounded-xl bg-red-600 font-black text-[10px] uppercase" : "flex-1 py-3.5 rounded-xl glass bg-white/5 font-black text-[10px] uppercase text-white/40";
            
            box.innerHTML = isDep ? `
                <input id="f-amt" type="number" placeholder="Amount (Min 200)" class="w-full bg-black p-4 rounded-xl text-xs outline-none">
                <input id="f-tid" type="text" placeholder="Transaction ID (TID)" class="w-full bg-black p-4 rounded-xl text-xs outline-none">
                <button class="w-full bg-blue-600 py-4 rounded-xl font-black uppercase text-[10px]">Submit Deposit Request</button>
            ` : `
                <input id="f-amt" type="number" placeholder="Withdraw Amount" class="w-full bg-black p-4 rounded-xl text-xs outline-none">
                <input id="f-addr" type="text" placeholder="Wallet Address / Number" class="w-full bg-black p-4 rounded-xl text-xs outline-none">
                <button class="w-full bg-red-600 py-4 rounded-xl font-black uppercase text-[10px]">Request Withdrawal</button>
            `;
        }

        // --- Profit Claim Logic ---
        async function claimDaily() {
            if(!userObj.activePlans || userObj.activePlans.length === 0) return alert("No active nodes found!");
            // This is a simple claim logic; ideally, you'd track last claim time in Firestore
            let total = 0;
            userObj.activePlans.forEach(p => { if(Date.now() < p.expiry) total += p.daily; });
            await db.collection("users").doc(userObj.name).update({ balance: userObj.balance + total });
            alert(`Claimed: ₨ ${total} from all active nodes.`);
        }
    </script>
</body>
</html>
