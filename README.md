<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Elite | Institutional Terminal</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap');
        :root { --primary: #3b82f6; --bg: #0b0f1a; --card: #161b2c; --gold: #fbbf24; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: #f1f5f9; overflow-x: hidden; -webkit-tap-highlight-color: transparent; }
        .glass { background: rgba(22, 27, 44, 0.8); backdrop-filter: blur(12px); border: 1px solid rgba(255,255,255,0.05); border-radius: 28px; }
        .hero-card { background: linear-gradient(135deg, #1e293b 0%, #0f172a 100%); border: 1px solid rgba(255,255,255,0.08); }
        .page { display: none; animation: slideUp 0.5s ease-out; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        #wheel { width: 300px; height: 300px; transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); border-radius: 50%; box-shadow: 0 0 50px rgba(59,130,246,0.2); }
        .progress-bar { height: 4px; background: rgba(255,255,255,0.1); border-radius: 2px; overflow: hidden; }
        .progress-fill { height: 100%; background: var(--primary); transition: width 1s linear; }
        input, select { background: #0f172a !important; border: 1px solid rgba(255,255,255,0.1) !important; color: white !important; }
        ::-webkit-scrollbar { width: 0; }
    </style>
</head>
<body class="min-h-screen pb-32">

    <div id="admin-panel" class="hidden fixed inset-0 z-[9999] bg-black p-6 overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-2xl font-black text-blue-500 italic uppercase">Admin Protocol</h2>
            <button onclick="closeAdmin()" class="text-4xl">&times;</button>
        </div>
        <div id="admin-req-list" class="space-y-4"></div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[5000] bg-[#0b0f1a] flex flex-col items-center justify-center p-10 text-center">
        <div onclick="handleLogoTap()" class="w-24 h-24 bg-blue-600 rounded-[2.5rem] flex items-center justify-center mb-8 shadow-2xl rotate-3 cursor-pointer active:scale-90 transition-transform">
            <i class="fa-solid fa-vault text-white text-4xl"></i>
        </div>
        <h1 class="text-4xl font-black italic tracking-tighter text-white uppercase">Vestify <span class="text-blue-500">Elite</span></h1>
        <p class="text-slate-500 text-xs mt-3 uppercase tracking-widest font-bold">Institutional Terminal v4.2</p>
        <button onclick="login()" class="w-full max-w-xs mt-16 bg-white text-black py-5 rounded-[2rem] font-extrabold flex items-center justify-center gap-4 active:scale-95 shadow-2xl transition-all">
            <i class="fa-brands fa-google text-xl"></i> GOOGLE SYNC
        </button>
    </section>

    <main id="app-ui" class="hidden pt-10 px-6">
        
        <div id="p-home" class="page active-page">
            <div class="hero-card rounded-[2.5rem] p-8 mb-8 shadow-2xl relative overflow-hidden">
                <div class="absolute -right-10 -top-10 w-40 h-40 bg-blue-600/10 rounded-full blur-3xl"></div>
                <p class="text-[10px] text-blue-400 font-black uppercase tracking-widest mb-1">Portfolio Value</p>
                <h2 class="text-5xl font-black italic tracking-tighter" id="v-bal">₨ 0.00</h2>
                <div class="flex gap-3 mt-8">
                    <button onclick="changePage('finance')" class="flex-1 bg-white text-black py-4 rounded-2xl font-black text-[10px] uppercase">Finance</button>
                    <button onclick="changePage('spin')" class="flex-1 bg-slate-800 text-white py-4 rounded-2xl font-black text-[10px] uppercase border border-white/5">Spin</button>
                </div>
            </div>

            <div class="flex justify-between items-center mb-6">
                <h3 class="text-xl font-black italic uppercase">Active Nodes</h3>
                <span id="node-count" class="text-[10px] bg-blue-600/20 text-blue-400 px-3 py-1 rounded-full font-bold">0 ACTIVE</span>
            </div>
            <div id="active-nodes-list" class="space-y-4 mb-10">
                <p class="text-center opacity-30 text-xs py-10 uppercase font-bold tracking-widest">No nodes mining...</p>
            </div>
        </div>

        <div id="p-plans" class="page">
            <h2 class="text-3xl font-black italic mb-8 uppercase">Node Market</h2>
            <div id="plans-grid" class="space-y-4 pb-10"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black italic mb-2">Lucky Terminal</h2>
            <div class="flex justify-center gap-4 mb-10 text-[10px] font-black uppercase tracking-tighter">
                <span class="text-blue-400">Free Spins: <span id="free-spins">1</span></span>
                <span class="text-gold">Elite Spins: <span id="elite-spins">0</span></span>
            </div>
            <div class="relative inline-block mb-12">
                <div class="absolute -top-6 left-1/2 -translate-x-1/2 w-10 h-12 bg-red-600 z-50 shadow-xl" style="clip-path: polygon(50% 100%, 0 0, 100% 0);"></div>
                <img id="wheel" src="IMG_20260513_123757.jpg" alt="Wheel">
            </div>
            <div class="flex flex-col gap-4 max-w-xs mx-auto">
                <button onclick="executeSpin()" class="w-full bg-blue-600 text-white py-5 rounded-[2rem] font-black uppercase text-xs tracking-widest shadow-2xl active:scale-95">Spin Now</button>
                <button onclick="buySpins()" class="w-full bg-slate-800 text-white py-4 rounded-[2rem] font-black uppercase text-[9px] border border-white/10 tracking-widest">Unlock 2 Spins (₨ 20)</button>
            </div>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-3xl font-black italic mb-8 uppercase">Finance Terminal</h2>
            <div class="glass p-8 space-y-6">
                <div class="grid grid-cols-2 gap-4">
                    <button onclick="showSubPage('dep')" class="p-4 rounded-2xl bg-blue-600/10 border border-blue-600/30 text-blue-400 font-black text-xs">DEPOSIT</button>
                    <button onclick="showSubPage('wd')" class="p-4 rounded-2xl bg-red-600/10 border border-red-600/30 text-red-400 font-black text-xs">WITHDRAW</button>
                </div>
                
                <div id="sub-dep" class="space-y-4">
                    <select id="dep-method" class="w-full p-4 rounded-xl" onchange="updateDepNum()">
                        <option value="easypaisa">EasyPaisa</option>
                        <option value="jazzcash">JazzCash / SadaPay</option>
                    </select>
                    <div class="bg-blue-600/5 p-4 rounded-xl border border-white/5">
                        <p class="text-[10px] opacity-50 uppercase font-black">Transfer to Account</p>
                        <p id="dep-target-num" class="text-xl font-black mt-1">03379827882</p>
                    </div>
                    <input id="dep-amt" type="number" placeholder="Amount (Min 200)" class="w-full p-4 rounded-xl">
                    <input id="dep-tid" type="text" placeholder="TID Number" class="w-full p-4 rounded-xl">
                    <input id="dep-img" type="file" accept="image/*" class="w-full text-xs">
                    <button onclick="submitDeposit()" class="w-full bg-blue-600 py-4 rounded-xl font-black text-xs uppercase">Submit Proof</button>
                </div>

                <div id="sub-wd" class="hidden space-y-4">
                    <input id="wd-amt" type="number" placeholder="Amount (Min 100)" class="w-full p-4 rounded-xl">
                    <input id="wd-acc" type="text" placeholder="Account Number" class="w-full p-4 rounded-xl">
                    <input id="wd-name" type="text" placeholder="Full Name" class="w-full p-4 rounded-xl">
                    <button onclick="submitWithdraw()" class="w-full bg-red-600 py-4 rounded-xl font-black text-xs uppercase">Confirm Withdraw</button>
                </div>

                <div class="pt-6 border-t border-white/5">
                    <h4 class="font-black text-xs uppercase opacity-40 mb-4">Activity Log</h4>
                    <div id="history-log" class="space-y-3"></div>
                </div>
            </div>
        </div>

    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-22 glass flex justify-around items-center z-[4000]">
        <button onclick="changePage('home')" class="nav-item flex flex-col items-center text-blue-500"><i class="fa-solid fa-house"></i><span class="text-[8px] font-bold mt-1 uppercase">Vault</span></button>
        <button onclick="changePage('plans')" class="nav-item flex flex-col items-center text-slate-500"><i class="fa-solid fa-microchip"></i><span class="text-[8px] font-bold mt-1 uppercase">Nodes</span></button>
        <button onclick="changePage('spin')" class="nav-item flex flex-col items-center text-slate-500"><i class="fa-solid fa-dharmachakra"></i><span class="text-[8px] font-bold mt-1 uppercase">Game</span></button>
        <button onclick="changePage('finance')" class="nav-item flex flex-col items-center text-slate-500"><i class="fa-solid fa-wallet"></i><span class="text-[8px] font-bold mt-1 uppercase">Cash</span></button>
    </nav>

    <script>
        // FIREBASE SETUP
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();
        const provider = new firebase.auth.GoogleAuthProvider();

        let userObj = null, tapCount = 0, spinActive = false, totalRot = 0;

        // PLANS DATA (20+ Plans)
        const ELITE_PLANS = [];
        for(let i=1; i<=22; i++) {
            const price = i === 1 ? 200 : (i * 1000);
            ELITE_PLANS.push({
                id: i,
                name: `Elite Node v.${i}`,
                price: price,
                daily: Math.floor(price * 0.12),
                days: 30,
                icon: i%2==0 ? 'bolt' : 'microchip'
            });
        }

        // AUTH & CORE
        async function login() {
            const r = await auth.signInWithPopup(provider);
            localStorage.setItem('v_elite_user', r.user.displayName);
            startApp(r.user.displayName);
        }

        function startApp(name) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            syncData(name);
            renderMarket();
            setInterval(updateTimers, 1000);
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { name: name, balance: 0, plans: [], lastSpin: 0, eliteSpins: 0 };
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
                document.getElementById('elite-spins').innerText = userObj.eliteSpins || 0;
                
                // Check free daily spin
                const today = new Date().toDateString();
                const last = userObj.lastSpin ? new Date(userObj.lastSpin).toDateString() : "";
                document.getElementById('free-spins').innerText = today === last ? 0 : 1;

                processAutomaticProfits();
                renderActiveNodes();
            });

            db.collection("requests").where("user", "==", name).orderBy("time", "desc").onSnapshot(snap => {
                let h = '';
                snap.forEach(d => {
                    const r = d.data();
                    const color = r.status==='Approved' ? 'text-green-400' : (r.status==='Rejected' ? 'text-red-400' : 'text-yellow-400');
                    h += `<div class="glass p-4 flex justify-between items-center text-[10px]">
                        <div><p class="font-bold uppercase">${r.type}</p><p class="opacity-50">₨ ${r.amount}</p></div>
                        <div class="font-black ${color}">${r.status}</div>
                    </div>`;
                });
                document.getElementById('history-log').innerHTML = h;
            });
        }

        // AUTOMATIC PROFIT CALCULATION (Every 24h)
        async function processAutomaticProfits() {
            if(!userObj.plans || userObj.plans.length === 0) return;
            const now = Date.now();
            let totalToCredit = 0;
            let updatedPlans = userObj.plans.map(p => {
                const hoursPassed = (now - p.lastClaim) / 3600000;
                if(hoursPassed >= 24) {
                    const daysToCredit = Math.floor(hoursPassed / 24);
                    totalToCredit += (p.daily * daysToCredit);
                    p.lastClaim += (daysToCredit * 86400000);
                }
                return p;
            });

            if(totalToCredit > 0) {
                await db.collection("users").doc(userObj.name).update({
                    balance: userObj.balance + totalToCredit,
                    plans: updatedPlans
                });
            }
        }

        // UI RENDERING
        function renderMarket() {
            const grid = document.getElementById('plans-grid');
            grid.innerHTML = ELITE_PLANS.map(p => `
                <div class="glass p-6 flex justify-between items-center">
                    <div class="flex items-center gap-4">
                        <div class="w-12 h-12 bg-blue-600/10 rounded-2xl flex items-center justify-center text-blue-500"><i class="fa-solid fa-${p.icon}"></i></div>
                        <div>
                            <h4 class="font-black text-xs uppercase italic">${p.name}</h4>
                            <p class="text-[9px] text-green-400 font-bold uppercase">Profit: ₨ ${p.daily}/day</p>
                        </div>
                    </div>
                    <button onclick="buyPlan(${p.id})" class="bg-blue-600 text-white px-5 py-2.5 rounded-xl text-[10px] font-black uppercase tracking-widest">₨ ${p.price}</button>
                </div>
            `).join('');
        }

        function renderActiveNodes() {
            const list = document.getElementById('active-nodes-list');
            const nodes = userObj.plans || [];
            document.getElementById('node-count').innerText = `${nodes.length} ACTIVE`;
            if(nodes.length === 0) return;

            list.innerHTML = nodes.map((p, index) => {
                const remaining = p.expiry - Date.now();
                const totalDur = p.expiry - p.boughtAt;
                const perc = Math.max(0, (remaining / totalDur) * 100);
                return `
                <div class="glass p-5">
                    <div class="flex justify-between items-center mb-3">
                        <p class="text-[10px] font-black uppercase italic">${p.name}</p>
                        <p class="text-[10px] text-blue-400 font-black timer" data-expiry="${p.expiry}">--:--:--</p>
                    </div>
                    <div class="progress-bar"><div class="progress-fill" style="width: ${perc}%"></div></div>
                </div>`;
            }).join('');
        }

        function updateTimers() {
            document.querySelectorAll('.timer').forEach(el => {
                const exp = parseInt(el.dataset.expiry);
                const diff = exp - Date.now();
                if(diff <= 0) el.innerText = "EXPIRED";
                else {
                    const h = Math.floor(diff/3600000);
                    const m = Math.floor((diff%3600000)/60000);
                    const s = Math.floor((diff%60000)/1000);
                    el.innerText = `${h}h ${m}m ${s}s`;
                }
            });
        }

        // ACTIONS
        async function buyPlan(id) {
            const p = ELITE_PLANS.find(x => x.id === id);
            if(userObj.balance < p.price) return alert("Insufficient Balance!");
            
            const newPlan = {
                ...p,
                boughtAt: Date.now(),
                lastClaim: Date.now(),
                expiry: Date.now() + (p.days * 86400000)
            };

            await db.collection("users").doc(userObj.name).update({
                balance: userObj.balance - p.price,
                plans: firebase.firestore.FieldValue.arrayUnion(newPlan)
            });
            alert("Node Activated!");
            changePage('home');
        }

        async function executeSpin() {
            if(spinActive) return;
            const today = new Date().toDateString();
            const last = userObj.lastSpin ? new Date(userObj.lastSpin).toDateString() : "";
            const hasFree = today !== last;

            if(!hasFree && (userObj.eliteSpins || 0) <= 0) return alert("Buy More Spins!");

            spinActive = true;
            const wheel = document.getElementById('wheel');
            
            // Rewards mapping: [Rs 5, Rs 10, Rs 50, Rs 2, 0, 0]
            const rewards = [
                { val: 5, deg: 0 }, { val: 10, deg: 300 }, { val: 50, deg: 240 },
                { val: 2, deg: 180 }, { val: 0, deg: 120 }, { val: 0, deg: 60 }
            ];
            const prize = rewards[Math.floor(Math.random() * rewards.length)];
            const extra = (Math.floor(Math.random() * 5) + 5) * 360;
            totalRot += extra + prize.deg - (totalRot % 360);
            
            wheel.style.transform = `rotate(${totalRot}deg)`;

            setTimeout(async () => {
                spinActive = false;
                const up = { lastSpin: Date.now() };
                if(!hasFree) up.eliteSpins = userObj.eliteSpins - 1;
                if(prize.val > 0) up.balance = userObj.balance + prize.val;
                
                await db.collection("users").doc(userObj.name).update(up);
                alert(prize.val > 0 ? `Congrats! ₨ ${prize.val} Added!` : "Try Again!");
            }, 4000);
        }

        async function buySpins() {
            if(userObj.balance < 20) return alert("Insufficient Funds!");
            await db.collection("users").doc(userObj.name).update({
                balance: userObj.balance - 20,
                eliteSpins: (userObj.eliteSpins || 0) + 2
            });
            alert("2 Elite Spins Unlocked!");
        }

        // FINANCE LOGIC
        function updateDepNum() {
            const m = document.getElementById('dep-method').value;
            document.getElementById('dep-target-num').innerText = m === 'easypaisa' ? "03379827882" : "03705519562";
        }

        async function submitDeposit() {
            const amt = parseInt(document.getElementById('dep-amt').value);
            const tid = document.getElementById('dep-tid').value;
            const file = document.getElementById('dep-img').files[0];
            if(amt < 200 || !tid || !file) return alert("Fill all details correctly!");

            const reader = new FileReader();
            reader.onloadend = async () => {
                await db.collection("requests").add({
                    user: userObj.name, type: 'Deposit', amount: amt, tid: tid, proof: reader.result,
                    status: 'Pending', time: Date.now(), method: document.getElementById('dep-method').value
                });
                alert("Request Sent!");
            };
            reader.readAsDataURL(file);
        }

        async function submitWithdraw() {
            const amt = parseInt(document.getElementById('wd-amt').value);
            if(amt < 100 || amt > userObj.balance) return alert("Invalid Amount!");
            await db.collection("requests").add({
                user: userObj.name, type: 'Withdraw', amount: amt, 
                acc: document.getElementById('wd-acc').value, name: document.getElementById('wd-name').value,
                status: 'Pending', time: Date.now()
            });
            await db.collection("users").doc(userObj.name).update({ balance: userObj.balance - amt });
            alert("Withdrawal Pending!");
        }

        // ADMIN ACCESS
        function handleLogoTap() { tapCount++; if(tapCount>=5){ const p=prompt("Key:"); if(p==="net204") document.getElementById('admin-panel').classList.remove('hidden'); tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function showSubPage(s) { document.getElementById('sub-dep').classList.toggle('hidden', s!=='dep'); document.getElementById('sub-wd').classList.toggle('hidden', s!=='wd'); }

        window.onload = () => { const u=localStorage.getItem('v_elite_user'); if(u) startApp(u); };
    </script>
</body>
</html>
