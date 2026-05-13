<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify | Elite Institutional Assets</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap');
        
        body { 
            font-family: 'Plus Jakarta Sans', sans-serif; 
            background-color: #fcfcfd; 
            color: #101828; 
            overflow-x: hidden;
        }

        /* Modern Glass Effect */
        .glass-card {
            background: rgba(255, 255, 255, 0.8);
            backdrop-filter: blur(10px);
            border: 1px solid #eaecf0;
            border-radius: 24px;
        }

        /* Premium Gradient for main balance */
        .main-gradient {
            background: linear-gradient(135deg, #101828 0%, #1d2939 100%);
            box-shadow: 0 20px 48px -12px rgba(16, 24, 40, 0.2);
        }

        .btn-modern {
            background: #ffffff;
            border: 1px solid #d0d5dd;
            border-radius: 16px;
            transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .btn-modern:active {
            transform: scale(0.96);
            background: #f9fafb;
        }

        .page { display: none; animation: slideUp 0.6s cubic-bezier(0.16, 1, 0.3, 1); }
        .active-page { display: block; }

        @keyframes slideUp {
            from { opacity: 0; transform: translateY(30px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* Minimalist Nav */
        nav {
            border-top: 1px solid #f2f4f7;
            background: rgba(255, 255, 255, 0.9);
            backdrop-filter: blur(12px);
        }
        
        .active-tab {
            color: #2e90fa;
        }
    </style>
</head>
<body class="min-h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[2000] bg-white flex flex-col items-center justify-center p-10">
        <div class="w-full max-w-sm">
            <div class="text-center mb-12">
                <div class="inline-flex items-center justify-center w-16 h-16 rounded-2xl bg-blue-600 shadow-xl shadow-blue-100 mb-6">
                    <i class="fa-solid fa-vault text-white text-2xl"></i>
                </div>
                <h1 class="text-3xl font-extrabold tracking-tighter italic">VESTIFY</h1>
                <p class="text-slate-400 text-xs font-bold uppercase tracking-widest mt-2">Next-Gen Asset Management</p>
            </div>
            <button onclick="loginWithGoogle()" class="w-full flex items-center justify-center gap-3 bg-[#101828] text-white py-4 px-6 rounded-2xl font-bold active:scale-95 transition-all shadow-lg">
                <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/action/google.svg" width="20" class="bg-white rounded-full p-0.5">
                Continue with Google
            </button>
            <p class="text-center mt-10 text-[10px] text-slate-400 font-medium">Secured by Enterprise Grade Encryption</p>
        </div>
    </section>

    <header class="px-6 py-6 flex justify-between items-center sticky top-0 z-[1000] bg-white/70 backdrop-blur-md">
        <div class="flex items-center gap-2">
            <span class="font-extrabold text-2xl tracking-tighter italic text-[#101828]">V<span class="text-blue-600">.</span></span>
        </div>
        <button onclick="adminTap()" class="w-11 h-11 rounded-full bg-[#f9fafb] flex items-center justify-center border border-[#eaecf0] active:scale-90 transition-all">
            <i class="fa-solid fa-fingerprint text-slate-400 text-lg"></i>
        </button>
    </header>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32 px-6">
        
        <div id="p-home" class="page active-page pt-4">
            <div class="mb-8">
                <h2 class="text-2xl font-bold tracking-tight">Portfolio Summary</h2>
                <p class="text-slate-400 text-xs font-medium mt-1">Status: <span class="text-green-500 font-bold">Encrypted</span></p>
            </div>

            <div class="main-gradient p-8 rounded-[2.5rem] text-white relative overflow-hidden mb-8">
                <div class="absolute -right-10 -bottom-10 w-40 h-40 bg-blue-500/10 rounded-full blur-3xl"></div>
                <div class="relative z-10">
                    <span class="text-[10px] font-bold opacity-60 uppercase tracking-[0.2em]">Total Available Asset</span>
                    <h3 class="text-5xl font-extrabold tracking-tighter mt-1 mb-10" id="v-bal">₨ 0</h3>
                    
                    <div class="grid grid-cols-2 gap-4 pt-6 border-t border-white/10">
                        <div>
                            <p class="text-[9px] opacity-50 uppercase font-black">Net Profits</p>
                            <p id="v-profit" class="text-xl font-bold text-blue-400">₨ 0</p>
                        </div>
                        <div class="text-right">
                            <p class="text-[9px] opacity-50 uppercase font-black">Membership</p>
                            <p id="tier-tag" class="text-xs font-bold uppercase italic text-slate-200">Processing...</p>
                        </div>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-10">
                <button onclick="changePage('deposit')" class="btn-modern p-6 flex flex-col items-center gap-3">
                    <div class="w-10 h-10 rounded-full bg-blue-50 flex items-center justify-center text-blue-600">
                        <i class="fa-solid fa-plus text-lg"></i>
                    </div>
                    <span class="text-[10px] font-bold uppercase tracking-widest text-slate-600">Add Funds</span>
                </button>
                <button onclick="changePage('withdraw')" class="btn-modern p-6 flex flex-col items-center gap-3">
                    <div class="w-10 h-10 rounded-full bg-slate-50 flex items-center justify-center text-slate-400">
                        <i class="fa-solid fa-arrow-up-right-from-square text-lg"></i>
                    </div>
                    <span class="text-[10px] font-bold uppercase tracking-widest text-slate-600">Withdraw</span>
                </button>
            </div>

            <div class="flex items-center justify-between mb-6">
                <h3 class="text-xs font-black uppercase tracking-[0.2em] text-slate-400">Premium Portfolios</h3>
                <span class="text-[9px] font-bold text-blue-600 px-2 py-1 bg-blue-50 rounded-lg">LIVE PLANS</span>
            </div>
            
            <div id="plans-list" class="space-y-4"></div>
        </div>

        <div id="p-activity" class="page">
            <h2 class="text-2xl font-bold tracking-tight mb-8">Asset History</h2>
            <div id="activity-list" class="space-y-3"></div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-0 left-0 w-full px-8 py-5 flex justify-around items-center z-[1000]">
        <button onclick="changePage('home')" id="n-home" class="active-tab flex flex-col items-center gap-1 transition-all">
            <i class="fa-solid fa-square-poll-vertical text-2xl"></i>
            <span class="text-[9px] font-bold uppercase">Assets</span>
        </button>
        <button onclick="changePage('activity')" id="n-activity" class="text-slate-400 flex flex-col items-center gap-1 transition-all">
            <i class="fa-solid fa-receipt text-2xl"></i>
            <span class="text-[9px] font-bold uppercase">Ledger</span>
        </button>
        <button onclick="logout()" class="text-rose-400 flex flex-col items-center gap-1 transition-all">
            <i class="fa-solid fa-power-off text-2xl"></i>
            <span class="text-[9px] font-bold uppercase">End</span>
        </button>
    </nav>

    <script>
        // Use your Firebase config
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
            // Special Tiers
            { n: "Weekend Booster", p: 1200, r: 10.0, cat: "Special", desc: "Limited Offer" },
            { n: "Founders Asset", p: 8000, r: 12.5, cat: "Special", desc: "Elite Choice" },
            // Regular Tiers
            { n: "Nano Node", p: 200, r: 2.5, cat: "Regular" },
            { n: "Micro Yield", p: 500, r: 3.0, cat: "Regular" },
            { n: "Standard Core", p: 1000, r: 3.5, cat: "Regular" },
            { n: "Bronze Venture", p: 5000, r: 5.0, cat: "Regular" },
            { n: "Gold Institutional", p: 50000, r: 8.5, cat: "Regular" }
        ];

        async function loginWithGoogle() {
            const res = await auth.signInWithPopup(provider);
            const name = res.user.displayName;
            localStorage.setItem('v_user', name);
            const ref = db.collection("users").doc(name);
            const d = await ref.get();
            if(!d.exists) await ref.set({ name, balance: 0, profit: 0, tierROI: 0, tierName: "None", time: Date.now() });
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
            let html = '';

            plans.forEach(p => {
                const isSpecial = p.cat === 'Special';
                html += `
                <div onclick="buyPlan(${p.p}, ${p.r}, '${p.n}')" class="glass-card p-6 flex justify-between items-center active:scale-[0.98] transition-all ${isSpecial ? 'border-l-4 border-blue-600' : ''}">
                    <div>
                        ${isSpecial ? `<span class="text-[8px] font-black bg-blue-600 text-white px-2 py-0.5 rounded-full uppercase mb-2 inline-block">${p.desc}</span>` : ''}
                        <h4 class="font-bold text-sm text-slate-800 tracking-tight">${p.n}</h4>
                        <p class="text-[10px] text-blue-600 font-bold mt-1">${p.r}% Daily Payout</p>
                    </div>
                    <div class="text-right">
                        <p class="font-extrabold text-[#101828]">₨ ${p.p.toLocaleString()}</p>
                        <i class="fa-solid fa-chevron-right text-[10px] text-slate-300 mt-1"></i>
                    </div>
                </div>`;
            });
            list.innerHTML = html;
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
            document.querySelectorAll('nav button').forEach(b => b.classList.add('text-slate-400'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.getElementById('n-'+p)?.classList.remove('text-slate-400');
            document.getElementById('n-'+p)?.classList.add('active-tab');
        }

        window.onload = () => {
            const saved = localStorage.getItem('v_user');
            if(saved) initApp(saved);
        };

        function logout() { localStorage.clear(); location.reload(); }
    </script>
</body>
</html>
