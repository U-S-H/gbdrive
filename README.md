<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify | Institutional Grade Assets</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #020617; color: #f8fafc; }
        .hero-gradient { background: radial-gradient(circle at top right, #1e3a8a 0%, #020617 50%); }
        .glass-panel { background: rgba(15, 23, 42, 0.6); backdrop-filter: blur(20px); border: 1px solid rgba(255, 255, 255, 0.08); border-radius: 32px; }
        .accent-border { border-left: 4px solid #3b82f6; }
        .premium-shadow { box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5); }
        .btn-glow:active { box-shadow: 0 0 20px rgba(59, 130, 246, 0.5); transform: scale(0.97); }
        .page { display: none; animation: slideUp 0.6s cubic-bezier(0.2, 1, 0.3, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body class="min-h-screen flex flex-col hero-gradient">

    <header class="p-6 flex justify-between items-center z-[1000] border-b border-white/5 bg-slate-950/20 backdrop-blur-sm">
        <div class="flex items-center gap-2">
            <div class="w-8 h-8 bg-blue-600 rounded-lg flex items-center justify-center rotate-12 shadow-lg shadow-blue-500/20">
                <i class="fa-solid fa-v text-white font-black -rotate-12"></i>
            </div>
            <span class="text-xl font-extrabold tracking-tighter uppercase italic">VEST<span class="text-blue-500">IFY</span></span>
        </div>
        <button onclick="adminTap()" class="text-slate-500 hover:text-white transition-colors">
            <i class="fa-solid fa-shield-halved"></i>
        </button>
    </header>

    <main id="app-ui" class="flex-1 overflow-y-auto pb-32 px-6 pt-8">
        
        <div id="p-home" class="page active-page">
            <div class="mb-8">
                <h1 class="text-2xl font-bold">Good Day, <span id="u-display-name" class="text-blue-500">Investor</span></h1>
                <p class="text-slate-500 text-xs">Market status: <span class="text-emerald-500 font-bold uppercase tracking-widest">● Operational</span></p>
            </div>

            <div class="glass-panel p-8 premium-shadow mb-8 border-t border-white/10 relative overflow-hidden">
                <div class="absolute top-0 right-0 p-4 opacity-10"><i class="fa-solid fa-chart-line text-8xl"></i></div>
                <p class="text-[10px] text-slate-400 font-bold uppercase tracking-[0.2em] mb-2">Institutional AUM</p>
                <h2 class="text-5xl font-extrabold tracking-tighter mb-8" id="v-bal">₨ 0.00</h2>
                
                <div class="grid grid-cols-2 gap-4 pt-6 border-t border-white/5">
                    <div>
                        <p class="text-[9px] text-slate-500 uppercase font-bold">Total Earnings</p>
                        <p id="v-profit" class="text-xl font-bold text-emerald-400">₨ 0</p>
                    </div>
                    <div class="text-right">
                        <p class="text-[9px] text-slate-500 uppercase font-bold">Security Tier</p>
                        <p id="tier-tag" class="text-xs font-black text-blue-500 uppercase italic">Unverified</p>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-10">
                <button onclick="changePage('wallet')" class="glass-panel p-6 flex flex-col items-center gap-3 active:bg-blue-600/10 btn-glow transition-all">
                    <div class="w-10 h-10 rounded-full bg-blue-500/10 flex items-center justify-center text-blue-500">
                        <i class="fa-solid fa-arrow-down"></i>
                    </div>
                    <span class="text-[10px] font-bold uppercase tracking-widest">Inbound</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass-panel p-6 flex flex-col items-center gap-3 active:bg-rose-600/10 btn-glow transition-all">
                    <div class="w-10 h-10 rounded-full bg-rose-500/10 flex items-center justify-center text-rose-500">
                        <i class="fa-solid fa-paper-plane"></i>
                    </div>
                    <span class="text-[10px] font-bold uppercase tracking-widest">Outbound</span>
                </button>
            </div>

            <div class="flex justify-between items-center mb-6">
                <h3 class="text-sm font-bold uppercase tracking-widest text-slate-400">Elite Portfolios</h3>
                <span class="text-[10px] text-blue-500 font-bold uppercase">View All</span>
            </div>
            <div id="plans-list" class="space-y-4"></div>
        </div>

        <div id="p-wallet" class="page">
            <h2 class="text-xl font-bold mb-8">Asset Funding</h2>
            </div>

    </main>

    <nav id="bottom-nav" class="fixed bottom-8 left-6 right-6 h-20 glass-panel border border-white/10 flex justify-around items-center px-4 shadow-2xl z-[1000]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-1 text-blue-500 transition-all">
            <i class="fa-solid fa-house-chimney text-xl"></i>
            <span class="text-[8px] font-bold uppercase">Terminal</span>
        </button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-1 text-slate-500 transition-all opacity-60">
            <i class="fa-solid fa-clock-rotate-left text-xl"></i>
            <span class="text-[8px] font-bold uppercase">History</span>
        </button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-1 text-slate-500 transition-all opacity-60">
            <i class="fa-solid fa-briefcase text-xl"></i>
            <span class="text-[8px] font-bold uppercase">Company</span>
        </button>
    </nav>

    <section id="auth-ui" class="fixed inset-0 z-[2000] bg-[#020617] flex items-center justify-center p-10">
        <div class="w-full max-w-sm text-center">
            <div class="mb-12">
                <div class="w-16 h-16 bg-blue-600 rounded-2xl flex items-center justify-center rotate-12 mx-auto mb-6 shadow-2xl shadow-blue-500/40">
                    <i class="fa-solid fa-v text-white text-3xl -rotate-12 font-black"></i>
                </div>
                <h1 class="text-3xl font-black tracking-tighter italic">VESTIFY<span class="text-blue-500">.</span></h1>
                <p class="text-slate-500 text-[10px] uppercase tracking-[0.4em] font-bold mt-2">Institutional Grade Custody</p>
            </div>
            <button onclick="loginWithGoogle()" class="w-full bg-white text-slate-900 py-5 rounded-2xl font-black text-[11px] uppercase tracking-widest flex items-center justify-center gap-4 active:scale-95 transition-all shadow-xl">
                <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/action/google.svg" width="20">
                Authorize with Google
            </button>
            <p class="mt-12 text-[8px] text-slate-600 uppercase font-bold tracking-widest">Protected by Enterprise Grade Encryption</p>
        </div>
    </section>

    <script>
        // Use your Vestify Config
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
            { n: "Standard Liquidity", p: 1000, r: 4.5 },
            { n: "Alpha Strategic", p: 5000, r: 7.2 },
            { n: "Institutional Vault", p: 25000, r: 10.5 }
        ];

        async function loginWithGoogle() {
            try {
                const result = await auth.signInWithPopup(provider);
                const user = result.user;
                localStorage.setItem('v_user', user.displayName);
                
                const ref = db.collection("users").doc(user.displayName);
                const doc = await ref.get();
                if(!doc.exists) {
                    await ref.set({ 
                        name: user.displayName, 
                        email: user.email, 
                        balance: 0, 
                        profit: 0, 
                        tierName: "Standard", 
                        time: Date.now() 
                    });
                }
                startApp(user.displayName);
            } catch (error) {
                console.error(error);
            }
        }

        function startApp(name) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('u-display-name').innerText = name.split(' ')[0];
            renderPlans();
            syncData(name);
        }

        function renderPlans() {
            const list = document.getElementById('plans-list');
            list.innerHTML = plans.map(p => `
                <div onclick="buyPlan(${p.p})" class="glass-panel p-6 accent-border flex justify-between items-center active:bg-white/5 transition-all">
                    <div>
                        <h4 class="font-bold text-sm text-slate-200">${p.n}</h4>
                        <p class="text-[9px] text-blue-500 font-black uppercase tracking-widest">${p.r}% APY Yield</p>
                    </div>
                    <div class="text-right">
                        <p class="font-bold text-white text-sm italic">₨ ${p.p.toLocaleString()}</p>
                        <i class="fa-solid fa-chevron-right text-[10px] text-slate-700"></i>
                    </div>
                </div>
            `).join('');
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                if(doc.exists) {
                    const u = doc.data();
                    document.getElementById('v-bal').innerText = "₨ " + (u.balance||0).toLocaleString();
                    document.getElementById('v-profit').innerText = "₨ " + (u.profit||0).toLocaleString();
                    document.getElementById('tier-tag').innerText = u.tierName;
                }
            });
        }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.querySelectorAll('nav button').forEach(b => {
                b.classList.add('opacity-60', 'text-slate-500');
                b.classList.remove('text-blue-500');
            });
            document.getElementById('p-'+p).classList.add('active-page');
            document.getElementById('n-'+p)?.classList.remove('opacity-60', 'text-slate-500');
            document.getElementById('n-'+p)?.classList.add('text-blue-500');
        }

        window.onload = () => {
            const saved = localStorage.getItem('v_user');
            if(saved) startApp(saved);
        };
    </script>
</body>
</html>
