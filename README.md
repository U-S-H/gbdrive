<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify | Elite Asset Management</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: #F8FAFC; color: #0F172A; }
        .premium-card { background: #FFFFFF; border: 1px solid rgba(0,0,0,0.05); border-radius: 24px; box-shadow: 0 10px 30px -5px rgba(0,0,0,0.02); }
        .blue-gradient { background: linear-gradient(135deg, #2563EB 0%, #1D4ED8 100%); }
        .page { display: none; animation: slideIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes slideIn { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .nav-active { color: #2563EB; transform: translateY(-3px); font-weight: 800; }
    </style>
</head>
<body class="min-h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[2000] bg-white flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <div class="mb-12">
                <div class="w-20 h-20 bg-blue-600 rounded-[2rem] flex items-center justify-center mx-auto mb-6 shadow-2xl shadow-blue-200 rotate-12">
                    <i class="fa-solid fa-v text-white text-4xl -rotate-12"></i>
                </div>
                <h1 class="text-4xl font-black tracking-tighter italic text-slate-800">VESTIFY</h1>
                <p class="text-[10px] tracking-[0.4em] text-slate-400 font-bold uppercase mt-2 text-center">Institutional Grade Assets</p>
            </div>
            <button onclick="loginWithGoogle()" class="w-full bg-slate-900 text-white py-5 rounded-2xl font-bold flex items-center justify-center gap-3 active:scale-95 transition-all shadow-xl">
                <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/action/google.svg" width="20" class="bg-white rounded-full p-0.5">
                Continue with Google
            </button>
        </div>
    </section>

    <header class="p-6 flex justify-between items-center bg-white/70 backdrop-blur-lg sticky top-0 z-[500] border-b border-slate-100">
        <div class="flex items-center gap-2">
            <span class="font-black text-2xl tracking-tighter italic text-blue-600">V.</span>
            <span class="font-bold text-sm tracking-tight text-slate-700">VESTIFY</span>
        </div>
        <button onclick="adminTap()" class="w-10 h-10 rounded-xl bg-slate-50 flex items-center justify-center border border-slate-200">
            <i class="fa-solid fa-shield-check text-slate-400"></i>
        </button>
    </header>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32 p-6">
        
        <div id="p-home" class="page active-page">
            <div class="blue-gradient p-8 rounded-[2.5rem] mb-8 text-white shadow-2xl shadow-blue-200 relative overflow-hidden">
                <p class="text-[10px] font-bold opacity-70 uppercase tracking-widest mb-1">Portfolio Value</p>
                <h2 class="text-5xl font-black tracking-tighter mb-8" id="v-bal">₨ 0</h2>
                <div class="flex justify-between items-center border-t border-white/10 pt-6">
                    <div>
                        <p class="text-[9px] opacity-70 uppercase font-bold">Total ROI</p>
                        <p id="v-profit" class="text-lg font-bold">₨ 0</p>
                    </div>
                    <div class="text-right">
                        <p class="text-[9px] opacity-70 uppercase font-bold">Current Tier</p>
                        <p id="tier-tag" class="text-xs font-bold bg-white/20 px-3 py-1 rounded-full uppercase">None</p>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-10">
                <button onclick="changePage('deposit')" class="premium-card py-6 flex flex-col items-center gap-2 active:bg-slate-50 border-b-4 border-blue-500">
                    <i class="fa-solid fa-plus-circle text-blue-600 text-xl"></i>
                    <span class="text-[10px] font-black uppercase">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="premium-card py-6 flex flex-col items-center gap-2 active:bg-slate-50 border-b-4 border-slate-200">
                    <i class="fa-solid fa-arrow-up-right-from-square text-slate-400 text-xl"></i>
                    <span class="text-[10px] font-black uppercase">Withdraw</span>
                </button>
            </div>

            <div id="plans-list" class="space-y-4"></div>
        </div>

        <div id="p-deposit" class="page">
            <h3 class="text-xl font-black mb-6">Fund Account</h3>
            <div class="premium-card p-6 mb-6">
                <p class="text-[10px] font-bold text-slate-400 uppercase mb-4 tracking-widest">Select Method</p>
                <select id="dep-method" class="w-full p-4 rounded-xl bg-slate-50 border border-slate-100 font-bold mb-4">
                    <option value="Easypaisa">Easypaisa</option>
                    <option value="JazzCash">JazzCash</option>
                    <option value="SadaPay">SadaPay</option>
                </select>
                <input type="number" id="dep-amt" placeholder="Enter Amount" class="w-full p-4 rounded-xl bg-slate-50 border border-slate-100 font-bold mb-6">
                <button onclick="submitRequest('deposit')" class="w-full bg-blue-600 text-white py-4 rounded-2xl font-black uppercase text-xs tracking-widest shadow-lg shadow-blue-100">Request Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <h3 class="text-xl font-black mb-6">Withdraw Assets</h3>
            <div class="premium-card p-6">
                <input type="number" id="wd-amt" placeholder="Amount" class="w-full p-4 rounded-xl bg-slate-50 border border-slate-100 font-bold mb-4">
                <input type="text" id="wd-acc" placeholder="Account Number" class="w-full p-4 rounded-xl bg-slate-50 border border-slate-100 font-bold mb-6">
                <button onclick="submitRequest('withdraw')" class="w-full bg-slate-900 text-white py-4 rounded-2xl font-black uppercase text-xs tracking-widest">Withdraw Funds</button>
            </div>
        </div>

        <div id="p-activity" class="page">
            <h3 class="text-xl font-black mb-6">Transaction Ledger</h3>
            <div id="activity-list" class="space-y-3"></div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-20 premium-card flex justify-around items-center px-4 shadow-2xl z-[1000]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-1 nav-active transition-all"><i class="fa-solid fa-house-chimney text-xl"></i><span class="text-[8px] font-bold uppercase">Vault</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-1 text-slate-400 transition-all opacity-60"><i class="fa-solid fa-list-ul text-xl"></i><span class="text-[8px] font-bold uppercase">History</span></button>
        <button onclick="logout()" class="flex flex-col items-center gap-1 text-rose-400 opacity-60"><i class="fa-solid fa-power-off text-xl"></i><span class="text-[8px] font-bold uppercase">Exit</span></button>
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

        const plans = [
            { n: "Nano Starter", p: 200, r: 2.5, cat: "Regular" },
            { n: "Micro Growth", p: 500, r: 3.0, cat: "Regular" },
            { n: "Basic Yield", p: 1000, r: 3.5, cat: "Regular" },
            { n: "Standard Asset", p: 2000, r: 4.0, cat: "Regular" },
            { n: "Bronze Tier", p: 3500, r: 4.5, cat: "Regular" },
            { n: "Silver Node", p: 5000, r: 5.0, cat: "Regular" },
            { n: "Gold Prime", p: 7500, r: 5.5, cat: "Regular" },
            { n: "Platinum Pro", p: 10000, r: 6.0, cat: "Regular" },
            { n: "Ruby Elite", p: 15000, r: 6.5, cat: "Regular" },
            { n: "Sapphire Plus", p: 20000, r: 7.0, cat: "Regular" },
            { n: "Emerald Vault", p: 30000, r: 7.5, cat: "Regular" },
            { n: "Diamond Core", p: 50000, r: 8.5, cat: "Regular" },
            { n: "Master Portfolio", p: 75000, r: 9.5, cat: "Regular" },
            { n: "Corporate King", p: 100000, r: 11.0, cat: "Regular" },
            { n: "Institutional", p: 250000, r: 15.0, cat: "Regular" },
            { n: "Weekend Booster", p: 1200, r: 10.0, cat: "Special", desc: "Limited Time" },
            { n: "Founder Choice", p: 8000, r: 12.5, cat: "Special", desc: "High Yield" },
            { n: "Midnight Flash", p: 25000, r: 18.0, cat: "Special", desc: "VIP Only" },
            { n: "Eid Special", p: 50000, r: 20.0, cat: "Special", desc: "Festival Offer" },
            { n: "Vestify Whale", p: 500000, r: 25.0, cat: "Special", desc: "Elite Member" }
        ];

        let user = null;

        async function loginWithGoogle() {
            const res = await auth.signInWithPopup(provider);
            const name = res.user.displayName;
            localStorage.setItem('v_user', name);
            const ref = db.collection("users").doc(name);
            const doc = await ref.get();
            if(!doc.exists) await ref.set({ name, balance: 0, profit: 0, tierROI: 0, tierName: "None", time: Date.now() });
            initApp(name);
        }

        function initApp(name) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
            syncData(name);
        }

        function renderPlans() {
            const list = document.getElementById('plans-list');
            let html = '<h3 class="text-[10px] font-black text-blue-600 uppercase tracking-widest mb-4">Special Offers</h3>';
            plans.filter(p => p.cat === 'Special').forEach(p => {
                html += `<div onclick="buyPlan(${p.p}, ${p.r}, '${p.n}')" class="premium-card p-6 mb-4 border-l-4 border-orange-500 bg-orange-50/20 active:scale-95 transition-all">
                    <div class="flex justify-between items-center">
                        <div><span class="text-[8px] font-bold bg-orange-500 text-white px-2 py-0.5 rounded-full uppercase mb-1 inline-block">${p.desc}</span><h4 class="font-black text-slate-800 text-sm">${p.n}</h4><p class="text-[10px] text-orange-600 font-bold">${p.r}% Daily ROI</p></div>
                        <p class="font-black text-slate-900">₨ ${p.p.toLocaleString()}</p>
                    </div></div>`;
            });
            html += '<h3 class="text-[10px] font-black text-slate-400 uppercase tracking-widest mt-8 mb-4">Standard Portfolios</h3>';
            plans.filter(p => p.cat === 'Regular').forEach(p => {
                html += `<div onclick="buyPlan(${p.p}, ${p.r}, '${p.n}')" class="premium-card p-5 mb-3 flex justify-between items-center active:scale-95 transition-all border-l-4 border-blue-500">
                    <div><h4 class="font-bold text-sm text-slate-800">${p.n}</h4><p class="text-[10px] text-blue-500 font-bold uppercase tracking-wider">${p.r}% Daily</p></div>
                    <p class="font-black text-slate-900 text-sm">₨ ${p.p.toLocaleString()}</p></div>`;
            });
            list.innerHTML = html;
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                user = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
                document.getElementById('v-profit').innerText = "₨ " + (user.profit||0).toLocaleString();
                document.getElementById('tier-tag').innerText = user.tierName;
                calculateProfit();
            });
            db.collection("requests").where("user", "==", name).orderBy("time", "desc").onSnapshot(snap => {
                const list = document.getElementById('activity-list');
                list.innerHTML = '';
                snap.forEach(d => {
                    const r = d.data();
                    list.innerHTML += `<div class="premium-card p-4 flex justify-between items-center"><div class="text-[10px] font-bold uppercase tracking-tighter">${r.type} <span class="text-slate-400">₨ ${r.amount}</span></div><span class="text-[8px] font-black uppercase ${r.status==='approved'?'text-emerald-500':'text-amber-500'}">${r.status}</span></div>`;
                });
            });
        }

        function calculateProfit() {
            if(user.tierROI > 0) {
                const now = Date.now();
                const diff = (now - user.time) / 1000;
                const profitPerSec = (user.balance * (user.tierROI/100)) / 86400;
                if(diff > 1) {
                    const gain = diff * profitPerSec;
                    db.collection("users").doc(user.name).update({ profit: (user.profit||0) + gain, time: now });
                }
            }
        }

        async function buyPlan(p, r, n) {
            if(user.balance < p) return alert("Low Balance!");
            if(confirm(`Activate ${n} for ₨ ${p}?`)) {
                await db.collection("users").doc(user.name).update({ 
                    balance: user.balance - p, 
                    tierROI: r, 
                    tierName: n, 
                    time: Date.now() 
                });
                alert("Plan Activated!");
            }
        }

        async function submitRequest(type) {
            const amt = parseInt(document.getElementById(type==='deposit'?'dep-amt':'wd-amt').value);
            if(!amt || amt < 200) return alert("Min amount ₨ 200");
            if(type==='withdraw' && user.balance < amt) return alert("Low balance");
            await db.collection("requests").add({ user: user.name, amount: amt, type: type, status: 'pending', time: Date.now() });
            if(type==='withdraw') await db.collection("users").doc(user.name).update({ balance: user.balance - amt });
            alert("Request Submitted!");
            changePage('home');
        }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.querySelectorAll('nav button').forEach(b => { b.classList.add('opacity-60', 'text-slate-400'); b.classList.remove('nav-active'); });
            document.getElementById('p-'+p).classList.add('active-page');
            document.getElementById('n-'+p)?.classList.remove('opacity-60', 'text-slate-400');
            document.getElementById('n-'+p)?.classList.add('nav-active');
        }

        function adminTap() { const pass = prompt("Access Key:"); if(pass==='vest786') location.href = 'admin.html'; }
        window.onload = () => { const saved = localStorage.getItem('v_user'); if(saved) initApp(saved); };
        function logout() { localStorage.clear(); location.reload(); }
    </script>
</body>
</html>
