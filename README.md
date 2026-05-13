<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Elite | Global Institutional Terminal</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --primary: #3b82f6; --bg: #0b0f1a; --card: #161b2c; --gold: #fbbf24; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: #f1f5f9; overflow-x: hidden; }
        .glass { background: rgba(22, 27, 44, 0.85); backdrop-filter: blur(14px); border: 1px solid rgba(255,255,255,0.06); border-radius: 28px; }
        .page { display: none; animation: slideUp 0.5s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        #wheel { width: 300px; height: 300px; transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); border-radius: 50%; border: 8px solid #1e293b; }
        .progress-bar { height: 6px; background: rgba(255,255,255,0.1); border-radius: 10px; overflow: hidden; }
        .progress-fill { height: 100%; background: linear-gradient(90deg, #3b82f6, #60a5fa); transition: width 1s linear; }
        .floating-support { position: fixed; bottom: 120px; right: 25px; z-index: 5000; }
        input, select { background: #0f172a !important; border: 1px solid rgba(255,255,255,0.1) !important; color: white !important; outline: none; }
        ::-webkit-scrollbar { width: 0; }
    </style>
</head>
<body class="min-h-screen pb-32">

    <a href="https://t.me/your_telegram" class="floating-support w-14 h-14 bg-blue-600 rounded-full flex items-center justify-center shadow-2xl animate-bounce">
        <i class="fa-brands fa-telegram text-white text-2xl"></i>
    </a>

    <div id="admin-panel" class="hidden fixed inset-0 z-[9999] bg-black p-6 overflow-y-auto">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black text-blue-500 uppercase">Master Admin</h2>
            <button onclick="closeAdmin()" class="text-3xl">&times;</button>
        </div>
        <div id="admin-requests" class="space-y-4"></div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[6000] bg-[#0b0f1a] flex flex-col items-center justify-center p-10 text-center">
        <div onclick="handleLogoTap()" class="w-24 h-24 bg-blue-600 rounded-[2.5rem] flex items-center justify-center mb-8 shadow-2xl rotate-3 cursor-pointer active:scale-95 transition-transform">
            <i class="fa-solid fa-vault text-white text-4xl"></i>
        </div>
        <h1 class="text-4xl font-black italic tracking-tighter uppercase">Vestify <span class="text-blue-500">Elite</span></h1>
        <p class="text-[10px] text-slate-500 mt-2 uppercase tracking-[0.5em] font-black">Licensed LLC Terminal</p>
        <button onclick="login()" class="w-full max-w-xs mt-16 bg-white text-black py-5 rounded-[2rem] font-black uppercase text-xs tracking-widest shadow-2xl transition-all active:scale-95">Sync Secure Google</button>
        <p class="mt-10 text-[9px] opacity-30 uppercase font-bold">SECURED BY AES-256 ENCRYPTION</p>
    </section>

    <main id="app-ui" class="hidden pt-10 px-6">
        
        <div id="p-home" class="page active-page">
            <div class="glass p-8 mb-8 relative overflow-hidden bg-gradient-to-br from-slate-900 to-black">
                <div class="flex justify-between items-start">
                    <div>
                        <p class="text-[9px] text-blue-400 font-black uppercase tracking-widest">Global Balance</p>
                        <h2 class="text-5xl font-black italic mt-1" id="v-bal">₨ 0.00</h2>
                    </div>
                    <div class="bg-blue-600/20 text-blue-400 px-3 py-1 rounded-full text-[8px] font-black uppercase">Elite Partner</div>
                </div>
                <div class="mt-8 flex gap-3">
                    <button onclick="changePage('finance')" class="flex-1 bg-white text-black py-4 rounded-2xl font-black text-[10px] uppercase">Banking</button>
                    <button onclick="changePage('affiliate')" class="flex-1 bg-slate-800 text-white py-4 rounded-2xl font-black text-[10px] uppercase border border-white/5">Referral</button>
                </div>
            </div>

            <h3 class="font-black text-lg mb-6 italic uppercase tracking-tighter">Mining Operations</h3>
            <div id="active-nodes-list" class="space-y-4 mb-10"></div>
        </div>

        <div id="p-plans" class="page">
            <h2 class="text-3xl font-black italic mb-8 uppercase">Node Terminal</h2>
            <div id="plans-grid" class="space-y-4 pb-10"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black italic mb-2 uppercase">Lucky Terminal</h2>
            <p class="text-[10px] text-slate-500 uppercase tracking-widest mb-10">Win Up to ₨ 50 Instant Bonus</p>
            <div class="relative inline-block mb-12">
                <div class="absolute -top-6 left-1/2 -translate-x-1/2 w-10 h-12 bg-red-600 z-50 shadow-xl" style="clip-path: polygon(50% 100%, 0 0, 100% 0);"></div>
                <img id="wheel" src="IMG_20260513_123757.jpg" alt="Wheel">
            </div>
            <div class="space-y-4 max-w-xs mx-auto">
                <button onclick="executeSpin()" class="w-full bg-blue-600 py-5 rounded-[2rem] font-black uppercase text-xs">Execute Spin (Free Daily)</button>
                <button onclick="buySpins()" class="w-full bg-slate-800 py-4 rounded-[2rem] font-black uppercase text-[9px] border border-white/5">Unlock 2 Extra Spins (₨ 20)</button>
            </div>
        </div>

        <div id="p-affiliate" class="page">
            <h2 class="text-3xl font-black italic mb-6 uppercase">Referral Portal</h2>
            <div class="glass p-6 mb-6">
                <p class="text-[10px] opacity-50 uppercase font-black mb-2">Your Invitation Link</p>
                <div class="flex gap-2">
                    <input id="ref-link" readonly value="https://u-s-h.github.io/vestify/?ref=USER" class="flex-1 p-3 rounded-xl text-[10px] font-bold">
                    <button onclick="copyRef()" class="bg-blue-600 px-4 rounded-xl font-black text-xs">COPY</button>
                </div>
                <div class="mt-6 grid grid-cols-2 gap-4 border-t border-white/5 pt-6">
                    <div><p class="text-[9px] opacity-50 uppercase font-bold">Referral Earnings</p><p class="text-lg font-black text-green-400">₨ 0.00</p></div>
                    <div><p class="text-[9px] opacity-50 uppercase font-bold">Team Salary</p><p class="text-lg font-black text-blue-400">Pending</p></div>
                </div>
            </div>
        </div>

        <div id="p-about" class="page pb-10">
            <h2 class="text-3xl font-black italic mb-8 uppercase">Company Profile</h2>
            <div class="glass p-6 space-y-6 text-[11px] leading-relaxed opacity-80">
                <div><h4 class="text-blue-500 font-black uppercase mb-2">Legal Identity</h4><p>Vestify Elite LLC is a registered financial technology firm (Reg No: LLC-2024-PK782). Headquartered in London, UK with regional mining clusters in Rawalpindi, Pakistan.</p></div>
                <div><h4 class="text-blue-500 font-black uppercase mb-2">Privacy Policy</h4><p>We use end-to-end encryption for all transactions. Your data is never shared with third-party brokers. Withdrawals are processed within 24 hours.</p></div>
                <div><h4 class="text-blue-500 font-black uppercase mb-2">FAQ</h4><p>Q: Min Deposit? A: 200 PKR. <br>Q: Min Withdraw? A: 100 PKR. <br>Q: How to earn? A: Buy Mining Nodes or refer friends.</p></div>
            </div>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-3xl font-black italic mb-8 uppercase">Banking</h2>
            <div class="glass p-6 space-y-6">
                <div class="flex gap-2">
                    <button onclick="toggleFinance('dep')" class="flex-1 py-4 rounded-2xl bg-blue-600 text-[10px] font-black uppercase">Deposit</button>
                    <button onclick="toggleFinance('wd')" class="flex-1 py-4 rounded-2xl bg-red-600 text-[10px] font-black uppercase">Withdraw</button>
                </div>
                
                <div id="finance-dep" class="space-y-4">
                    <select id="dep-method" class="w-full p-4 rounded-xl" onchange="updateDepInfo()">
                        <option value="easypaisa">EasyPaisa (03379827882)</option>
                        <option value="jazzcash">JazzCash / SadaPay (03705519562)</option>
                    </select>
                    <input id="dep-amt" type="number" placeholder="Min 200 PKR" class="w-full p-4 rounded-xl">
                    <input id="dep-tid" type="text" placeholder="TID Number" class="w-full p-4 rounded-xl">
                    <input id="dep-img" type="file" accept="image/*" class="w-full text-[10px]">
                    <button onclick="submitDeposit()" class="w-full bg-blue-600 py-4 rounded-xl font-black uppercase text-xs">Verify Payment</button>
                </div>

                <div id="finance-wd" class="hidden space-y-4">
                    <input id="wd-amt" type="number" placeholder="Min 100 PKR" class="w-full p-4 rounded-xl">
                    <input id="wd-acc" type="text" placeholder="Account Number" class="w-full p-4 rounded-xl">
                    <input id="wd-name" type="text" placeholder="Account Title" class="w-full p-4 rounded-xl">
                    <button onclick="submitWithdraw()" class="w-full bg-red-600 py-4 rounded-xl font-black uppercase text-xs">Submit Request</button>
                </div>
                <div id="history-log" class="pt-6 space-y-3"></div>
            </div>
        </div>

    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-22 glass flex justify-around items-center z-[4000] border-t border-white/5">
        <button onclick="changePage('home')" class="flex flex-col items-center p-4 text-blue-500"><i class="fa-solid fa-house"></i><span class="text-[8px] font-black uppercase mt-1">Vault</span></button>
        <button onclick="changePage('plans')" class="flex flex-col items-center p-4 text-slate-500"><i class="fa-solid fa-microchip"></i><span class="text-[8px] font-black uppercase mt-1">Nodes</span></button>
        <button onclick="changePage('spin')" class="flex flex-col items-center p-4 text-slate-500"><i class="fa-solid fa-dharmachakra"></i><span class="text-[8px] font-black uppercase mt-1">Game</span></button>
        <button onclick="changePage('about')" class="flex flex-col items-center p-4 text-slate-500"><i class="fa-solid fa-building-columns"></i><span class="text-[8px] font-black uppercase mt-1">Legal</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();
        const provider = new firebase.auth.GoogleAuthProvider();

        let userObj = null, tapCount = 0, spinActive = false, rot = 0;

        // AUTH & CORE
        async function login() {
            const r = await auth.signInWithPopup(provider);
            localStorage.setItem('v_user', r.user.displayName);
            initApp(r.user.displayName);
        }

        function initApp(name) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('ref-link').value = `https://u-s-h.github.io/vestify/?ref=${name.replace(/\s/g,'')}`;
            syncData(name);
            renderPlans();
            setInterval(updateTimers, 1000);
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { name: name, balance: 0, plans: [], lastSpin: 0, eliteSpins: 0 };
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
                processAutoProfit();
                renderActiveNodes();
            });
            db.collection("requests").where("user", "==", name).orderBy("time", "desc").onSnapshot(snap => {
                let h = '';
                snap.forEach(d => {
                    const r = d.data();
                    h += `<div class="glass p-4 flex justify-between items-center text-[9px] uppercase font-bold">
                        <div><p>${r.type}</p><p class="opacity-50">₨ ${r.amount}</p></div>
                        <div class="${r.status==='Approved' ? 'text-green-400' : 'text-yellow-400'}">${r.status}</div>
                    </div>`;
                });
                document.getElementById('history-log').innerHTML = h;
            });
        }

        async function processAutoProfit() {
            if(!userObj.plans) return;
            const now = Date.now();
            let credit = 0;
            let updated = userObj.plans.map(p => {
                const hours = (now - p.lastClaim) / 3600000;
                if(hours >= 24) {
                    const days = Math.floor(hours/24);
                    credit += (p.daily * days);
                    p.lastClaim += (days * 86400000);
                }
                return p;
            });
            if(credit > 0) await db.collection("users").doc(userObj.name).update({ balance: userObj.balance + credit, plans: updated });
        }

        function renderPlans() {
            let h = '';
            for(let i=1; i<=22; i++) {
                const price = i===1 ? 200 : i*1000;
                h += `<div class="glass p-6 flex justify-between items-center">
                    <div><p class="font-black text-xs uppercase italic">Node v.${i}</p><p class="text-[9px] text-green-400 font-bold uppercase">ROI: ₨ ${Math.floor(price*0.1)}/day</p></div>
                    <button onclick="buyPlan(${i}, ${price})" class="bg-blue-600 px-5 py-2 rounded-xl text-[10px] font-black uppercase">₨ ${price}</button>
                </div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
        }

        async function buyPlan(i, price) {
            if(userObj.balance < price) return alert("Insufficient Capital!");
            const plan = { name: `Node v.${i}`, daily: price*0.1, lastClaim: Date.now(), expiry: Date.now() + (30*86400000), boughtAt: Date.now() };
            await db.collection("users").doc(userObj.name).update({ balance: userObj.balance - price, plans: firebase.firestore.FieldValue.arrayUnion(plan) });
            alert("Node Activated!"); changePage('home');
        }

        function renderActiveNodes() {
            const list = document.getElementById('active-nodes-list');
            if(!userObj.plans || userObj.plans.length === 0) { list.innerHTML = '<p class="text-center opacity-30 text-[10px] uppercase py-10 font-black">No Active Mining Nodes</p>'; return; }
            list.innerHTML = userObj.plans.map(p => {
                const perc = Math.max(0, ((p.expiry - Date.now()) / (30*86400000)) * 100);
                return `<div class="glass p-5">
                    <div class="flex justify-between items-center mb-3"><p class="text-[10px] font-black uppercase">${p.name}</p><p class="text-[10px] font-black text-blue-400 timer" data-exp="${p.expiry}">--:--:--</p></div>
                    <div class="progress-bar"><div class="progress-fill" style="width: ${perc}%"></div></div>
                </div>`;
            }).join('');
        }

        function updateTimers() {
            document.querySelectorAll('.timer').forEach(el => {
                const diff = parseInt(el.dataset.exp) - Date.now();
                if(diff <= 0) el.innerText = "EXPIRED";
                else {
                    const h = Math.floor(diff/3600000), m = Math.floor((diff%3600000)/60000), s = Math.floor((diff%60000)/1000);
                    el.innerText = `${h}h ${m}m ${s}s`;
                }
            });
        }

        async function executeSpin() {
            if(spinActive) return;
            const today = new Date().toDateString();
            const hasFree = today !== (userObj.lastSpin ? new Date(userObj.lastSpin).toDateString() : "");
            if(!hasFree && userObj.eliteSpins <= 0) return alert("Unlock More Spins!");
            
            spinActive = true;
            const stopDeg = [0, 60, 120, 180, 240, 300][Math.floor(Math.random()*6)];
            rot += (3600 + stopDeg - (rot%360));
            document.getElementById('wheel').style.transform = `rotate(${rot}deg)`;
            
            setTimeout(async () => {
                spinActive = false;
                const win = [5, 0, 0, 2, 50, 10][stopDeg/60];
                const up = { lastSpin: Date.now(), balance: userObj.balance + win };
                if(!hasFree) up.eliteSpins = userObj.eliteSpins - 1;
                await db.collection("users").doc(userObj.name).update(up);
                alert(win > 0 ? `Elite Bonus: ₨ ${win}` : "System Recycle - Try Again!");
            }, 4000);
        }

        async function submitDeposit() {
            const amt = parseInt(document.getElementById('dep-amt').value);
            const tid = document.getElementById('dep-tid').value;
            const img = document.getElementById('dep-img').files[0];
            if(amt < 200 || !tid || !img) return alert("Complete all protocols!");
            const reader = new FileReader();
            reader.onloadend = async () => {
                await db.collection("requests").add({ user: userObj.name, type: 'Deposit', amount: amt, tid: tid, proof: reader.result, status: 'Pending', time: Date.now() });
                alert("Request Logged!");
            };
            reader.readAsDataURL(img);
        }

        async function submitWithdraw() {
            const amt = parseInt(document.getElementById('wd-amt').value);
            if(amt < 100 || amt > userObj.balance) return alert("Invalid Capital Amount!");
            await db.collection("requests").add({ user: userObj.name, type: 'Withdraw', amount: amt, acc: document.getElementById('wd-acc').value, name: document.getElementById('wd-name').value, status: 'Pending', time: Date.now() });
            await db.collection("users").doc(userObj.name).update({ balance: userObj.balance - amt });
            alert("Withdrawal Pending!");
        }

        function handleLogoTap() { tapCount++; if(tapCount>=5){ const p=prompt("Master Key:"); if(p==="net204") { document.getElementById('admin-panel').classList.remove('hidden'); loadAdmin(); } tapCount=0; } }
        function loadAdmin() {
            db.collection("requests").where("status", "==", "Pending").onSnapshot(snap => {
                let h = '';
                snap.forEach(d => {
                    const r = d.data();
                    h += `<div class="glass p-5 text-[10px] uppercase font-bold">
                        <p class="text-blue-400 mb-2">${r.type} REQUEST</p>
                        <p>User: ${r.user} | ₨ ${r.amount}</p>
                        ${r.type==='Deposit' ? `<p>TID: ${r.tid}</p><img src="${r.proof}" class="w-full mt-2 rounded-xl border border-white/10">` : `<p>Acc: ${r.acc} (${r.name})</p>`}
                        <div class="flex gap-2 mt-4">
                            <button onclick="approve('${d.id}', '${r.user}', ${r.amount})" class="bg-green-600 px-4 py-2 rounded-lg">Approve</button>
                            <button onclick="reject('${d.id}')" class="bg-red-600 px-4 py-2 rounded-lg">Reject</button>
                        </div>
                    </div>`;
                });
                document.getElementById('admin-requests').innerHTML = h || '<p class="text-center opacity-20">No pending protocols</p>';
            });
        }
        async function approve(id, user, amt) {
            await db.collection("requests").doc(id).update({ status: 'Approved' });
            const ref = db.collection("users").doc(user);
            const cur = (await ref.get()).data().balance;
            await ref.update({ balance: cur + amt });
        }
        async function reject(id) { await db.collection("requests").doc(id).update({ status: 'Rejected' }); }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function toggleFinance(s) { document.getElementById('finance-dep').classList.toggle('hidden', s!=='dep'); document.getElementById('finance-wd').classList.toggle('hidden', s!=='wd'); }
        function copyRef() { const el = document.getElementById('ref-link'); el.select(); document.execCommand('copy'); alert("Link Copied!"); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }

        window.onload = () => { if(localStorage.getItem('v_user')) initApp(localStorage.getItem('v_user')); };
    </script>
</body>
</html>
