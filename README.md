<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Pro Elite</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&family=JetBrains+Mono:wght@500&display=swap');
        :root { --primary: #2563eb; --bg: #f8fafc; --card: #ffffff; --text: #0f172a; }
        .dark-mode { --bg: #0b0f1a; --card: #161b2c; --text: #f1f5f9; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: var(--bg); color: var(--text); transition: 0.3s; }
        .mono { font-family: 'JetBrains Mono', monospace; }
        .glass { background: var(--card); border: 1px solid rgba(0,0,0,0.04); border-radius: 28px; box-shadow: 0 10px 25px -5px rgba(0,0,0,0.04); }
        .hero-card { background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%); border-radius: 35px; }
        .page { display: none; animation: slideUp 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        #wheel { width: 260px; height: 260px; border-radius: 50%; border: 8px solid #2563eb; transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); }
        .method-card { border: 2px solid transparent; transition: 0.2s; cursor: pointer; }
        .method-card.selected { border-color: #2563eb; background: #eff6ff; }
    </style>
</head>
<body class="min-h-screen pb-32">

    <section id="auth-ui" class="fixed inset-0 z-[5000] bg-white flex flex-col items-center justify-center p-10">
        <div class="w-full max-w-sm text-center">
            <div class="w-20 h-20 bg-blue-600 rounded-3xl flex items-center justify-center mx-auto mb-8 shadow-2xl rotate-6">
                <i class="fa-solid fa-vault text-white text-3xl"></i>
            </div>
            <h1 class="text-4xl font-black tracking-tighter italic">VESTIFY ELITE</h1>
            <p class="text-slate-400 text-[10px] font-bold uppercase tracking-[0.4em] mt-2 mb-12">Institutional Node</p>
            <button onclick="loginWithGoogle()" class="w-full bg-[#0f172a] text-white py-5 rounded-3xl font-bold flex items-center justify-center gap-3 active:scale-95 transition-all">
                Sync with Terminal
            </button>
        </div>
    </section>

    <header class="px-6 py-6 flex justify-between items-center sticky top-0 z-[1000] bg-inherit/80 backdrop-blur-md">
        <span class="font-black text-2xl tracking-tighter text-blue-600 italic">V.</span>
        <div class="flex items-center gap-4">
            <button onclick="toggleTheme()" class="text-slate-400"><i class="fa-solid fa-circle-half-stroke text-xl"></i></button>
            <div id="rank-badge" class="px-3 py-1 bg-blue-50 text-blue-600 text-[9px] font-black rounded-lg uppercase tracking-tighter">Standard Node</div>
        </div>
    </header>

    <main id="app-ui" class="hidden px-6">
        
        <div id="p-home" class="page active-page">
            <div class="hero-card p-8 text-white mb-8 shadow-2xl relative overflow-hidden">
                <div class="absolute -right-10 -top-10 w-32 h-32 bg-blue-500/10 rounded-full blur-3xl"></div>
                <p class="text-[10px] font-bold opacity-50 uppercase tracking-widest mb-1">Total Assets</p>
                <h2 class="text-4xl font-extrabold tracking-tighter mb-10 mono" id="v-bal">₨ 0.00</h2>
                <div class="grid grid-cols-2 gap-4 pt-6 border-t border-white/5">
                    <div><p class="text-[8px] opacity-40 uppercase font-black">Live Profit</p><p id="v-profit" class="text-sm font-bold text-emerald-400 mono">₨ 0.000000</p></div>
                    <div class="text-right"><button onclick="claimBonus()" class="text-[10px] font-black text-blue-300 underline uppercase">Daily +₨ 5</button></div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-10">
                <button onclick="changePage('deposit')" class="glass p-6 flex flex-col items-center gap-3 active:scale-95 transition-all">
                    <div class="w-12 h-12 rounded-2xl bg-blue-50 flex items-center justify-center text-blue-600"><i class="fa-solid fa-arrow-down text-lg"></i></div>
                    <span class="text-[9px] font-black uppercase tracking-widest text-slate-500">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-6 flex flex-col items-center gap-3 active:scale-95 transition-all">
                    <div class="w-12 h-12 rounded-2xl bg-rose-50 flex items-center justify-center text-rose-500"><i class="fa-solid fa-arrow-up-right-from-square text-lg"></i></div>
                    <span class="text-[9px] font-black uppercase tracking-widest text-slate-500">Withdraw</span>
                </button>
            </div>

            <h3 class="text-[10px] font-black uppercase tracking-[0.4em] text-slate-400 mb-6">Investment Packages</h3>
            <div id="plans-list" class="space-y-4"></div>
        </div>

        <div id="p-spin" class="page text-center pt-4">
            <h2 class="text-2xl font-black mb-10">Lucky Terminal</h2>
            <div class="relative inline-block mb-12">
                <div class="absolute -top-4 left-1/2 -translate-x-1/2 w-8 h-10 bg-red-500 z-10" style="clip-path: polygon(50% 100%, 0 0, 100% 0);"></div>
                <img id="wheel" src="https://i.ibb.co/v4S69YV/lucky-wheel.png" onerror="this.src='https://cdn-icons-png.flaticon.com/512/6119/6119533.png'">
            </div>
            <button onclick="handleSpin()" id="spin-btn" class="w-full bg-blue-600 text-white py-5 rounded-[2rem] font-black uppercase text-xs tracking-widest shadow-xl active:scale-95 transition-all">Initiate Protocol</button>
        </div>

        <div id="p-deposit" class="page">
            <h2 class="text-2xl font-black mb-6">Add Capital</h2>
            <div class="glass p-8">
                <div class="grid grid-cols-2 gap-4 mb-8">
                    <div onclick="selectDep('Easypaisa')" class="method-card glass p-4 text-center selected" id="m-Easypaisa">
                        <img src="https://i.ibb.co/V9z0jWw/easypaisa.png" class="h-8 mx-auto mb-2" onerror="this.style.display='none'">
                        <p class="text-[8px] font-black uppercase">Easypaisa</p>
                    </div>
                    <div onclick="selectDep('JazzCash')" class="method-card glass p-4 text-center" id="m-JazzCash">
                        <img src="https://i.ibb.co/68vM839/jazzcash.png" class="h-8 mx-auto mb-2" onerror="this.style.display='none'">
                        <p class="text-[8px] font-black uppercase">JazzCash</p>
                    </div>
                </div>
                <p class="text-[10px] font-bold text-slate-400 mb-2 uppercase">Account: 0300 1234567</p>
                <input type="number" id="dep-amt" placeholder="Amount (₨)" class="w-full p-5 bg-slate-50 rounded-2xl mb-4 font-bold outline-none">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TRX)" class="w-full p-5 bg-slate-50 rounded-2xl mb-6 font-bold outline-none">
                <button onclick="submitRequest('Deposit')" class="w-full bg-blue-600 text-white py-5 rounded-2xl font-black uppercase tracking-widest text-[10px]">Submit Request</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <h2 class="text-2xl font-black mb-6">Request Payout</h2>
            <div class="glass p-8">
                <input type="number" id="wd-amt" placeholder="Payout Amount" class="w-full p-5 bg-slate-50 rounded-2xl mb-4 font-bold outline-none">
                <input type="text" id="wd-acc" placeholder="Account Number" class="w-full p-5 bg-slate-50 rounded-2xl mb-6 font-bold outline-none">
                <button onclick="submitRequest('Withdraw')" class="w-full bg-slate-900 text-white py-5 rounded-2xl font-black uppercase tracking-widest text-[10px]">Confirm Transfer</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-20 glass flex justify-around items-center shadow-2xl z-[3000]">
        <button onclick="changePage('home')" id="n-home" class="text-blue-600 flex flex-col items-center gap-1"><i class="fa-solid fa-house-chimney text-lg"></i><span class="text-[8px] font-bold uppercase">Vault</span></button>
        <button onclick="changePage('spin')" id="n-spin" class="text-slate-400 flex flex-col items-center gap-1"><i class="fa-solid fa-dharmachakra text-lg"></i><span class="text-[8px] font-bold uppercase">Games</span></button>
        <button onclick="logout()" class="text-rose-400 flex flex-col items-center gap-1"><i class="fa-solid fa-power-off text-lg"></i><span class="text-[8px] font-bold uppercase">Exit</span></button>
    </nav>

    <script>
        const firebaseConfig = {
            apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g",
            authDomain: "vestify-991f2.firebaseapp.com",
            projectId: "vestify-991f2",
            storageBucket: "vestify-991f2.firebasestorage.app",
            messagingSenderId: "799007097733",
            appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec"
        };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();
        const provider = new firebase.auth.GoogleAuthProvider();

        let userObj = null;
        let isSpinning = false;
        let selectedMethod = 'Easypaisa';
        const spinTick = new Audio('https://www.soundjay.com/buttons/sounds/button-20.mp3');

        async function loginWithGoogle() {
            const res = await auth.signInWithPopup(provider);
            const name = res.user.displayName;
            localStorage.setItem('v_user', name);
            const ref = db.collection("users").doc(name);
            const doc = await ref.get();
            if(!doc.exists) await ref.set({ name, balance: 0, profit: 0, tierROI: 0, lastBonus: 0, lastSpin: 0 });
            initApp(name);
        }

        function initApp(name) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            syncData(name);
            renderPlans();
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance||0).toLocaleString();
                startTicker();
            });
        }

        // Live Profit Ticker - Har second balance update karega
        function startTicker() {
            if(userObj.tierROI > 0) {
                const interval = setInterval(async () => {
                    const daily = userObj.balance * (userObj.tierROI / 100);
                    const perSec = daily / 86400;
                    userObj.profit = (userObj.profit || 0) + perSec;
                    userObj.balance += perSec;
                    document.getElementById('v-profit').innerText = "₨ " + userObj.profit.toFixed(6);
                    document.getElementById('v-bal').innerText = "₨ " + userObj.balance.toLocaleString();
                }, 1000);
            }
        }

        async function claimBonus() {
            const now = Date.now();
            if (now - userObj.lastBonus < 86400000) return alert("Sweetie, 24h wait karein!");
            await db.collection("users").doc(userObj.name).update({ balance: userObj.balance + 5, lastBonus: now });
            alert("₨ 5 Added to Vault!");
        }

        async function handleSpin() {
            if (isSpinning) return;
            const now = Date.now();
            let isPaid = false;
            if (now - userObj.lastSpin < 86400000) {
                if (userObj.balance < 20) return alert("Low funds for paid spin!");
                if (!confirm("Aaj ka free spin ho chuka. ₨ 20 pay karein?")) return;
                isPaid = true;
            }
            isSpinning = true;
            if (isPaid) await db.collection("users").doc(userObj.name).update({ balance: userObj.balance - 20 });
            const wheel = document.getElementById('wheel');
            const deg = Math.floor(Math.random() * 3600) + 1800;
            wheel.style.transform = `rotate(${deg}deg)`;
            const t = setInterval(() => spinTick.play(), 250);
            setTimeout(async () => {
                clearInterval(t);
                isSpinning = false;
                const wins = [0, 5, 10, 50, 2, 100];
                const win = wins[Math.floor(Math.random() * wins.length)];
                let up = { balance: userObj.balance + win };
                if(!isPaid) up.lastSpin = now;
                await db.collection("users").doc(userObj.name).update(up);
                alert(`You Won ₨ ${win}!`);
                wheel.style.transform = "rotate(0deg)";
            }, 4000);
        }

        function selectDep(m) {
            selectedMethod = m;
            document.querySelectorAll('.method-card').forEach(c => c.classList.remove('selected'));
            document.getElementById('m-'+m).classList.add('selected');
        }

        async function submitRequest(type) {
            const amt = type === 'Deposit' ? document.getElementById('dep-amt').value : document.getElementById('wd-amt').value;
            if(!amt || amt < 100) return alert("Minimum 100 required!");
            alert("Request sent to Admin. Status: Pending");
            changePage('home');
        }

        function renderPlans() {
            const ps = [
                { n: "Nano Node", p: 500, r: 3 }, { n: "Micro Node", p: 1000, r: 3.5 },
                { n: "Mega Node", p: 5000, r: 4.5 }, { n: "Giga Node", p: 10000, r: 5 },
                { n: "Tera Node", p: 50000, r: 6.5 }, { n: "Peta Node", p: 100000, r: 8 },
                { n: "Elite Node", p: 500000, r: 12 }
            ];
            document.getElementById('plans-list').innerHTML = ps.map(p => `
                <div class="glass p-6 flex justify-between items-center">
                    <div><h4 class="font-bold text-xs uppercase">${p.n}</h4><p class="text-[9px] text-blue-600 font-black">${p.r}% DAILY</p></div>
                    <div class="text-right">
                        <p class="text-xs font-black mb-2">₨ ${p.p.toLocaleString()}</p>
                        <button onclick="buyPlan(${p.p}, ${p.r})" class="px-4 py-2 bg-slate-900 text-white text-[8px] font-bold rounded-lg uppercase">Activate</button>
                    </div>
                </div>
            `).join('');
        }

        async function buyPlan(p, r) {
            if(userObj.balance < p) return alert("Paisa kam hai sweetie!");
            await db.collection("users").doc(userObj.name).update({ balance: userObj.balance - p, tierROI: r, profit: 0 });
            alert("Protocol Activated! Your profit is now live.");
        }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.querySelectorAll('nav button').forEach(b => b.classList.replace('text-blue-600', 'text-slate-400'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.getElementById('n-'+p)?.classList.replace('text-slate-400', 'text-blue-600');
        }

        function toggleTheme() { document.body.classList.toggle('dark-mode'); }
        function logout() { localStorage.clear(); location.reload(); }
        window.onload = () => { if(localStorage.getItem('v_user')) initApp(localStorage.getItem('v_user')); };
    </script>
</body>
</html>
