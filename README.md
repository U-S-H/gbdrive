<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Elite | Premium Cluster</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --primary: #3b82f6; --bg: #030712; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: #f1f5f9; overflow-x: hidden; -webkit-tap-highlight-color: transparent; }
        .glass { background: rgba(17, 24, 39, 0.7); backdrop-filter: blur(15px); border: 1px solid rgba(255,255,255,0.08); border-radius: 24px; }
        .plan-card { position: relative; overflow: hidden; transition: all 0.3s ease; }
        .plan-card:active { transform: scale(0.97); }
        .progress-bar { height: 4px; background: rgba(59, 130, 246, 0.2); border-radius: 2px; }
        .progress-fill { height: 100%; background: #3b82f6; border-radius: 2px; transition: width 1s linear; }
        .page { display: none; animation: fadeIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        #wheel { width: 280px; height: 280px; transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); border-radius: 50%; }
        ::-webkit-scrollbar { width: 0; }
        .nav-active { color: #3b82f6; }
    </style>
</head>
<body class="min-h-screen pb-32">

    <div id="maintenance-ui" class="hidden fixed inset-0 z-[10000] bg-black flex flex-col items-center justify-center text-center p-10">
        <i class="fa-solid fa-gears text-6xl text-blue-500 mb-6 animate-pulse"></i>
        <h2 class="text-2xl font-black uppercase">System Maintenance</h2>
        <p class="text-xs opacity-50 mt-2">Institutional Servers are being optimized. Back soon.</p>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[6000] bg-[#030712] flex flex-col items-center justify-center p-10 text-center">
        <div onclick="handleLogoTap()" class="w-24 h-24 bg-blue-600 rounded-[2.5rem] flex items-center justify-center mb-8 rotate-3 shadow-2xl cursor-pointer">
            <i class="fa-solid fa-shield-halved text-white text-4xl"></i>
        </div>
        <h1 class="text-4xl font-black italic tracking-tighter uppercase">Vestify <span class="text-blue-500">Elite</span></h1>
        <button onclick="login()" class="w-full max-w-xs mt-16 bg-white text-black py-5 rounded-[2rem] font-black uppercase text-xs tracking-widest shadow-2xl">Secure Sync</button>
    </section>

    <main id="app-ui" class="hidden pt-8 px-6">
        
        <div class="flex items-center justify-between mb-8">
            <div>
                <p class="text-[10px] font-black opacity-40 uppercase tracking-[0.2em]">Portfolio Balance</p>
                <h2 class="text-4xl font-black italic tracking-tighter" id="v-bal">₨ 0.00</h2>
            </div>
            <img id="user-photo" src="" class="w-12 h-12 rounded-2xl border border-white/10 shadow-xl">
        </div>

        <div id="p-home" class="page active-page">
            <h3 class="font-black text-xs uppercase tracking-widest opacity-40 mb-6 italic">Mining Nodes</h3>
            <div id="plans-container" class="space-y-6"></div>
        </div>

        <div id="p-assets" class="page">
            <h2 class="text-3xl font-black italic mb-8 uppercase text-blue-500">My Clusters</h2>
            <div id="my-active-plans" class="space-y-4"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black italic mb-2 uppercase">Lucky Terminal</h2>
            <p id="spin-status" class="text-[10px] text-blue-400 uppercase mb-10 tracking-widest font-bold italic">Loading...</p>
            
            <div class="relative inline-block mb-10">
                <div class="absolute -top-6 left-1/2 -translate-x-1/2 w-10 h-12 bg-red-600 z-50" style="clip-path: polygon(50% 100%, 0 0, 100% 0);"></div>
                <img id="wheel" src="https://i.ibb.co/v4m0Yv8/wheel.png" alt="Spin Wheel">
            </div>

            <div class="space-y-3 px-4">
                <button id="spin-btn" onclick="executeSpin()" class="w-full bg-blue-600 py-5 rounded-[2rem] font-black uppercase text-xs italic shadow-xl disabled:opacity-30">Execute Free Spin</button>
                <button id="buy-spin-btn" onclick="buyExtraSpins()" class="w-full glass py-4 rounded-[2rem] font-black uppercase text-[10px] italic border-blue-500/30">Buy 2 Extra Spins (₨ 20)</button>
            </div>
            <p class="text-[9px] opacity-30 mt-6 uppercase font-bold tracking-widest">Extra spins are deducted from liquidity</p>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-3xl font-black italic mb-8 uppercase">Banking</h2>
            <div class="glass p-6 space-y-6">
                <div class="flex gap-2">
                    <button onclick="toggleFinance('dep')" class="flex-1 py-4 rounded-2xl bg-blue-600 text-[10px] font-black uppercase">Deposit</button>
                    <button onclick="toggleFinance('wd')" class="flex-1 py-4 rounded-2xl bg-red-600 text-[10px] font-black uppercase">Withdraw</button>
                </div>
                <div id="finance-dep" class="space-y-4">
                    <input id="dep-amt" type="number" placeholder="Min 200 PKR" class="w-full p-4 rounded-xl bg-slate-900 text-xs outline-none">
                    <input id="dep-tid" type="text" placeholder="TID Number" class="w-full p-4 rounded-xl bg-slate-900 text-xs outline-none">
                    <input id="dep-img" type="file" accept="image/*" class="w-full text-[10px] text-slate-500">
                    <button onclick="submitDeposit()" class="w-full bg-white text-black py-4 rounded-xl font-black uppercase text-xs">Verify Payment</button>
                </div>
                <div id="history-log" class="space-y-3 pt-6 border-t border-white/5"></div>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-20 glass flex justify-around items-center z-[4000]">
        <button onclick="changePage('home')" class="nav-item flex flex-col items-center p-4"><i class="fa-solid fa-layer-group"></i><span class="text-[8px] font-black mt-1 uppercase">Shop</span></button>
        <button onclick="changePage('assets')" class="nav-item flex flex-col items-center p-4"><i class="fa-solid fa-microchip"></i><span class="text-[8px] font-black mt-1 uppercase">Active</span></button>
        <button onclick="changePage('spin')" class="nav-item flex flex-col items-center p-4"><i class="fa-solid fa-dharmachakra"></i><span class="text-[8px] font-black mt-1 uppercase">Spin</span></button>
        <button onclick="changePage('finance')" class="nav-item flex flex-col items-center p-4"><i class="fa-solid fa-wallet"></i><span class="text-[8px] font-black mt-1 uppercase">Bank</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();
        const provider = new firebase.auth.GoogleAuthProvider();

        let userObj = null, spinActive = false, rot = 0, tapCount = 0;

        const AVAILABLE_PLANS = [
            { id: 'n1', name: 'Alpha Core', price: 500, daily: 50, days: 30, img: 'https://images.unsplash.com/photo-1639322537228-f710d846310a?w=400' },
            { id: 'n2', name: 'Delta Mining', price: 2000, daily: 220, days: 30, img: 'https://images.unsplash.com/photo-1644088379091-d574269d422f?w=400' },
            { id: 'n3', name: 'Elite Cluster', price: 5000, daily: 600, days: 30, img: 'https://images.unsplash.com/photo-1620641788421-7a1c342ea42e?w=400' }
        ];

        // Maintenance Sync
        db.collection("settings").doc("system").onSnapshot(doc => {
            if(doc.exists && doc.data().maintenance) document.getElementById('maintenance-ui').classList.remove('hidden');
            else document.getElementById('maintenance-ui').classList.add('hidden');
        });

        async function login() {
            const r = await auth.signInWithPopup(provider);
            initApp(r.user);
        }

        function initApp(user) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('user-photo').src = user.photoURL;
            syncData(user.displayName);
            setInterval(updateSpinUI, 1000);
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { name: name, balance: 0, activePlans: [], lastSpin: 0, extraSpins: 0 };
                document.getElementById('v-bal').innerText = "₨ " + userObj.balance.toLocaleString();
                calculateProfit();
                renderPlans();
                renderAssets();
                updateSpinUI();
            });
        }

        async function calculateProfit() {
            if(!userObj.activePlans || userObj.activePlans.length === 0) return;
            let earned = 0;
            let updated = userObj.activePlans.map(p => {
                const hours = (Date.now() - (p.lastClaim || p.purchaseDate)) / 3600000;
                if(hours >= 24) {
                    const days = Math.floor(hours / 24);
                    earned += (days * p.dailyRate);
                    p.lastClaim = (p.lastClaim || p.purchaseDate) + (days * 86400000);
                    p.daysRemaining -= days;
                }
                return p;
            }).filter(p => p.daysRemaining > 0);

            if(earned > 0) {
                await db.collection("users").doc(userObj.name).update({ balance: userObj.balance + earned, activePlans: updated });
                alert(`Mubarak! ₨ ${earned} profit automatically added.`);
            }
        }

        function renderPlans() {
            let h = '';
            AVAILABLE_PLANS.forEach(p => {
                h += `<div class="glass p-4 plan-card">
                    <img src="${p.img}" class="w-full h-32 object-cover rounded-2xl mb-4 opacity-70">
                    <div class="flex justify-between items-end">
                        <div><h4 class="font-black uppercase text-[11px]">${p.name}</h4><p class="text-[9px] opacity-40 italic">30 Days Duration</p></div>
                        <div class="text-right"><p class="text-blue-500 font-black text-xs">₨ ${p.daily}/day</p></div>
                    </div>
                    <button onclick="buyPlan('${p.id}')" class="w-full mt-4 bg-white text-black py-3 rounded-xl font-black text-[10px] uppercase">Activate Node (₨ ${p.price})</button>
                </div>`;
            });
            document.getElementById('plans-container').innerHTML = h;
        }

        function renderAssets() {
            let h = '';
            (userObj.activePlans || []).forEach(p => {
                const prog = ((30 - p.daysRemaining) / 30) * 100;
                h += `<div class="glass p-5">
                    <div class="flex justify-between mb-2">
                        <h4 class="font-black text-[10px] uppercase">${p.name}</h4>
                        <span class="font-black text-[10px] italic text-blue-500">${p.daysRemaining} Days left</span>
                    </div>
                    <div class="progress-bar mb-2"><div class="progress-fill" style="width: ${prog}%"></div></div>
                    <p class="text-[8px] opacity-30 font-black uppercase tracking-widest">Earning Status: ₨ ${p.dailyRate} Daily</p>
                </div>`;
            });
            document.getElementById('my-active-plans').innerHTML = h || '<p class="text-center opacity-20 py-20 uppercase font-black text-xs">No Active Clusters</p>';
        }

        async function buyPlan(id) {
            const p = AVAILABLE_PLANS.find(x => x.id === id);
            if(userObj.balance < p.price) return alert("Insufficient Liquidity!");
            const node = { id: p.id, name: p.name, dailyRate: p.daily, daysRemaining: p.days, purchaseDate: Date.now(), lastClaim: Date.now() };
            await db.collection("users").doc(userObj.name).update({ balance: userObj.balance - p.price, activePlans: firebase.firestore.FieldValue.arrayUnion(node) });
            alert("Protocol Initialized!");
        }

        function updateSpinUI() {
            if(!userObj) return;
            const diff = Date.now() - (userObj.lastSpin || 0);
            const canFree = diff >= 86400000;
            const btn = document.getElementById('spin-btn');
            const status = document.getElementById('spin-status');

            if(canFree) {
                btn.disabled = false; btn.innerText = "EXECUTE FREE SPIN";
                status.innerText = "DAILY FREE SPIN READY";
            } else if(userObj.extraSpins > 0) {
                btn.disabled = false; btn.innerText = `USE EXTRA SPIN (${userObj.extraSpins} LEFT)`;
                status.innerText = "EXTRA SPIN PROTOCOL ACTIVE";
            } else {
                btn.disabled = true; btn.innerText = "LOCKED";
                const rem = 86400000 - diff;
                const h = Math.floor(rem/3600000), m = Math.floor((rem%3600000)/60000), s = Math.floor((rem%60000)/1000);
                status.innerText = `NEXT FREE SPIN IN ${h}H ${m}M ${s}S`;
            }
        }

        async function buyExtraSpins() {
            if(userObj.balance < 20) return alert("Insufficient Balance (Requires ₨ 20)");
            if(confirm("Confirm ₨ 20 for 2 Extra Spins?")) {
                await db.collection("users").doc(userObj.name).update({
                    balance: userObj.balance - 20,
                    extraSpins: (userObj.extraSpins || 0) + 2
                });
                alert("Protocol Updated: 2 Spins Added.");
            }
        }

        async function executeSpin() {
            if(spinActive) return; spinActive = true;
            const isFree = (Date.now() - (userObj.lastSpin || 0)) >= 86400000;
            const stop = [0, 60, 120, 180, 240, 300][Math.floor(Math.random()*6)];
            rot += (3600 + stop - (rot%360));
            document.getElementById('wheel').style.transform = `rotate(${rot}deg)`;
            setTimeout(async () => {
                spinActive = false;
                const win = [50, 0, 10, 5, 100, 2][stop/60];
                let upd = { balance: userObj.balance + win };
                if(isFree) upd.lastSpin = Date.now();
                else upd.extraSpins = userObj.extraSpins - 1;
                await db.collection("users").doc(userObj.name).update(upd);
                alert(win > 0 ? `Gained: ₨ ${win}` : "No Gains Detected.");
            }, 4000);
        }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('nav-active'));
        }
        function handleLogoTap() { tapCount++; if(tapCount>=5){ const p=prompt("Admin Key:"); if(p==="net204") alert("Admin mode active via Firebase."); tapCount=0; } }
    </script>
</body>
</html>
