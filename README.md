<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Pro | Neo-Banking Terminal</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&family=JetBrains+Mono:wght@500&display=swap');
        
        :root { --primary: #2563eb; --bg: #f8fafc; --card: #ffffff; --text: #0f172a; }
        .dark-mode { --bg: #0b0f1a; --card: #161b2c; --text: #f1f5f9; }

        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: var(--bg); color: var(--text); transition: background 0.3s; overflow-x: hidden; }
        .mono { font-family: 'JetBrains Mono', monospace; }

        .glass { background: var(--card); border: 1px solid rgba(0,0,0,0.04); border-radius: 32px; box-shadow: 0 10px 30px -10px rgba(0,0,0,0.05); }
        .hero-card { background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%); border-radius: 40px; }

        /* Wheel Styles */
        #wheel-container { position: relative; width: 280px; height: 280px; margin: 20px auto; }
        #wheel { width: 100%; height: 100%; border-radius: 50%; border: 10px solid #2563eb; transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); box-shadow: 0 20px 40px rgba(37, 99, 235, 0.15); }
        .wheel-pointer { position: absolute; top: -15px; left: 50%; transform: translateX(-50%); width: 30px; height: 35px; background: #ef4444; clip-path: polygon(50% 100%, 0 0, 100% 0); z-index: 50; }

        /* Page Transitions */
        .page { display: none; animation: slideIn 0.4s cubic-bezier(0.16, 1, 0.3, 1); }
        .active-page { display: block; }
        @keyframes slideIn { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

        .method-card { border: 2px solid transparent; transition: all 0.2s; cursor: pointer; }
        .method-card.selected { border-color: #2563eb; background: #eff6ff; }
        
        .no-scrollbar::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="min-h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[5000] bg-white flex flex-col items-center justify-center p-10">
        <div class="w-full max-w-sm text-center">
            <div class="w-24 h-24 bg-blue-600 rounded-[2.5rem] flex items-center justify-center mx-auto mb-8 shadow-2xl shadow-blue-200">
                <i class="fa-solid fa-vault text-white text-4xl"></i>
            </div>
            <h1 class="text-4xl font-black tracking-tighter italic text-slate-900">VESTIFY PRO</h1>
            <p class="text-slate-400 text-[10px] font-bold uppercase tracking-[0.5em] mt-3 mb-16">Secure Asset Terminal</p>
            <button onclick="loginWithGoogle()" class="w-full bg-[#0f172a] text-white py-5 rounded-3xl font-bold flex items-center justify-center gap-3 shadow-xl active:scale-95 transition-all">
                <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/action/google.svg" width="20" class="bg-white rounded-full p-0.5">
                Sync with Terminal
            </button>
        </div>
    </section>

    <header class="px-6 py-6 flex justify-between items-center sticky top-0 z-[1000] bg-inherit/70 backdrop-blur-xl">
        <div class="flex items-center gap-3">
            <div class="w-10 h-10 bg-blue-600 rounded-2xl flex items-center justify-center shadow-lg"><i class="fa-solid fa-v text-white text-sm"></i></div>
            <div>
                <p id="rank-badge" class="text-[10px] font-bold text-blue-600">Standard Node</p>
            </div>
        </div>
        <button onclick="toggleTheme()" class="text-slate-400 w-10 h-10 flex items-center justify-center"><i class="fa-solid fa-circle-half-stroke"></i></button>
    </header>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-36 px-6 pt-4">
        
        <div id="p-home" class="page active-page">
            <div class="hero-card p-8 text-white mb-8 relative overflow-hidden shadow-2xl">
                <div class="relative z-10">
                    <p class="text-[10px] font-bold opacity-50 uppercase tracking-[0.3em]">Total Balance</p>
                    <h2 class="text-5xl font-extrabold tracking-tighter mb-12 mono" id="v-bal">₨ 0.00</h2>
                    <div class="grid grid-cols-2 gap-6 pt-6 border-t border-white/5">
                        <div><p class="text-[8px] opacity-40 uppercase font-black">Live Profit</p><p id="v-profit" class="text-xl font-bold text-emerald-400 mono">₨ 0.0000</p></div>
                        <div class="text-right"><button onclick="claimBonus()" class="text-[10px] font-black text-blue-400 underline uppercase">Claim ₨ 5</button></div>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-10">
                <button onclick="changePage('deposit')" class="glass p-7 flex flex-col items-center gap-4 active:bg-slate-50 transition-all">
                    <div class="w-14 h-14 rounded-2xl bg-blue-50 text-blue-600 flex items-center justify-center shadow-inner"><i class="fa-solid fa-arrow-down text-xl"></i></div>
                    <span class="text-[10px] font-black uppercase tracking-widest text-slate-500">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-7 flex flex-col items-center gap-4 active:bg-slate-50 transition-all">
                    <div class="w-14 h-14 rounded-2xl bg-slate-50 text-slate-400 flex items-center justify-center shadow-inner"><i class="fa-solid fa-arrow-up-right-from-square text-lg"></i></div>
                    <span class="text-[10px] font-black uppercase tracking-widest text-slate-500">Withdraw</span>
                </button>
            </div>

            <div id="plans-list" class="space-y-6"></div>
        </div>

        <div id="p-deposit" class="page">
            <h2 class="text-3xl font-black tracking-tighter mb-10">Deposit</h2>
            <div class="grid grid-cols-2 gap-4 mb-8">
                <div onclick="selectDep('Easypaisa')" class="method-card glass p-6 text-center" id="m-Easypaisa">
                    <p class="text-[10px] font-black uppercase">Easypaisa</p>
                </div>
                <div onclick="selectDep('JazzCash')" class="method-card glass p-6 text-center" id="m-JazzCash">
                    <p class="text-[10px] font-black uppercase">JazzCash</p>
                </div>
            </div>
            <div class="glass p-8">
                <input type="number" id="dep-amt" placeholder="Amount (Rs)" class="w-full p-5 bg-slate-50 rounded-2xl font-bold mb-4 focus:outline-none">
                <input type="text" id="dep-trx" placeholder="Transaction ID" class="w-full p-5 bg-slate-50 rounded-2xl font-bold mb-8 focus:outline-none">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 text-white py-5 rounded-3xl font-black uppercase text-xs">Submit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <h2 class="text-3xl font-black tracking-tighter mb-10">Withdraw</h2>
            <div class="glass p-8">
                <input type="number" id="wd-amt" placeholder="Amount" class="w-full p-5 bg-slate-50 rounded-2xl font-bold mb-4 focus:outline-none">
                <input type="text" id="wd-acc" placeholder="Account Number" class="w-full p-5 bg-slate-50 rounded-2xl font-bold mb-8 focus:outline-none">
                <button onclick="submitWithdraw()" class="w-full bg-[#0f172a] text-white py-5 rounded-3xl font-black uppercase text-xs">Confirm Payout</button>
            </div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black tracking-tighter mb-12">Lucky Wheel</h2>
            <div id="wheel-container">
                <div class="wheel-pointer"></div>
                <img id="wheel" src="https://i.ibb.co/v4S69YV/lucky-wheel.png" onerror="this.src='https://cdn-icons-png.flaticon.com/512/6119/6119533.png'">
            </div>
            <button onclick="handleSpin()" id="spin-btn" class="mt-16 w-full max-w-xs bg-blue-600 text-white py-6 rounded-[2.5rem] font-black text-xs uppercase tracking-[0.3em]">Spin</button>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-24 glass flex justify-around items-center px-6 z-[3000]">
        <button onclick="changePage('home')" id="n-home" class="text-blue-600 flex flex-col items-center gap-2"><i class="fa-solid fa-house-chimney text-xl"></i><span class="text-[8px] font-black uppercase">Home</span></button>
        <button onclick="changePage('spin')" id="n-spin" class="text-slate-400 flex flex-col items-center gap-2"><i class="fa-solid fa-dharmachakra text-xl"></i><span class="text-[8px] font-black uppercase">Spin</span></button>
        <button onclick="logout()" class="text-rose-400 flex flex-col items-center gap-2"><i class="fa-solid fa-power-off text-xl"></i><span class="text-[8px] font-black uppercase">Exit</span></button>
    </nav>

    <script>
        // --- Firebase (Aapki Key) ---
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
                document.getElementById('rank-badge').innerText = userObj.balance > 100000 ? "Diamond VIP" : "Standard Node";
                startTicker();
            });
        }

        function startTicker() {
            if(userObj.tierROI > 0) {
                setInterval(() => {
                    const ps = (userObj.balance * (userObj.tierROI / 100)) / 86400;
                    userObj.profit = (userObj.profit || 0) + ps;
                    document.getElementById('v-profit').innerText = "₨ " + userObj.profit.toFixed(6);
                }, 1000);
            }
        }

        async function claimBonus() {
            const now = Date.now();
            if (now - userObj.lastBonus < 86400000) return alert("Wait for 24h!");
            await db.collection("users").doc(userObj.name).update({ balance: userObj.balance + 5, lastBonus: now });
            alert("₨ 5 Added!");
        }

        async function handleSpin() {
            if (isSpinning) return;
            const now = Date.now();
            let isPaid = false;
            if (now - userObj.lastSpin < 86400000) {
                if (userObj.balance < 20) return alert("Need ₨ 20!");
                if (!confirm("Spend ₨ 20 for spin?")) return;
                isPaid = true;
            }
            isSpinning = true;
            if (isPaid) await db.collection("users").doc(userObj.name).update({ balance: userObj.balance - 20 });
            const wheel = document.getElementById('wheel');
            const deg = Math.floor(Math.random() * 3600) + 1800;
            wheel.style.transform = `rotate(${deg}deg)`;
            const t = setInterval(() => spinTick.play(), 200);
            setTimeout(async () => {
                clearInterval(t);
                isSpinning = false;
                const wins = [0, 5, 10, 50, 2, 0];
                const win = wins[Math.floor(Math.random() * wins.length)];
                let up = { balance: userObj.balance + win };
                if(!isPaid) up.lastSpin = now;
                await db.collection("users").doc(userObj.name).update(up);
                alert(win > 0 ? `Won ₨ ${win}!` : "Try Again!");
                wheel.style.transform = "rotate(0deg)";
            }, 4000);
        }

        function selectDep(m) {
            selectedMethod = m;
            document.querySelectorAll('.method-card').forEach(c => c.classList.remove('selected'));
            document.getElementById('m-'+m).classList.add('selected');
        }

        async function submitDeposit() {
            const amt = document.getElementById('dep-amt').value;
            const trx = document.getElementById('dep-trx').value;
            if(!amt || !trx) return alert("Enter Details!");
            await db.collection("requests").add({ user: userObj.name, type: 'Deposit', amount: amt, method: selectedMethod, trx: trx, status: 'pending', time: Date.now() });
            alert("Request Sent!");
            changePage('home');
        }

        async function submitWithdraw() {
            const amt = document.getElementById('wd-amt').value;
            const acc = document.getElementById('wd-acc').value;
            if(!amt || !acc) return alert("Enter Details!");
            if(userObj.balance < amt) return alert("Low Balance!");
            await db.collection("requests").add({ user: userObj.name, type: 'Withdraw', amount: amt, account: acc, status: 'pending', time: Date.now() });
            await db.collection("users").doc(userObj.name).update({ balance: userObj.balance - amt });
            alert("Withdraw Processed!");
            changePage('home');
        }

        function renderPlans() {
            const ps = [
                { n: "Quantum Starter", p: 200, r: 2.5, img: "https://images.unsplash.com/photo-1639762681485-074b7f938ba0?w=400" },
                { n: "Titanium Node", p: 10000, r: 7.5, img: "https://images.unsplash.com/photo-1621761191319-c6fb62004040?w=400" }
            ];
            document.getElementById('plans-list').innerHTML = ps.map(p => `
                <div class="glass overflow-hidden">
                    <img src="${p.img}" class="h-40 w-full object-cover">
                    <div class="p-8">
                        <div class="flex justify-between mb-6">
                            <div><h4 class="font-bold">${p.n}</h4><p class="text-[9px] text-blue-600 font-black">${p.r}% DAILY</p></div>
                            <p class="font-black">₨ ${p.p}</p>
                        </div>
                        <button onclick="buyPlan(${p.p}, ${p.r}, '${p.n}')" class="w-full bg-[#0f172a] text-white py-5 rounded-[2rem] font-black text-[10px] uppercase">Activate</button>
                    </div>
                </div>
            `).join('');
        }

        async function buyPlan(p, r, n) {
            if(userObj.balance < p) return alert("Low Balance!");
            await db.collection("users").doc(userObj.name).update({ balance: userObj.balance - p, tierROI: r, profit: 0 });
            alert("Plan Activated!");
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
