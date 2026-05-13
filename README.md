<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify | Asset Management</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap');
        
        body { 
            font-family: 'Plus Jakarta Sans', sans-serif; 
            background-color: #F8FAFC; 
            color: #0F172A; 
        }

        .premium-card {
            background: #FFFFFF;
            border: 1px solid rgba(0, 0, 0, 0.05);
            border-radius: 28px;
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.03);
        }

        .blue-gradient-bg {
            background: linear-gradient(135deg, #2563EB 0%, #1D4ED8 100%);
        }

        .nav-btn { color: #64748B; transition: all 0.3s ease; }
        .nav-active { color: #2563EB; }

        .page { display: none; animation: fadeIn 0.4s ease-out; }
        .active-page { display: block; }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* Modern Custom Button */
        .btn-action {
            background: #FFFFFF;
            border: 1px solid #E2E8F0;
            transition: all 0.2s;
        }
        .btn-action:active { transform: scale(0.96); background: #F1F5F9; }
    </style>
</head>
<body class="min-h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[2000] bg-white flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <div class="mb-12">
                <div class="w-16 h-16 bg-blue-600 rounded-2xl flex items-center justify-center mx-auto mb-6 shadow-xl shadow-blue-200">
                    <i class="fa-solid fa-bolt-lightning text-white text-2xl"></i>
                </div>
                <h1 class="text-3xl font-extrabold tracking-tighter italic">VESTIFY</h1>
                <p class="text-[10px] tracking-[0.3em] text-slate-400 font-bold uppercase mt-2">Smart Investment Portal</p>
            </div>
            <button onclick="loginWithGoogle()" class="w-full bg-slate-900 text-white py-4 rounded-2xl font-bold text-sm flex items-center justify-center gap-3 active:scale-95 transition-all shadow-lg">
                <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/action/google.svg" width="18" class="bg-white rounded-full p-0.5">
                Sign in with Google
            </button>
            <p class="mt-8 text-[9px] text-slate-400 font-medium">By continuing, you agree to our Terms of Service.</p>
        </div>
    </section>

    <header class="p-6 flex justify-between items-center bg-white/80 backdrop-blur-md sticky top-0 z-[500] border-b border-slate-100">
        <span class="font-extrabold text-xl tracking-tighter italic text-blue-600">VESTIFY</span>
        <button onclick="adminTap()" class="w-10 h-10 rounded-full bg-slate-50 flex items-center justify-center border border-slate-200">
            <i class="fa-solid fa-fingerprint text-slate-400"></i>
        </button>
    </header>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32 p-6">
        
        <div id="p-home" class="page active-page">
            <div class="mb-6">
                <h2 class="text-sm font-bold text-slate-400 uppercase tracking-widest">Portfolio Overview</h2>
            </div>

            <div class="blue-gradient-bg p-8 rounded-[2.5rem] mb-8 text-white shadow-xl shadow-blue-100 relative overflow-hidden">
                <div class="relative z-10">
                    <p class="text-[10px] font-bold opacity-80 uppercase tracking-widest mb-1">Total Balance</p>
                    <h3 class="text-4xl font-extrabold tracking-tight mb-8" id="v-bal">₨ 0</h3>
                    
                    <div class="flex justify-between items-center border-t border-white/10 pt-6">
                        <div>
                            <p class="text-[9px] opacity-70 uppercase font-bold">Today's Profit</p>
                            <p id="v-profit" class="text-lg font-bold">₨ 0</p>
                        </div>
                        <div class="text-right">
                            <p class="text-[9px] opacity-70 uppercase font-bold">Active Tier</p>
                            <p id="tier-tag" class="text-xs font-bold bg-white/20 px-3 py-1 rounded-full">Scanning...</p>
                        </div>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-10">
                <button onclick="changePage('wallet')" class="premium-card py-6 flex flex-col items-center gap-2 active:bg-slate-50">
                    <i class="fa-solid fa-plus-circle text-blue-600 text-xl"></i>
                    <span class="text-[10px] font-bold uppercase text-slate-600">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="premium-card py-6 flex flex-col items-center gap-2 active:bg-slate-50">
                    <i class="fa-solid fa-arrow-up-right-from-square text-slate-400 text-xl"></i>
                    <span class="text-[10px] font-bold uppercase text-slate-600">Withdraw</span>
                </button>
            </div>

            <h3 class="text-xs font-extrabold uppercase tracking-[0.2em] text-slate-400 mb-5">Investment Plans</h3>
            <div id="plans-list" class="space-y-4"></div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-20 premium-card flex justify-around items-center px-4 shadow-2xl z-[1000]">
        <button onclick="changePage('home')" id="n-home" class="nav-btn nav-active flex flex-col items-center gap-1">
            <i class="fa-solid fa-house-simple text-xl"></i>
            <span class="text-[8px] font-bold uppercase">Home</span>
        </button>
        <button onclick="changePage('activity')" id="n-activity" class="nav-btn flex flex-col items-center gap-1">
            <i class="fa-solid fa-chart-pie text-xl"></i>
            <span class="text-[8px] font-bold uppercase">Stats</span>
        </button>
        <button onclick="logout()" class="nav-btn flex flex-col items-center gap-1 text-rose-400">
            <i class="fa-solid fa-sign-out-alt text-xl"></i>
            <span class="text-[8px] font-bold uppercase">Exit</span>
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
        const provider = new firebase.auth.GoogleAuthProvider();

        const plans = [
            { n: "Bronze Package", p: 1000, r: 3.5 },
            { n: "Silver Growth", p: 5000, r: 5.0 },
            { n: "Gold Institutional", p: 25000, r: 8.5 },
            { n: "Black Asset", p: 100000, r: 12.0 }
        ];

        async function loginWithGoogle() {
            try {
                const res = await auth.signInWithPopup(provider);
                const name = res.user.displayName;
                localStorage.setItem('v_user', name);
                const ref = db.collection("users").doc(name);
                const d = await ref.get();
                if(!d.exists) {
                    await ref.set({ name, balance: 0, profit: 0, tierName: "Inactive", time: Date.now() });
                }
                initApp(name);
            } catch (e) { alert("Login failed!"); }
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
                <div class="premium-card p-6 flex justify-between items-center active:scale-[0.98] transition-all border-l-4 border-blue-500">
                    <div>
                        <h4 class="font-bold text-sm text-slate-800">${p.n}</h4>
                        <p class="text-[10px] text-blue-600 font-bold uppercase tracking-wider">${p.r}% Daily Return</p>
                    </div>
                    <p class="font-extrabold text-slate-900">₨ ${p.p.toLocaleString()}</p>
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
            document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('nav-active'));
            document.getElementById('p-'+p).classList.add('active-page');
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
