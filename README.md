<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify | Next-Gen Asset Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300;500;700&family=Inter:wght@400;700&display=swap');
        
        :root {
            --primary: #00f2ff;
            --bg-deep: #050505;
            --card-bg: rgba(18, 18, 18, 0.8);
        }

        body { 
            font-family: 'Inter', sans-serif; 
            background-color: var(--bg-deep); 
            color: #ffffff;
            margin: 0;
            overflow-x: hidden;
        }

        .heading-font { font-family: 'Space Grotesk', sans-serif; }

        /* Cloud-Node Style Background */
        .grid-bg {
            background-image: linear-gradient(rgba(0, 242, 255, 0.03) 1px, transparent 1px), 
                              linear-gradient(90deg, rgba(0, 242, 255, 0.03) 1px, transparent 1px);
            background-size: 30px 30px;
            background-position: center center;
        }

        .node-glow {
            box-shadow: 0 0 20px rgba(0, 242, 255, 0.15);
            border: 1px solid rgba(0, 242, 255, 0.1);
        }

        .glass-module {
            background: var(--card-bg);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.05);
            border-radius: 20px;
        }

        .cyan-text { color: var(--primary); text-shadow: 0 0 10px rgba(0, 242, 255, 0.5); }
        
        .btn-node {
            background: linear-gradient(90deg, #00f2ff, #0072ff);
            color: #000;
            font-weight: 800;
            text-transform: uppercase;
            letter-spacing: 1px;
            transition: 0.3s all ease;
        }

        .btn-node:active { transform: scale(0.95); box-shadow: 0 0 30px rgba(0, 242, 255, 0.4); }

        .page { display: none; animation: nodeAppear 0.4s ease; }
        .active-page { display: block; }

        @keyframes nodeAppear {
            from { opacity: 0; filter: blur(10px); transform: scale(0.98); }
            to { opacity: 1; filter: blur(0); transform: scale(1); }
        }
    </style>
</head>
<body class="min-h-screen flex flex-col grid-bg">

    <section id="auth-ui" class="fixed inset-0 z-[2000] bg-black flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <div class="mb-12 relative">
                <div class="absolute -top-10 left-1/2 -translate-x-1/2 w-40 h-40 bg-cyan-500/10 blur-[80px] rounded-full"></div>
                <h1 class="heading-font text-5xl font-bold tracking-tighter italic cyan-text uppercase">VESTIFY</h1>
                <p class="text-[8px] tracking-[0.6em] text-slate-500 font-bold uppercase mt-2">Decentralized Asset Node</p>
            </div>
            <div class="glass-module p-8 node-glow">
                <button onclick="loginWithGoogle()" class="w-full bg-white text-black py-4 rounded-xl font-black text-xs uppercase flex items-center justify-center gap-3 active:scale-95 transition-all">
                    <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/action/google.svg" width="18">
                    Sync via Google
                </button>
            </div>
        </div>
    </section>

    <header class="p-6 flex justify-between items-center border-b border-white/5 bg-black/40 backdrop-blur-md">
        <div class="flex items-center gap-2">
            <div class="w-2 h-2 rounded-full bg-cyan-400 animate-pulse"></div>
            <span class="heading-font font-bold text-lg tracking-widest cyan-text">VESTIFY.SYS</span>
        </div>
        <div class="text-[10px] text-slate-500 font-mono tracking-tighter" id="node-id">NODE_7733_ACTIVE</div>
    </header>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32 p-5">
        
        <div id="p-home" class="page active-page">
            <div class="glass-module p-8 mb-6 node-glow bg-gradient-to-br from-cyan-500/5 to-transparent">
                <div class="flex justify-between items-start mb-4">
                    <p class="text-[9px] font-bold text-slate-400 uppercase tracking-widest italic">Encrypted Balance</p>
                    <i class="fa-solid fa-microchip text-cyan-500/50"></i>
                </div>
                <h2 class="text-5xl font-light heading-font mb-6 tracking-tighter" id="v-bal">₨ 0</h2>
                
                <div class="grid grid-cols-2 gap-4 border-t border-white/5 pt-6">
                    <div>
                        <p class="text-[8px] text-slate-500 uppercase font-bold">Accumulated ROI</p>
                        <p id="v-profit" class="text-xl font-bold cyan-text">₨ 0</p>
                    </div>
                    <div class="text-right">
                        <p class="text-[8px] text-slate-500 uppercase font-bold">Protocol Status</p>
                        <p id="tier-tag" class="text-[10px] font-black uppercase text-white/80 italic">Standby</p>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="changePage('wallet')" class="glass-module py-6 flex flex-col items-center gap-2 border-b-2 border-cyan-500 active:bg-cyan-500/5">
                    <i class="fa-solid fa-cloud-arrow-up text-cyan-400"></i>
                    <span class="text-[9px] font-black uppercase tracking-widest">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass-module py-6 flex flex-col items-center gap-2 border-b-2 border-blue-500 active:bg-blue-500/5">
                    <i class="fa-solid fa-vault text-blue-400"></i>
                    <span class="text-[9px] font-black uppercase tracking-widest">Withdraw</span>
                </button>
            </div>

            <h3 class="text-[10px] font-bold uppercase tracking-[0.3em] text-slate-500 mb-4 px-2">Active Asset Tiers</h3>
            <div id="plans-list" class="space-y-3"></div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-16 glass-module border border-white/10 flex justify-around items-center px-4 shadow-2xl z-[1000]">
        <button onclick="changePage('home')" id="n-home" class="cyan-text transition-all"><i class="fa-solid fa-terminal"></i></button>
        <button onclick="changePage('activity')" id="n-activity" class="text-slate-500 opacity-60"><i class="fa-solid fa-database"></i></button>
        <button onclick="logout()" class="text-slate-500 opacity-60"><i class="fa-solid fa-power-off"></i></button>
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
            { n: "Node Basic", p: 1000, r: 4.0 },
            { n: "Core Power", p: 5000, r: 6.5 },
            { n: "Grid Master", p: 20000, r: 9.2 },
            { n: "Cloud Infinity", p: 75000, r: 15.0 }
        ];

        async function loginWithGoogle() {
            try {
                const res = await auth.signInWithPopup(provider);
                const name = res.user.displayName;
                localStorage.setItem('v_user', name);
                
                const ref = db.collection("users").doc(name);
                const d = await ref.get();
                if(!d.exists) {
                    await ref.set({ name, balance: 0, profit: 0, tierName: "Node Inactive", time: Date.now() });
                }
                initApp(name);
            } catch (e) { alert("Authorization Error"); }
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
                <div class="glass-module p-6 flex justify-between items-center active:bg-white/5 border-l-2 border-transparent hover:border-cyan-500 transition-all">
                    <div>
                        <h4 class="font-bold text-xs uppercase tracking-tighter text-slate-200">${p.n}</h4>
                        <p class="text-[9px] cyan-text font-black uppercase tracking-widest">${p.r}% ROI / 24H</p>
                    </div>
                    <p class="heading-font font-bold text-white">₨ ${p.p.toLocaleString()}</p>
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
            document.querySelectorAll('nav button').forEach(b => b.classList.add('opacity-60', 'text-slate-500'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.getElementById('n-'+p)?.classList.remove('opacity-60', 'text-slate-500');
            document.getElementById('n-'+p)?.classList.add('cyan-text');
        }

        window.onload = () => {
            const saved = localStorage.getItem('v_user');
            if(saved) initApp(saved);
        };
        
        function logout() { localStorage.clear(); location.reload(); }
    </script>
</body>
</html>
