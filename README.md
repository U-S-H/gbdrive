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
        :root { --primary: #3b82f6; --bg: #090e1a; --card: #13192a; --gold: #fbbf24; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: #f1f5f9; overflow-x: hidden; -webkit-tap-highlight-color: transparent; }
        .glass { background: rgba(19, 25, 42, 0.7); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); border-radius: 24px; }
        .page { display: none; animation: fadeIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }
        #ticker { background: rgba(59, 130, 246, 0.1); border-bottom: 1px solid rgba(59, 130, 246, 0.2); }
        .btn-primary { background: linear-gradient(135deg, #3b82f6 0%, #2563eb 100%); transition: all 0.3s ease; }
        .btn-primary:active { transform: scale(0.96); }
        #wheel { width: 280px; height: 280px; transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); border-radius: 50%; border: 8px solid #1e293b; box-shadow: 0 0 30px rgba(59, 130, 246, 0.3); }
        .nav-item.active { color: #3b82f6; }
        ::-webkit-scrollbar { width: 0; }
    </style>
</head>
<body class="min-h-screen pb-32">

    <div id="ticker" class="fixed top-0 left-0 right-0 z-[5000] py-2 overflow-hidden whitespace-nowrap">
        <div id="ticker-text" class="inline-block text-[10px] font-bold uppercase tracking-widest text-blue-400">
            Initialising Institutional Data Stream...
        </div>
    </div>

    <div id="admin-panel" class="hidden fixed inset-0 z-[9999] bg-[#050810] p-6 overflow-y-auto">
        <div class="flex justify-between items-center mb-8 border-b border-white/10 pb-4">
            <h2 class="text-xl font-black text-blue-500 italic">MASTER TERMINAL</h2>
            <button onclick="closeAdmin()" class="text-3xl text-white">&times;</button>
        </div>
        <div id="admin-requests" class="space-y-4"></div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[6000] bg-[#090e1a] flex flex-col items-center justify-center p-10 text-center">
        <div onclick="handleLogoTap()" class="w-24 h-24 btn-primary rounded-[2.5rem] flex items-center justify-center mb-8 rotate-3 cursor-pointer">
            <i class="fa-solid fa-shield-halved text-white text-4xl"></i>
        </div>
        <h1 class="text-4xl font-black italic tracking-tighter uppercase">Vestify <span class="text-blue-500">Elite</span></h1>
        <p class="text-[9px] text-slate-500 mt-2 uppercase tracking-[0.4em] font-black">Institutional LLC Mining Cluster</p>
        <button onclick="login()" class="w-full max-w-xs mt-16 bg-white text-black py-5 rounded-[2rem] font-black uppercase text-xs tracking-widest shadow-2xl">Secure Sync</button>
    </section>

    <main id="app-ui" class="hidden pt-16 px-6">
        
        <div id="p-home" class="page active-page">
            <div class="glass p-8 mb-8 bg-gradient-to-br from-slate-900 to-black">
                <p class="text-[9px] text-blue-400 font-black uppercase tracking-widest">Global Portfolio Balance</p>
                <h2 class="text-5xl font-black italic mt-1 tracking-tighter" id="v-bal">₨ 0.00</h2>
                <div class="mt-8 flex gap-3">
                    <button onclick="changePage('finance')" class="flex-1 bg-white text-black py-4 rounded-2xl font-black text-[10px] uppercase">Banking</button>
                    <button onclick="changePage('affiliate')" class="flex-1 bg-slate-800 text-white py-4 rounded-2xl font-black text-[10px] uppercase border border-white/5">Network</button>
                </div>
            </div>
            <h3 class="font-black text-xs uppercase tracking-widest opacity-40 mb-6">Operational Clusters</h3>
            <div id="active-nodes-list" class="space-y-4 mb-10"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black italic mb-2 uppercase">Lucky Terminal</h2>
            <p id="spin-timer-msg" class="text-[10px] text-blue-400 uppercase mb-10 tracking-widest font-bold">Checking Status...</p>
            <div class="relative inline-block mb-12">
                <div class="absolute -top-6 left-1/2 -translate-x-1/2 w-10 h-12 bg-red-600 z-50" style="clip-path: polygon(50% 100%, 0 0, 100% 0);"></div>
                <img id="wheel" src="https://i.ibb.co/v4m0Yv8/wheel.png" alt="Wheel">
            </div>
            <div class="space-y-4 max-w-xs mx-auto">
                <button id="spin-btn" onclick="executeSpin()" class="w-full btn-primary py-5 rounded-[2rem] font-black uppercase text-xs">Execute Spin</button>
                <button onclick="buySpins()" class="w-full bg-slate-800 py-4 rounded-[2rem] font-black uppercase text-[9px] border border-white/5">Buy 2 Extra (₨ 20)</button>
            </div>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-3xl font-black italic mb-8 uppercase">Finance</h2>
            
            <div class="glass p-4 mb-6 flex items-center gap-3">
                <input id="promo-input" type="text" placeholder="ENTER PROMOCODE" class="flex-1 bg-transparent border-none text-xs font-bold uppercase focus:outline-none">
                <button onclick="applyPromocode()" class="text-blue-500 font-black text-[10px] uppercase">Apply</button>
            </div>

            <div class="glass p-6 space-y-6">
                <div class="flex gap-2">
                    <button onclick="toggleFinance('dep')" class="flex-1 py-4 rounded-2xl bg-blue-600 text-[10px] font-black uppercase">Deposit</button>
                    <button onclick="toggleFinance('wd')" class="flex-1 py-4 rounded-2xl bg-red-600 text-[10px] font-black uppercase">Withdraw</button>
                </div>
                <div id="finance-dep" class="space-y-4">
                    <select id="dep-method" class="w-full p-4 rounded-xl font-bold bg-slate-900 text-xs">
                        <option value="easypaisa">EasyPaisa (03379827882)</option>
                        <option value="jazzcash">JazzCash (03705519562)</option>
                    </select>
                    <input id="dep-amt" type="number" placeholder="Min 200 PKR" class="w-full p-4 rounded-xl bg-slate-900 text-xs">
                    <input id="dep-tid" type="text" placeholder="TID Number" class="w-full p-4 rounded-xl bg-slate-900 text-xs">
                    <input id="dep-img" type="file" accept="image/*" class="w-full text-[10px] text-slate-500">
                    <button onclick="submitDeposit()" class="w-full btn-primary py-4 rounded-xl font-black uppercase text-xs">Verify Influx</button>
                </div>
                <div id="finance-wd" class="hidden space-y-4">
                    <input id="wd-amt" type="number" placeholder="Min 100 PKR" class="w-full p-4 rounded-xl bg-slate-900 text-xs">
                    <input id="wd-acc" type="text" placeholder="Account Number" class="w-full p-4 rounded-xl bg-slate-900 text-xs">
                    <input id="wd-name" type="text" placeholder="Account Name" class="w-full p-4 rounded-xl bg-slate-900 text-xs">
                    <button onclick="submitWithdraw()" class="w-full bg-red-600 py-4 rounded-xl font-black uppercase text-xs">Request Settlement</button>
                </div>
                <div id="history-log" class="space-y-3 pt-6 border-t border-white/5"></div>
            </div>
        </div>

        <div id="p-legal" class="page pb-10">
            <h2 class="text-3xl font-black italic mb-8 uppercase">Identity</h2>
            <div class="glass p-6 space-y-6 text-[11px] leading-relaxed">
                <div class="border-b border-white/5 pb-4">
                    <h4 class="text-blue-500 font-black uppercase mb-1">Company LLC</h4>
                    <p class="opacity-60">Vestify Elite Global LLC (Reg: LLC-2024-PK-782). Authorized for high-frequency mining clusters.</p>
                </div>
                <div>
                    <h4 class="text-blue-500 font-black uppercase mb-2">Institutional Support</h4>
                    <button onclick="window.open('https://wa.me/923379827882')" class="w-full bg-[#25D366] text-black py-3 rounded-xl font-black flex items-center justify-center gap-2">
                        <i class="fa-brands fa-whatsapp text-lg"></i> WHATSAPP SUPPORT
                    </button>
                </div>
            </div>
        </div>

    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-20 glass flex justify-around items-center z-[4000]">
        <button onclick="changePage('home')" class="nav-item flex flex-col items-center p-4"><i class="fa-solid fa-vault"></i><span class="text-[8px] font-black mt-1">VAULT</span></button>
        <button onclick="changePage('plans')" class="nav-item flex flex-col items-center p-4"><i class="fa-solid fa-microchip"></i><span class="text-[8px] font-black mt-1">NODES</span></button>
        <button onclick="changePage('spin')" class="nav-item flex flex-col items-center p-4"><i class="fa-solid fa-dharmachakra"></i><span class="text-[8px] font-black mt-1">GAMES</span></button>
        <button onclick="changePage('finance')" class="nav-item flex flex-col items-center p-4"><i class="fa-solid fa-wallet"></i><span class="text-[8px] font-black mt-1">BANK</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();
        const provider = new firebase.auth.GoogleAuthProvider();

        let userObj = null, tapCount = 0, spinActive = false, rot = 0;

        async function login() {
            try {
                const r = await auth.signInWithPopup(provider);
                localStorage.setItem('v_elite_user', r.user.displayName);
                initApp(r.user.displayName);
            } catch(e) { alert("Auth Failed!"); }
        }

        function initApp(name) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            syncData(name);
            startTicker();
            setInterval(updateSpinTimer, 1000);
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { name: name, balance: 0, plans: [], lastSpin: 0, usedPromos: [] };
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
                updateSpinTimer();
            });
            db.collection("requests").where("user", "==", name).orderBy("time", "desc").onSnapshot(snap => {
                let h = ''; snap.forEach(d => { const r = d.data(); h += `<div class="glass p-4 flex justify-between items-center text-[9px] uppercase font-bold"><div><p>${r.type}</p><p class="opacity-40">₨ ${r.amount}</p></div><div class="${r.status==='Approved' ? 'text-green-400' : 'text-yellow-500'}">${r.status}</div></div>`; });
                document.getElementById('history-log').innerHTML = h;
            });
        }

        async function applyPromocode() {
            const code = document.getElementById('promo-input').value.trim().toUpperCase();
            const codes = { 'J5B82': 100, 'G7J99': 250 };
            if(!codes[code]) return alert("Invalid Code!");
            if(userObj.usedPromos?.includes(code)) return alert("Already Used!");
            
            await db.collection("users").doc(userObj.name).update({
                balance: userObj.balance + codes[code],
                usedPromos: firebase.firestore.FieldValue.arrayUnion(code)
            });
            alert(`Elite Bonus Applied: ₨ ${codes[code]}`);
            document.getElementById('promo-input').value = '';
        }

        function updateSpinTimer() {
            if(!userObj) return;
            const last = userObj.lastSpin || 0;
            const diff = Date.now() - last;
            const remaining = (24 * 60 * 60 * 1000) - diff;
            const btn = document.getElementById('spin-btn');
            const msg = document.getElementById('spin-timer-msg');

            if(remaining <= 0) {
                btn.disabled = false;
                btn.innerText = "Execute Free Spin";
                msg.innerText = "FREE DAILY SPIN READY";
            } else {
                btn.disabled = true;
                const h = Math.floor(remaining / 3600000), m = Math.floor((remaining%3600000)/60000), s = Math.floor((remaining%60000)/1000);
                btn.innerText = `LOCKED (${h}h ${m}m)`;
                msg.innerText = `NEXT FREE SPIN IN ${h}H ${M}H ${S}S`;
            }
        }

        async function executeSpin() {
            if(spinActive) return;
            spinActive = true;
            const stop = [0, 60, 120, 180, 240, 300][Math.floor(Math.random()*6)];
            rot += (3600 + stop - (rot%360));
            document.getElementById('wheel').style.transform = `rotate(${rot}deg)`;
            
            setTimeout(async () => {
                spinActive = false;
                const win = [50, 0, 10, 5, 100, 2][stop/60];
                await db.collection("users").doc(userObj.name).update({ 
                    balance: userObj.balance + win, 
                    lastSpin: Date.now() 
                });
                alert(win > 0 ? `Elite Bonus: ₨ ${win}` : "Recycle Protocol. Try Tomorrow!");
            }, 4000);
        }

        async function submitDeposit() {
            const amt = parseInt(document.getElementById('dep-amt').value);
            const tid = document.getElementById('dep-tid').value;
            const file = document.getElementById('dep-img').files[0];
            if(amt < 200 || !tid || !file) return alert("All data required!");

            const reader = new FileReader();
            reader.onloadend = async () => {
                await db.collection("requests").add({
                    user: userObj.name, type: 'Deposit', amount: amt, tid: tid, 
                    proof: reader.result, status: 'Pending', time: Date.now()
                });
                alert("Protocol Logged for Verification!");
            };
            reader.readAsDataURL(file);
        }

        function handleLogoTap() { tapCount++; if(tapCount>=5){ const p=prompt("Key:"); if(p==="net204") { document.getElementById('admin-panel').classList.remove('hidden'); loadAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); 
            document.getElementById('p-'+p).classList.add('active-page'); 
        }
        function toggleFinance(s) {
            document.getElementById('finance-dep').classList.toggle('hidden', s!=='dep');
            document.getElementById('finance-wd').classList.toggle('hidden', s!=='wd');
        }

        window.onload = () => { if(localStorage.getItem('v_elite_user')) initApp(localStorage.getItem('v_elite_user')); };
    </script>
</body>
</html>
