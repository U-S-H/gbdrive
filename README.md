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
    <title>Vestify Elite | Global Financial Terminal</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&family=JetBrains+Mono:wght@500&display=swap');
        
        :root { --primary: #2563eb; --bg: #fcfcfd; --card: #ffffff; --text: #101828; }
        .dark-mode { --bg: #0f172a; --card: #1e293b; --text: #f8fafc; }

        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: var(--bg); color: var(--text); transition: 0.3s; overflow-x: hidden; }
        .mono { font-family: 'JetBrains Mono', monospace; }

        .bento-card { background: var(--card); border: 1px solid rgba(0,0,0,0.05); border-radius: 28px; box-shadow: 0 4px 20px -5px rgba(0,0,0,0.02); }
        .premium-gradient { background: linear-gradient(135deg, #1e293b 0%, #0f172a 100%); }

        /* Spin Wheel Style */
        #wheel-container { position: relative; width: 240px; height: 240px; margin: 20px auto; }
        #wheel { width: 100%; height: 100%; border-radius: 50%; border: 8px solid #2563eb; transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); box-shadow: 0 10px 30px rgba(37, 99, 235, 0.2); }
        .wheel-pointer { position: absolute; top: -15px; left: 50%; transform: translateX(-50%); width: 25px; height: 30px; background: #ef4444; clip-path: polygon(50% 100%, 0 0, 100% 0); z-index: 20; border-radius: 4px; }

        /* Animations */
        .page { display: none; animation: slideUp 0.5s cubic-bezier(0.16, 1, 0.3, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }

        .btn-action { transition: all 0.2s ease; }
        .btn-action:active { transform: scale(0.95); }
        
        /* Hide Scrollbar */
        .no-scrollbar::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="min-h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[3000] bg-white flex flex-col items-center justify-center p-10">
        <div class="w-full max-w-sm text-center">
            <div class="w-20 h-20 bg-blue-600 rounded-3xl flex items-center justify-center mx-auto mb-6 shadow-2xl shadow-blue-100 rotate-6">
                <i class="fa-solid fa-vault text-white text-3xl"></i>
            </div>
            <h1 class="text-4xl font-black tracking-tighter italic text-slate-900">VESTIFY</h1>
            <p class="text-slate-400 text-[10px] font-bold uppercase tracking-[0.4em] mt-3 mb-12">Institutional Grade Node</p>
            <button onclick="loginWithGoogle()" class="w-full bg-[#0f172a] text-white py-5 rounded-2xl font-bold flex items-center justify-center gap-3 active:scale-95 transition-all">
                <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/action/google.svg" width="20" class="bg-white rounded-full p-0.5">
                Terminal Sync
            </button>
        </div>
    </section>

    <header class="px-6 py-5 flex justify-between items-center sticky top-0 z-[1000] bg-inherit/80 backdrop-blur-md">
        <div class="flex items-center gap-2">
            <span class="font-black text-2xl tracking-tighter italic text-blue-600">V.</span>
            <div class="h-4 w-[1px] bg-slate-200 mx-1"></div>
            <span id="rank-badge" class="text-[8px] font-black uppercase tracking-widest text-slate-400">Standard Member</span>
        </div>
        <div class="flex items-center gap-4">
            <button onclick="toggleTheme()" class="text-slate-400"><i class="fa-solid fa-circle-half-stroke"></i></button>
            <div class="w-10 h-10 rounded-full bg-slate-100 flex items-center justify-center border border-slate-200">
                <i class="fa-solid fa-fingerprint text-slate-300"></i>
            </div>
        </div>
    </header>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32 px-6">
        
        <div id="p-home" class="page active-page">
            
            <div class="premium-gradient p-8 rounded-[2.8rem] text-white mb-8 relative overflow-hidden shadow-2xl shadow-blue-900/20">
                <div class="absolute -right-10 -top-10 w-40 h-40 bg-blue-500/10 rounded-full blur-3xl"></div>
                <div class="relative z-10">
                    <div class="flex justify-between items-center mb-1">
                        <p class="text-[9px] font-bold opacity-50 uppercase tracking-[0.2em]">Total Available Asset</p>
                        <div class="flex items-center gap-1.5 bg-emerald-500/20 px-2 py-0.5 rounded-full">
                            <div class="w-1 h-1 bg-emerald-400 rounded-full animate-pulse"></div>
                            <span class="text-[7px] font-bold text-emerald-400 uppercase">Live</span>
                        </div>
                    </div>
                    <h2 class="text-4xl font-extrabold tracking-tighter mb-10 mono" id="v-bal">₨ 0.00</h2>
                    
                    <div class="grid grid-cols-2 gap-4 pt-6 border-t border-white/5">
                        <div>
                            <p class="text-[8px] opacity-40 uppercase font-black">Yield Ticker</p>
                            <p id="v-profit" class="text-lg font-bold text-emerald-400 mono">₨ 0.0000</p>
                        </div>
                        <div class="text-right">
                            <p class="text-[8px] opacity-40 uppercase font-black">Daily Reward</p>
                            <button onclick="claimBonus()" id="bonus-btn" class="text-[10px] font-black text-blue-300 uppercase hover:text-white transition-all">Claim ₨ 5</button>
                        </div>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-4 gap-3 mb-8">
                <button class="flex flex-col items-center gap-2 btn-action">
                    <div class="w-12 h-12 bento-card flex items-center justify-center text-blue-600"><i class="fa-solid fa-plus text-sm"></i></div>
                    <span class="text-[8px] font-bold text-slate-400 uppercase">Deposit</span>
                </button>
                <button class="flex flex-col items-center gap-2 btn-action">
                    <div class="w-12 h-12 bento-card flex items-center justify-center text-slate-400"><i class="fa-solid fa-arrow-up-right-from-square text-xs"></i></div>
                    <span class="text-[8px] font-bold text-slate-400 uppercase">Payout</span>
                </button>
                <button onclick="changePage('spin')" class="flex flex-col items-center gap-2 btn-action">
                    <div class="w-12 h-12 bento-card flex items-center justify-center text-amber-500 bg-amber-50/50"><i class="fa-solid fa-dharmachakra text-sm"></i></div>
                    <span class="text-[8px] font-bold text-slate-400 uppercase">Spin</span>
                </button>
                <button class="flex flex-col items-center gap-2 btn-action">
                    <div class="w-12 h-12 bento-card flex items-center justify-center text-emerald-500"><i class="fa-solid fa-users text-sm"></i></div>
                    <span class="text-[8px] font-bold text-slate-400 uppercase">Team</span>
                </button>
            </div>

            <h3 class="text-xs font-black uppercase tracking-[0.3em] text-slate-400 mb-6">Investment Terminal</h3>
            <div id="plans-list" class="space-y-6"></div>
        </div>

        <div id="p-spin" class="page text-center pt-6">
            <h2 class="text-2xl font-black tracking-tighter mb-2">Lucky Terminal</h2>
            <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-10">1 Free Spin Daily • ₨ 20 Paid Spin</p>
            
            <div id="wheel-container">
                <div class="wheel-pointer"></div>
                <img id="wheel" src="https://i.ibb.co/3Ym9G2F/wheel-bg.png" alt="wheel">
            </div>

            <button onclick="handleSpin()" id="spin-btn" class="mt-12 w-full max-w-xs bg-blue-600 text-white py-5 rounded-[2rem] font-black text-xs uppercase tracking-[0.2em] shadow-xl shadow-blue-100 active:scale-95 transition-all">
                Initiate Spin
            </button>
            <p class="mt-4 text-[9px] font-bold text-slate-400 italic">Wins are automatically credited to your vault.</p>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-20 bento-card flex justify-around items-center px-6 shadow-2xl z-[2000] border-slate-100">
        <button onclick="changePage('home')" id="n-home" class="text-blue-600 flex flex-col items-center gap-1"><i class="fa-solid fa-house-chimney text-xl"></i><span class="text-[8px] font-bold uppercase">Vault</span></button>
        <button onclick="changePage('spin')" id="n-spin" class="text-slate-400 flex flex-col items-center gap-1"><i class="fa-solid fa-dharmachakra text-xl"></i><span class="text-[8px] font-bold uppercase">Games</span></button>
        <button onclick="logout()" class="text-rose-400 flex flex-col items-center gap-1"><i class="fa-solid fa-power-off text-xl"></i><span class="text-[8px] font-bold uppercase">Exit</span></button>
    </nav>

    <script>
        // --- Firebase Config ---
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

        // --- Auth Logic ---
        async function loginWithGoogle() {
            const res = await auth.signInWithPopup(provider);
            const name = res.user.displayName;
            localStorage.setItem('v_user', name);
            const ref = db.collection("users").doc(name);
            const doc = await ref.get();
            if(!doc.exists) {
                await ref.set({ name, balance: 0, profit: 0, tierROI: 0, tierName: "Standard", lastBonus: 0, lastSpin: 0 });
            }
            initApp(name);
        }

        function initApp(name) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
            syncData(name);
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance||0).toLocaleString();
                document.getElementById('rank-badge').innerText = userObj.balance > 50000 ? "Diamond VIP" : "Standard Member";
                startTicker();
            });
        }

        // --- Core Features ---
        function startTicker() {
            if(userObj.tierROI > 0) {
                setInterval(() => {
                    const profitPerSec = (userObj.balance * (userObj.tierROI / 100)) / 86400;
                    userObj.profit = (userObj.profit || 0) + profitPerSec;
                    document.getElementById('v-profit').innerText = "₨ " + userObj.profit.toFixed(6);
                }, 1000);
            }
        }

        async function claimBonus() {
            const now = Date.now();
            const cooldown = 24 * 60 * 60 * 1000;
            if (now - userObj.lastBonus < cooldown) {
                alert("Sweetie, agla bonus 24 ghante baad milega!");
                return;
            }
            await db.collection("users").doc(userObj.name).update({
                balance: userObj.balance + 5,
                lastBonus: now
            });
            alert("Mubarak ho! ₨ 5 add ho gaye.");
        }

        async function handleSpin() {
            if (isSpinning) return;
            const now = Date.now();
            const cooldown = 24 * 60 * 60 * 1000;
            let isPaid = false;

            if (now - userObj.lastSpin < cooldown) {
                if (userObj.balance < 20) return alert("Paid spin ke liye ₨ 20 chahiye!");
                if (!confirm("Aaj ka free spin ho chuka hai. ₨ 20 pay karein?")) return;
                isPaid = true;
            }

            isSpinning = true;
            if (isPaid) {
                await db.collection("users").doc(userObj.name).update({ balance: userObj.balance - 20 });
            }

            const wheel = document.getElementById('wheel');
            const deg = Math.floor(Math.random() * 3600) + 1440;
            wheel.style.transform = `rotate(${deg}deg)`;

            setTimeout(async () => {
                isSpinning = false;
                const rewards = [0, 5, 10, 50, 100, 2, 0];
                const win = rewards[Math.floor(Math.random() * rewards.length)];
                
                let updateData = { balance: userObj.balance + win };
                if (!isPaid) updateData.lastSpin = now;
                
                await db.collection("users").doc(userObj.name).update(updateData);
                alert(win > 0 ? `BINGO! ₨ ${win} Jeet liye!` : "Oh ho! Phir try karein.");
                wheel.style.transform = "rotate(0deg)";
            }, 4000);
        }

        // --- UI Logic ---
        function renderPlans() {
            const plans = [
                { n: "Quantum Starter", p: 200, r: 2.5, img: "https://images.unsplash.com/photo-1639762681485-074b7f938ba0?w=400" },
                { n: "Titanium Node", p: 10000, r: 7.5, img: "https://images.unsplash.com/photo-1621761191319-c6fb62004040?w=400" }
            ];
            document.getElementById('plans-list').innerHTML = plans.map(p => `
                <div class="bento-card overflow-hidden group">
                    <img src="${p.img}" class="h-32 w-full object-cover brightness-75 group-hover:brightness-100 transition-all">
                    <div class="p-6">
                        <div class="flex justify-between items-start mb-4">
                            <div><h4 class="font-bold text-slate-800">${p.n}</h4><p class="text-[9px] text-blue-600 font-black">${p.r}% DAILY</p></div>
                            <p class="font-black text-slate-900">₨ ${p.p.toLocaleString()}</p>
                        </div>
                        <button onclick="buyPlan(${p.p}, ${p.r}, '${p.n}')" class="w-full bg-slate-900 text-white py-4 rounded-2xl font-black text-[10px] uppercase tracking-widest">Activate Node</button>
                    </div>
                </div>
            `).join('');
        }

        async function buyPlan(p, r, n) {
            if(userObj.balance < p) return alert("Paisa kam hai sweetie!");
            await db.collection("users").doc(userObj.name).update({
                balance: userObj.balance - p,
                tierROI: r,
                tierName: n,
                profit: 0
            });
            alert("Plan Activated!");
        }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.querySelectorAll('nav button').forEach(b => b.classList.replace('text-blue-600', 'text-slate-400'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.getElementById('n-'+p).classList.replace('text-slate-400', 'text-blue-600');
        }

        function toggleTheme() { document.body.classList.toggle('dark-mode'); }
        function logout() { localStorage.clear(); location.reload(); }
        window.onload = () => { if(localStorage.getItem('v_user')) initApp(localStorage.getItem('v_user')); };
    </script>
</body>
</html>
