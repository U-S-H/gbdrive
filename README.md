<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Elite | Institutional Mining</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --primary: #3b82f6; --bg: #050810; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: #f1f5f9; overflow-x: hidden; -webkit-tap-highlight-color: transparent; }
        .glass { background: rgba(15, 23, 42, 0.8); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); border-radius: 28px; }
        .page { display: none; animation: fadeIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        #wheel { width: 280px; height: 280px; transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); border-radius: 50%; border: 8px solid #1e293b; box-shadow: 0 0 30px rgba(59, 130, 246, 0.2); }
        .vip-gold { border: 2px solid #fbbf24; box-shadow: 0 0 20px rgba(251, 191, 36, 0.15); }
        .nav-active { color: #3b82f6; }
        ::-webkit-scrollbar { width: 0; }
    </style>
</head>
<body class="min-h-screen pb-32">

    <div id="maintenance-ui" class="hidden fixed inset-0 z-[10000] bg-black flex flex-col items-center justify-center text-center p-10">
        <i class="fa-solid fa-screwdriver-wrench text-6xl text-blue-500 mb-6 animate-bounce"></i>
        <h2 class="text-2xl font-black uppercase italic">System Optimization</h2>
        <p class="text-[10px] opacity-40 mt-2 tracking-widest uppercase">Institutional Servers are currently undergoing scheduled maintenance.</p>
    </div>

    <div id="admin-panel" class="hidden fixed inset-0 z-[9999] bg-[#050810] p-6 overflow-y-auto">
        <div class="flex justify-between items-center mb-8 border-b border-white/10 pb-4">
            <h2 class="text-xl font-black text-blue-500 italic">ADMIN TERMINAL</h2>
            <button onclick="closeAdmin()" class="text-3xl">&times;</button>
        </div>
        <div id="admin-requests" class="space-y-4"></div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[6000] bg-[#050810] flex flex-col items-center justify-center p-10 text-center">
        <div onclick="handleLogoTap()" class="w-24 h-24 bg-blue-600 rounded-[2.5rem] flex items-center justify-center mb-8 rotate-3 shadow-2xl cursor-pointer active:scale-90 transition-all">
            <i class="fa-solid fa-shield-halved text-white text-4xl"></i>
        </div>
        <h1 class="text-4xl font-black italic tracking-tighter uppercase">Vestify <span class="text-blue-500">Elite</span></h1>
        <p class="text-[9px] text-slate-500 mt-2 uppercase tracking-[0.4em] font-black">Mining & Institutional LLC</p>
        <button onclick="login()" class="w-full max-w-xs mt-16 bg-white text-black py-5 rounded-[2rem] font-black uppercase text-xs tracking-widest shadow-2xl active:scale-95 transition-all">Synchronize Google</button>
    </section>

    <main id="app-ui" class="hidden pt-12 px-6">
        
        <div class="flex items-center justify-between mb-8">
            <div class="flex items-center gap-3">
                <img id="user-photo" src="" class="w-12 h-12 rounded-2xl border-2 border-blue-500/20 shadow-xl">
                <div>
                    <p class="text-[9px] font-black opacity-40 uppercase tracking-widest">Active Operator</p>
                    <h3 id="user-display-name" class="text-sm font-black italic">--</h3>
                </div>
            </div>
            <div onclick="alert('Notification: Welcome to Vestify Elite. Institutional mining nodes are stable.')" class="relative">
                <i class="fa-solid fa-bell text-xl opacity-30"></i>
                <span class="absolute -top-1 -right-1 w-2 h-2 bg-red-500 rounded-full"></span>
            </div>
        </div>

        <div id="p-home" class="page active-page">
            <div id="balance-card" class="glass p-8 mb-8 bg-gradient-to-br from-slate-900 to-black relative overflow-hidden transition-all duration-500">
                <div class="absolute -right-10 -top-10 w-32 h-32 bg-blue-600/10 rounded-full blur-3xl"></div>
                <p class="text-[9px] text-blue-400 font-black uppercase tracking-widest">Total Liquidity</p>
                <h2 class="text-5xl font-black italic mt-1 tracking-tighter" id="v-bal">₨ 0.00</h2>
                <div id="vip-status" class="mt-4 text-[8px] font-black uppercase px-3 py-1 bg-blue-500/10 inline-block rounded-lg text-blue-400">BRONZE MEMBER</div>
            </div>
            
            <h3 class="font-black text-xs uppercase tracking-widest opacity-40 mb-6">Cluster Operations</h3>
            <div id="active-nodes" class="space-y-4 pb-10">
                <div class="glass p-5 flex justify-between items-center bg-white/[0.02]">
                    <div>
                        <h4 class="font-black text-[11px] uppercase">Node Core v.01</h4>
                        <p class="text-[9px] text-blue-500 font-bold">Daily: ₨ 25.00</p>
                    </div>
                    <button class="bg-blue-600 px-6 py-2 rounded-xl text-[10px] font-black uppercase italic">Activate</button>
                </div>
            </div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black italic mb-2 uppercase">Lucky Terminal</h2>
            <p id="spin-timer-msg" class="text-[10px] text-blue-400 uppercase mb-10 tracking-widest font-bold italic">Checking Status...</p>
            <div class="relative inline-block mb-12">
                <div class="absolute -top-6 left-1/2 -translate-x-1/2 w-10 h-12 bg-red-600 z-50" style="clip-path: polygon(50% 100%, 0 0, 100% 0);"></div>
                <img id="wheel" src="https://i.ibb.co/v4m0Yv8/wheel.png" alt="Wheel">
            </div>
            <button id="spin-btn" onclick="executeSpin()" class="w-full max-w-xs bg-blue-600 py-5 rounded-[2rem] font-black uppercase text-xs italic shadow-xl disabled:opacity-50">Execute Spin</button>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-3xl font-black italic mb-8 uppercase">Banking</h2>
            
            <div class="glass p-4 mb-6 flex items-center gap-3">
                <input id="promo-input" type="text" placeholder="APPLY PROMOCODE" class="flex-1 bg-transparent border-none text-[10px] font-black uppercase focus:outline-none placeholder:opacity-30">
                <button onclick="applyPromocode()" class="text-blue-500 font-black text-[10px] uppercase">Verify</button>
            </div>

            <div class="glass p-6 space-y-6">
                <div class="flex gap-2">
                    <button onclick="toggleFinance('dep')" class="flex-1 py-4 rounded-2xl bg-blue-600 text-[10px] font-black uppercase italic">Deposit</button>
                    <button onclick="toggleFinance('wd')" class="flex-1 py-4 rounded-2xl bg-red-600 text-[10px] font-black uppercase italic">Withdraw</button>
                </div>
                <div id="finance-dep" class="space-y-4">
                    <select id="dep-method" class="w-full p-4 rounded-xl font-bold bg-slate-900 text-xs border border-white/5 outline-none">
                        <option value="easypaisa">EasyPaisa (03379827882)</option>
                        <option value="jazzcash">JazzCash (03705519562)</option>
                    </select>
                    <input id="dep-amt" type="number" placeholder="Min 200 PKR" class="w-full p-4 rounded-xl bg-slate-900 text-xs border border-white/5 outline-none">
                    <input id="dep-tid" type="text" placeholder="Transaction ID" class="w-full p-4 rounded-xl bg-slate-900 text-xs border border-white/5 outline-none">
                    <input id="dep-img" type="file" accept="image/*" class="w-full text-[10px] text-slate-500">
                    <button onclick="submitDeposit()" class="w-full bg-white text-black py-4 rounded-xl font-black uppercase text-xs">Verify Settlement</button>
                </div>
                <div id="history-log" class="space-y-3 pt-6 border-t border-white/5"></div>
            </div>
        </div>

    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-20 glass flex justify-around items-center z-[4000]">
        <button onclick="changePage('home')" class="nav-btn flex flex-col items-center p-4 nav-active"><i class="fa-solid fa-vault"></i><span class="text-[8px] font-black mt-1">VAULT</span></button>
        <button onclick="changePage('spin')" class="nav-btn flex flex-col items-center p-4"><i class="fa-solid fa-dharmachakra"></i><span class="text-[8px] font-black mt-1">LUCKY</span></button>
        <button onclick="changePage('finance')" class="nav-btn flex flex-col items-center p-4"><i class="fa-solid fa-wallet"></i><span class="text-[8px] font-black mt-1">BANK</span></button>
        <button onclick="window.open('https://wa.me/923379827882')" class="flex flex-col items-center p-4 text-green-500"><i class="fa-brands fa-whatsapp"></i><span class="text-[8px] font-black mt-1">HELP</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();
        const provider = new firebase.auth.GoogleAuthProvider();

        let userObj = null, tapCount = 0, spinActive = false, rot = 0;

        // Maintenance Checker
        db.collection("settings").doc("system").onSnapshot(doc => {
            if(doc.exists && doc.data().maintenance) document.getElementById('maintenance-ui').classList.remove('hidden');
            else document.getElementById('maintenance-ui').classList.add('hidden');
        });

        async function login() {
            try {
                const r = await auth.signInWithPopup(provider);
                initApp(r.user);
            } catch(e) { alert("Institutional Sync Failed."); }
        }

        function initApp(user) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('user-display-name').innerText = user.displayName.toUpperCase();
            document.getElementById('user-photo').src = user.photoURL;
            syncData(user.displayName);
            setInterval(updateSpinTimer, 1000);
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { name: name, balance: 0, plans: [], lastSpin: 0, usedPromos: [] };
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
                updateVIP(userObj.balance);
                updateSpinTimer();
            });
            // Load History
            db.collection("requests").where("user", "==", name).orderBy("time", "desc").onSnapshot(snap => {
                let h = ''; snap.forEach(d => { const r = d.data(); h += `<div class="glass p-4 flex justify-between items-center text-[9px] uppercase font-black"><div><p>${r.type}</p><p class="opacity-30">₨ ${r.amount}</p></div><div class="${r.status==='Approved' ? 'text-green-500' : 'text-yellow-500'}">${r.status}</div></div>`; });
                document.getElementById('history-log').innerHTML = h;
            });
        }

        function updateVIP(bal) {
            const el = document.getElementById('vip-status');
            const card = document.getElementById('balance-card');
            if(bal >= 10000) { 
                el.innerText = "GOLD ELITE MEMBER"; el.className = "mt-4 text-[8px] font-black uppercase px-3 py-1 bg-yellow-500/20 inline-block rounded-lg text-yellow-500";
                card.classList.add('vip-gold');
            } else if(bal >= 5000) {
                el.innerText = "SILVER MEMBER"; el.className = "mt-4 text-[8px] font-black uppercase px-3 py-1 bg-slate-400/20 inline-block rounded-lg text-slate-400";
                card.classList.remove('vip-gold');
            } else {
                el.innerText = "BRONZE MEMBER"; el.className = "mt-4 text-[8px] font-black uppercase px-3 py-1 bg-blue-500/10 inline-block rounded-lg text-blue-400";
                card.classList.remove('vip-gold');
            }
        }

        async function applyPromocode() {
            const code = document.getElementById('promo-input').value.trim().toUpperCase();
            const codes = { 'J5B82': 100, 'G7J99': 250 };
            if(!codes[code]) return alert("Invalid Protocol.");
            if(userObj.usedPromos?.includes(code)) return alert("Already Consumed.");
            
            await db.collection("users").doc(userObj.name).update({
                balance: userObj.balance + codes[code],
                usedPromos: firebase.firestore.FieldValue.arrayUnion(code)
            });
            alert("Elite Credit Applied!");
            document.getElementById('promo-input').value = '';
        }

        function updateSpinTimer() {
            if(!userObj) return;
            const last = userObj.lastSpin || 0;
            const diff = Date.now() - last;
            const rem = (24 * 60 * 60 * 1000) - diff;
            const btn = document.getElementById('spin-btn');
            const msg = document.getElementById('spin-timer-msg');

            if(rem <= 0) { btn.disabled = false; msg.innerText = "FREE DAILY SPIN READY"; }
            else { 
                btn.disabled = true; 
                const h = Math.floor(rem/3600000), m = Math.floor((rem%3600000)/60000), s = Math.floor((rem%60000)/1000);
                msg.innerText = `NEXT CLUSTER SPIN IN ${h}H ${m}M ${s}S`;
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
                await db.collection("users").doc(userObj.name).update({ balance: userObj.balance + win, lastSpin: Date.now() });
                alert(win > 0 ? `Credit Added: ₨ ${win}` : "No Gains. Try Tomorrow!");
            }, 4000);
        }

        async function submitDeposit() {
            const amt = parseInt(document.getElementById('dep-amt').value);
            const tid = document.getElementById('dep-tid').value;
            const file = document.getElementById('dep-img').files[0];
            if(amt < 200 || !tid || !file) return alert("Fill all clusters!");
            const reader = new FileReader();
            reader.onloadend = async () => {
                await db.collection("requests").add({ user: userObj.name, type: 'Deposit', amount: amt, tid: tid, proof: reader.result, status: 'Pending', time: Date.now() });
                alert("Protocol Logged for Audit.");
            };
            reader.readAsDataURL(file);
        }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-btn').forEach(btn => btn.classList.remove('nav-active'));
            event.currentTarget.classList.add('nav-active');
        }

        function handleLogoTap() { tapCount++; if(tapCount>=5){ const p=prompt("Key:"); if(p==="net204") alert("Admin: Manage via Firestore Console."); tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
    </script>
</body>
</html>
