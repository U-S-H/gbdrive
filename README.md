<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify | Future of Investing</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700;800&display=swap');
        body { font-family: 'Inter', sans-serif; background: #0F172A; color: #F8FAFC; overflow-x: hidden; }
        .glass-card { background: rgba(30, 41, 59, 0.7); backdrop-filter: blur(12px); border: 1px solid rgba(255, 255, 255, 0.05); border-radius: 24px; }
        .gradient-text { background: linear-gradient(90deg, #38BDF8, #818CF8); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        .btn-primary { background: linear-gradient(135deg, #3B82F6 0%, #2563EB 100%); transition: all 0.3s ease; }
        .btn-primary:active { transform: scale(0.95); opacity: 0.9; }
        .nav-active { color: #38BDF8; position: relative; }
        .nav-active::after { content: ''; position: absolute; bottom: -8px; width: 4px; height: 4px; background: #38BDF8; border-radius: 50%; }
        .page { display: none; animation: fadeIn 0.5s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        input, select { background: rgba(15, 23, 42, 0.6) !important; border: 1px solid rgba(255, 255, 255, 0.1) !important; color: white !important; }
    </style>
</head>
<body class="min-h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#0F172A] flex items-center justify-center p-8">
        <div class="w-full max-w-md text-center">
            <div class="mb-10">
                <h1 class="text-5xl font-extrabold tracking-tighter italic">VEST<span class="text-blue-500">IFY</span></h1>
                <p class="text-slate-500 text-xs uppercase tracking-[0.3em] mt-2">Smart Asset Management</p>
            </div>
            <div class="glass-card p-10 shadow-2xl">
                <h2 class="text-xl font-semibold mb-6">Welcome back</h2>
                <button onclick="loginWithGoogle()" class="w-full bg-white text-slate-900 py-4 rounded-xl font-bold flex items-center justify-center gap-3 active:scale-95 transition-all">
                    <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/action/google.svg" width="20">
                    Sign in with Google
                </button>
            </div>
        </div>
    </section>

    <header class="p-6 flex justify-between items-center sticky top-0 z-[500] bg-[#0F172A]/80 backdrop-blur-md border-b border-white/5">
        <span class="text-2xl font-black italic tracking-tighter gradient-text">VESTIFY</span>
        <div onclick="adminTap()" class="w-10 h-10 rounded-full bg-slate-800 flex items-center justify-center border border-white/10">
            <i class="fa-solid fa-user-shield text-blue-400"></i>
        </div>
    </header>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32 p-6">
        <div id="p-home" class="page active-page">
            <div class="glass-card p-8 mb-6 relative overflow-hidden bg-gradient-to-br from-blue-600/20 to-indigo-600/20">
                <p class="text-slate-400 text-xs font-bold uppercase tracking-widest mb-1">Portfolio Balance</p>
                <h2 class="text-4xl font-extrabold tracking-tight mb-4" id="v-bal">₨ 0</h2>
                <div class="flex gap-4">
                    <div class="flex-1">
                        <p class="text-[10px] text-slate-500 uppercase">Total Profit</p>
                        <p id="v-profit" class="text-lg font-bold text-emerald-400">₨ 0</p>
                    </div>
                    <div class="flex-1 text-right">
                        <p class="text-[10px] text-slate-500 uppercase">Current Fleet</p>
                        <p id="tier-tag" class="text-sm font-bold text-blue-400">Scanning...</p>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="changePage('wallet')" class="glass-card py-6 flex flex-col items-center gap-2 border-b-2 border-blue-500">
                    <i class="fa-solid fa-wallet text-blue-400"></i>
                    <span class="text-[10px] font-bold uppercase">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass-card py-6 flex flex-col items-center gap-2 border-b-2 border-rose-500">
                    <i class="fa-solid fa-arrow-up-right-from-square text-rose-400"></i>
                    <span class="text-[10px] font-bold uppercase">Withdraw</span>
                </button>
            </div>
            
            <h3 class="text-sm font-bold uppercase tracking-widest text-slate-500 mb-4">Investment Tiers</h3>
            <div id="plans-list" class="space-y-3"></div>
        </div>

        <div id="p-activity" class="page">
            <h2 class="text-xl font-bold mb-6">Transaction Ledger</h2>
            <div id="user-history" class="space-y-3"></div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-0 left-0 w-full bg-[#1E293B]/90 backdrop-blur-xl border-t border-white/5 p-6 flex justify-around items-center rounded-t-[2.5rem]">
        <button onclick="changePage('home')" id="n-home" class="nav-active"><i class="fa-solid fa-grid-2 text-xl"></i></button>
        <button onclick="changePage('activity')" id="n-activity" class="opacity-40"><i class="fa-solid fa-list-ul text-xl"></i></button>
        <button onclick="logout()" class="opacity-40 text-rose-400"><i class="fa-solid fa-power-off text-xl"></i></button>
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
            { n: "Starter Fleet", p: 500, r: 4 },
            { n: "Growth Pro", p: 2000, r: 6 },
            { n: "Business Elite", p: 10000, r: 8 },
            { n: "Royal Assets", p: 50000, r: 12 }
        ];

        async function loginWithGoogle() {
            const res = await auth.signInWithPopup(provider);
            const name = res.user.displayName;
            localStorage.setItem('v_user', name);
            const ref = db.collection("users").doc(name);
            const d = await ref.get();
            if(!d.exists) await ref.set({ name, balance: 0, profit: 0, tierName: "None", time: Date.now() });
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
            list.innerHTML = plans.map(p => `
                <div onclick="buyPlan(${p.p}, ${p.r}, '${p.n}')" class="glass-card p-5 flex justify-between items-center active:scale-95 transition-all">
                    <div>
                        <p class="font-bold text-sm">${p.n}</p>
                        <p class="text-[10px] text-emerald-400 font-bold">${p.r}% Daily Yield</p>
                    </div>
                    <p class="font-bold">₨ ${p.p.toLocaleString()}</p>
                </div>
            `).join('');
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                const u = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (u.balance||0).toLocaleString();
                document.getElementById('v-profit').innerText = "₨ " + (u.profit||0).toLocaleString();
                document.getElementById('tier-tag').innerText = u.tierName;
            });
        }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.querySelectorAll('nav button').forEach(b => b.classList.add('opacity-40'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.getElementById('n-'+p)?.classList.remove('opacity-40');
            document.getElementById('n-'+p)?.classList.add('nav-active');
        }

        window.onload = () => {
            const saved = localStorage.getItem('v_user');
            if(saved) initApp(saved);
        };
        
        function logout() { localStorage.clear(); location.reload(); }
    </script>
</body>
</html>
