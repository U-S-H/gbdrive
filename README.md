<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify | Next-Gen Financial Hub</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&family=JetBrains+Mono:wght@500&display=swap');
        
        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: #f8fafc; color: #0f172a; overflow-x: hidden; }
        .mono { font-family: 'JetBrains Mono', monospace; }
        
        .premium-card {
            background: #ffffff;
            border: 1px solid rgba(0,0,0,0.03);
            border-radius: 28px;
            box-shadow: 0 10px 25px -5px rgba(0,0,0,0.02);
            overflow: hidden;
        }

        .hero-gradient {
            background: linear-gradient(135deg, #1e293b 0%, #0f172a 100%);
            box-shadow: 0 20px 40px -12px rgba(15, 23, 42, 0.3);
        }

        .plan-img { width: 100%; height: 120px; object-fit: cover; filter: brightness(0.8); transition: 0.5s; }
        .premium-card:hover .plan-img { filter: brightness(1); transform: scale(1.05); }

        .btn-primary {
            background: #2563eb;
            color: #fff;
            box-shadow: 0 8px 20px -6px rgba(37, 99, 235, 0.4);
        }

        .status-dot { width: 6px; height: 6px; border-radius: 50%; }

        /* Smooth Slide Animation */
        .page { display: none; animation: slideUp 0.6s cubic-bezier(0.16, 1, 0.3, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }

        nav { backdrop-filter: blur(12px); background: rgba(255, 255, 255, 0.85); border-top: 1px solid #f1f5f9; }
    </style>
</head>
<body class="min-h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[2000] bg-white flex flex-col items-center justify-center p-10">
        <div class="w-full max-w-sm text-center">
            <div class="mb-12">
                <div class="w-20 h-20 bg-blue-600 rounded-[2rem] flex items-center justify-center mx-auto mb-6 shadow-2xl shadow-blue-200">
                    <i class="fa-solid fa-bolt-lightning text-white text-3xl"></i>
                </div>
                <h1 class="text-4xl font-black tracking-tighter italic">VESTIFY</h1>
                <p class="text-slate-400 text-[10px] font-bold uppercase tracking-[0.4em] mt-2">Elite Asset Terminal</p>
            </div>
            <button onclick="loginWithGoogle()" class="w-full bg-[#0f172a] text-white py-5 rounded-2xl font-bold flex items-center justify-center gap-3 active:scale-95 transition-all">
                <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/action/google.svg" width="20" class="bg-white rounded-full p-0.5">
                Sync with Google
            </button>
        </div>
    </section>

    <header class="px-6 py-6 flex justify-between items-center sticky top-0 z-[1000] bg-white/80 backdrop-blur-md">
        <div class="flex items-center gap-2">
            <span class="font-black text-2xl tracking-tighter italic text-blue-600">V.</span>
            <span class="text-[10px] font-black tracking-[0.2em] text-slate-400 uppercase">Secure Node</span>
        </div>
        <button onclick="adminTap()" class="w-10 h-10 rounded-full bg-slate-50 flex items-center justify-center border border-slate-100"><i class="fa-solid fa-fingerprint text-slate-300"></i></button>
    </header>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32 px-6">
        
        <div id="p-home" class="page active-page">
            <div class="hero-gradient p-8 rounded-[2.5rem] text-white mb-8 relative overflow-hidden">
                <div class="absolute -right-10 -top-10 w-40 h-40 bg-blue-500/10 rounded-full blur-3xl"></div>
                <div class="relative z-10">
                    <p class="text-[9px] font-bold opacity-50 uppercase tracking-[0.2em] mb-1">Total Portfolio Value</p>
                    <h2 class="text-4xl font-extrabold tracking-tighter mb-10 mono" id="v-bal">₨ 0.00</h2>
                    
                    <div class="grid grid-cols-2 gap-4 pt-6 border-t border-white/5">
                        <div>
                            <p class="text-[8px] opacity-40 uppercase font-black">Hourly Yield</p>
                            <p id="v-profit" class="text-lg font-bold text-emerald-400 mono">₨ 0.0000</p>
                        </div>
                        <div class="text-right">
                            <p class="text-[8px] opacity-40 uppercase font-black">Plan Status</p>
                            <p id="tier-tag" class="text-[10px] font-black uppercase text-blue-300">Scanning...</p>
                        </div>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-10">
                <button onclick="changePage('deposit')" class="premium-card p-6 flex flex-col items-center gap-2 active:bg-slate-50 transition-all">
                    <div class="w-10 h-10 rounded-xl bg-blue-50 flex items-center justify-center text-blue-600"><i class="fa-solid fa-plus text-sm"></i></div>
                    <span class="text-[9px] font-bold uppercase tracking-widest">Add Funds</span>
                </button>
                <button onclick="changePage('withdraw')" class="premium-card p-6 flex flex-col items-center gap-2 active:bg-slate-50 transition-all">
                    <div class="w-10 h-10 rounded-xl bg-slate-50 flex items-center justify-center text-slate-400"><i class="fa-solid fa-arrow-up-right-from-square text-xs"></i></div>
                    <span class="text-[9px] font-bold uppercase tracking-widest">Withdraw</span>
                </button>
            </div>

            <div class="bg-emerald-50/50 p-4 rounded-2xl mb-10 border border-emerald-100 flex items-center gap-3">
                <div class="status-dot bg-emerald-500 animate-pulse"></div>
                <p class="text-[10px] font-bold text-emerald-800" id="live-news">Real-time trading active on Global Nodes...</p>
            </div>

            <h3 class="text-xs font-black uppercase tracking-[0.3em] text-slate-400 mb-6">Investment Tiers</h3>
            <div id="plans-list" class="space-y-6"></div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-0 left-0 w-full h-24 px-10 flex justify-around items-center z-[1000]">
        <button onclick="changePage('home')" id="n-home" class="text-blue-600 flex flex-col items-center gap-1"><i class="fa-solid fa-house-chimney text-xl"></i><span class="text-[8px] font-bold uppercase">Terminal</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="text-slate-400 flex flex-col items-center gap-1"><i class="fa-solid fa-chart-line text-xl"></i><span class="text-[8px] font-bold uppercase">History</span></button>
        <button onclick="logout()" class="text-rose-400 flex flex-col items-center gap-1"><i class="fa-solid fa-power-off text-xl"></i><span class="text-[8px] font-bold uppercase">Exit</span></button>
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
            { n: "Quantum Starter", p: 200, r: 2.5, d: 30, img: "https://images.unsplash.com/photo-1639762681485-074b7f938ba0?auto=format&fit=crop&w=400&q=80" },
            { n: "Silver Node", p: 5000, r: 5.2, d: 15, img: "https://images.unsplash.com/photo-1621761191319-c6fb62004040?auto=format&fit=crop&w=400&q=80" },
            { n: "Titanium Vault", p: 25000, r: 8.5, d: 10, img: "https://images.unsplash.com/photo-1551288049-bbbda5366392?auto=format&fit=crop&w=400&q=80" },
            { n: "Flash Booster", p: 1200, r: 15.0, d: 3, img: "https://images.unsplash.com/photo-1614064641938-3bbee52942c7?auto=format&fit=crop&w=400&q=80" },
            { n: "Whale Institutional", p: 100000, r: 12.0, d: 45, img: "https://images.unsplash.com/photo-1640341719942-834c898b1ec3?auto=format&fit=crop&w=400&q=80" }
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
            list.innerHTML = plans.map(p => {
                const dailyProfit = (p.p * (p.r/100)).toFixed(2);
                const totalProfit = (dailyProfit * p.d).toFixed(2);
                return `
                <div class="premium-card group active:scale-[0.98] transition-all">
                    <img src="${p.img}" class="plan-img" alt="${p.n}">
                    <div class="p-6">
                        <div class="flex justify-between items-start mb-4">
                            <div>
                                <h4 class="font-bold text-lg text-slate-800 tracking-tight">${p.n}</h4>
                                <span class="text-[9px] font-black text-blue-600 bg-blue-50 px-2 py-1 rounded-md uppercase">${p.r}% DAILY PROFIT</span>
                            </div>
                            <p class="font-extrabold text-xl text-slate-900 mono">₨ ${p.p.toLocaleString()}</p>
                        </div>
                        <div class="grid grid-cols-3 gap-2 py-4 border-y border-slate-50 mb-4">
                            <div><p class="text-[8px] text-slate-400 font-bold uppercase">Daily</p><p class="text-xs font-bold">₨ ${dailyProfit}</p></div>
                            <div><p class="text-[8px] text-slate-400 font-bold uppercase">Net Payout</p><p class="text-xs font-bold text-emerald-500">₨ ${totalProfit}</p></div>
                            <div class="text-right"><p class="text-[8px] text-slate-400 font-bold uppercase">Cycle</p><p class="text-xs font-bold text-blue-600">${p.d} Days</p></div>
                        </div>
                        <button onclick="buyPlan(${p.p}, ${p.r}, '${p.n}')" class="w-full btn-primary py-4 rounded-xl font-bold text-xs uppercase tracking-widest">Activate Contract</button>
                    </div>
                </div>`;
            }).join('');
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                user = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
                document.getElementById('tier-tag').innerText = user.tierName;
                startTicker();
            });
        }

        function startTicker() {
            if(user.tierROI > 0) {
                setInterval(() => {
                    const profitPerSec = (user.balance * (user.tierROI / 100)) / 86400;
                    user.profit = (user.profit || 0) + profitPerSec;
                    document.getElementById('v-profit').innerText = "₨ " + user.profit.toFixed(6);
                }, 1000);
            }
        }

        async function buyPlan(p, r, n) {
            if(user.balance < p) return alert("Insufficient Balance. Please top up your node.");
            if(confirm(`Do you want to initiate the ${n} protocol?`)) {
                await db.collection("users").doc(user.name).update({ 
                    balance: user.balance - p, 
                    tierROI: r, 
                    tierName: n, 
                    time: Date.now(),
                    profit: 0
                });
                alert("Protocol Activated Successfully!");
            }
        }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
        }

        function logout() { localStorage.clear(); location.reload(); }
        window.onload = () => { if(localStorage.getItem('v_user')) initApp(localStorage.getItem('v_user')); };
    </script>
</body>
</html>
