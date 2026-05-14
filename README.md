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
        :root { --primary: #3b82f6; --bg: #030712; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: #f1f5f9; overflow-x: hidden; -webkit-tap-highlight-color: transparent; }
        .glass { background: rgba(17, 24, 39, 0.7); backdrop-filter: blur(15px); border: 1px solid rgba(255,255,255,0.08); border-radius: 24px; }
        .page { display: none; animation: fadeIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        #wheel { width: 280px; height: 280px; transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); border-radius: 50%; }
        ::-webkit-scrollbar { width: 0; }
        .nav-active { color: #3b82f6; }
        .admin-badge { background: linear-gradient(90deg, #ef4444, #f59e0b); padding: 2px 8px; border-radius: 6px; font-size: 8px; font-weight: 900; }
    </style>
</head>
<body class="min-h-screen pb-32">

    <div id="maintenance-ui" class="hidden fixed inset-0 z-[20000] bg-black flex flex-col items-center justify-center text-center p-10">
        <div class="w-32 h-32 bg-blue-600/20 rounded-full flex items-center justify-center mb-6 animate-pulse">
            <i class="fa-solid fa-gears text-6xl text-blue-500"></i>
        </div>
        <h2 class="text-3xl font-black uppercase italic tracking-tighter">System Upgrade</h2>
        <p class="text-xs opacity-50 mt-4 leading-relaxed">Our institutional servers are currently undergoing deep optimization. We will be back online shortly.</p>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[6000] bg-[#030712] flex flex-col items-center justify-center p-10 text-center">
        <div onclick="handleLogoTap()" class="w-24 h-24 bg-blue-600 rounded-[2.5rem] flex items-center justify-center mb-8 rotate-3 shadow-2xl cursor-pointer">
            <i class="fa-solid fa-shield-halved text-white text-4xl"></i>
        </div>
        <h1 class="text-4xl font-black italic tracking-tighter uppercase">Vestify <span class="text-blue-500">Elite</span></h1>
        <p class="text-[10px] opacity-30 mt-2 uppercase tracking-[0.3em]">Institutional Grade Assets</p>
        <button onclick="login()" class="w-full max-w-xs mt-16 bg-white text-black py-5 rounded-[2rem] font-black uppercase text-xs tracking-widest shadow-2xl active:scale-95 transition-all">Secure Access</button>
    </section>

    <main id="app-ui" class="hidden pt-8 px-6">
        
        <div class="flex items-center justify-between mb-8">
            <div>
                <p class="text-[10px] font-black opacity-40 uppercase tracking-[0.2em]">Net Liquidity</p>
                <h2 class="text-4xl font-black italic tracking-tighter text-white" id="v-bal">₨ 0.00</h2>
            </div>
            <div class="relative">
                <img id="user-photo" src="" class="w-12 h-12 rounded-2xl border border-white/10 shadow-xl">
                <div id="admin-indicator" class="hidden absolute -bottom-2 -right-2 admin-badge">ADMIN</div>
            </div>
        </div>

        <div id="p-home" class="page active-page">
            <div id="plans-container" class="space-y-6"></div>
        </div>

        <div id="p-assets" class="page">
            <h2 class="text-2xl font-black italic mb-6 uppercase">Asset Management</h2>
            <div id="my-active-plans" class="space-y-4 mb-10"></div>
            
            <h3 class="font-black text-xs uppercase opacity-30 mb-4">Transaction History</h3>
            <div id="user-history-log" class="space-y-3"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black italic mb-2 uppercase">Lucky Terminal</h2>
            <p id="spin-status" class="text-[10px] text-blue-400 uppercase mb-10 tracking-widest font-bold italic">Syncing...</p>
            <div class="relative inline-block mb-10">
                <div class="absolute -top-6 left-1/2 -translate-x-1/2 w-10 h-12 bg-red-600 z-50" style="clip-path: polygon(50% 100%, 0 0, 100% 0);"></div>
                <img id="wheel" src="https://i.ibb.co/v4m0Yv8/wheel.png" alt="Spin">
            </div>
            <div class="space-y-3 px-4">
                <button id="spin-btn" onclick="executeSpin()" class="w-full bg-blue-600 py-5 rounded-[2rem] font-black uppercase text-xs italic shadow-xl">Execute Free Spin</button>
                <button id="buy-spin-btn" onclick="buyExtraSpins()" class="w-full glass py-4 rounded-[2rem] font-black uppercase text-[10px] italic">Buy 2 Extra Spins (₨ 20)</button>
            </div>
        </div>

        <div id="p-info" class="page">
            <h2 class="text-3xl font-black italic mb-8 uppercase">Legal & Support</h2>
            <div class="space-y-4">
                <a href="https://chat.whatsapp.com/YOUR_LINK" class="glass p-6 flex items-center justify-between">
                    <div><h4 class="font-black uppercase text-xs">Official WhatsApp</h4><p class="text-[9px] opacity-40 uppercase">Community Group</p></div>
                    <i class="fa-brands fa-whatsapp text-2xl text-green-500"></i>
                </a>
                <div class="glass p-6">
                    <h4 class="font-black uppercase text-xs mb-2">Privacy Policy</h4>
                    <p class="text-[10px] opacity-50 leading-relaxed uppercase">We protect your data with institutional grade encryption. Your financial history is never shared with third parties.</p>
                </div>
                <div class="glass p-6">
                    <h4 class="font-black uppercase text-xs mb-4">FAQ</h4>
                    <div class="space-y-4">
                        <details class="text-[10px] uppercase font-bold"><summary class="opacity-80">How to withdraw?</summary><p class="mt-2 opacity-40">Minimum withdrawal is 500 PKR. Processed within 24 hours.</p></details>
                        <details class="text-[10px] uppercase font-bold"><summary class="opacity-80">Referral Bonus?</summary><p class="mt-2 opacity-40">Earn 10% from every direct node activation by your referral.</p></details>
                    </div>
                </div>
            </div>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-3xl font-black italic mb-8 uppercase">Terminal Bank</h2>
            
            <div class="glass p-4 mb-6 flex items-center gap-3">
                <input id="promo-input" type="text" placeholder="ENTER PROMOCODE" class="flex-1 bg-transparent border-none text-[10px] font-black uppercase focus:outline-none">
                <button onclick="applyPromocode()" class="text-blue-500 font-black text-[10px] uppercase">Activate</button>
            </div>

            <div class="glass p-6 space-y-6">
                <div class="flex gap-2">
                    <button onclick="toggleFinance('dep')" class="flex-1 py-4 rounded-2xl bg-blue-600 text-[10px] font-black uppercase">Deposit</button>
                    <button onclick="toggleFinance('wd')" class="flex-1 py-4 rounded-2xl bg-red-600 text-[10px] font-black uppercase">Withdraw</button>
                </div>
                
                <div id="finance-dep" class="space-y-4">
                    <input id="dep-amt" type="number" placeholder="Min 200 PKR" class="w-full p-4 rounded-xl bg-slate-900 text-xs outline-none">
                    <input id="dep-tid" type="text" placeholder="Transaction ID" class="w-full p-4 rounded-xl bg-slate-900 text-xs outline-none">
                    <button onclick="submitRequest('Deposit')" class="w-full bg-white text-black py-4 rounded-xl font-black uppercase text-xs">Verify Payment</button>
                </div>

                <div id="finance-wd" class="hidden space-y-4">
                    <input id="wd-amt" type="number" placeholder="Min 500 PKR" class="w-full p-4 rounded-xl bg-slate-900 text-xs outline-none">
                    <input id="wd-wallet" type="text" placeholder="Wallet / Phone Number" class="w-full p-4 rounded-xl bg-slate-900 text-xs outline-none">
                    <button onclick="submitRequest('Withdrawal')" class="w-full bg-red-600 text-white py-4 rounded-xl font-black uppercase text-xs">Request Cashout</button>
                </div>
            </div>

            <button id="admin-btn" onclick="openAdmin()" class="hidden w-full mt-10 bg-yellow-500 text-black py-4 rounded-2xl font-black uppercase text-xs italic">Open Admin Command Center</button>
        </div>

    </main>

    <div id="admin-panel" class="hidden fixed inset-0 z-[15000] bg-black/95 overflow-y-auto p-6">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-3xl font-black italic uppercase">Master Console</h2>
            <button onclick="closeAdmin()" class="text-2xl text-white/50">&times;</button>
        </div>

        <div class="grid grid-cols-2 gap-4 mb-10">
            <button onclick="toggleMaintenance()" class="bg-red-600 p-6 rounded-3xl font-black text-[10px] uppercase">Toggle Maintenance</button>
            <button onclick="alert('Promocode Manager: Use Firestore Dashboard')" class="bg-blue-600 p-6 rounded-3xl font-black text-[10px] uppercase">Promo Logic</button>
        </div>

        <h3 class="font-black text-xs uppercase opacity-30 mb-6">Pending Requests</h3>
        <div id="admin-requests" class="space-y-4 mb-10"></div>

        <h3 class="font-black text-xs uppercase opacity-30 mb-6">Registered Assets (Users)</h3>
        <div id="admin-users" class="space-y-4"></div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-20 glass flex justify-around items-center z-[4000]">
        <button onclick="changePage('home')" class="nav-item flex flex-col items-center p-4 nav-active"><i class="fa-solid fa-layer-group"></i><span class="text-[8px] font-black mt-1 uppercase">Nodes</span></button>
        <button onclick="changePage('assets')" class="nav-item flex flex-col items-center p-4"><i class="fa-solid fa-clock-rotate-left"></i><span class="text-[8px] font-black mt-1 uppercase">Logs</span></button>
        <button onclick="changePage('spin')" class="nav-item flex flex-col items-center p-4"><i class="fa-solid fa-dharmachakra"></i><span class="text-[8px] font-black mt-1 uppercase">Spin</span></button>
        <button onclick="changePage('finance')" class="nav-item flex flex-col items-center p-4"><i class="fa-solid fa-wallet"></i><span class="text-[8px] font-black mt-1 uppercase">Bank</span></button>
        <button onclick="changePage('info')" class="nav-item flex flex-col items-center p-4"><i class="fa-solid fa-circle-info"></i><span class="text-[8px] font-black mt-1 uppercase">Info</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();
        const provider = new firebase.auth.GoogleAuthProvider();

        let userObj = null, spinActive = false, rot = 0, tapCount = 0, isMaint = false;
        const ADMIN_ID = "net204"; // Global trigger

        const AVAILABLE_PLANS = [
            { id: 'n1', name: 'Alpha Core', price: 500, daily: 50, days: 30, img: 'https://images.unsplash.com/photo-1639322537228-f710d846310a?w=400' },
            { id: 'n2', name: 'Delta Mining', price: 2000, daily: 220, days: 30, img: 'https://images.unsplash.com/photo-1644088379091-d574269d422f?w=400' },
            { id: 'n3', name: 'Elite Cluster', price: 5000, daily: 600, days: 30, img: 'https://images.unsplash.com/photo-1620641788421-7a1c342ea42e?w=400' }
        ];

        // Global Maintenance Watcher
        db.collection("settings").doc("system").onSnapshot(doc => {
            isMaint = doc.exists ? doc.data().maintenance : false;
            if(isMaint && !isAdmin()) document.getElementById('maintenance-ui').classList.remove('hidden');
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

        function isAdmin() { return userObj?.isAdmin === true; }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { name: name, balance: 0, activePlans: [], history: [], lastSpin: 0, extraSpins: 0, isAdmin: false };
                document.getElementById('v-bal').innerText = "₨ " + userObj.balance.toLocaleString();
                if(userObj.isAdmin) {
                    document.getElementById('admin-btn').classList.remove('hidden');
                    document.getElementById('admin-indicator').classList.remove('hidden');
                }
                calculateProfit();
                renderPlans();
                renderAssets();
                renderUserHistory();
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
                const log = { type: 'Profit', amt: earned, date: Date.now() };
                await db.collection("users").doc(userObj.name).update({ 
                    balance: userObj.balance + earned, 
                    activePlans: updated,
                    history: firebase.firestore.FieldValue.arrayUnion(log)
                });
            }
        }

        function renderPlans() {
            let h = '';
            AVAILABLE_PLANS.forEach(p => {
                h += `<div class="glass p-4 plan-card">
                    <img src="${p.img}" class="w-full h-32 object-cover rounded-2xl mb-4 opacity-70">
                    <div class="flex justify-between items-end">
                        <div><h4 class="font-black uppercase text-[11px]">${p.name}</h4><p class="text-[9px] opacity-40 italic">30 Days</p></div>
                        <div class="text-right"><p class="text-blue-500 font-black text-xs">₨ ${p.daily}/day</p></div>
                    </div>
                    <button onclick="buyPlan('${p.id}')" class="w-full mt-4 bg-white text-black py-3 rounded-xl font-black text-[10px] uppercase">Activate</button>
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
                        <span class="font-black text-[10px] italic text-blue-500">${p.daysRemaining} Days</span>
                    </div>
                    <div class="progress-bar mb-1"><div class="progress-fill" style="width: ${prog}%"></div></div>
                </div>`;
            });
            document.getElementById('my-active-plans').innerHTML = h || '<p class="text-center opacity-20 py-10 uppercase text-[10px]">No active clusters</p>';
        }

        function renderUserHistory() {
            let h = '';
            (userObj.history || []).reverse().slice(0, 10).forEach(l => {
                h += `<div class="flex justify-between items-center p-4 glass rounded-2xl">
                    <div><p class="text-[10px] font-black uppercase">${l.type}</p><p class="text-[8px] opacity-40">${new Date(l.date).toLocaleDateString()}</p></div>
                    <p class="text-xs font-black ${l.amt > 0 ? 'text-green-500' : 'text-red-500'}">${l.amt > 0 ? '+' : ''}₨ ${l.amt}</p>
                </div>`;
            });
            document.getElementById('user-history-log').innerHTML = h;
        }

        async function submitRequest(type) {
            const amt = type === 'Deposit' ? document.getElementById('dep-amt').value : document.getElementById('wd-amt').value;
            const detail = type === 'Deposit' ? document.getElementById('dep-tid').value : document.getElementById('wd-wallet').value;
            if(!amt || !detail) return alert("Fill all fields!");
            
            await db.collection("requests").add({
                user: userObj.name,
                type: type,
                amt: parseFloat(amt),
                detail: detail,
                status: 'Pending',
                date: Date.now()
            });
            alert("Request submitted for institutional verification.");
        }

        async function buyPlan(id) {
            const p = AVAILABLE_PLANS.find(x => x.id === id);
            if(userObj.balance < p.price) return alert("Insufficient Liquidity!");
            const node = { id: p.id, name: p.name, dailyRate: p.daily, daysRemaining: p.days, purchaseDate: Date.now(), lastClaim: Date.now() };
            const log = { type: 'Activation', amt: -p.price, date: Date.now() };
            await db.collection("users").doc(userObj.name).update({ 
                balance: userObj.balance - p.price, 
                activePlans: firebase.firestore.FieldValue.arrayUnion(node),
                history: firebase.firestore.FieldValue.arrayUnion(log)
            });
            alert("Node Initialized.");
        }

        // --- Admin Functions ---
        function openAdmin() {
            document.getElementById('admin-panel').classList.remove('hidden');
            loadAdminData();
        }

        function loadAdminData() {
            // Load Requests
            db.collection("requests").where("status", "==", "Pending").onSnapshot(snap => {
                let h = '';
                snap.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="glass p-5 border-l-4 border-yellow-500">
                        <p class="text-[10px] font-black uppercase text-blue-500">${d.type} - ${d.user}</p>
                        <p class="text-xl font-black">₨ ${d.amt}</p>
                        <p class="text-[9px] opacity-40 mb-4">${d.detail}</p>
                        <div class="flex gap-2">
                            <button onclick="approveRequest('${doc.id}', '${d.user}', ${d.amt}, '${d.type}')" class="flex-1 bg-green-600 py-3 rounded-xl font-black text-[9px] uppercase">Approve</button>
                            <button onclick="rejectRequest('${doc.id}')" class="flex-1 bg-red-600 py-3 rounded-xl font-black text-[9px] uppercase">Reject</button>
                        </div>
                    </div>`;
                });
                document.getElementById('admin-requests').innerHTML = h || '<p class="text-center opacity-20 py-10 uppercase text-[10px]">No pending requests</p>';
            });

            // Load Users
            db.collection("users").limit(20).onSnapshot(snap => {
                let h = '';
                snap.forEach(doc => {
                    const u = doc.data();
                    h += `<div class="glass p-4 flex justify-between items-center">
                        <div><p class="text-[10px] font-black uppercase">${u.name}</p><p class="text-[9px] text-blue-500 font-bold">₨ ${u.balance.toLocaleString()}</p></div>
                        <button onclick="alert('Manual Adjust: Firestore Dashboard')" class="bg-white/5 p-3 rounded-lg"><i class="fa-solid fa-pen text-[10px]"></i></button>
                    </div>`;
                });
                document.getElementById('admin-users').innerHTML = h;
            });
        }

        async function approveRequest(id, userName, amt, type) {
            await db.collection("requests").doc(id).update({ status: 'Approved' });
            if(type === 'Deposit') {
                const uRef = db.collection("users").doc(userName);
                const cur = (await uRef.get()).data().balance;
                const log = { type: 'Deposit Approved', amt: amt, date: Date.now() };
                await uRef.update({ balance: cur + amt, history: firebase.firestore.FieldValue.arrayUnion(log) });
            }
            alert("Request Approved.");
        }

        async function rejectRequest(id) {
            await db.collection("requests").doc(id).update({ status: 'Rejected' });
            alert("Request Rejected.");
        }

        async function toggleMaintenance() {
            await db.collection("settings").doc("system").set({ maintenance: !isMaint });
            alert("System Status Toggled.");
        }

        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function handleLogoTap() { tapCount++; if(tapCount >= 5) { const p = prompt("Master Key:"); if(p === ADMIN_ID) { db.collection("users").doc(userObj.name).update({ isAdmin: true }); alert("Privileges Granted."); } tapCount = 0; } }
        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('nav-active'));
            event.currentTarget.classList.add('nav-active');
        }
        function toggleFinance(s) {
            document.getElementById('finance-dep').classList.toggle('hidden', s !== 'dep');
            document.getElementById('finance-wd').classList.toggle('hidden', s !== 'wd');
        }
        
        // Spin Logic (Previously added)
        function updateSpinUI() {
            if(!userObj) return;
            const diff = Date.now() - (userObj.lastSpin || 0);
            const canFree = diff >= 86400000;
            const btn = document.getElementById('spin-btn');
            const status = document.getElementById('spin-status');
            if(canFree) { btn.disabled = false; status.innerText = "FREE SPIN READY"; }
            else if(userObj.extraSpins > 0) { btn.disabled = false; status.innerText = `EXTRA SPIN (${userObj.extraSpins})`; }
            else { btn.disabled = true; status.innerText = "LOCKED"; }
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
                const log = { type: 'Spin Win', amt: win, date: Date.now() };
                upd.history = firebase.firestore.FieldValue.arrayUnion(log);
                await db.collection("users").doc(userObj.name).update(upd);
                alert(`Gained: ₨ ${win}`);
            }, 4000);
        }

        async function buyExtraSpins() {
            if(userObj.balance < 20) return alert("Need ₨ 20!");
            const log = { type: 'Spin Buy', amt: -20, date: Date.now() };
            await db.collection("users").doc(userObj.name).update({
                balance: userObj.balance - 20,
                extraSpins: (userObj.extraSpins || 0) + 2,
                history: firebase.firestore.FieldValue.arrayUnion(log)
            });
            alert("2 Extra Spins Added.");
        }
    </script>
</body>
</html>
