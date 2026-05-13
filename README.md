<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify | Global Asset Terminal</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&family=JetBrains+Mono:wght@500&display=swap');
        
        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: #fcfcfd; color: #101828; }
        .mono { font-family: 'JetBrains Mono', monospace; }
        
        /* Modern Bento Grid Cards */
        .bento-card {
            background: #ffffff;
            border: 1px solid #f2f4f7;
            border-radius: 24px;
            box-shadow: 0 4px 20px -4px rgba(16, 24, 40, 0.03);
            transition: all 0.3s ease;
        }

        .premium-gradient {
            background: linear-gradient(135deg, #004EEB 0%, #002C85 100%);
        }

        /* Live Ticker Animation */
        .ticker-glow { animation: pulse-glow 2s infinite; }
        @keyframes pulse-glow {
            0% { text-shadow: 0 0 5px rgba(52, 211, 153, 0.2); }
            50% { text-shadow: 0 0 15px rgba(52, 211, 153, 0.6); }
            100% { text-shadow: 0 0 5px rgba(52, 211, 153, 0.2); }
        }

        .progress-bar { height: 6px; background: #f2f4f7; border-radius: 10px; overflow: hidden; }
        .progress-fill { height: 100%; background: #2e90fa; width: 45%; transition: width 1s ease; }

        .page { display: none; animation: slideIn 0.5s cubic-bezier(0.16, 1, 0.3, 1); }
        .active-page { display: block; }
        @keyframes slideIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }
    </style>
</head>
<body class="min-h-screen flex flex-col">

    <header class="px-6 py-5 flex justify-between items-center sticky top-0 z-[1000] bg-white/80 backdrop-blur-md border-b border-slate-100">
        <div class="flex items-center gap-2">
            <div class="w-8 h-8 bg-black rounded-lg flex items-center justify-center shadow-lg"><i class="fa-solid fa-v text-white text-xs"></i></div>
            <span class="font-bold text-lg tracking-tighter">VESTIFY</span>
        </div>
        <div class="flex items-center gap-3">
            <div class="bg-emerald-50 px-3 py-1 rounded-full flex items-center gap-1.5">
                <div class="w-1.5 h-1.5 bg-emerald-500 rounded-full animate-pulse"></div>
                <span class="text-[9px] font-bold text-emerald-700 uppercase tracking-widest">Live Terminal</span>
            </div>
        </div>
    </header>

    <main id="app-ui" class="flex-1 overflow-y-auto pb-32 px-6 pt-6">
        
        <div id="p-home" class="page active-page">
            <div class="premium-gradient p-8 rounded-[2.5rem] text-white mb-6 relative overflow-hidden shadow-2xl shadow-blue-100">
                <div class="relative z-10">
                    <div class="flex justify-between items-start mb-2">
                        <span class="text-[9px] font-bold opacity-60 uppercase tracking-[0.2em]">Net Capital Value</span>
                        <i class="fa-solid fa-microchip opacity-30"></i>
                    </div>
                    <h2 class="text-4xl font-extrabold tracking-tighter mb-8 mono" id="v-bal">₨ 0.00</h2>
                    
                    <div class="grid grid-cols-2 gap-4 pt-6 border-t border-white/10">
                        <div>
                            <p class="text-[9px] opacity-50 uppercase font-black">Live Yield</p>
                            <p id="v-profit" class="text-xl font-bold text-emerald-400 ticker-glow mono">₨ 0.00</p>
                        </div>
                        <div class="text-right">
                            <p class="text-[9px] opacity-50 uppercase font-black">Account Rank</p>
                            <p id="tier-tag" class="text-[10px] font-black uppercase text-blue-200">Standard</p>
                        </div>
                    </div>
                </div>
            </div>

            <div class="bento-card p-5 mb-6">
                <div class="flex justify-between items-center mb-2">
                    <span class="text-[9px] font-bold text-slate-400 uppercase tracking-widest">Next Tier Progress</span>
                    <span class="text-[9px] font-bold text-blue-600">45%</span>
                </div>
                <div class="progress-bar"><div class="progress-fill"></div></div>
            </div>

            <div class="grid grid-cols-4 gap-3 mb-8">
                <button onclick="changePage('deposit')" class="flex flex-col items-center gap-2">
                    <div class="w-12 h-12 bento-card flex items-center justify-center text-blue-600 active:bg-blue-50 transition-all shadow-sm"><i class="fa-solid fa-plus text-sm"></i></div>
                    <span class="text-[8px] font-bold text-slate-500 uppercase">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="flex flex-col items-center gap-2">
                    <div class="w-12 h-12 bento-card flex items-center justify-center text-slate-400 active:bg-slate-50 transition-all shadow-sm"><i class="fa-solid fa-paper-plane text-xs"></i></div>
                    <span class="text-[8px] font-bold text-slate-500 uppercase">Payout</span>
                </button>
                <button class="flex flex-col items-center gap-2">
                    <div class="w-12 h-12 bento-card flex items-center justify-center text-emerald-500 active:bg-emerald-50 transition-all shadow-sm"><i class="fa-solid fa-arrows-rotate text-xs"></i></div>
                    <span class="text-[8px] font-bold text-slate-500 uppercase">Compound</span>
                </button>
                <button class="flex flex-col items-center gap-2">
                    <div class="w-12 h-12 bento-card flex items-center justify-center text-amber-500 active:bg-amber-50 transition-all shadow-sm"><i class="fa-solid fa-gift text-xs"></i></div>
                    <span class="text-[8px] font-bold text-slate-500 uppercase">Offers</span>
                </button>
            </div>

            <div class="bg-blue-50/50 p-4 rounded-2xl mb-8 flex items-center gap-3 border border-blue-100">
                <div class="w-2 h-2 bg-blue-500 rounded-full animate-ping"></div>
                <p class="text-[10px] font-bold text-blue-800" id="live-update">Investor Ali just activated "Gold Prime" tier</p>
            </div>

            <div class="flex items-center justify-between mb-6">
                <h3 class="text-xs font-black uppercase tracking-[0.2em] text-slate-400">Wealth Tiers</h3>
                <span class="text-[9px] font-bold text-blue-600">View Strategy</span>
            </div>
            
            <div id="plans-list" class="space-y-4"></div>
        </div>
    </main>

    <nav id="bottom-nav" class="fixed bottom-6 left-6 right-6 h-20 bento-card flex justify-around items-center px-4 shadow-2xl z-[1000] border border-slate-200">
        <button onclick="changePage('home')" id="n-home" class="text-blue-600 flex flex-col items-center gap-1">
            <i class="fa-solid fa-grid-2 text-xl"></i>
            <span class="text-[8px] font-bold uppercase">Terminal</span>
        </button>
        <button onclick="changePage('activity')" id="n-activity" class="text-slate-400 flex flex-col items-center gap-1">
            <i class="fa-solid fa-clock-rotate-left text-xl"></i>
            <span class="text-[8px] font-bold uppercase">Ledger</span>
        </button>
        <button onclick="logout()" class="text-rose-400 flex flex-col items-center gap-1">
            <i class="fa-solid fa-power-off text-xl"></i>
            <span class="text-[8px] font-bold uppercase">Logout</span>
        </button>
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

        let userData = { balance: 0, profit: 0, tierROI: 0, time: Date.now() };

        // Real-time Profit Ticker Logic
        setInterval(() => {
            if(userData.tierROI > 0) {
                const profitPerSec = (userData.balance * (userData.tierROI / 100)) / 86400;
                userData.profit += profitPerSec;
                document.getElementById('v-profit').innerText = "₨ " + userData.profit.toFixed(4);
            }
        }, 1000);

        // Social Proof Updates
        const updates = [
            "User @Asad just withdrew ₨ 1,200",
            "Investor @Sara activated Ruby Elite",
            "System: Weekend Booster is now LIVE!",
            "Withdrawal of ₨ 50,000 processed for @M_Akbar"
        ];
        setInterval(() => {
            document.getElementById('live-update').innerText = updates[Math.floor(Math.random()*updates.length)];
        }, 5000);

        function renderPlans() {
            // Plans from 200 to 500,000
            const plans = [
                { n: "Nano Starter", p: 200, r: 2.5 },
                { n: "Silver Node", p: 5000, r: 5.0 },
                { n: "Gold Prime", p: 15000, r: 7.5 },
                { n: "Vestify Whale", p: 500000, r: 25.0 }
            ];
            const list = document.getElementById('plans-list');
            list.innerHTML = plans.map(p => `
                <div class="bento-card p-6 flex justify-between items-center active:scale-[0.98] transition-all">
                    <div>
                        <h4 class="font-bold text-sm text-slate-800">${p.n}</h4>
                        <p class="text-[10px] text-blue-600 font-bold uppercase tracking-wider">${p.r}% Daily ROI</p>
                    </div>
                    <p class="font-black text-slate-900 mono">₨ ${p.p.toLocaleString()}</p>
                </div>
            `).join('');
        }

        async function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                if(doc.exists) {
                    userData = doc.data();
                    document.getElementById('v-bal').innerText = "₨ " + (userData.balance||0).toLocaleString();
                    document.getElementById('tier-tag').innerText = userData.tierName || "Inactive";
                }
            });
        }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
        }

        window.onload = () => {
            const saved = localStorage.getItem('v_user');
            if(saved) {
                document.getElementById('app-ui').classList.remove('hidden');
                renderPlans();
                syncData(saved);
            }
        };
    </script>
</body>
</html>
